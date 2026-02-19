# K-Nearest Neighbours and Clustering

This note covers **KNN** (a supervised, instance-based method) and **clustering** (unsupervised grouping) — K-Means, the Elbow/Silhouette methods for choosing $k$, and the Expectation-Maximization (EM) algorithm with Gaussian Mixture Models.

---

# Part I — K-Nearest Neighbours (KNN)

KNN is a simple yet powerful **supervised** algorithm for both classification and regression. It is **non-parametric** and **instance-based (lazy)**: it does not build an explicit model during training but makes decisions based on stored training data when predictions are requested.

## 1. Algorithm

1. **Choose the number of neighbors $k$** — a user-defined parameter.
2. **Calculate distance:** for a new data point, compute the distance to all points in the training set using a distance metric.
3. **Find nearest neighbors:** select the $k$ closest training points.
4. **Make prediction:**
   - **Classification:** majority voting among the $k$ neighbors' classes.
   - **Regression:** mean value of the $k$ neighbors.

## 2. Distance Metrics

KNN uses distance metrics to quantify similarity:

- **Euclidean distance:** straight-line distance, $\sqrt{\sum_i (x_i - y_i)^2}$.
- **Manhattan distance:** along axes at right angles (city blocks), $\sum_i |x_i - y_i|$.
- **Minkowski distance:** generalization, $\left(\sum_i |x_i - y_i|^p\right)^{1/p}$ — Euclidean ($p=2$) and Manhattan ($p=1$) are special cases.
- **Others:** Hamming, Cosine, depending on the problem.

## 3. Characteristics

- **Lazy learner:** no model learned upfront; all training data is stored.
- **Non-parametric:** no assumptions about the data distribution.
- Works for both **classification and regression**.
- Performance depends on the choice of $k$ and the distance metric.
- **Computationally intensive** for large datasets — requires computing distances to all training points at prediction time.

## 4. How to Choose the Right $k$

- **Cross-validation:** use k-fold CV on the training data to evaluate different $k$ values and select the one minimizing prediction error.
- **Odd vs even $k$:** for classification, choose **odd** $k$ to avoid ties in majority voting (especially for two classes).
- **Domain knowledge:** sometimes $k$ is set by problem context or dataset size (e.g. $k \approx \sqrt{n}$).
- **Elbow method:** plot error vs. $k$ and pick the bend.
- **Weighting neighbors:** weight neighbors by **inverse distance** so closer points matter more.

> **Bias-variance trade-off:** small $k$ → low bias, high variance (noisy, jagged boundary); large $k$ → high bias, low variance (smooth boundary, may under-fit).

---

# Part II — Clustering

## 5. The K-Means Clustering Algorithm

K-Means is a **centroid-based, partitioning** clustering algorithm.

**Goal:** partition $n$ data points into $k$ clusters such that:

- Each point belongs to the cluster with the **nearest centroid** (mean).
- The **within-cluster variance** (compactness) is minimized.

### Algorithm

**Input:** dataset $X = \{x_1, x_2, \dots, x_n\}$ and number of clusters $k$.

1. **Initialization:** randomly choose $k$ points from the data as initial centroids (or use **K-Means++** for smarter initialization).
2. **Assignment step (E-step / Expectation):** for each point, compute distance to all centroids (commonly Euclidean) and assign it to the cluster with the **nearest centroid**.
3. **Update step (M-step / Maximization):** for each cluster, compute the **new centroid = mean of all assigned points**.
4. **Iteration:** repeat steps 2-3 until centroids stop changing significantly, OR assignments no longer change.

### Key Properties

- **Hard clustering:** each point belongs to exactly one cluster.
- **Objective function:** K-Means minimizes the **Within-Cluster Sum of Squares (WCSS)**:
$$
\text{WCSS} = \sum_{j=1}^{k} \sum_{x_i \in C_j} \|x_i - \mu_j\|^2
$$
where $\mu_j$ is the centroid of cluster $C_j$.
- **Assumption:** clusters are spherical and of similar size.
- **Limitations:**
  - Sensitive to initial centroids.
  - May converge to **local optima**.
  - Struggles with non-spherical clusters or clusters of very different sizes/densities.

