# Causal Inference

Most ML predicts **correlation** ($P(Y \mid X)$); decisions need **causation** — what happens to $Y$ if we *intervene* and change $X$ ($P(Y \mid \mathrm{do}(X))$). Causal inference is the framework for estimating intervention effects, from randomized experiments to messy observational data. It is increasingly tested in DS/Applied-Scientist interviews and underlies every "does this feature/treatment actually work?" question.

> Companion to `11_Hypothesis_Testing_and_AB_Testing.md`: A/B tests are the gold standard for causation; this note covers the theory and the **observational** methods used when you can't randomize.

---

## 1. The Potential Outcomes Framework (Rubin Causal Model)

For each unit $i$ with binary treatment $T_i \in \{0, 1\}$, define two **potential outcomes**:

- $Y_i(1)$ — the outcome **if treated**.
- $Y_i(0)$ — the outcome **if untreated**.

The **individual treatment effect** is $\tau_i = Y_i(1) - Y_i(0)$.

**The fundamental problem of causal inference:** we only ever observe **one** of the two for each unit — the one corresponding to the treatment actually received:

$$
Y_i^{\text{obs}} = T_i\, Y_i(1) + (1 - T_i)\, Y_i(0)
$$

The other is the unobserved **counterfactual**. Causal inference is fundamentally a **missing-data problem**: we must estimate the missing potential outcome.

---

## 2. Estimands (what we actually estimate)

Since individual effects are unidentifiable, we target **averages**:

$$
\text{ATE} = \mathbb{E}[\,Y(1) - Y(0)\,] \quad\text{(Average Treatment Effect)}
$$
$$
\text{ATT} = \mathbb{E}[\,Y(1) - Y(0) \mid T = 1\,] \quad\text{(on the Treated)}
$$
$$
\text{CATE}(x) = \mathbb{E}[\,Y(1) - Y(0) \mid X = x\,] \quad\text{(Conditional / heterogeneous effects)}
$$

ATT matters when the treated group differs from the population (e.g. effect of a promo on the users who got it). CATE drives **personalization / uplift modeling** (treat only users with positive effect).

---

## 3. Identifying Assumptions

To estimate these from data we need:

