# Hypothesis Testing & A/B Testing / Experimentation

Online experimentation (A/B testing) is the **single most common interview round** for data scientists at tech companies, and the daily tool for shipping decisions. It rests on classical **frequentist hypothesis testing**: we collect data, compute a test statistic, and decide whether an observed difference is real or noise. This note builds the statistics first, then the full experimentation toolkit.

---

## 1. The Hypothesis-Testing Framework

We state two competing claims about a population parameter $\theta$ (e.g. a mean, a proportion, a difference):

- **Null hypothesis $H_0$:** the "no effect / status quo" claim, e.g. $\theta = \theta_0$ (often $\mu_A = \mu_B$).
- **Alternative $H_1$:** what we want evidence for, e.g. $\theta \neq \theta_0$ (two-sided) or $\theta > \theta_0$ (one-sided).

Procedure:

1. Choose a **test statistic** $T$ whose distribution under $H_0$ is known.
2. Choose a **significance level** $\alpha$ (the tolerated false-positive rate, e.g. 0.05).
3. Compute $T$ from the data and its **p-value**.
4. Reject $H_0$ if p-value $< \alpha$ (equivalently, if $T$ falls in the rejection region).

### p-value

$$
\text{p-value} = P\big(\,T \text{ as or more extreme than observed} \mid H_0 \,\big)
$$

It is **not** $P(H_0 \mid \text{data})$. A small p-value means the data would be surprising *if the null were true*.

### The two error types

| | $H_0$ true | $H_0$ false |
|---|---|---|
| **Reject $H_0$** | Type I error (rate $\alpha$) | Correct (power $1-\beta$) |
| **Fail to reject** | Correct ($1-\alpha$) | Type II error (rate $\beta$) |

- **Type I ($\alpha$):** false positive — ship a change that does nothing.
- **Type II ($\beta$):** false negative — miss a real improvement.
- **Power $= 1 - \beta$:** probability of detecting a true effect. The whole game of experiment design is getting enough power at a fixed $\alpha$.

---

## 2. Z-test (large sample / known variance)

When the sampling distribution of the estimator is approximately normal (CLT, large $n$):

$$
Z = \frac{\hat\theta - \theta_0}{\mathrm{SE}(\hat\theta)} \sim \mathcal{N}(0,1) \ \text{ under } H_0
$$

**One-sample mean** ($\sigma$ known): $\ Z = \dfrac{\bar X - \mu_0}{\sigma/\sqrt{n}}$.

**Two-proportion z-test** (the workhorse of A/B tests on conversion / CTR). With successes giving $\hat p_A, \hat p_B$ and pooled rate $\hat p = \dfrac{x_A + x_B}{n_A + n_B}$:

$$
Z = \frac{\hat p_B - \hat p_A}{\sqrt{\hat p (1 - \hat p)\left(\dfrac{1}{n_A} + \dfrac{1}{n_B}\right)}}
$$

Reject (two-sided) if $\lvert Z \rvert > z_{1-\alpha/2}$ (= 1.96 for $\alpha = 0.05$).

---

## 3. t-test (small sample / unknown variance)

When $\sigma$ is estimated by the sample SD $s$ and $n$ is small, the statistic follows a **Student's t** distribution with $\nu$ degrees of freedom (heavier tails than normal).

- **One-sample:** $\ t = \dfrac{\bar X - \mu_0}{s/\sqrt{n}}, \quad \nu = n-1$.
- **Paired** (before/after on the same unit): run a one-sample t-test on the differences $d_i = X_i - Y_i$. Removes between-unit variance → more power.
- **Two-sample, equal variance (pooled):**

$$
t = \frac{\bar X_B - \bar X_A}{s_p \sqrt{\tfrac{1}{n_A} + \tfrac{1}{n_B}}}, \qquad s_p^2 = \frac{(n_A-1)s_A^2 + (n_B-1)s_B^2}{n_A + n_B - 2}
$$

- **Welch's t-test (unequal variance, default in practice):** $\ t = \dfrac{\bar X_B - \bar X_A}{\sqrt{s_A^2/n_A + s_B^2/n_B}}$, with the Welch–Satterthwaite $\nu$.

As $n \to \infty$, the t-distribution → normal, so z and t agree for large experiments.

---

## 4. Chi-square Test (categorical data)

For counts in categories, compare observed $O$ to expected-under-$H_0$ $E$:

$$
\chi^2 = \sum_{\text{cells}} \frac{(O - E)^2}{E}
$$

- **Goodness-of-fit:** does a categorical variable match a hypothesized distribution? $\nu = k - 1$.
- **Test of independence (contingency table):** are two categorical variables (e.g. variant × converted) independent? Expected cell $E_{ij} = \dfrac{(\text{row}_i\ \text{total})(\text{col}_j\ \text{total})}{N}$, with $\nu = (r-1)(c-1)$.