## 6. Choosing the Number of Clusters $k$

### 6.1 Domain Knowledge
Use prior understanding of the problem to set $k$.

### 6.2 The Elbow Method
1. Run K-Means with $k = 1, 2, \dots, N$.
2. Compute WCSS (inertia) for each $k$.
3. Plot $k$ vs. WCSS.

**Interpretation:** WCSS decreases as $k$ increases. Look for an **"elbow"** — a sharp bend where improvements flatten. That $k$ is often a good choice.

### 6.3 Silhouette Analysis
Evaluates **cohesion vs. separation**. For each point:

- $a$: mean distance to points in the **same** cluster.
- $b$: mean distance to points in the **nearest other** cluster.

**Silhouette score:**

$$
s = \frac{b - a}{\max(a, b)}, \qquad s \in [-1, 1]
$$

- $+1$: well-clustered.
- $0$: on the boundary between clusters.
- $-1$: wrongly clustered.

**Procedure:** compute the average silhouette score for each $k$; choose the $k$ with the **highest** average silhouette score.

## 7. K-Means++ Initialization

1. Choose the first centroid randomly from the data points.
2. For each data point $x$ not chosen as a centroid, compute $D(x)$ = distance to the nearest already-chosen centroid.
3. Select the next centroid from remaining points with probability **proportional to $D(x)^2$** — points farthest from existing centroids are more likely to be chosen.

This spreads out the initial centroids, reducing the chance of poor local optima.

---

## 8. Expectation-Maximization (EM) and its Connection to K-Means

**EM** is a general algorithm for finding **maximum likelihood estimates** in statistical models with **latent (hidden) variables** — works especially for incomplete or hidden-data problems (e.g. unknown cluster labels).

### Gaussian Mixture Model (GMM)

Assumes data comes from a mixture of $k$ Gaussian distributions. Each Gaussian has:

- A **mean vector** $\mu$.
- A **covariance matrix** $\Sigma$.
- A **mixing weight** $\pi$.

**Goal:** learn these parameters.

### General EM Algorithm for GMM

1. **Initialization:** randomly initialize means, covariances, and weights.
2. **E-step (Expectation):** compute the probability that each point belongs to each Gaussian (**soft assignments**) using **Bayes' theorem**.
3. **M-step (Maximization):** update Gaussian parameters:
   - New **mean** = weighted average of points.
   - New **covariance** = weighted variance.
   - New **weight** = fraction of points assigned.
4. **Iteration:** repeat until parameters converge.

### Connection to K-Means

K-Means is a **special case of EM with a GMM**, with:

- **Hard assignments** instead of soft probabilities.
- **Equal priors** for clusters.
- **Identical spherical covariances.**

### K-Means vs EM for GMM

| Step | K-Means | EM for GMM |
|---|---|---|
| **Assignment** | Hard (nearest centroid) | Soft (probabilities of belonging) |
| **Update** | Mean of assigned points | Weighted mean, covariance, weight |
| **Cluster shape** | Spherical only | Arbitrary elliptical |
| **Output** | Cluster labels | Cluster labels + probabilities + params |
| **Speed** | Fast | Slower (more computation) |

---

## Key Takeaways

- **KNN** is lazy and non-parametric: predict by majority vote / mean of the $k$ nearest neighbors; choose $k$ via cross-validation (odd for classification).
- **K-Means** minimizes WCSS via alternating assignment (E) and update (M) steps; choose $k$ via the Elbow method or Silhouette analysis; use K-Means++ to seed centroids.
- **EM + GMM** generalizes K-Means to soft assignments and elliptical clusters; K-Means is the hard, spherical, equal-prior special case.
