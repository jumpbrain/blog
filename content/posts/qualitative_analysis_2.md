---
date: '2025-08-27T12:48:30+02:00'
draft: true
title: 'Data Scientist at War: Part 2'
description: 'Adding uncertainty to our beliefs.'
tags: ["Useful thought model"]
categories: ["Data Science"]
# comments: true
---


### Introduction
In my last post we talked about the importance of building a wide repetoire when it comes to modelling. In this post we'll build on the method presented by incorporating uncertanity into our views. 

*Caveat: Your first question could sound something like; should we inject uncertanity to our beliefs? And you're right to question that! After all, we are dealing in assumptions and beliefs here not evidence. My honest answer is that I don't know. By adding complexity to our beliefs I think we're at the risk of interpreting emotional padding as evidence. Nontheless, here's a method for adding uncertanity, should you insist!*

In early spring 2022 I made a prediction: 

> The Russo-Ukrainian war will not end in russias favour. The invasion will deteriorate into territorial battles raging indifinetly **given the same engagement from the west and the east.**

To make my prediction I borrowed a method from Bruno B. de Mesquitas book 'The predictioneer's game' (2009). Last time, we identified all possible outcomes for actors and placed them on a numbers line as a ruler. Then, we assigned values between 0 and 100 for three attributes: $Influence$, how much influence each actor could use to get what they want. $Salience$, how much each actor cares about the issue. We found each actors $\text{Ideal point}$, the ideal outcome (using our ruler) for each actor. Lastly, we produced a weighted average of $\text{Ideal point}$, weighted by $Power$. Where, $Power = Salince \\cdot Influence$. Now, we're going to incorporate some uncertanity into this method.

### Method
Since we don't know the exact values for how much each actor cares or how much influence they can use we treat **Salience** and **Influence** as random variables that live between 0 and 100, rather than constants (as we did before). Then, we pick a prior, our starting belief. We can use the same numbers as we did in the previous post with one important caveat, our starting belief should reflect our belief about each actors influence and salience **on average**. This time around it's not feasible to use pen and paper. We will follow the same procedure as outlined in the last post but simulate our estimates using R coding language. We start by establishing some basic notation:




# re-write method section for log-normal prior.



For each actor $a$ we pick two things, for salience, $S\_a$, and influence, $I\_a$, respectively: 
> **A best-guess average** $\mu$ between 0 and 100. **A spread** $s$ of how unsure we are. 

We model these quantities with a **Beta** prior for simplicity:

$S\_a \sim  \text{Beta}(\alpha\_{a}^S, \beta\_{a}^S)$, $I\_a \sim  \text{Beta}(\alpha\_{a}^I, \beta\_{a}^I)$.

*Caveat: We don't hand-pick the $\alpha$ or $\beta$. We use a computer for that. The computer will convert $\mu$ and $s$ into ($\alpha, \beta$) such that the $\beta$-draws have the average $\mu$ and $s$ we picked.* 

**Step 1:** Draw uncertainty. That is, for each actor $a$ and run $r$, draw
$S\_{a, r}$ and $I\_{a, r}$ from their **Beta** priors.

**Step 2:** Find Power, $P\_{a, r}$ where $P\_{a, r}= S\_{a, r} \\cdot I\_{a, r}$. 

**Step 3:** Find a weighted Ideal point. With each actor's Ideal point $Y\_a$ form the weigthed compromise. 

$$\hat{Y}\_r = \frac{\sum\_{a} P\_{a,r} \cdot Y\_a}{\sum\_{a} P\_{a, r}}$$

**Step 4:** Repeat many, many, times. The collection of $\hat{Y}\_r$ now contains the range of plausible outcomes. 

### Results
Same as last time, our ruler:

**Step 1:**

**0:** Ukraine becomes a fully fledged member of NATO. 

**15:** Ukraine joins the EU.

**35:** Ukraine regains control with foreign policy autonomy.

**50:** Russia gets the Russian areas but looses influence over Ukraine.

**65:** Russia retains nominal control over a territorially unstable Ukraine.

**85:** Russia turns Ukraine into a puppet (gets russian areas and retains influnce over the rest).

**100:** Russia takes all of Ukraine.