For a 2×2 table this is essentially equivalent to the two-proportion z-test ($\chi^2 = Z^2$).

---

## 5. ANOVA (comparing 3+ groups)

Running many pairwise t-tests inflates Type I error. **ANOVA** tests $H_0: \mu_1 = \mu_2 = \dots = \mu_k$ with a single **F-test** comparing variance *between* groups to variance *within* groups:

$$
F = \frac{\text{MS}_{\text{between}}}{\text{MS}_{\text{within}}}
= \frac{\sum_j n_j (\bar X_j - \bar X)^2 / (k-1)}{\sum_j \sum_i (X_{ij} - \bar X_j)^2 / (N-k)}
$$

Large $F$ ⇒ group means differ more than within-group noise. A significant ANOVA says "some group differs"; follow with **post-hoc** pairwise tests (Tukey HSD) that correct for multiplicity.

---

## 6. Confidence Intervals (and their duality with tests)

A $(1-\alpha)$ CI is the set of parameter values not rejected by a level-$\alpha$ test:

$$
\hat\theta \pm z_{1-\alpha/2}\cdot \mathrm{SE}(\hat\theta)
$$

**Duality:** a two-sided test rejects $H_0:\theta=\theta_0$ at level $\alpha$ $\iff$ the $(1-\alpha)$ CI excludes $\theta_0$. Reporting the CI is usually more informative than the p-value alone because it shows **effect size and precision**.

---

## 7. Power, Effect Size, MDE, and Sample Size

The four quantities $\alpha$, power $1-\beta$, effect size $\delta$, and sample size $n$ are linked — fix any three to get the fourth.

- **Minimum Detectable Effect (MDE):** the smallest true effect you want to be able to detect with the target power. Smaller MDE ⇒ much larger $n$ ($n \propto 1/\delta^2$).

**Sample size — difference of means** (two-sided, equal groups):

$$
n_{\text{per group}} = \frac{2\,(z_{1-\alpha/2} + z_{1-\beta})^2\,\sigma^2}{\delta^2}
$$

**Sample size — difference of proportions:**

$$
n_{\text{per group}} \approx \frac{(z_{1-\alpha/2} + z_{1-\beta})^2\,\big(p_A(1-p_A) + p_B(1-p_B)\big)}{(p_B - p_A)^2}
$$

Intuition for the $(z_{1-\alpha/2} + z_{1-\beta})^2$ term: you must separate the null and alternative sampling distributions by enough SEs to control **both** false positives ($\alpha$) and false negatives ($\beta$). For $\alpha=0.05,\ \text{power}=0.8$: $(1.96 + 0.84)^2 \approx 7.85$.

**Levers to gain power:** larger $n$, larger true effect, lower variance (see CUPED §10), paired/within designs, one-sided tests (use cautiously), higher $\alpha$ (rarely advisable).

---

## 8. Multiple Testing

Testing $m$ hypotheses at level $\alpha$ each makes the chance of **at least one** false positive explode: $P(\ge 1\ \text{FP}) = 1 - (1-\alpha)^m$ (e.g. 20 metrics → ~64%).

- **Family-Wise Error Rate (FWER)** — probability of *any* false positive.
  - **Bonferroni:** test each at $\alpha/m$. Simple, conservative.
  - **Holm:** step-down, uniformly more powerful than Bonferroni, still controls FWER.
- **False Discovery Rate (FDR)** — expected *fraction* of false positives among rejections; less conservative, good for many metrics/screening.
  - **Benjamini–Hochberg:** sort p-values $p_{(1)} \le \dots \le p_{(m)}$; find the largest $k$ with $p_{(k)} \le \dfrac{k}{m}\alpha$; reject $p_{(1)},\dots,p_{(k)}$.

Use FWER when any false positive is costly (e.g. one launch decision); FDR when exploring many metrics.

---

## 9. A/B Test Design

1. **Hypothesis & metric:** define the **OEC (Overall Evaluation Criterion)** — the one primary metric tied to the goal (e.g. revenue per user) — plus **guardrail metrics** that must not regress (latency, crash rate, unsubscribes).
2. **Randomization unit:** usually the **user** (not the request/session), so a user has a consistent experience and to avoid within-user correlation. The unit also determines the analysis unit (avoid analyzing at a finer grain than randomization).
3. **Power & duration:** compute $n$ from the MDE; convert to **runtime** given traffic. Run **at least one full week** to cover weekday/weekend seasonality.
4. **Randomization check — Sample Ratio Mismatch (SRM):** if you split 50/50 but observe 49/51, a chi-square test on the counts flags a bug; an SRM **invalidates** the experiment.
5. **Triggering:** analyze only users who could be affected (entered the experiment surface) to avoid diluting the effect.

---

## 10. Variance Reduction

Since $n \propto \sigma^2$, **cutting variance is equivalent to free sample size**.

