---
date: '2025-08-26T17:12:11+02:00'
draft: true
title: "Data Scientist at War: Part 2"
description: 'Structured, bidirectional information flow'
tags: ["Useful thought model", "Bayes"]
categories: ["Data Science"]
# comments: true
---

> It's not about the tools, it's how you apply them. In data science, it's not about the model...

I call all models 'thought models' for a reason. It should ideally force you to confront YOUR known facts, YOUR priors and YOUR assumptions about them explicitly. It's not about the model, it's about your thinking about the model. 

That's why I'm drawn to statistical models and algos in general. Beyond trivial notation, beneath the practical aspects, you find very clever best-practices that instantly elevate your reasoning. Consider this...

### Introduction
You're a forward deployed analyst in a country called Extremiststan. The FOB at which you sit is located a few miles south of the main city, Dystopolis. Tonight, some of your colleagues are going to meet with a prospective source. There are three roads leading into Dystopolis, the horizontal road, the vertical road and the diagonal road. They need your call on which route to take. 

You scrape incident reports via some social media channels that you're currently tracking as well as police reports sourced from the local department. You aggregate the last year of data for each road.

The vertical road has seen a spike in incidents over the last two weeks, then it cleared up. In the last 72 hours exactly zero incidents have been reported on that road. 

Perfect! You run a quick forecast, the vertical road has the lowest probability of incidents over the next 12 hours. You recommend it. They move out.

A few moments later, you find that:

The vertical road has been red listed by local police. They no longer respond there. A local news station has been trying to send crews there but the reporters won't go. A reporter from a competing local news agency recently responded to a tweet (X) that he wouldn't set foot there no matter what. Your collegaues left 2 hours ago... 

> Sh*t!

Similar examples show up in cyber incidents, medical risk, the list goes on... You don't need a Masters in statistics to see the problem. No exposure, no patrols, no reporters, no logs, can make a very dangerous road look favourable. 

When thinking about count data, e.g number of incidents on a road over time, try and think of the counts as manifestations from an underlying (latent) risk surface, that you cannot directly observe. 

Then, you model that surface...

### Method
We briefly go over the simulated data, read scenario, and cover a hierarchical approach.

### Data
The Extremiststan situation is simplistic. Each day, each segment of each road either sees some incidents or not. A road segment on a given day will produce 1 row in our data. We observe number of incidents on that segment and day. We note down which of the three roads. We use segment ID's along the roads, an index for the day, coordinates for plotting and a proxy for how much opportunity there was for something bad to happen, called exposure.

You can think of exposure as, number of vehicles using that segment, patrol presence or how much of that road was observable and reportable.

We call the observed incident count on segment–day $i$ by $y_i$. For that observation we have corresponding exposure $exp_i$, $road[i]$ and $day[i]$.

## Model
The reason why you use a hierarchy is that it allows a controlled way for information to flow.

[image]

The $\alpha$ and hyperparameters $\simga_{road}$ and $\sigma_{day}$ sit at the top of the hierarchy. They summarize overall activity and heterogeneity between different roads and different days. Think of it like **regional information** trickling down to your risk estimates for individual roads. Underneath that you have the road-level deviations. $u_r carries tendencies of each road and the day-level deviations over time. $v_d$ carries system wide swings over time. At the bottom we have the segment-day means $\mu_i$. 

When you observe a count $y_i$ on one segment, at one time, you're nudging the segment, its road, its day, and through them the global/overarching picture. 

> Information flow upwards.

That updated global/overarching picture feeds back into all other segments, especially where there is little direct data. 

> Information flow downwards.

That is the point of a hierarchical approach, structured, bi-directional information flow between local/ground and global/overarching levels of information. 

