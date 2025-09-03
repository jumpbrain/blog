---
date: '2025-08-26T17:12:11+02:00'
draft: false
title: 'Data Scientist at War: Part 1'
description: 'A useful thought model.'
tags: ["Useful thought model"]
categories: ["Data Science"]
# comments: true
---

### Introduction
As a data scientist you must be able to navigate situations that don't easily lend themselves to quantitative modelling. Sometimes simple heuristics can remove the need for a model altogether. Yes, really. Sometimes you are best of with a qualitative approach.

In early spring 2022 I made a prediction: 

> The Russo-Ukrainian war will not end in Russia's favour. The invasion will deteriorate into territorial battles raging indifinetly **given the same engagement from the west and the east.**

A bold claim at the time as many experts thought that Russia would conquer Ukraine in a matter of days or weeks. By now we know that didn't come to pass. Sidenote: Tetlock, P. E., & Gardner, D. (2015) found that experts tend to be as good as non-experts on average when trying to predict what will come to pass. He also found that predictioneers using theoretical models of the world perform just as poorly or worse. I know what you're thinking: "Time to bring out the (o.g) one fair coin!". Hold that thought... 

### Method
To make my prediction I borrowed a method from Bruno B. de Mesquitas book 'The predictioneer's game' (2009). This method can boost your qualitative conditional predictions. Meaning, qualitative predictions that end with "...given [insert your constraint]". Here's the blueprint:

**Step 1:** Identify all possible outcomes for actors and place them on a numbers line, e.g from 0 to 100, as a ruler.  

**Step 2:** Having identified all the actors involved, assign values between 0 and 100 for three attributes: $Influence$, how much influence each actor could use to get what they want. $Salience$, how much each actor cares about the issue. The $\text{Ideal point}$, the ideal outcome (using our ruler from the previous step) for each actor. 

*Caveat: If you subscribe to Ground News or similar you'll likely be able to figure out the actors and assign these numbers on your own. If you can't figure out the actors and assign these values: Do resist the temptation to ask a famous chatbot and go find an actual human expert made of flesh. **This will save you money** as you know they're using said chatbot to answer your question.* 

**Step 3:** Produce a weighted average of $\text{Ideal point}$, weighted by $Power$. Where, $Power = Salince \\cdot Influence$. 


### Results

Let's try it:

**Step 1:**

**0:** Ukraine becomes a fully fledged member of NATO. 

**15:** Ukraine joins the EU.

**35:** Ukraine regains control with foreign policy autonomy.

**50:** Russia gets the Russian areas but looses influence over Ukraine.

**65:** Russia retains nominal control over a territorially unstable Ukraine.

**85:** Russia turns Ukraine into a puppet (gets Russian areas and retains influnce over the rest).

**100:** Russia takes all of Ukraine.

*Caveat: There are multiple possible rulers.* 


**Step 2:** 

**Russia**, had and still has large influence in european bordering nations; I put Russian influence at 80. They cared a lot and for the Russian leadership one could argue that the outcome was existential, although not for the population as a whole; I put salience at 95. Their ideal point was obviously 100, Russian leadership spouting that Ukraine in some distant history had been part of an early Russia making it somehow still Russian.  

> Influence: 80,
> Salience: 95,
> Ideal point: 100. 

**The west**, refering to meaningful western coalitions e.g EU and NATO, is influential. However, it's a coalition with idiosyncratic interests within. Supply chain is a steeper challenge for the west compared to Russia. Because of all this I put the west at a 60 for influence. I put Western salience at 70. Ukraine was a near EU state before the invasion but still had issues with corruption etc. To maintain that the ideal point for the west would be around 25. 

> Influence: 60, 
> Salience: 70, 
> Ideal point: 25.
 
**Ukraine**, is not very influential, influence at 25. Cares a lot, more than Russia, completely existential, salience at 100. Wants autonomy and peace, ideal point 0. 

> Influence: 25,
> Salience: 100,
> Ideal point: 0.

**Step 3:**

We find $Power$ for each actor respectively, where $Power = Salince \\cdot Influence$:

$$Power\_{Russia} = 80 \\cdot 95 = 7600$$

$$Power\_{West} = 60 \\cdot 70 = 4200$$

$$Power\_{Ukraine} = 25 \\cdot 100 = 2500$$

Then, we find a **weighted average** of $\text{Ideal point}$, weighted by power:

$$Power\_{Total} = 7600 + 4200 + 2500 = 14300$$

$$\frac{(\text{Ideal point}\_{Russia} \\cdot Power\_{Russia}) + (\text{Ideal point}\_{West} \\cdot Power\_{West}) + (\text{Ideal point}\_{Ukraine} \\cdot Power\_{Ukraine})}{Power\_{Total}} = $$

$$\frac{(100 \\cdot 7600) + (25 \\cdot 4200) + (0 \\cdot 2500)}{14300} = 60.5 $$

This puts the weighted average ideal point in between a scenario where Russia gets the Russian areas but looses influence over Ukraine and a scenario where Russia retains nominal control over a territorially unstable Ukraine. 

### Conclusion
> The Russo-Ukrainian war will not end in Russia's favour. The invasion will deteriorate into territorial battles raging indifinetly given the same engagement from the west and the east.

This prediction was made early spring 2022, what ensued after was increasing support for Ukraine from the west coupled with declining morale within the Russian ranks. Today we're proably sitting at a soft sub 60's, **do you agree?**

**Why do experts get it wrong?** I don't know. Maybe they tried extrapolating from past or similar examples to see through extreme complexity rather than stepping back to look at the bigger picture. Maybe I got lucky. It would behoove one to never forget that one should always carry one fair coin.

I would like to end with a stark reminder, that **these estimates are not evidence driven, they are driven by our own assumptions**. If we're biased in any direction the numbers will reflect that. By adding complexity to our beliefs I think we're at the risk of interpreting emotional padding as evidence. Nontheless, this is a feasible method for producing a weighted belief, should you insist!

In the next post I'll probably write something about curating security situation related text for fine-tuning a large language model (llm), or something along those lines. Then I'll write about retrieval augmented generation, vector based and graph based, using security related text. Then I'll likely move into something more tangible, e.g cybersecurity and the NIST frameworks. 

Meet you there!

### References
Tetlock, P. E., & Gardner, D. (2015). Superforecasting: The Art and Science of Prediction.
Mesquita, Bruce B. (2009). The Predictioneer's Game: Using the Logic of Brazen Self-Interest to See and Shape the Future.

More reading:

Tetlock, P. E., & Gardner, D. (2015). Sharpening Your Forecasting Skills.
Tetlock, P. E. (1999). Theory-driven reasoning about plausible pasts and probable futures in world politics: are we prisoners of our preconceptions?. American Journal of Political Science, 335-366.
