---
title: "Data dependent inference in instrumental variable settings"
date: 2026-09-17
permalink: /posts/2026/09/data-dependent-inference/
tags:
  - econometrics
  - inference
---

Adventure Time is one of my favorite TV shows. Like many animated series (initially) targeting children, Adventure Time began as a collection of stories documenting the various adventures of the series’ juvenile protagonist, Finn, and his best friend and brother, Jake the Dog. And just as the show develops and its audience matures, so does the series’ characters, world building, and storyline.

We learn in a [side story](https://www.youtube.com/watch?v=GZVfR1XYGrY) of Urgence Evergreen, who was, in the simplest terms, the most powerful ice wizard thousands of years before Finn and Jake. In the flashback scene, Urgence warns his peers of a comet hurling toward Earth, one that is “no ordinary comet… [one that] aches for [their] extinction” and advises creating a crown which will “bond to its first bearer and grant him his one deepest wish” to sidestep extinction. But his peers disagree, warning that “wish magic is really the real deal. This wish may see things in you you cannot see yourself. Can you truly say you know your heart’s truest desire?”

From the show’s cast far in the future, we know both that Urgence won and created the crown, but the wish imbued in the crown was far from what Urgence intended. Indeed, as the comet neared Earth, Urgence was unable to use the crown. In a desperate attempt to save himself, he urges his servant, Gunther, to coronate himself and, with his entire being, wish for the destruction of the comet. But Gunther, treated poorly during his tenure of service to Evergreen, truly wishes only to become his master. The flash back ends with a scene of the crown turning Gunther into his distorted mental image of his master, Urgence Evergreen, as the comet destroys Earth.

Despite my love for this show (and this scene, in particular), I also raise this story for illustrative purposes: we often cannot truly know our own deepest, truest, desires until our actions reveal them to us. Today’s researchers work toward the discovery of truth, as has been the North Star of science since antiquity. But doing research facing incentives of the 21st century academic establishment is much like wearing the crown of Urgence Evergreen. In the researcher’s path toward truth, they may—knowingly or unknowingly—give in to a deeper-rooted desire to align with their incentives and, in doing so, engage in methodological malpractice.

## The discredibility revolution and data-dependent inference

The credibility revolution, for which the 2021 Nobel Prize in Economics was awarded to David Card, Joshua Angrist, and Guido Imbens, was a pivotal advancement (revolutionary, some might say) in the rigor of economic methods that spread like wildfire to neighboring fields in the social sciences. With this methodological change came incentive change, later birthing many subfields aimed at auditing the credibility revolution. One might consider the growth of these subfields, those focused on the prescription and resolution of deceptive methodological malpractice, to be a sort of “discredibility revolution.”

Identifying methodological malpractice is not difficult, and in some disciplines and programs, it has become a core part of academic culture. Academics at the frontier of knowledge creation are familiar with multiple forms of this malpractice: p-hacking, HARKing, post hoc theorizing/analysis, multiple testing, publication bias, selective inference/model selection, and so on.

But remedying malpractice is understandably more difficult than prescribing them for two reasons. First, remedies often, but not always, themselves necessitate knowledge creation. A good econometrician, for example, cannot simply point out the shortcoming of his peers without proposing a better alternative. Second, it is often the responsibility of the researcher guilty of malpractice to accept and implement the remedy when available. That is, if existing incentive structures reward malpractice, the researcher must have a greater incentive (internal or external) to defect from the status quo and implement the remedy.  Herein lies the difficulty.

A common theme among problems targeted in this discredibility revolution is data-dependent inference, a process in which observed data drives both hypothesis formation and analysis. This pattern is called post-hoc theorizing (or post-hoc analysis) and roughly follows a four-step process:

1. Some patterns exist in the data that may or may not be observed before a hypothesis is posed

2. The researcher (knowingly or unknowingly) hypothesizes these patterns to be present in the data

3. The researcher tests these hypotheses using the observed data

4. Conclusions revealing the pattern are presented as if the hypotheses were formulated a priori

It is not always clear how to map this process onto a researcher’s data analysis workflow to identify malpractice; it is, however, much clearer how to identify malpractice from the process. To illustrate this point, I present two related forms of methodological malpractice and briefly discuss remedies: selective inference and instrument hacking. I gloss over selective inference relative to instrument hacking for econometric relevance.

## Selective inference

Also called post-selection inference, selective inference is a manifestation of selection bias in statistics. Leeb and Pötscher first discussed in detail this problem of “post-model-selection estimators” in their [2005 paper](https://www.jstor.org/stable/3533623) “Model selection and inference: facts and fiction,” which are estimators a researcher chooses optimally according to the two-step process:

1. Choose a model by optimizing some function over observed data (e.g., minimize statistical significance, maximize goodness of fit, etc.)

2. Fit the model on the same observed data to obtain an estimate.

The fundamental problem of selective inference is that the null distribution of a test statistic \\(T\\) obtained from the data pre-selection (only step 2) likely differs from the null distribution of the same test statistic obtained from the data post-selection (step 1 and 2). Supposing selection is some event \\(E\\), we can formally describe the problem as the unconditional null distribution of \\(T\\) differing from the null distribution of \\(T\\) conditional on the selection event \\(E\\).

### Inference under selection

To illustrate this problem and a simple solution, consider data \\(X_i\\) indexed \\(i = 1, 2, \ldots, n\\) drawn identically and independently (IID) from a standard normal distribution. The inference target is the mean of the \\(j^{th}\\) observation, where \\(j\\) is the index belonging to the largest of the data points \\(X_i\\) (formally \\(j = \operatorname*{arg\,max}_{i \in [n]} X_i\\)). Our choosing of the largest data point is the selection event. Using projection matrix notation for brevity, let

$$
M_{e_j}(\boldsymbol X) \equiv (X_1, \dots, X_{j-1}, 0, X_{j+1}, \dots, X_n)
$$

be the data vector with the selected coordinate \\(X_j\\) set to zero.

Suppose we choose \\(X_j\\) as the test statistic for \\(\mu_j\\). Unconditional on the selection event, we may (wrongly) use the marginal distribution of \\(X_i\\) for inference. That is, because \\(X_i \sim \mathcal N(0,1)\\), we can compute \\(p = 1 - \Phi(X_j)\\). But our naive p-value is too small relative to the p-value computed from the distribution conditional on selection since

$$
\left. X_j \;\right|\;
\left\{j = \operatorname*{arg\,max}_{i \in [n]} X_i,\; M_{e_j}(\boldsymbol X)\right\}
\sim \mathrm{TN}\!\left(0, 1; \max_{i\neq j} X_i, +\infty\right),
$$

a standard normal distribution truncated at \\(x = \max_{i\neq j} X_i\\).[^1] Our new p-value is then an adjustment of our z-score p-value:

$$
\tilde p = \frac{1 - \Phi(X_j)}{1 - \Phi(\max_{i\neq j} X_i)}.
$$

Therefore conditioning on the selection event allows the researcher to conduct valid inference. When the researcher is unable to enumerate all possible selection events for conditioning, they need alternative robust frameworks for valid inference.

One possible framework is the statistical scapegoat that is sample/data splitting. Splitting data enables the researcher to use data for selection independent (or in worst cases, bound dependence) of data for analysis/inference so long as the observed data are IID (or at least dependent in some knowable way). Simple methods like sample splitting are preferred for their simplicity by statisticians like [Cosma Shalizi](https://bactra.org/notebooks/post-model-selection-inference.html), who noted, “with all sincere respect to those working heroically on what I called the other direction [development of new inferential procedures to compensate for the fact the model was picked in a data-dependent way], I honestly don’t know why the sample-splitting approach isn’t the default we all use.” Critics of sample splitting as a solution to data-dependent inference problems point out it may be suboptimal because of the fact we may not use all the data, producing results which are overly conservative if our sample is too small or underpowered.

In linear regression contexts, it has become common to think of selective inference problems as simultaneity problems which can be wrestled with using methods familiar to the multiple testing literature. For example, [Barber and Candès (2015)](https://arxiv.org/abs/1404.5609) propose knockoffs for model selection, which allows the researcher to select a model from a sequence of models according to the researcher’s desired level of false discovery rate control in both fixed and model \\(X\\) settings. Knockoffs are “fake” covariates which mimic the properties of and are equal in distribution to the original covariates \\(\boldsymbol X\\). Under some additional assumptions like pairwise exchangeability, a researcher can use these knockoff covariates and a model selection procedure (e.g., stepwise procedures) to estimate the average false discovery proportion and compare it to their desired level of control.

Sample splitting and knockoffs are just two broad types of inferential frameworks valid under selection. As Shalizi noted in his 2025 blog post, others (e.g., [Panigrahi et al. 2026](https://arxiv.org/pdf/2212.12940), [Fithian et al. 2014](https://arxiv.org/abs/1410.2597), [Berk et al. 2013](https://projecteuclid.org/journals/annals-of-statistics/volume-41/issue-2/Valid-post-selection-inference/10.1214/12-AOS1077.full), but I have read nothing but the abstracts and introductions of these papers) are finding and continue to find valid frameworks for approaching this problem. The framework which is best for the scrupulous researcher is then the one that best suits their specific data setting.

## Instrument hacking

Instrument hacking, a more critical term referring to what some researchers call instrument pre-testing, is a relatively new problem in data-dependent inference that [Angrist and Kolesar (2024)](https://arxiv.org/abs/2110.10556) pose to be the instrumental variables analog to selective inference in predictive statistics. Hall and coauthors first documented inferential problems with pre-screening on first-stage test statistics in their [1996 simulation study](https://papers.ssrn.com/sol3/papers.cfm?abstract_id=5894), noting: “in circumstances in which the [weak instrument problem] was a concern (low correlation between instrument and regressor, high correlation between regressor and disturbance), application of the screening test only exaggerates the [weak instruments] problem.” Despite the concern on first-stage pre-screening being echoed by others (e.g., [Zivot and Wang 1998](https://econwpa.ub.uni-muenchen.de/econ-wp/em/papers/9610/9610005.pdf), [Andrews et al. 2019](https://www.annualreviews.org/doi/10.1146/annurev-economics-080218-025643), [Lee et al. 2022](https://www.nber.org/papers/w29124)), it has remained common practice in applied economics for the past three decades.[^2]

Angrist and Kolesar (2024) discuss problematic first-stage F statistic (FSFS) pre-screening in the just-identified case (i.e., as many instruments as there are endogenous regressors) and possible solutions; [Keane and coauthors (2026)](https://www.nber.org/papers/w35633) observe similar issues with pre-screening on first-stage F statistics (FSFS), second-stage t-statistics (SSTS), and joint pre-screening on both FSFS and SSTS. The latter define instrument selection accordingly:

1. First-stage instrument hacking: selection among candidate instruments to maximize the FSFS

2. Second-stage instrument hacking: selection among candidate instruments to maximize the SSTS

3. Joint instrument hacking: selection among candidate instruments to maximize simultaneously both the FSFS and SSTS

The primary pitfall of instrument hacking is that selecting for the instrument with the highest relevance often corresponds to selecting the instrument with greater endogeneity within the same sample it was being pre-screened against. This selection reintroduces bias into the IV estimate and pulls it toward the OLS estimate, undermining an advantage of the IV estimator over the OLS estimator in estimating causal effects.[^3]

### Inference under instrument hacking

The question of inference in the presence of instrument hacking remains. One possible inferential framework is the test inversion of Anderson and Rubin (AR), a popular home remedy for weak instruments inference.[^4] With weak instruments, the inversion property of AR inference guarantees unbiased confidence interval construction and exact coverage probability at the expense of unbounded bands for very weak instruments (i.e., F < 3.84).

Keane and coauthors report mixed findings on the robustness of AR inference with instrument hacking. If a researcher chooses to hack the second stage with respect to AR significance, the AR framework diminishes the biasing effect on the IV point estimate and confidence interval estimation relative to hacking the SSTS. There is still considerable size distortion as the number of instruments increases. However, if the researcher chooses to hack the FSFS, the AR framework does maintain its bias reducing properties but it does not eliminate the power asymmetry quirk of 2SLS pointed out by [Keane and Neal (2024).](https://www.annualreviews.org/content/journals/10.1146/annurev-economics-092123-111021)

Angrist and Kolesar (2024) propose a pre-screening method for weak instrument settings that screens for samples in which the sign of the estimated first stage coefficient matches the sign of the theoretical first stage coefficient, guaranteeing bias reduction with no cost to confidence interval width.

We can draw several parallels between inference to deal with instrument hacking and inference to deal with weak- and many-instruments bias. A simple thought experiment posed by Keane and coauthors (2026) supposing instruments drawn from some fixed distribution skewed rightward in instrument strength (that is, a researcher is more likely to draw a weak instrument than a strong one) demonstrates this connection more clearly (see pg. 14-15).

To deal with this bias, Keane and coauthors alternatively advise against pre-screening and instead recommend using all available instruments with robust inference but assume researchers can pre-screen for theoretically aligned signed first-stage coefficients. In particular, they find using all available instruments under limited information maximum likelihood (LIML) estimator of [Anderson and Rubin (1949)](https://projecteuclid.org/journals/annals-of-mathematical-statistics/volume-20/issue-1/Estimation-of-the-Parameters-of-a-Single-Equation-in-a/10.1214/aoms/1177730090.full) and the conditional likelihood ratio (CLR) test of [Moreira (2003)](https://www.jstor.org/stable/1555489) to yield favorable results.[^5] 2SLS or GMM is also permissible to aggregate all available instruments, with Keane, Neal, and Vu observing 2SLS to perform worse than LIML and hypothesizing continuously updated GMM to perform better.

If we map patterns in selective inference to those in instrument hacking, I suspect we may be able to find analogous inferential frameworks which are also robust under instrument hacking. Instrumental variable analogs of data splitting and knockoffs in selective inference, such as [Angrist and Krueger’s (1995)](https://www.nber.org/papers/t0150) unbiased split-sample IV estimator, may provide the scrupulous practitioner more options to make the best of their data without engaging in deceptive methodological malpractice.[^6]

[^1]: We truncate at \\(x = \max_{i\neq j} X_i\\) because \\(X_j \geq \max_{i\neq j} X_i\\) by our selection event.

[^2]: Indeed, in Angrist and Pischke’s [seminal textbook on econometrics](https://www.jstor.org/stable/j.ctvcm4j72) (2009, p. 157, as highlighted by Keane and coauthors), they advise: “pick your best single instrument and report just-identified estimates using this one only.”

[^3]: Keane and coauthors’ 2026 paper refers to two papers by Keane and Neal ([2023](https://papers.ssrn.com/sol3/papers.cfm?abstract_id=4294302), [2024](https://www.annualreviews.org/content/journals/10.1146/annurev-economics-092123-111021)) discussing a “power asymmetry” of the 2SLS estimator, a property of the 2SLS estimator which produces smaller standard errors than the OLS estimator in samples where the distance between the 2SLS and OLS estimates is sufficiently small. The literature of IV papers is vast and unbounded. In writing this essay, I have only gone through a puddle of it and have not read these papers myself.

[^4]: [Staiger and Stock](https://stock.scholars.harvard.edu/publications/instrumental-variables-regression-weak-instruments) rediscovered Anderson and Rubin’s 1949 paper in the 1990s. Since then, other researchers have developed alternative inferential frameworks to AR which improve on confidence interval width at the cost of bias-adjustment and non-exact coverage.

[^5]: I admit I know little of the LIML estimator or CLR test beyond its presentation in a textbook-style format and results presented in Keane et al. (2026). Being a debiased form of LIML, [Fuller’s (1977)](https://ideas.repec.org/a/ecm/emetrp/v45y1977i4p939-53.html) LIML estimator may perform better than Anderson and Rubin’s LIML Keane, Neal, and Vu use in their paper. I provide links to the papers directly for the reader’s interest. I owe my thanks to ChatGPT and Claude in helping me to gain a basic grasp on how LIML and CLR work.

[^6]: A major disadvantage of SSIV is that it still suffers from the poor finite-sample properties of instrumental variables estimation, which may explain why it is not a very popular estimator in modern IV applications.

* Footnotes
{:footnotes}

## Presentation deck

For readers interested in the presentation, [view the presentation deck (PDF)]({{ '/files/2026-09-17-data-dependent-inference.pdf' | relative_url }}).