1. **SUTVA (Stable Unit Treatment Value):** no interference (one unit's treatment doesn't affect another's outcome) and a single version of treatment. (Violated by network effects / marketplaces.)
2. **Unconfoundedness / Ignorability:** $\big(Y(0), Y(1)\big) \perp T \mid X$ — given covariates $X$, treatment is "as good as random." (No unmeasured confounders.)
3. **Positivity / Overlap:** $0 < e(x) < 1$ for all $x$ — every type of unit has a chance of both treatment and control.
4. **Consistency:** the observed outcome equals the potential outcome for the received treatment.

Under (2)–(4), causal effects are **identified** from observational data via covariate adjustment.

---

## 4. Why Randomization Solves Everything

Randomized assignment forces $T \perp \big(Y(0), Y(1)\big)$ **unconditionally** — treatment is independent of *all* covariates, observed or not. Then there are no confounders and:

$$
\text{ATE} = \mathbb{E}[Y \mid T = 1] - \mathbb{E}[Y \mid T = 0]
$$

a simple difference in observed means. This is why A/B tests are the gold standard — they make the naive comparison valid by design. Everything below is about **recovering this** when randomization isn't available.

---

## 5. Confounding, Selection Bias, and Simpson's Paradox

- **Confounder:** a variable $X$ that causes **both** $T$ and $Y$ (e.g. user activity affects both seeing a feature and converting). Ignoring it biases the naive estimate.
- **Selection bias:** conditioning on a variable affected by treatment/outcome (e.g. analyzing only users who completed checkout).
- **Simpson's paradox:** the direction of an effect **reverses** when you aggregate over a confounder.

**Worked example (Simpson's paradox).** A drug appears worse overall but better in *both* subgroups:

| Group | Drug recovery | No-drug recovery |
|---|---|---|
| Mild cases | 81/87 (93%) | 234/270 (87%) |
| Severe cases | 192/263 (73%) | 55/80 (69%) |
| **Combined** | **273/350 (78%)** | **289/350 (83%)** |

The drug helps in each subgroup, yet looks harmful combined — because severe patients (lower recovery) were disproportionately given the drug. **Severity is the confounder**; conditioning on it reveals the true (positive) effect.

---

## 6. Causal Graphs (DAGs)

A **Directed Acyclic Graph** encodes assumptions: nodes = variables, arrows = direct causes. They tell you **what to adjust for** (and what *not* to).

Three building blocks on a path:

- **Chain** $A \to B \to C$: $B$ is a **mediator**; $A$ and $C$ are associated; conditioning on $B$ blocks it.
- **Fork** $A \leftarrow B \to C$: $B$ is a **confounder**; induces spurious association; **condition on $B$** to block it.
- **Collider** $A \to B \leftarrow C$: $A,C$ are marginally independent; **conditioning on $B$ (or its descendant) *opens* a spurious path** — do **not** condition on colliders.

**Backdoor criterion:** to estimate the effect of $T$ on $Y$, find a set $Z$ that (a) blocks all "backdoor" paths from $T$ to $Y$ (paths into $T$), and (b) contains no descendants of $T$. Then:

$$
P(Y \mid \mathrm{do}(T = t)) = \sum_{z} P(Y \mid T = t, Z = z)\, P(Z = z)
$$

This **backdoor adjustment** is the formal justification for "control for the confounders." (When no valid backdoor set exists, the **front-door criterion** can sometimes identify the effect via a mediator.)

---

## 7. Adjustment Methods Under Unconfoundedness

### Regression adjustment
Fit $\mathbb{E}[Y \mid T, X]$ (e.g. include $T$ and covariates $X$); the coefficient on $T$ estimates the effect. Simple but relies on a correct outcome model.

### Propensity score
The **propensity score** $e(x) = P(T = 1 \mid X = x)$ (estimated by logistic regression / GBM) is a one-number summary of confounders. Rosenbaum–Rubin: if unconfoundedness holds given $X$, it also holds given $e(X)$ — so balancing on the scalar $e(X)$ suffices.

- **Matching:** pair each treated unit with control unit(s) of similar $e(x)$; compare outcomes. Estimates ATT.
- **Stratification:** bin by $e(x)$, average within-bin effects.
- **Inverse Propensity Weighting (IPW):** reweight to create a pseudo-population where treatment is independent of $X$:

$$
\widehat{\text{ATE}} = \frac{1}{n}\sum_{i=1}^{n}\left[ \frac{T_i\, Y_i}{e(X_i)} - \frac{(1 - T_i)\, Y_i}{1 - e(X_i)} \right]
$$

Intuition: up-weight treated units that *looked* unlikely to be treated (and vice versa). Sensitive to small $e(x)$ (extreme weights) — trim/clip or use **stabilized weights**.

### Doubly Robust (AIPW)
Combine an outcome model **and** a propensity model; the estimator is consistent if **either** model is correct. The default modern choice (and the basis of meta-learners / double ML).

---

## 8. Difference-in-Differences (DiD)

When you have **before/after** data for a **treated** and a **control** group (e.g. a feature launched in one region):

$$
\widehat{\text{ATT}} = \big(\bar Y_{\text{treat,post}} - \bar Y_{\text{treat,pre}}\big) - \big(\bar Y_{\text{ctrl,post}} - \bar Y_{\text{ctrl,pre}}\big)
$$

Differencing **twice** cancels (a) fixed group differences and (b) common time trends. Equivalent regression with the interaction term:

$$
Y = \beta_0 + \beta_1\,\text{Treat} + \beta_2\,\text{Post} + \beta_3\,(\text{Treat}\cdot\text{Post}) + \varepsilon, \qquad \widehat{\text{ATT}} = \beta_3
$$

**Key assumption — parallel trends:** absent treatment, the two groups would have moved in parallel. Check pre-period trends; use event-study plots.

---

## 9. Instrumental Variables (IV)

When there's an **unmeasured confounder**, find an **instrument** $Z$ that:

1. **Relevance:** affects the treatment ($Z \to T$, $\mathrm{Cov}(Z,T)\neq 0$).
2. **Exclusion:** affects $Y$ **only through** $T$ (no direct $Z \to Y$, no common cause).

Then the effect is recovered by the **Wald estimator**:

$$
\widehat{\tau}_{\text{IV}} = \frac{\mathrm{Cov}(Y, Z)}{\mathrm{Cov}(T, Z)} = \frac{\text{effect of } Z \text{ on } Y}{\text{effect of } Z \text{ on } T}
$$

Generalized by **Two-Stage Least Squares (2SLS):** regress $T$ on $Z$ to get $\hat T$, then regress $Y$ on $\hat T$. With heterogeneous effects, IV estimates the **LATE** (effect on "compliers"). Classic instruments: randomized **encouragement** (intent-to-treat as instrument for actual usage), lottery assignment, distance/policy changes.

---

## 10. Regression Discontinuity (RDD)

When treatment is assigned by a **threshold** on a continuous running variable $X$ (e.g. scholarship if test score $\ge c$), units **just above** vs **just below** the cutoff are comparable (as-good-as-random near $c$):

$$
\widehat{\tau}_{\text{RDD}} = \lim_{x \downarrow c} \mathbb{E}[Y \mid X = x] - \lim_{x \uparrow c} \mathbb{E}[Y \mid X = x]
$$

Estimated by **local linear regression** on each side of $c$.

- **Sharp RDD:** treatment is a deterministic step at $c$.
- **Fuzzy RDD:** the threshold only changes the *probability* of treatment → combine with IV (use "above cutoff" as the instrument).

Validity check: the running variable shouldn't be **manipulable** around the cutoff (McCrary density test), and other covariates should be smooth at $c$.

---

## 11. Choosing a Method (decision guide)

- **Can you randomize?** → run an **A/B test** (note 11). Always first choice.
- **Have a threshold rule?** → **RDD**.
- **Have a valid instrument?** → **IV / 2SLS**.
- **Have panel (before/after, treat/control)?** → **DiD**.
- **Have rich covariates and believe no unmeasured confounders?** → **matching / IPW / doubly robust**.
- **None of the above?** → be honest about untestable assumptions; do sensitivity analysis.

**Common pitfalls:** controlling for **colliders** or **mediators** (introduces bias — don't blindly "add all features"); poor **overlap** (extrapolation); unmeasured confounding (the assumption no method can fully test); interpreting CATE noise as real heterogeneity.

---

## Key Takeaways

- **Potential outcomes:** $\tau_i = Y_i(1) - Y_i(0)$; we observe only one outcome per unit → causal inference is a missing-data problem. Estimands: **ATE, ATT, CATE**.
- **Assumptions:** SUTVA, unconfoundedness, positivity, consistency. **Randomization** makes them hold by design ⇒ ATE = difference in means.
- **Confounders** (forks) must be adjusted; **colliders** and **mediators** must **not** be conditioned on — DAGs + the **backdoor criterion** tell you which.
- **Observational toolkit:** regression adjustment, **propensity scores** (matching / IPW / doubly-robust), **DiD** (parallel trends), **IV/2SLS** (relevance + exclusion → LATE), **RDD** (threshold).
- **Correlation $\neq$ causation:** Simpson's paradox shows aggregates can reverse subgroup effects — always reason about the causal structure, not just the data.
