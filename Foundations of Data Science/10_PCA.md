# Principal Components Analysis (PCA)

PCA is a dimensionality-reduction technique: it summarizes high-dimensional data by projecting it onto a lower-dimensional subspace that captures the most variance, using the eigenvectors of the covariance matrix.

---

## 1. The Big Picture

We start with $p$-dimensional vectors and want to summarize them by projecting them down into a $q$-dimensional subspace ($q < p$). The summary is the projection of the original vectors onto $q$ directions — the **principal components** — which span the subspace.

**How to find principal components:**

- The **first principal component** is the direction in space along which projections have the **largest variance**.
- The **second principal component** is the direction maximizing variance among all directions **orthogonal to the first**.
- Similarly, the $k$-th component is the variance-maximizing direction orthogonal to the previous $k - 1$ components.

> **Key duality:** the projection with the **smallest average (mean-squared) distance** between the original vectors and their projections turns out to be **equivalent to maximizing the variance** of the projections.

---

## 2. Minimizing Projection Residuals

We have a dataset of $n$ vectors in $p$-dimensional space $x_1, x_2, \dots, x_n$, each $x_i \in \mathbb{R}^p$.

**Assumption:** the data is **centered** — the mean of all vectors is zero, $\frac1n\sum_i x_i = 0$.

We project these vectors onto a **line** (1D subspace) through the origin, defined by a unit direction vector $w$, $\|w\| = 1$.

- **Projection scalar:** $x_i \cdot w$ — the signed distance of the projection from the origin along the line.
- **Projected point:** $(x_i \cdot w)\, w$.

### Mean of the projections

The mean of the projected vectors is $\left(w \cdot \frac1n \sum_i x_i\right) w$. Since $w$ is constant we factor it out, and because the **data mean is zero**, the mean projection is **zero** too.

### Residual (reconstruction error)

The residual is the difference between the original vector and its projection:

$$
\text{Residual} = x_i - (w \cdot x_i)\, w
$$

The residual is zero only if $x_i = c\,w$ for some scalar $c$ (i.e. the point already lies on the line).

### Magnitude of the residual

$$
\|x_i - (w\cdot x_i)w\|^2 = \|x_i\|^2 - (w \cdot x_i)^2
$$

This makes sense because:

- $\|x_i\|^2$ is the **total energy** of the vector.
- $(w \cdot x_i)^2$ is the **energy captured** by the projection.
- The residual is the **lost energy** due to projection.

### Mean Squared Error (MSE)

$$
\text{MSE}(w) = \frac1n \sum_{i=1}^n \|x_i\|^2 - \frac1n \sum_{i=1}^n (w \cdot x_i)^2
$$

**Key observations:**

- The first term $\frac1n \sum_i \|x_i\|^2$ is **independent of $w$** (just the average squared norm of the data).
- The second term $\frac1n \sum_i (w \cdot x_i)^2$ depends on $w$ and represents the **variance of the projections**.

**Optimization goal:** to minimize MSE, we must **maximize**

$$
\frac1n \sum_{i=1}^n (w \cdot x_i)^2
$$

This is the average squared projection length — equivalent to the **variance** of the projected data (since the data is centered). This confirms the duality: minimizing reconstruction error $=$ maximizing variance.

---

## 3. Maximizing Variance

We want to choose a unit vector $w$ to maximize the projected variance $\sigma^2$. We do this with a **Lagrange multiplier** $\lambda$ to enforce $\|w\| = 1$, turning it into an unconstrained problem:

$$
\mathcal{L}(w, \lambda) = w^T V w - \lambda(w^T w - 1)
$$

Setting the gradient to zero gives the **eigenvalue equation**:

$$
V w = \lambda w
$$

where $V = \frac1n X X^T$ is the **covariance matrix** of the (centered) data. So the optimal direction $w$ is an **eigenvector of the covariance matrix**, and the **maximizing vector is the eigenvector associated with the largest eigenvalue $\lambda$**.

### Properties of the covariance matrix $V$ ($p \times p$)

- It has $p$ eigenvectors.
- It is **symmetric** $\Rightarrow$ the eigenvectors are **orthogonal** to one another.
- It is **positive (semi)definite**, $x \cdot V x \ge 0$ for any $x$ $\Rightarrow$ all eigenvalues $\lambda \ge 0$.

### The principal components

> The eigenvectors of $V$ are the **principal components** of the data.

- The **first principal component** (eigenvector with the largest $\lambda$) is the direction of **maximum variance**.
- The **second principal component** (second eigenvector) is the direction **orthogonal to the first** with the most variance. Because it is orthogonal to the first, their projections are **uncorrelated**.

---

## 4. Projection Operator

If we select the top $q$ principal components, we define a projection operator $P_q$ that projects the original data $x$ onto the subspace spanned by the first $q$ eigenvectors.

- **Projected data (images):** $x P_q$.
- **Residuals:** $x - x P_q = x(I - P_q)$ — residuals are small when the data is **almost $q$-dimensional**.

### Variance-explained metric ($R^2$)

$$
R^2 = \frac{\text{variance captured by top } q \text{ PCs}}{\text{total variance}} = \frac{\sum_{i=1}^q \lambda_i}{\sum_{i=1}^p \lambda_i}
$$

| $R^2$ value | Interpretation |
|---|---|
| $R^2 = 1$ | All variance preserved (data is perfectly $q$-dimensional) |
| $R^2 \approx 1$ | Projection captures most of the structure |
| $R^2 \ll 1$ | Significant information lost by projecting to $q$ dimensions |

---

## 5. Recursive Nature of Principal Components

Taking $q = 1$ repeatedly:

- The **first principal component (PC1)** is the direction of maximum variance in the data.
- **Residuals** (after subtracting PC1 projections) are **orthogonal to PC1**.
- The **second principal component (PC2)** is the direction of maximum variance **in these residuals** — making it the next-best orthogonal direction.

**General rule:** the $k$-th PC is the leading component of the residuals after subtracting the first $k - 1$ components.

---

## 6. Examples

### Cars PCA Example

- **PC1** separates fuel-efficient vs. performance cars.
- **PC2** contrasts size (SUVs) vs. power (sports cars).
- Use **loadings** (the eigenvector entries) to name axes based on extreme values (e.g. hybrids vs. gas guzzlers).

### Text Analysis (Latent Semantic Indexing)

- **Takeaway:** PCA groups correlated words (e.g. "farming" + "agriculture") into themes (PCs), enabling **synonym-aware search**.

### NYT Articles PCA

- **PC1** distinguishes art (personal narratives) from music (performances).
- **PC2** reveals sub-themes like art commerce vs. musical theater.
- Inspect **loadings** to decode the semantic axes. Also enables synonym-based similarity search.

---

## Key Takeaways

- PCA finds orthogonal directions of maximum variance; equivalently, minimum reconstruction error.
- The principal components are the **eigenvectors of the covariance matrix** $V = \frac1n X X^T$, ordered by eigenvalue.
- Largest eigenvalue $\to$ direction of most variance; eigenvalues give the variance explained ($R^2$).
- Projecting onto the top $q$ eigenvectors gives the best $q$-dimensional linear summary of the data.
