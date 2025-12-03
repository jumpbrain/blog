---
title: "Beauty Contest"
url: "/beauty-contest/"
draft: false
---

Sometimes simple heuristics can remove the need for a model altogether. Yes, really. Sometimes you are best off with a qualitative approach.

In early spring 2022 I made a prediction:

The Russo-Ukrainian war will not end in Russia’s favour. The invasion will deteriorate into territorial battles raging indefinitely given the same engagement from the west and the east.

A bold claim at the time as many experts thought that Russia would conquer Ukraine in a matter of days or weeks. By now we know that didn’t come to pass. Sidenote: Tetlock, P. E., & Gardner, D. (2015) found that experts tend to be as good as non-experts on average when trying to predict what will come to pass. He also found that predictioneers using theoretical models of the world perform just as poorly or worse. I know what you’re thinking: “Time to bring out the (o.g) one fair coin!”. Hold that thought…

## Method

To make my prediction I borrowed a method from Bruce Bueno de Mesquita’s book ‘The Predictioneer’s Game’ (2009). This method can boost your qualitative conditional predictions — meaning qualitative predictions that end with “…given [insert your constraint]”. Here’s the blueprint:

1. Identify all possible outcomes for actors and place them on a number line (e.g., from 0 to 100).
2. For each actor assign values between 0 and 100 for three attributes:
   - Influence — how much influence each actor could use to get what they want.
   - Salience — how much each actor cares about the issue.
   - Ideal point — the preferred outcome for each actor on the 0–100 ruler.
3. Compute Power = Salience × Influence for each actor, then compute a weighted average of the actors’ Ideal points using Power as the weights.

**Caveat:** If you subscribe to a news-aggregator you’ll likely be able to identify actors and assign numbers. If not, resist the temptation to ask a chatbot for definitive values — instead consult human domain experts when possible.

### Results (example)

Step 1 — outcomes (ruler):

0: Ukraine becomes a fully fledged member of NATO.

15: Ukraine joins the EU.

35: Ukraine regains control with foreign policy autonomy.

50: Russia gets the Russian areas but loses influence over Ukraine.

65: Russia retains nominal control over a territorially unstable Ukraine.

85: Russia turns Ukraine into a puppet (gets Russian areas and retains influence over the rest).

100: Russia takes all of Ukraine.

Step 2 — assign actor values (illustrative):

- Russia: Influence 80, Salience 95, Ideal point 100.
- The West (EU/NATO coalition): Influence 60, Salience 70, Ideal point 25.
- Ukraine: Influence 25, Salience 100, Ideal point 0.

Step 3 — compute Power and weighted average:

Power_Russia = 80 × 95 = 7600

Power_West = 60 × 70 = 4200

Power_Ukraine = 25 × 100 = 2500

Power_Total = 7600 + 4200 + 2500 = 14300

Weighted average = ((100 × 7600) + (25 × 4200) + (0 × 2500)) / 14300 ≈ 60.5

Interpretation: the system’s aggregate, power-weighted ideal lies around 60.5 on our ruler — closer to outcomes where Russia retains substantial influence, but not full territorial control.

---

<!-- Embed Streamlit app (uses site param `params.beautyContest.streamlitUrl` by default). -->
{{< streamlit full="true" >}}
