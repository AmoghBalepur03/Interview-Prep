# XGBoost (Extreme Gradient Boosting)

XGBoost is a scalable implementation of **gradient-boosted decision trees (GBDT)**. Its two defining ideas are (1) a **second-order (Newton) approximation** of any differentiable loss, which gives a closed-form score for tree structures and leaf weights, and (2) an **explicit regularization** term on tree complexity baked into the objective. Together with engineering tricks (weighted quantile sketch, sparsity-aware splits, cache-aware blocks) this makes it fast and accurate.

> This note assumes the boosting basics from `05_Tree_Based_Models.md` (additive trees, gradient boosting, shrinkage). Here we derive XGBoost from first principles, fully math-wise.

---

## 1. The Model: an Additive Ensemble of Trees

A prediction is the sum of $K$ regression trees (a tree ensemble / **CART** ensemble):

$$
\hat y_i = \phi(x_i) = \sum_{k=1}^{K} f_k(x_i), \qquad f_k \in \mathcal{F}
$$

where $\mathcal{F} = \{ f(x) = w_{q(x)} \}$ is the space of regression trees. A single tree is two objects:

- **Structure** $q : \mathbb{R}^d \to \{1, 2, \dots, T\}$ — maps an input to one of $T$ **leaves**.
- **Leaf weights** $w \in \mathbb{R}^T$ — the value $w_j$ output at leaf $j$.

So $f(x) = w_{q(x)}$: route $x$ down the tree to a leaf via $q$, then output that leaf's weight. Note every tree outputs a **continuous score on each leaf** (even for classification — the score is a logit), unlike a classification tree that outputs a class.

---

## 2. The Regularized Objective

XGBoost minimizes **training loss + complexity penalty**:

$$
\mathcal{L}(\phi) = \sum_{i=1}^{n} l\big(y_i, \hat y_i\big) + \sum_{k=1}^{K} \Omega(f_k)
$$

- $l$ is any **convex, twice-differentiable** loss (squared error, logistic, etc.).
- $\Omega$ penalizes the complexity of each tree:

$$
\Omega(f) = \gamma\, T + \tfrac12 \lambda \sum_{j=1}^{T} w_j^2 + \alpha \sum_{j=1}^{T} |w_j|
$$

| Term | Meaning | Effect |
|---|---|---|
| $\gamma T$ | cost per leaf ($T$ = number of leaves) | discourages large trees → **pre-pruning** |
| $\tfrac12 \lambda \sum w_j^2$ | **L2** on leaf weights | shrinks weights, smooths output |
| $\alpha \sum |w_j|$ | **L1** on leaf weights | drives some leaf weights to 0 (sparsity) |

This explicit $\Omega$ is what the name "regularized boosting" refers to; classic GBM has no such term in its objective.

---

## 3. Additive (Forward Stagewise) Training

We cannot optimize all $K$ trees jointly, so we add one tree at a time. Let $\hat y_i^{(t)}$ be the prediction after $t$ trees:

$$
\hat y_i^{(t)} = \hat y_i^{(t-1)} + f_t(x_i)
$$

At iteration $t$ we hold the previous $t-1$ trees fixed and choose $f_t$ to minimize:

$$
\mathcal{L}^{(t)} = \sum_{i=1}^{n} l\Big(y_i,\ \hat y_i^{(t-1)} + f_t(x_i)\Big) + \Omega(f_t)
$$

The first prediction uses a constant **base score** $\hat y_i^{(0)} = b$ (e.g. 0.5, or the global mean / log-odds).

---

## 4. Second-Order Taylor Approximation (the key step)

Expand $l$ around the current prediction $\hat y_i^{(t-1)}$ to **second order** in the increment $f_t(x_i)$. Define the first- and second-order gradients of the loss w.r.t. the prediction:

$$
g_i = \frac{\partial\, l(y_i, \hat y^{(t-1)})}{\partial\, \hat y^{(t-1)}}, \qquad
h_i = \frac{\partial^2\, l(y_i, \hat y^{(t-1)})}{\partial\, (\hat y^{(t-1)})^2}
$$

Then (Taylor, treating $f_t(x_i)$ as the small step):

$$
\mathcal{L}^{(t)} \approx \sum_{i=1}^{n} \Big[\, l(y_i, \hat y_i^{(t-1)}) + g_i\, f_t(x_i) + \tfrac12 h_i\, f_t^2(x_i) \,\Big] + \Omega(f_t)
$$

The term $l(y_i, \hat y_i^{(t-1)})$ is **constant** w.r.t. $f_t$, so drop it. The objective to optimize at step $t$ becomes:

