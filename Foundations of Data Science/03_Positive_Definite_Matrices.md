# Positive Definite Matrices

A **positive definite (PD)** matrix is a symmetric (or Hermitian, in the complex case) matrix $A \in \mathbb{R}^{n \times n}$ that satisfies one of several equivalent conditions. PD matrices play a central role in optimization (unique minima), statistics (covariance matrices), and numerical linear algebra (stable factorizations).

---

## 1. Equivalent Conditions for Positive Definiteness

A symmetric matrix $A$ is positive definite if **any one** (hence all) of the following hold:

1. **Quadratic Form Condition:**
$$
x^T A x > 0 \quad \forall\, x \in \mathbb{R}^n,\ x \neq 0
$$

2. **Eigenvalue Condition:** All eigenvalues of $A$ are **strictly positive**, $\lambda_i > 0$.

3. **Cholesky Decomposition:** $A$ can be factored as
$$
A = L L^T
$$
where $L$ is a **lower triangular** matrix with **positive diagonal** entries.

4. **Principal Minors Condition:** All **leading principal minors** of $A$ are positive.

5. **Pivots Condition:** All pivots (without row exchanges) in Gaussian elimination are positive.

---

## 2. Key Properties

- All PD matrices are **symmetric**.
- All PD matrices are **invertible** (no zero eigenvalues).
- If $A$ is **strictly diagonally dominant** and has positive diagonal entries, then it is positive definite.
- A PD matrix $A$ **defines an inner product:**
$$
\langle x, y \rangle_A = x^T A y
$$
- The **$A$-norm** $\|x\|_A = \sqrt{x^T A x}$ measures distances *after* this reshaping of space (stretching/rotating along eigen-directions).

> **PSD vs PD:** A matrix is positive **semi**definite (PSD) if $x^T A x \ge 0$ for all $x$ (eigenvalues $\ge 0$). PD is the strict version ($> 0$, eigenvalues $> 0$).

---

## 3. Cholesky Decomposition

The **Cholesky decomposition** factors a PD matrix $A$ into a single lower-triangular matrix $L$ (and its transpose):

$$
A = L L^T
$$

It is roughly twice as efficient as LU decomposition and is numerically stable for PD matrices (no pivoting needed).

### Algorithm (computing $L$ from $A$)

Process columns left to right. For $j = 1, 2, \dots, n$:

**Diagonal entry:**

$$
L_{jj} = \sqrt{ A_{jj} - \sum_{k=1}^{j-1} L_{jk}^2 }
$$

**Below-diagonal entries** (for each $i > j$):

$$
L_{ij} = \frac{1}{L_{jj}} \left( A_{ij} - \sum_{k=1}^{j-1} L_{ik} L_{jk} \right)
$$

The term under the square root is guaranteed positive precisely because $A$ is positive definite — this is, in fact, a constructive proof of condition (3).

### $LDL^T$ variant (square-root-free)

To avoid square roots, factor as $A = L D L^T$, where $L$ is unit lower triangular (ones on the diagonal) and $D$ is diagonal:

$$
D_{jj} = A_{jj} - \sum_{k=1}^{j-1} L_{jk}^2 D_{kk}, \qquad
L_{ij} = \frac{1}{D_{jj}} \left( A_{ij} - \sum_{k=1}^{j-1} L_{ik} L_{jk} D_{kk} \right)
$$

The standard Cholesky factor relates by $L_{\text{chol}} = L \sqrt{D}$.

### Solving $Ax = b$ with Cholesky

1. Factor $A = L L^T$.
2. Solve $L y = b$ by **forward substitution**.
3. Solve $L^T x = y$ by **back substitution**.

---

## Key Takeaways

- Positive definiteness can be checked five equivalent ways — quadratic form, eigenvalues, Cholesky, leading minors, or pivots.
- PD $\Rightarrow$ symmetric, invertible, and induces a valid inner product / norm.
- The Cholesky factorization $A = L L^T$ is the workhorse for solving SPD systems efficiently and stably.