*Caveat: There are multiple possible rulers.* 


**Step 2:** Find Power, $P\_{a, r}$ where $P\_{a, r}= S\_{a, r} \\cdot I\_{a, r}$. 


{{< highlight r >}}

set.seed(777)  # make results reproducible

# ------------------------------------------------------------
# Config / parameters 
# ------------------------------------------------------------
runs <- 5000
n_blocks <- 101
block_n  <- 10
nrow_total <- n_blocks * block_n
ncol_total <- runs
col_names  <- paste0("run", seq_len(ncol_total))  # keep names in sync


# ------------------------------------------------------------
impute_na_uniform <- function(df) {
 # Simple imputation: fill NAs with a uniform draw within the observed range.
 # Neutral choice that avoids injecting extra structure.
 if (anyNA(df)) {
  rng <- range(df, na.rm = TRUE)
  if (!is.finite(rng[1]) || !is.finite(rng[2])) {
   stop("All values became NA after clipping; adjust base/uncertainty.")
  }
  if (rng[1] == rng[2]) {
   # If all observed values are identical, use that single value (clipped to [0,100])
   df[is.na(df)] <- max(min(100, rng[1]), 0)
  } else {
   # Otherwise: uniform over [min, max] of observed values
   df[is.na(df)] <- runif(sum(is.na(df)), min = rng[1], max = rng[2])
  }
 }
 df
}


# ------------------------------------------------------------
# Beta priors: specify (mean, sd) on [0,1], then compute (alpha, beta)
# ------------------------------------------------------------
beta_ab_from_mean_sd <- function(m, s){
 # m in (0,1), s > 0, and s^2 < m(1-m) must hold
 v <- s^2
 k <- (m*(1-m)/v) - 1
 if (k <= 0) stop("Infeasible (mean, sd): sd too large for a Beta.")
 a <- m*k; b <- (1-m)*k
 c(a=a, b=b)
}

rbeta_mat_01 <- function(nrow, ncol, m, s){
 # Draw nrow*ncol Beta samples with target (mean=m, sd=s) on [0,1] and shape into a matrix.
 ab <- beta_ab_from_mean_sd(m, s)
 matrix(rbeta(nrow*ncol, ab["a"], ab["b"]), nrow=nrow, ncol=ncol)
}

# ------------------------------------------------------------
# Priors per actor, initials.
# int_m = prior mean (best guess), int_sd = prior standard deviation (uncertainty)
# ------------------------------------------------------------
priors <- list(
 actor1 = list(int_m = 0.80, int_sd = 0.20, inf_m = 0.95, inf_sd = 0.10),
 actor2 = list(int_m = 0.60, int_sd = 0.25, inf_m = 0.70, inf_sd = 0.20),
 actor3 = list(int_m = 0.25, int_sd = 0.25, inf_m = 0.99, inf_sd = 0.05)
)

# ------------------------------------------------------------
# Draws from priors (independent across cells/columns)
# Scale to 0..100 after sampling for reporting convenience.
# ------------------------------------------------------------
set.seed(2)  

salience_df1 <- 100 * rbeta_mat_01(nrow_total, ncol_total, priors$actor1$int_m, priors$actor1$int_sd)
salience_df2 <- 100 * rbeta_mat_01(nrow_total, ncol_total, priors$actor2$int_m, priors$actor2$int_sd)
salience_df3 <- 100 * rbeta_mat_01(nrow_total, ncol_total, priors$actor3$int_m, priors$actor3$int_sd)

influence_df1 <- 100 * rbeta_mat_01(nrow_total, ncol_total, priors$actor1$inf_m, priors$actor1$inf_sd)
influence_df2 <- 100 * rbeta_mat_01(nrow_total, ncol_total, priors$actor2$inf_m, priors$actor2$inf_sd)
influence_df3 <- 100 * rbeta_mat_01(nrow_total, ncol_total, priors$actor3$inf_m, priors$actor3$inf_sd)

# ------------------------------------------------------------
# Power = salience * influence (cell-wise), then take column means per run
# ------------------------------------------------------------
power_raw_1 <- salience_df1 * influence_df1
power_raw_2 <- salience_df2 * influence_df2
power_raw_3 <- salience_df3 * influence_df3