$$
\tilde{\mathcal{L}}^{(t)} = \sum_{i=1}^{n} \Big[\, g_i\, f_t(x_i) + \tfrac12 h_i\, f_t^2(x_i) \,\Big] + \Omega(f_t)
$$

**Why second order?** Using both $g_i$ and $h_i$ is a **Newton step** in function space (classic GBM uses only $g_i$ — gradient descent). It (a) converges faster, (b) adapts the step size per-instance via the curvature $h_i$, and (c) yields the clean closed-form leaf score below. XGBoost only needs $g_i, h_i$ — the loss itself is abstracted away, so any differentiable loss plugs in.

---

## 5. Optimal Leaf Weights and the Structure Score

Now reorganize the sum **by leaf** instead of by instance. Let

$$
I_j = \{\, i \mid q(x_i) = j \,\}
$$

be the set of training instances that land in leaf $j$. Since every instance in $I_j$ outputs the same weight $w_j$, and expanding $\Omega$ (take $\alpha = 0$ for now):

$$
\tilde{\mathcal{L}}^{(t)} = \sum_{j=1}^{T} \Big[\, \Big(\sum_{i \in I_j} g_i\Big) w_j + \tfrac12 \Big(\sum_{i \in I_j} h_i + \lambda\Big) w_j^2 \,\Big] + \gamma T
$$

Define the per-leaf gradient sums:

$$
G_j = \sum_{i \in I_j} g_i, \qquad H_j = \sum_{i \in I_j} h_i
$$

so each leaf contributes an **independent quadratic** in $w_j$:

$$
\tilde{\mathcal{L}}^{(t)} = \sum_{j=1}^{T} \Big[\, G_j w_j + \tfrac12 (H_j + \lambda) w_j^2 \,\Big] + \gamma T
$$

### Optimal weight

For a **fixed structure** $q$, minimize each quadratic by setting its derivative to zero:

$$
\frac{\partial}{\partial w_j}\Big[ G_j w_j + \tfrac12 (H_j + \lambda) w_j^2 \Big] = G_j + (H_j + \lambda) w_j = 0
\;\;\Longrightarrow\;\;
\boxed{\,w_j^{\ast} = -\dfrac{G_j}{H_j + \lambda}\,}
$$

This is a **regularized Newton step**: numerator = aggregated gradient, denominator = aggregated curvature (plus $\lambda$). The $\lambda$ shrinks weights toward 0 and prevents blow-up when $H_j$ is tiny.

### Structure score

Substituting $w_j^{\ast}$ back (using that a quadratic $a w + \tfrac12 b w^2$ has minimum $-\tfrac{a^2}{2b}$ at $w = -a/b$):

$$
\boxed{\,\tilde{\mathcal{L}}^{(t)}(q) = -\tfrac12 \sum_{j=1}^{T} \dfrac{G_j^2}{H_j + \lambda} + \gamma T\,}
$$

This **structure score** measures how good a tree structure $q$ is (smaller = better, like an impurity). It is the XGBoost analogue of Gini/entropy, but derived directly from the loss and the regularizer. The quantity $\dfrac{G_j^2}{H_j + \lambda}$ is the "quality" of leaf $j$ (larger is better, since it enters with a minus sign).

---

## 6. Split Finding: the Gain Formula

We cannot enumerate all possible structures $q$, so we grow the tree **greedily**, one split at a time, choosing splits that **reduce the structure score** the most.

Consider splitting a leaf whose instance set is $I = I_L \cup I_R$ into a left child $I_L$ and right child $I_R$. Using the leaf-quality terms:

- **Before** (one leaf): quality $\dfrac{(G_L + G_R)^2}{H_L + H_R + \lambda}$, with one $\gamma$.
- **After** (two leaves): quality $\dfrac{G_L^2}{H_L + \lambda} + \dfrac{G_R^2}{H_R + \lambda}$, with two $\gamma$.

The **gain** (reduction in objective from making the split) is:

$$
\boxed{\;\mathrm{Gain} = \tfrac12 \left[ \dfrac{G_L^2}{H_L + \lambda} + \dfrac{G_R^2}{H_R + \lambda} - \dfrac{(G_L + G_R)^2}{H_L + H_R + \lambda} \right] - \gamma\;}
$$

Reading the formula:

