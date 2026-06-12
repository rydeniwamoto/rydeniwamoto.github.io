---
title: "SUTVA violations as unit-level compliance heterogeneity in clustered data settings"
date: 2026-06-09
permalink: /posts/2026/06/sutva-compliance-heterogeneity/
tags:
---

While working on my [thesis](https://rydeniwamoto.github.io/files/STR-STS.pdf), I encountered several methodological hurdles with a flavor reminiscent of SUTVA violations. I want to share some descriptive and cautionary observations about those hurdles, as well as some broader implications for causal inference using cluster-level data in place of unit-level data.

## A Brief Introduction To SUTVA

In applied microeconometrics, the stable unit value treatment assumption (SUTVA) is, despite its necessity for many popular causal inference methods, often unaddressed or unjustified. Whether this inattention to SUTVA is simply convention or, more critically, shoddy econometrics does not justify it in a majority of, if not all, applied settings.

Despite its description as one assumption, SUTVA is actually two assumptions wearing a long trench coat and hat (the second of which is overlooked by some practitioners, which may partially explain why SUTVA is unaddressed in many applied papers). In the Neyman-Rubin potential outcomes framework, SUTVA is composed of the following two conditions. Let \\(Y_{i}\\) be the outcome for unit \\(i\\) with treatment status \\(W_{i}\\). Denote the vector of treatment statuses for all other units except \\(i\\) to be \\(\boldsymbol W_{-i}\\):

1. **No interference**: Unit \\(i\\)'s potential outcomes are only dependent on their own treatment status: \\(Y_{i}(W_{i}, \boldsymbol W_{-i}) = Y_{i}(W_{i})\\).
2. **Consistency/no hidden treatment**: There are no different versions of treatment producing different potential outcomes. Put simply, treatment is defined unambiguously so \\(W_{i}\\) only takes on permitted values.[^1]

[Rubin (1986)](https://doi.org/10.2307/2289065) presents a more thorough coverage of SUTVA as an assumption. There is also a vast literature on identifying causal effects when SUTVA is violated (e.g., [VanderWeele and Hernan 2013](https://doi.org/10.1515/jci-2012-0002), [Leung 2022](https://doi.org/10.3982/ECTA17841), [Gao 2024](https://arxiv.org/abs/2412.02183)).

## Causal Inference With Unit vs. Cluster Level Data

Some of the most credible causal inference methods rely on panel data at a level of relevant variation, allowing the researcher to observe the same individual regularly over multiple time periods.[^2] In many observational settings, however, panel data is not available due to data collection, privacy, or affordability restrictions.

In such settings, an amateur researcher (in the professional context, not in a skill context) with access to data at the unit level should still consider themselves lucky. Privacy and data collection restrictions persist even in the construction of unit level cross sectional datasets. Collection agencies like the Census Bureau thus produce and publish data at the cluster level, which is then available to the amateur researcher to analyze.

A silly example is the following. Consider a population of 300 blobs which can be partitioned into three clusters by color: red, yellow, and blue. We assign an arbitrary treatment at random with probability \\(p = \frac{1}{2}\\) to each cluster, which is strictly beneficial. Because treatment is assigned at random within clusters, we can use a blocked difference-in-means (BDM) estimator to compute the treatment effect on the entire population. Specifically, letting \\(Y_{[k]i}(W_{[k]i})\\) denote the potential outcome for blob \\(i\\) in cluster \\(k\\), a good estimator for the average treatment effect on an arbitrary outcome \\(Y\\) is:

$$
\hat\tau = \frac{1}{3}\sum\limits_{k=1}^{3}\Bigg[\frac{1}{n_{[k]1}}\sum\limits_{i:W_{[k]i} =1}Y_{i} - \frac{1}{n_{[k]0}}\sum\limits_{i:W_{[k]i} = 0}Y_{i}\Bigg],
$$

where \\(n_{[k]1} + n_{[k]0} = 100\\). In this setting, we can still nicely estimate treatment effects even if we only observe the three clusters \\(k \in \{1, 2, 3\}\\) and their cluster-level characteristics. That is, the cluster-level data \\(n_{[k]1}, n_{[k]0}, Y_{[k]1},\\) and \\(Y_{[k]0}\\) are sufficient to point identify \\(\tau\\), and there is no information lost under aggregation. Even under weaker treatment assignment mechanisms, estimation of causal effects is still possible so long as the population is closed (e.g., defection in one cluster results in absorption in another cluster of the same magnitude).

Now suppose blobs like to bounce, and we know 30 blobs in each cluster bounce regularly. At time \\(t^{*}\\), one cluster (red) is selected at random and bouncing is prohibited; bouncing is still permitted in the other two clusters (blue and yellow). Every blob inherits its cluster's assignment, but blobs may not comply with their cluster's bouncing policy. For instance, a bouncing red blob may stop bouncing (comply) or they may defect to a blue or yellow cluster and continue bouncing (defect). Since blobs like to bounce, defection is strictly one-sided: blue and yellow blobs will never defect to the red cluster. Blobs, like humans, are subject to various constraints. Some blue and yellow blobs may start to bounce while others may quit (e.g., some blobs may sometimes be too busy to bounce).

Remember the red cluster is not allowed to bounce. Of red's 30 bouncers, 18 stop bouncing and quit while 12 defect to blue and yellow clusters. In the bouncing clusters, there are a net of 3 organic bouncers (i.e., some blobs quit bouncing while more blobs start); in the red cluster, there are no organic bouncers (bouncing is prohibited).

| Cluster | Bouncers \\(t^{*}-1\\) | Defection | Organic Net | Bouncers \\(t^{*}+1\\) | Observed \\(\Delta\\) |
| ------- | ---------------------- | --------- | ----------- | ---------------------- | -------------------- |
| Red     | 30                     | -12       | 0           | 0                      | -30                  |
| Yellow  | 30                     | +6        | +3          | 39                     | +9                   |
| Blue    | 30                     | +6        | +3          | 39                     | +9                   |

If we observe the blobs at the unit-level, we can observe each blob's compliance with the bouncing policies. At the cluster-level, however, we only observe bouncing counts \\((30, 30, 30)\\) and \\((0, 39, 39)\\). The blue and yellow clusters' gain of \\(+9\\) contains both displaced and organic bouncers, but at the cluster-level we cannot discern between them. The naive comparison using cluster data of \\((-30) - (+9) = -39\\) is greater than any of the reasonable and relevant causal estimands:

- Complier effect/cATT (-18): "Does blob \\(i\\) bounce anywhere?" The bouncing ban on the red cluster only caused 18 bouncing red blobs to stop since the other 12 continue bouncing elsewhere.
- Local (cluster) effect (-33): "How many blobs stopped bouncing in the red cluster?" Red has a no ban counterfactual of 18 + 12 + 3 = 33 (the +3 comes from the organic net gain experienced by the blue and yellow clusters), so the ban's effect on red is of the same magnitude with the sign flipped.
- Global effect (-21): "Did the ban reduce bouncing?" Looking at the total number of bouncing blobs eliminated and prevented, the red cluster ban reduced bouncing overall by -18 (complier effect) and -3 (counterfactual organic net gain in absence of ban).

## Issues With SUTVA In The Cluster Data Setting

The central empirical challenge in our setting is that we observe outcomes only at the cluster level, while the relevant compliance structure is defined and observed at the individual level. In the individual-level LATE framework from [Imbens and Angrist](https://www.jstor.org/stable/2951620?origin=crossref), the population complier share can be recovered from the first stage since both assignment and treatment receipt are observed. In our setting, however, the observed cluster outcome is an aggregate mixture of latent strata for which we do not observe the first stage. This makes point identification of a relevant causal estimand difficult (if not impossible) without additional or stronger assumptions on latent compliance composition (e.g., perfect compliance and no organic entry).

In our blob world from before, the bias from our naive comparison maps to two SUTVA violations at the cluster level. No interference fails because the control clusters' outcomes depend on red's assignment. That is, because blue and yellow absorb red's defectors (and, plausibly, red's bounce demand), the cluster-level potential outcomes for blue and yellow depend both on their own treatment status \\(W_\mathrm{blue}, W_\mathrm{yellow}\\) but also red's treatment status \\(W_\mathrm{red}\\). Consistency fails because the -30 loss of bouncing in red is a mixture of defection and compliance, so these two subgroups with different compliance compositions effectively receive "versions of treatment varying in effectiveness" ([Rubin 1990](https://doi.org/10.1016/0378-3758(90)90077-8)). The baseline treatment assigned to red \\(W_\mathrm{red}\\) was sufficiently strong to convince the 18 blobs who stopped bouncing to stop but was not strong enough to convince the 12 blobs who relocated to stop.[^3]

The conclusion I draw is that unit-level compliance heterogeneity with organic entry and exit manifests as cluster-level challenges to SUTVA. This pattern arose in a setting where I had cross-sectional data at the unit-level (not simply cluster-level data) but could not track units to monitor their compliance (you can track the movement of blobs but you cannot know whether a house in a permitted zone became a vacation rental after a ban organically or because an operator relocated from the prohibited zone). One can imagine this pattern would also arise in data sources like census data, where we cannot observe individual compliance and where organic entry and exit is very plausible. Because of that, I think we ought to be careful when attempting causal inference with these data. More broadly, after carefully choosing a relevant causal estimand, researchers using cluster data must ask themselves the extent to which this pattern may arise, and, if present, be careful with the strength of the conclusions they advance.[^4]

[^1]: I want to note there is some epistemic disagreement on the necessity of the consistency assumption (e.g., see [Keele 2015](https://doi.org/10.1093/pan/mpv007), p. 17, for brief coverage), though I defer its discussion to the statistical philosophers.
[^2]: Repeated cross sectional data may also suffice for these methods, though panel data is weakly (if not strictly) better statistically. "Relevant variation" refers to the ideal level at which we want to measure and observe variation in response to treatment which empowers causal inference. In many applied settings, this is at the individual (person) level.
[^3]: This might be analogous to the following setting. Suppose in an RCT setting we are interested in the effect of some drug on patient outcomes. We may want to assign treatment proportionally to the patient's weight to ensure the drug is effective; we cannot assign a small woman the same amount of the drug as a powerlifting champion and expect to make a good causal comparison.
[^4]: I haven't had time to go through it, but according to Claude, this pattern of unit-level compliance heterogeneity manifesting as SUTVA violations at the cluster level is a pattern documented as far back as the 1950s under the "ecological interference" literature. This may be an interesting area for future work, whether for myself or others, much like how econometricians rediscovered the work of Anderson and Rubin from the 1950s for weak instrument robust inference. For those interested, the referenced papers were the following: [Robinson (1950)](https://doi.org/10.2307/2087176), [Duncan and Davis (1953)](https://www.jstor.org/stable/2088122?origin=crossref), and [Gelman et al. (2001)](https://sites.stat.columbia.edu/gelman/research/published/ecological.pdf).