For this toy example we start with a simple Poisson model for count data (maths can induce panic, try skimming through this part anyway, you might surprise yourself, it's **easier** than it looks at first glance):

$y_i \sim Poisson(\mu_i)$

Here $\mu_i$ represents the expected number of incidents on that segment-day. We could define it as a rate,

$$\lambda_i = \frac{\mu_i}{exp_i}$$

But for cleanliness we prefer the log-scale and treat exposure as a covariate:

$$log \mu_i = \alpha + u_{road[i]}+ v_{day[i]} + \beta_{exp} \cdot zlogexp_i$$ 

where $zlogexp_i$ is the standardized log(exp_i).

$\alpha$ is a global baseline log-rate, think of it as regional assessments. If all deviations were zero, it's the log **expected** incident count at typical exposure for the region. The $u_{road}$ terms are road specific deviations around that baseline. The $v_{day}$ terms are day-specific deviations that capture the overall swings over time. $\beta_{exp}$ describes how strongly the expected count scales with exposure once we have already accounted for road and day.

The hierarchical part comes from how we treat those road and day effects. Instead of giving each road its own unrelated parameter, we assume that roads are similar but not identical draws from a common distribution (keep reading). Formally,

$$u_r \sim N(0, \sigma_{road})$$ for $r \in 1, ..., R$.

$$v_d \sim N(0, \sigma_day)$$ for $d \in 1, ..., D$.

To maintain $\alpha$ as the global average log-rate and the road/day terms as deviations, rather than duplicate intercepts we typically place constraints by,

$\sum_{r=1}^R v_r = 0$ and $\sum_{d=1}^D v_d = 0$

The hyperparameters $\sigma_{road}$ and $\sigma_{day}$ control how tightly roads and days are coupled to the global mean. With small $\sigma_{road}$ the models believes the roads behave similarily, their effects are pulled toward zero unless the data clearly states something different. With large $\simga_{road}$ the model is willing to let roads drift far away from each other. We use a weakly informed prior for them,

$$\simga_{road} \sim N^+(0.5)$$

$$\sigma_{day} \sim N^+(0.2)$$

Basically, differences exist, but the default is modest differences unless the data shows big gaps.

The global baseline gets a weakly informative prior in a plausible range for rare incidents,

$$\alpha \sim N(-4, 1)$$

The exposure coefficient is allowed to move but gently shrunk toward zero,

$$\beta_{exp} \sim N(0, 0.5)$$

We then fit this model with Hamiltonian Monte Carlo in PyMC.

### Results
Posting this again for clarity:

$$log \mu_i = \alpha + u_{road[i]}+ v_{day[i]} + \beta_{exp} \cdot zlogexp_i$$ 

Now, return to the vertical road. For two weeks it was active, several incidents distributed over segments and days, with decent exposure. Those data have already pushed its road effect up relative to the other roads in the area. They have also told the model something about the variability for all roads: roads are not identical; at least one of them behaves worse than the grand mean.

Next, exposure on that road collapses. Patrols avoid it, civilians detour, media shy away. What you see in the feed is a string of zeros.

In a non-hierarchical, the one you used in the intro, those zeros can be treated in isolation from the other roads. Additionally, the most recent days heavily dominate your estimate of the current risk rate on that road. In that world, one could assume that the vertical road has calmed down, since there is nothing in the structure that remembers, or cares, that this road was systematically bad before everyone stopped going there or that the region overall has gotten worse. 

In the hierarchical model, the same zeros mean something different. They are interpreted against a backdrop of an already elevated incident count on the vertical road, a known exposure pattern, and what the other roads are doing **all the way to the regional level and over time**. 

The information flow is asymmetric. A quiet, heavily travelled road will aggressively pull its road effect down. The model reads that as genuine indications of lower risk: Lots of opportunities, nothing happened. A quiet, abandoned road over a short period barely moves the needle; the prior belief encoded in 
the road effect resists overreaction.

Even across roads. If you have one very bad road and two very quiet roads, the posterior for road variability has to stretch to accommodate that spread. With more quiet roads, the vertical road has to work harder, data wise, to claim that it is fundamentally different rather than just unlucky. Suppose the diagonal road has been consistently quiet, with good exposure, lots of traffic. All road effects share a deviation term and are tied to the same overarching information about how calm, a calm road can be. This should affect how extreme you are willing to believe the vertical road is. 

Pooling over time behaves similarly. If there is a week where all roads spike, the day effects capture that as a shared elevation in risk over time. When the spike ends, the estimates go back down, but they don't instantly forget that the system had a bad week. On top of that, the individual road effects capture that even on bad weeks, some roads are worse than others, and segment–day means combine the two.

Out of this machinery you can extract, whatever summary, a map colored by latent risk rather than a count map. Aggregating over time gives you road-level risk estimates that already contain pooling across days and segments. You can even compute probabilities of one route being safer than another, and use that as a more honest input to a discussion than recent counts.

Is this truth? Of course not. If exposure is mis-measured, if reporting is suppressed, if one road is governed by a genuinely different process you will still be wrong. The difference is that now the wrongness is traceable: you can point directly at terms, road variability, time variable patterns and say;

> This is where I expressed my beliefs. This is where I went wrong. This is how I'll adapt. 

### Conclusion

A hierarchical model is a useful thought tool. It forces you to say, on the record, how you think information should flow between the big picture and the local one. 

> Keyword is: Humility. 

In Extremiststan, the benefit of a small Bayesian hierarchical Poisson model has nothing to do with fancy computation. It is about how information is allowed to flow. The global mean and hyperparameters carry a rough, system-wide (regional) picture. The road-level terms carry persistent, route-specific tendencies but borrow information from one another. The day-level terms carry swings over time. Segment–day risks sit at the bottom of this stack and are shaped by all of the above. 

Counts still matter. Zeros still matter. A zero with high exposure on an ordinarily safe road is strong evidence of safety. The same zero with low exposure on a historically dangerous, partially abandoned road is not. The hierarchy encodes that difference.

As with our power-weighted prediction exercise for the Russo–Ukrainian war, [link to post], the final warning is the same. These estimates are not data; they are your assumptions, pushed through a model and returned to you in numeric form. If you are biased about which roads are similar or how exposure works, the output will mirror that bias.

### References
Gelman, A., Hill, J., & Vehtari, A. (2020). Regression and Other Stories. Cambridge University Press.

McElreath, R. (2020). Statistical Rethinking: A Bayesian Course with Examples in R and Stan (2nd ed.). CRC Press.

PyMC Development Team. PyMC: Bayesian Modeling and Probabilistic Programming in Python.