- The bracket = (score of left) + (score of right) − (score if we don't split). It is always $\ge 0$ (a consequence of convexity), so splitting never hurts the *fit*.
- The $-\gamma$ term is the **price of one extra leaf**. We accept the split only if $\mathrm{Gain} > 0$, i.e. the fit improvement exceeds $\gamma$.
- Thus $\gamma$ acts as a **minimum gain threshold** — a principled, built-in **pre-pruning** parameter. (XGBoost also supports post-pruning: grow to `max_depth`, then prune back nodes with negative gain.)

This single formula replaces Gini/entropy/variance reduction and works for any loss.

---

## 7. Exact Greedy Split Finding

For each leaf, find the best (feature, threshold) by scanning:

```
for each feature k:
    sort instances in the node by value of feature k
    G_L, H_L = 0, 0
    G, H = sum of g_i, sum of h_i  over the node   # totals
    for each instance j in sorted order (candidate split = after j):
        G_L += g_j ;  H_L += h_j
        G_R = G - G_L ;  H_R = H - H_L
        gain = 0.5*( G_L^2/(H_L+λ) + G_R^2/(H_R+λ) - G^2/(H+λ) ) - γ
        track the max gain and its (feature, threshold)
pick the global best split; if best gain <= 0, make this node a leaf
```

- Sorting each feature once is $O(n \log n)$; the scan accumulates $G_L, H_L$ **incrementally** in $O(n)$ using $G_R = G - G_L$, $H_R = H - H_L$.
- Overall (exact greedy): about $O\big(K \cdot d \cdot \lVert x \rVert_0 \cdot \log n\big)$, where $K$ = trees, $d$ = depth, $\lVert x \rVert_0$ = number of non-missing entries. With **column blocks** (sort each feature once, reuse across the whole training), the $\log n$ sort cost is paid once.

Leaf weights at the end of growth are set to $w_j^{\ast} = -G_j/(H_j + \lambda)$.

---

## 8. Approximate Split Finding + Weighted Quantile Sketch

Exact greedy enumerates **every** value as a candidate split — infeasible for huge / out-of-core data. The **approximate** algorithm proposes a small set of candidate thresholds per feature from the feature's **percentiles**, buckets instances into bins, and only evaluates gains at bin boundaries.

- **Global** variant: propose candidates once per tree (more candidates needed).
- **Local** variant: re-propose after each split (fewer candidates, refines deeper nodes).

### Why weight quantiles by $h_i$

Rewrite the step-$t$ objective by **completing the square**:

$$
\tilde{\mathcal{L}}^{(t)} = \sum_{i=1}^{n} \Big[ g_i f_t(x_i) + \tfrac12 h_i f_t^2(x_i) \Big] + \Omega
= \sum_{i=1}^{n} \tfrac12 h_i \Big( f_t(x_i) - \big(-\tfrac{g_i}{h_i}\big) \Big)^2 + \Omega + \text{const}
$$

(the constant $-\sum_i \tfrac{g_i^2}{2 h_i}$ is independent of $f_t$). This is **weighted squared error** regressing $f_t$ onto pseudo-targets $-g_i/h_i$ with **per-instance weight $h_i$**. So when choosing candidate split points, the natural measure of "data mass" is $h_i$, not raw counts.

Define the **rank function** for feature $k$ over the multiset $D_k = \{(x_{1k}, h_1), (x_{2k}, h_2), \dots\}$:

$$
r_k(z) = \frac{\displaystyle\sum_{(x, h)\in D_k,\ x < z} h}{\displaystyle\sum_{(x, h)\in D_k} h}
$$

Choose candidate splits $\{s_{k1}, s_{k2}, \dots, s_{kl}\}$ so that consecutive candidates differ in rank by at most $\epsilon$:

$$
\big| r_k(s_{k,j}) - r_k(s_{k,j+1}) \big| < \epsilon \quad\Rightarrow\quad \text{about } \tfrac{1}{\epsilon} \text{ candidate splits.}
$$

XGBoost's **weighted quantile sketch** computes these $h$-weighted quantiles approximately with provable error bounds in a distributed/streaming setting — a key reason it scales.

---

## 9. Sparsity-Aware Split Finding (Missing Values)

Real data has missing values, zero entries (from one-hot encoding), etc. XGBoost handles this by learning a **default direction** at every split: instances with a missing value for the split feature are sent to the default branch.

The default direction is **learned from the data**: during the scan, try (a) sending all missing to the **right** (enumerate present values ascending) and (b) sending all missing to the **left** (enumerate descending); pick whichever direction + threshold gives the larger gain.

- Only **non-missing** entries are visited → cost is linear in $\lVert x \rVert_0$ (number of present values), not $n \times d$. On sparse data this is a large speedup (the paper reports ~50×).

---

## 10. Shrinkage and Subsampling (extra regularization)

Beyond $\Omega$, three further controls fight overfitting:

1. **Shrinkage / learning rate $\eta$:** scale each new tree before adding it,
$$
\hat y_i^{(t)} = \hat y_i^{(t-1)} + \eta\, f_t(x_i), \qquad 0 < \eta \le 1.
$$
Small $\eta$ means each tree contributes little, leaving "room" for later trees → needs more trees but generalizes better. (Trade-off: smaller $\eta$ ↔ larger `n_estimators`.)

2. **Row (instance) subsampling:** fit each tree on a random fraction `subsample` of the rows (stochastic boosting) — adds variance reduction and speed.

3. **Column (feature) subsampling:** at the tree / level / node level (`colsample_bytree`, `colsample_bylevel`, `colsample_bynode`), use only a random subset of features — decorrelates trees (as in random forests) and is often more effective than row subsampling, and speeds up split finding.

---

## 11. Worked Gradients for Common Losses

XGBoost needs only $(g_i, h_i)$. Two canonical cases:

### (a) Squared error regression — $l = \tfrac12 (y - \hat y)^2$

$$
g_i = \frac{\partial l}{\partial \hat y} = \hat y_i^{(t-1)} - y_i \;\; (=\text{negative residual}), \qquad h_i = 1
$$

Then $G_j = \sum_{i\in I_j}(\hat y_i^{(t-1)} - y_i)$ and $H_j = |I_j| = n_j$, so the optimal leaf weight is

$$
w_j^{\ast} = -\frac{G_j}{n_j + \lambda} = \frac{\sum_{i \in I_j}\big(y_i - \hat y_i^{(t-1)}\big)}{n_j + \lambda}
$$

i.e. the (L2-shrunk) **mean residual** in the leaf. With $\lambda = 0$ this is exactly the GBM leaf value — XGBoost reduces to standard gradient boosting in this special case.

### (b) Binary logistic — raw score (logit) $\hat y$, label $y \in \{0,1\}$

With $p_i = \sigma(\hat y_i) = \dfrac{1}{1 + e^{-\hat y_i}}$ and $l = -[\,y\ln p + (1-y)\ln(1-p)\,]$:

$$
g_i = p_i - y_i, \qquad h_i = p_i (1 - p_i)
$$

Here $h_i$ is largest ($=0.25$) when the model is most **uncertain** ($p_i = 0.5$) and near 0 when confident — so confident points contribute little curvature. The leaf score $w_j^{\ast} = -G_j/(H_j + \lambda)$ is then added to the logit; the final probability is $\sigma$ of the summed logits.

(Multiclass softmax, Poisson, ranking objectives, etc. follow the same pattern — just supply their $g_i, h_i$.)

---

## 12. Advanced: L1 on Leaf Weights (soft-thresholding)

With $\alpha > 0$, the per-leaf objective is $G_j w + \tfrac12 (H_j + \lambda) w^2 + \alpha |w|$. Because of the non-smooth $|w|$, the minimizer is a **soft-thresholded** Newton step:

$$
w_j^{\ast} = -\frac{\mathrm{soft}(G_j,\ \alpha)}{H_j + \lambda}, \qquad
\mathrm{soft}(G, \alpha) = \mathrm{sign}(G)\cdot \max\big(|G| - \alpha,\ 0\big)
$$

So if $|G_j| \le \alpha$ the leaf weight is exactly **0**. The gain formula uses the same thresholded leaf scores in place of $G^2/(H+\lambda)$.

---

## 13. Full Training Algorithm

$$
\begin{aligned}
&\textbf{Input: } \{(x_i, y_i)\}_{i=1}^n,\ \text{loss } l,\ K,\ \eta,\ \lambda,\ \gamma,\ \alpha,\ \texttt{max\_depth},\ \texttt{min\_child\_weight} \\
&\text{Initialize } \hat y_i^{(0)} = b \ (\text{base score}) \\
&\textbf{for } t = 1 \text{ to } K: \\
&\quad \text{compute } g_i = \partial_{\hat y} l(y_i, \hat y_i^{(t-1)}),\ \ h_i = \partial_{\hat y}^2 l(y_i, \hat y_i^{(t-1)}) \quad \forall i \\
&\quad \text{grow tree } f_t \text{ greedily: at each node pick the split maximizing } \mathrm{Gain},\\
&\quad\quad \text{stop if } \mathrm{Gain} \le 0,\ \text{depth} = \texttt{max\_depth},\ \text{or child } H < \texttt{min\_child\_weight} \\
&\quad \text{set leaf weights } w_j^{\ast} = -G_j/(H_j + \lambda) \ (\text{or soft-thresholded if } \alpha>0) \\
&\quad \hat y_i^{(t)} = \hat y_i^{(t-1)} + \eta\, f_t(x_i) \\
&\textbf{return } \phi(x) = b + \eta \sum_{t=1}^{K} f_t(x)
\end{aligned}
$$

---

## 14. Hyperparameters (and the math they control)

| Parameter | Symbol | Role |
|---|---|---|
| `eta` / learning_rate | $\eta$ | shrinkage per tree (Sec. 10) |
| `n_estimators` / num_round | $K$ | number of boosting rounds |
| `max_depth` | $d$ | max tree depth (model capacity) |
| `min_child_weight` | — | min $H_j = \sum_{i\in I_j} h_i$ required in a child; **stops splits on low-curvature (low-confidence-mass) leaves**. For squared loss ($h=1$) this is just the min #samples; for logistic it is the min sum of $p(1-p)$ |
| `gamma` / min_split_loss | $\gamma$ | minimum gain to split; complexity cost per leaf (Sec. 6) |
| `lambda` / reg_lambda | $\lambda$ | L2 on leaf weights (Sec. 5) |
| `alpha` / reg_alpha | $\alpha$ | L1 on leaf weights → sparse leaves (Sec. 12) |
| `subsample` | — | row sampling fraction |
| `colsample_by*` | — | feature sampling fraction (tree/level/node) |

**Tuning intuition:** $\eta$ down + $K$ up = slower, more accurate. $\gamma$, $\lambda$, $\alpha$, `min_child_weight` up, `max_depth` down, subsampling down = **stronger regularization** (combat overfitting).

---

## 15. XGBoost vs. GBM vs. Random Forest

| Aspect | Random Forest | Classic GBM | XGBoost |
|---|---|---|---|
| Tree combination | parallel (bagging, average/vote) | sequential (fit residuals) | sequential (fit $g_i, h_i$) |
| Loss approximation | — | 1st order (gradient) | **2nd order (Newton)** |
| Regularization in objective | implicit (sampling) | shrinkage, tree size | **explicit $\gamma, \lambda, \alpha$ + shrinkage + sampling** |
| Leaf value | mean / majority | line-search on gradient | closed form $-G_j/(H_j+\lambda)$ |
| Split criterion | Gini / variance | gradient-based | **gain from structure score** |
| Missing values | needs handling | needs handling | **sparsity-aware default direction** |
| Bias / variance | mainly ↓ variance | mainly ↓ bias | ↓ bias and variance, well-regularized |

---

## 16. System Optimizations (brief)

Math aside, much of XGBoost's speed is engineering:

- **Column block (CSC) layout:** store data sorted by feature value in compressed blocks so split finding is **parallelizable** across features and the sort is done **once**.
- **Cache-aware access:** prefetch gradient statistics into a buffer to avoid cache misses from non-contiguous $g_i, h_i$ reads.
- **Out-of-core / block compression & sharding:** train on data larger than RAM by streaming compressed blocks from disk.
- **Approximate + weighted sketch:** enables distributed training (Sec. 8).

---

## Key Takeaways

- **Model:** $\hat y = \sum_k f_k$, each $f$ a regression tree $f(x) = w_{q(x)}$; objective = loss $+\ \Omega(f) = \gamma T + \tfrac12\lambda\lVert w\rVert^2 + \alpha\lVert w\rVert_1$.
- **Second-order trick:** approximate the loss with $g_i$ (gradient) and $h_i$ (Hessian); the per-step objective becomes $\sum_i [g_i f + \tfrac12 h_i f^2] + \Omega$ — a Newton step in function space, loss-agnostic.
- **Closed forms:** optimal leaf weight $w_j^{\ast} = -\dfrac{G_j}{H_j+\lambda}$; structure score $-\tfrac12\sum_j \dfrac{G_j^2}{H_j+\lambda} + \gamma T$.
- **Split gain:** $\tfrac12\big[\frac{G_L^2}{H_L+\lambda} + \frac{G_R^2}{H_R+\lambda} - \frac{(G_L+G_R)^2}{H_L+H_R+\lambda}\big] - \gamma$; split iff $>0$, so $\gamma$ is built-in pre-pruning.
- **Scalability:** weighted quantile sketch (weights $= h_i$), sparsity-aware default directions, column blocks, cache-aware and out-of-core training.
- **Regularization everywhere:** $\gamma, \lambda, \alpha$ in the objective **plus** shrinkage $\eta$ and row/column subsampling — why XGBoost generalizes well out of the box.
