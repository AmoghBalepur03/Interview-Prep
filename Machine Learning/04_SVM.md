# Support Vector Machines (SVM)

SVMs are powerful classifiers that find the **maximum-margin** separating hyperplane, extend to non-separable data via **soft margins**, and capture **non-linear boundaries** using the **kernel trick**.

---

## 1. What is a Hyperplane?

In $p$-dimensional space, a hyperplane is a flat affine subspace of dimension $p - 1$. Its equation:

$$
\beta_0 + \beta_1 X_1 + \beta_2 X_2 + \cdots + \beta_p X_p = 0
$$

where $\beta = (\beta_1, \dots, \beta_p)$ is a coefficient vector and $\beta_0$ is the intercept.

- Points $X$ satisfying the equation lie **exactly on** the hyperplane.
- Points with $\beta_0 + \beta^T X > 0$ lie on **one side**; points with $< 0$ lie on the **opposite side**.

---

## 2. Classification Based on a Separating Hyperplane

Consider training data $\{(x_i, y_i)\}_{i=1}^n$ with features $x_i \in \mathbb{R}^p$ and labels $y_i \in \{-1, +1\}$.

A **separating hyperplane** perfectly divides the classes if:

$$
y_i\big(\beta_0 + \beta_1 x_{i1} + \cdots + \beta_p x_{ip}\big) > 0 \quad \text{for all } i
$$

Classify a new observation $x$ by the **sign** of $f(x) = \beta_0 + \beta_1 x_1 + \cdots + \beta_p x_p$: positive → class $+1$, else class $-1$. The **magnitude** of $f(x)$ indicates confidence — higher magnitude means farther from the hyperplane and more confident.

---

## 3. The Maximal Margin Classifier

Infinitely many hyperplanes may perfectly separate the data, but the choice affects robustness. The **maximal margin classifier** chooses the separating hyperplane that **maximizes the margin** — the minimum distance between the hyperplane and any training point.

### Margin and Support Vectors

- The **margin** is the perpendicular distance from the hyperplane to the nearest data points.
- The hyperplane maximizing this margin is most robust to small perturbations.
- The closest points to the hyperplane are called **support vectors** — they "support" (define) the hyperplane.

### Mathematical Formulation

$$
\max_{\beta, \beta_0, M} \; M
$$

subject to:

$$
\|\beta\| = 1, \qquad y_i(\beta_0 + \beta^T x_i) \ge M, \quad i = 1, \dots, n
$$

where $M$ is the margin.

- The constraint $y_i(\beta_0 + \beta^T x_i) \ge M$ ensures every observation is on the correct side with margin at least $M$.
- The normalization $\|\beta\| = 1$ makes $M$ represent the actual **geometric margin**.

This is a **convex optimization problem** solvable efficiently with quadratic programming. (Equivalently, one minimizes $\tfrac12\|\beta\|^2$ subject to $y_i(\beta_0 + \beta^T x_i) \ge 1$.)

### Limitations and Non-Separable Data

- Requires **perfectly separable** data.
- In many real cases no separating hyperplane exists (classes overlap) → **no solution**.
- **Sensitivity to outliers:** a single mislabeled/outlying observation can drastically change the hyperplane, reducing the margin and increasing overfitting risk.

---

## 4. Support Vector Classifier (Soft Margin SVM)

To handle non-separable data, the support vector classifier allows some **misclassification or margin violations** via **slack variables** $\xi_i$ and a tuning parameter $C$:

$$
\max_{\beta, \beta_0, \xi} \; M \quad \text{subject to} \quad \|\beta\| = 1, \quad y_i(\beta_0 + \beta^T x_i) \ge M(1 - \xi_i), \quad \xi_i \ge 0, \quad \sum_i \xi_i \le C
$$

equivalently the standard form:

$$
\min_{\beta, \beta_0} \; \tfrac12 \|\beta\|^2 + C\sum_{i=1}^n \xi_i \quad \text{subject to} \quad y_i(\beta^T x_i + \beta_0) \ge 1 - \xi_i, \quad \xi_i \ge 0
$$

### Slack variables $\xi_i$

$$
\xi_i = \max\big(0,\; 1 - y_i(w^T x_i + b)\big)
$$

where $1 - y_i(w^T x_i + b)$ is how far the point is from satisfying the margin requirement.

| Value of $\xi_i$ | Meaning |
|---|---|
| $\xi_i = 0$ | On the **correct side** of the margin |
| $0 < \xi_i \le 1$ | On the **wrong side of the margin** but still correctly classified (between margin and boundary) |
| $\xi_i > 1$ | **Misclassified** (wrong side of the hyperplane) |

- **$C$:** tuning parameter controlling the total allowed margin violation (the "budget" for slack). Large $C$ → narrow margin, fewer violations (low bias, high variance); small $C$ → wide margin, more violations (high bias, low variance).
- Points with $\xi_i > 0$ are **support vectors** — they lie on the margin or violate it. **Only support vectors affect the hyperplane.** Points strictly outside the margin have $\xi_i = 0$ and do not impact the solution.