# Column means (per run). NA-safe (should not appear with Beta, but good practice).
power_actor1 <- colMeans(power_raw_1, na.rm = TRUE)
power_actor2 <- colMeans(power_raw_2, na.rm = TRUE)
power_actor3 <- colMeans(power_raw_3, na.rm = TRUE)

# Quick summary for sanity checks: min/max/midpoint per actor
power_stats <- data.frame(
 actor    = c("actor1","actor2","actor3"),
 min      = c(min(power_actor1), min(power_actor2), min(power_actor3)),
 max      = c(max(power_actor1), max(power_actor2), max(power_actor3)),
 midpoint = c(
  (min(power_actor1) + max(power_actor1)) / 2,
  (min(power_actor2) + max(power_actor2)) / 2,
  (min(power_actor3) + max(power_actor3)) / 2
 ),
 row.names = NULL
)


{{< /highlight >}}


**Step 3:** Find a weighted Ideal point. With each actor's Ideal point $Y\_a$ form the weigthed compromise. 

$$\hat{Y}\_r = \frac{\sum\_{a} P\_{a,r} \cdot Y\_a}{\sum\_{a} P\_{a, r}}$$

{{< highlight r >}}

# ------------------------------------------------------------
# Aggregate to a compromise on the outcome line (power-weighted ideal points)
# ideal_actor* = each actor's ideal point on 0..100
# \hat{Y} ≈ (Σ P_a * Y_a) / (Σ P_a) — computed per run.
# ------------------------------------------------------------
tot_power <- colSums(rbind(power_actor1, power_actor2, power_actor3))  # Σ P_a per run

ideal_actor1 <- 100  # Y_1
ideal_actor2 <- 25   # Y_2
ideal_actor3 <- 0    # Y_3

# Numerator: Σ (Y_a * P_a). Denominator: tot_power.
sum_ideal <- ideal_actor1 * power_actor1 +
 ideal_actor2 * power_actor2 +
 ideal_actor3 * power_actor3

partials      <- sum_ideal / tot_power   # \hat{Y} per run (0..100)
sum_mary  <- summary(partials)          # quick stats for reporting
mean_pred  <- mean(partials)             # point estimate (mean of compromises)

# ------------------------------------------------------------
# Result object (collect everything; nothing is printed implicitly)
# ------------------------------------------------------------
resultat <- list(
 salience      = list(actor1 = salience_df1, actor2 = salience_df2, actor3 = salience_df3),
 influence     = list(actor1 = influence_df1, actor2 = influence_df2, actor3 = influence_df3),
 power_means   = list(actor1 = power_actor1, actor2 = power_actor2, actor3 = power_actor3),
 power_stats   = power_stats,
 tot_power     = tot_power,
 sum_ideal     = sum_ideal,
 partials         = partials,
 sum_mary     = sum_mary,
 mean_pred     = mean_pred
)


{{< /highlight >}}



> **All this yields a min/max of min: 59.16 and max: 61.96.**



### Conclusions 
Using **Beta** priors we incorporated some uncertanity regarding our beliefs. The word belief should act as a stark reminder that these estimates are not evidence driven, they are driven by our own assumptions. If we're biased in any direction the numbers will reflect that. They are essentially the same numbers as in the last post in a wider costume. It might be usful for some applications to see how much our estimates could vary. Note that min and max are generally considered unstable. 





### Extras

Gradio app where users can add or remove actors. Add influence, salience, and uncertanity respectively. Produce a numbers line, template in place, upon pressing edit it should be similar to add and remove actors. Then they get the measure visualised, and pull the nearest numbers scenario. 

### References
Tetlock, P. E., & Gardner, D. (2015). Superforecasting: The Art and Science of Prediction.
Mesquita, Bruce B. (2009). The Predictioneer's Game: Using the Logic of Brazen Self-Interest to See and Shape the Future.

More reading:

Tetlock, P. E., & Gardner, D. (2015). Sharpening Your Forecasting Skills.
Tetlock, P. E. (1999). Theory-driven reasoning about plausible pasts and probable futures in world politics: are we prisoners of our preconceptions?. American Journal of Political Science, 335-366.