### CUPED (Controlled-experiment Using Pre-Experiment Data)

Use a pre-experiment covariate $X$ (e.g. the same metric measured *before* the test) that is correlated with the outcome $Y$ but **unaffected by treatment**. Define the adjusted metric:

$$
Y_{\text{cv}} = Y - \theta\,(X - \mathbb{E}[X]), \qquad \theta = \frac{\mathrm{Cov}(Y, X)}{\mathrm{Var}(X)}
$$

$Y_{\text{cv}}$ is **unbiased** for the same mean (since $\mathbb{E}[X-\mathbb{E}X]=0$) but has variance reduced by the factor:

$$
\mathrm{Var}(Y_{\text{cv}}) = \mathrm{Var}(Y)\,(1 - \rho^2), \qquad \rho = \mathrm{Corr}(X, Y)
$$

A covariate with $\rho = 0.7$ removes ~50% of the variance → roughly halves the required sample size. (CUPED is just regression adjustment on a pre-period covariate.)

### Stratification

Split the population into strata (country, platform), estimate within each, and combine. Removes between-strata variance. **Post-stratification** does this at analysis time.

### Delta Method for Ratio Metrics

Many metrics are **ratios of sums over a unit different from the randomization unit** (e.g. clicks-per-session, where the user is randomized but the ratio is over sessions). The naive SE is wrong because numerator and denominator are correlated and the unit differs. For $R = \mu_Y/\mu_X$ estimated by $\bar Y / \bar X$, a first-order Taylor expansion gives:

$$
\mathrm{Var}\!\left(\frac{\bar Y}{\bar X}\right) \approx \frac{1}{n\,\mu_X^2}\left( \sigma_Y^2 - 2\,\frac{\mu_Y}{\mu_X}\,\sigma_{XY} + \frac{\mu_Y^2}{\mu_X^2}\,\sigma_X^2 \right)
$$

Use this (or bootstrap at the user level) to get correct CIs for ratio metrics.

---

## 11. Pitfalls

- **The peeking problem:** repeatedly checking the test and stopping when $p < 0.05$ massively inflates Type I error (each peek is another chance to cross the line). Fixes: fix the sample size in advance, or use methods valid under continuous monitoring — **sequential testing** (mSPRT, always-valid p-values) or **alpha-spending** (group sequential / O'Brien–Fleming boundaries).
- **Novelty & primacy effects:** users react to *change* itself; early effects can fade (novelty) or grow as users learn (primacy). Run longer; analyze new vs returning users.
- **SUTVA violation / interference / network effects:** one unit's treatment affects another's outcome (social networks, marketplaces, shared inventory). Standard A/B underestimates/overestimates effects. Fixes: **cluster randomization** (by community/region), ego-network or switchback designs, two-sided market experiments.
- **Simpson's paradox:** an aggregate effect reverses within subgroups (often from confounded traffic mix) — segment your results.
- **Twyman's law:** any figure that looks too good/surprising is probably an error (check instrumentation, SRM, logging).
- **Multiple-comparisons / metric fishing:** correct for it (§8).

---

## 12. Quasi-Experiments (when you can't randomize)

Sometimes randomization is impossible (ethics, all-or-nothing rollouts, historical data). Use observational/causal designs — covered in depth in `12_Causal_Inference.md`:

- **Difference-in-Differences:** compare treated vs control change over time (assumes parallel trends).
- **Regression Discontinuity:** exploit a threshold that assigns treatment.
- **Instrumental Variables:** use an instrument that shifts treatment but not the outcome directly.
- **Propensity-score matching / IPW:** balance covariates between treated and untreated.
- **Interrupted time series / synthetic control:** model the counterfactual trajectory.

---

## Key Takeaways

- **Framework:** state $H_0/H_1$, pick a statistic with a known null distribution, control $\alpha$ (Type I), and design for **power** $1-\beta$ (Type II). The p-value is $P(\text{data}\mid H_0)$, not $P(H_0\mid\text{data})$.
- **Pick the right test:** z/t for means, two-proportion z (or chi-square) for rates, ANOVA/F for 3+ groups, chi-square for contingency tables; Welch's t is the safe default for two means.
- **Sample size** $\propto \dfrac{(z_{1-\alpha/2}+z_{1-\beta})^2 \sigma^2}{\delta^2}$ — smaller MDE or higher power costs quadratically more users.
- **Multiplicity:** Bonferroni/Holm for FWER, Benjamini–Hochberg for FDR.
- **Design:** define an **OEC + guardrails**, randomize by user, check **SRM**, run ≥1 week.
- **Variance reduction = free power:** **CUPED** ($1-\rho^2$), stratification, and the **delta method** for ratio metrics.
- **Avoid:** peeking (use sequential/alpha-spending), novelty/primacy, interference (cluster/switchback), Simpson's paradox.