### Robustness and Decision Boundary

- The decision boundary is still **linear**: $f(x) = \beta_0 + \beta_1 x_1 + \cdots + \beta_p x_p$; classify by $\mathrm{sign}(f(x))$.
- **Support vectors:** observations on or violating the margin; the classifier depends only on these → robust to distant points ("outliers").
- **Comparison to other methods:**
  - LDA and QDA depend on **all** data points (modeling class means and variances).
  - Logistic regression is typically less sensitive to outliers (maximizes likelihood).
  - The support vector classifier is especially robust to distant points that are not support vectors.

---

## 5. Support Vector Machines (Kernels)

Many real-world problems have **non-linear** boundaries, where linear classifiers fail. SVMs **project the data into a higher-dimensional space** using a **kernel**, then draw a **linear** hyperplane in that higher dimension (which maps back to a non-linear boundary in the original space).

### The Kernel Trick

- The SVM optimization only involves **inner products** between data points.
- **Key insight:** if you can compute an inner product in the enlarged feature space **without explicitly representing the features**, high-dimensional mappings become computationally tractable.
- Formally, a **kernel** is a function $K(x, x') : \mathcal{X} \times \mathcal{X} \to \mathbb{R}$ that computes a "similarity" score between two vectors.
- **Requirement (Mercer's condition):** there must exist a feature mapping $\phi : \mathcal{X} \to \mathcal{H}$ into a (possibly infinite-dimensional) Hilbert space such that:
$$
K(x, x') = \langle \phi(x), \phi(x') \rangle_{\mathcal{H}}
$$
- **Example:** original $x = (x_1, x_2)$, map to $\phi(x) = (x_1, x_2, x_1^2, x_1 x_2, x_2^2)$.

Growing the feature space explicitly is expensive/unstable (especially for high-dimensional data or infinite expansions). But in SVMs, **all computations depend only on dot products** between feature vectors — so if you can compute $K(x, x') = \langle \phi(x), \phi(x') \rangle$ without building $\phi(x)$, you work "as if" in the higher-dimensional space efficiently.

### Mathematical Formulation (Dual Form)

The original decision boundary is $f(x) = \beta_0 + w^T x$. In the **dual form**, the weight vector $w$ can be written as:

$$
w = \sum_{i=1}^n \alpha_i y_i x_i
$$

Only **support vectors** have $\alpha_i \neq 0$. Substituting back:

$$
f(x) = \beta_0 + \sum_{i=1}^n \alpha_i y_i\, x_i^T x
$$

When we project into higher space, the dot product $x_i^T x$ is replaced by the kernel $K(x, x_i)$:

$$
f(x) = \beta_0 + \sum_{i \in S} \alpha_i y_i\, K(x, x_i)
$$

### Geometric and Computational Insight

- All SVM computations (training and prediction) depend only on **pairwise similarities (kernels)** between data points.
- This obviates the need to explicitly enumerate the expanded feature space.
- Many popular kernels correspond to **implicitly working in infinite-dimensional spaces** (e.g. the RBF kernel).

---

## 6. SVMs with Kernels: What Differs

### Training
- Every instance of $x_i^T x_j$ is replaced by $K(x_i, x_j)$.
- Only the **support vectors** (points on/inside the margin or misclassified) determine the final classifier, keeping prediction efficient.

### Prediction
- Prediction function: $f(x) = \beta_0 + \sum_{i \in S} \alpha_i K(x, x_i)$.
- Non-support vectors ($\alpha_i = 0$) do not contribute.
- The decision boundary in the original space becomes **non-linear**, shaped by the chosen kernel.

### Commonly Used Kernels

| Kernel | Formula | Notes |
|---|---|---|
| **Linear** | $K(x, x') = x^T x'$ | No mapping; the plain SVC |
| **Polynomial (degree $d$)** | $K(x, x') = (x^T x' + c)^d$ | Captures feature interactions up to degree $d$ |
| **Radial Basis Function (RBF / Gaussian)** | $K(x, x') = \exp(-\gamma \|x - x'\|^2)$ | Infinite-dimensional feature space; very flexible local boundaries |
| **Sigmoid** | $K(x, x') = \tanh(\kappa\, x^T x' + c)$ | Related to neural networks |

### Multiple Classifications

SVMs are inherently binary; for $K > 2$ classes:

- **One-vs-One (OvO):** train $\binom{K}{2}$ classifiers (one per pair), classify by majority vote.
- **One-vs-All (OvA / One-vs-Rest):** train $K$ classifiers (each class vs. the rest), assign the class with the highest $f(x)$.

---

## Key Takeaways

- SVM finds the **maximum-margin** hyperplane; only **support vectors** define it.
- The **soft margin** ($C$, slack $\xi_i$) handles overlapping classes and trades margin width against violations.
- The **kernel trick** computes inner products in a high-dimensional space implicitly, yielding non-linear boundaries; RBF is the most popular kernel.
- The dual form $f(x) = \beta_0 + \sum_{i\in S}\alpha_i y_i K(x, x_i)$ makes everything depend only on kernels between points.
