---
date: '2025-08-26T17:12:11+02:00'
draft: true
title: 'Data Science at War: Part 1'
description: 'A useful thought model.'
tags: ["Useful thought model"]
categories: ["Data Science"]
comments: true
---

### Introduction
As a data scientist you must be able to navigate situations that don't easily lend themselves to quantitative modelling. Sometimes simple heuristics can remove the need for a model altogether. Yes, really. Sometimes you are best of with a qualitative approach.

In early spring 2022 I made a prediction: 

> The Russo-Ukrainian war will not end in russias favour. The invasion will deteriorate into territorial battles raging indifinetly **given the same engagement from the west and the east.**

A bold claim at the time as many experts thought that Russia would conquer Ukraine in a matter of days or weeks. By now we know that didn't come to pass. Sidenote: Tetlock P.E (källa) found that experts tend to be as good as non-experts on average when trying to predict what will come to pass. He also found that predictioneers using theoretical models of the world perform just as poorly or worse. I know what you're thinking: "Time to bring out the (o.g) one fair coin!". Maybe not yet... 

### Method
To make my prediction I borrowed a method from Bruno B. de Mesquitas book The predictioneer's game (källa). This method can boost your qualitative conditional predictions. Meaning, qualitative predictions that end with "...given [insert your constraint]". 

**Step 1:** Identify all possible outcomes for actors and place them on a numbers line, e.g from 0 to 100.  

**Step 2:** Identify all the actors involved in the process of interest and assign values between 0 and 100 for these three attributes: $Influence$, how much power each actor could use to get what they want. $Salience$, how much each actor cares about the issue. $Ideal$, the ideal outcome (using the numbers line from the previous step) for each actor. 

*Caveat: If you subscribe to Ground News or similar you will likely be able to figure out the actors and assign these numbers on your own. If you cannot figure out the actors and assign these values: Do resist the temptation to ask a famous chatbot and go find an actual human expert made out of flesh. This will save you money as you know they are using said chatbot to answer your question. If you already subscribe, then use your own I guess...* 

**Step 3:** Produce a weighted average of ideal point, weighted by power. Where, $Power = Salince \\cdot Influence$. 


### Results

#### Step 1:


# remove boxes

{{< highlight r >}}

# Russia
Influence: 80 # had large influence.
Salience: 95 # cared a lot, existential for leadership but not populace as whole.
Ideal point: 100 # wanted all of it.


# The West/Nato (U.S)/Europe
Influence: 60 # powerful, not at border, supply challegens, leadership challenges coalition. 
Salience: 70 # US had many competing interests but european nations very interested.
Ideal point: 25 # Ukraine was a near EU state but issues with corruption, etc. 

# Ukraine
Influence: 25 # not very powerful but at home turf.
Salience: 100 # cares a lot, existential.
Ideal point: 100 # wants autonomy and peace.

{{< /highlight >}}


#### Step 2: 

{{< highlight r >}}

0: Ukraine becomes a fully fledged member of NATO 

15: Ukraine joins the EU

35: Ukraine regains control with foreign policy autonomy 

50: Russia gets the Russian areas but looses influence over Ukraine

65: Russia retains nominal control over a territorially unstable Ukraine

85: Russia turns Ukraine into a puppet (gets russian areas and retains influnce over the rest)

100: Russia takes all of Ukraine

{{< /highlight >}}

*Caveat: There are mulitple possible continuums here.* 


#### Step 3:

We find $Power$ for each actor respectively, where $Power = Salince \\cdot Influence$:

$$Power\_{Russia} = 80 \\cdot 95 = 7600$$

$$Power\_{West} = 60 \\cdot 70 = 4200$$

$$Power\_{Ukraine} = 25 \\cdot 100 = 2500$$

$$Power\_{Total} = 7600 + 4200 + 2500 = 14300$$

Then, we find a **weighted average** of ideal point, weighted by power:

$$\frac{(100 \\cdot 7600) + (25 \\cdot 4200) + (0 \\cdot 2500)}{14300} = 60.5 $$

This puts us in between a scenario where Russia gets the Russian areas but looses influence over Ukraine and Russia retains nominal control over a territorially unstable Ukraine. 

### Conclusion
> The Russo-Ukrainian war will not end in russias favour. The invasion will deteriorate into territorial battles raging indifinetly given the same engagement from the west and the east.

This prediction was made early spring 2022, what ensued after was increasing support for Ukraine from the west coupled with moral decline within the russian ranks. Today we're proably sitting at a soft sub 60's. Do you agree? Try this method out for yourself!   

Why where do experts get it wrong? I don't know. Maybe they tried using past or similar examples to see through extreme complexity rather than stepping back to look at the bigger picture.

Where do we go from here?

In the next post I'll incorporate some priors to incorporate uncertainty regarding the salience, influence and power of involved actors when using this method. 

In the post after that I'll probably use this as a base case to produce competing hypo's. 

Then I'll create a post about curating security situation related text for fine-tuning an LLM. 


