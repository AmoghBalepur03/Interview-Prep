# Foundations of Data Science — Notes

A complete set of notes covering the linear algebra and optimization foundations behind data science and machine learning. Each topic is a self-contained Markdown file with full derivations, proofs, and LaTeX-formatted math.

> **Tip:** These render best in any Markdown viewer with LaTeX support (e.g. VS Code with a math preview extension, Obsidian, or GitHub).

---

## Reading Order

The files are numbered in a logical learning sequence — linear-algebra foundations first, then convexity, then the optimization algorithms that build on them, ending with PCA as a capstone application.

| # | Topic | What it covers |
|---|---|---|
| 01 | [Inner Products and Orthogonality](01_Inner_Products_and_Orthogonality.md) | Inner product axioms, norms, Cauchy–Schwarz, orthonormal bases, Gram–Schmidt, orthogonal complements & projections, best-approximation (least-squares) and polynomial approximation |
| 02 | [Quadratic Forms](02_Quadratic_Forms.md) | $x^T A x$, critical points, positive definiteness in 2D, geometry (bowl/saddle), the Hessian, least squares & normal equations, Cholesky/QR solution |
| 03 | [Positive Definite Matrices](03_Positive_Definite_Matrices.md) | Five equivalent PD conditions, properties, the Cholesky decomposition algorithm ($A = LL^T$ and $LDL^T$) |
| 04 | [Convexity](04_Convexity.md) | Convexity definition & proofs, quadratic-form convexity, geometric "flip" intuition, ellipsoidal level sets, null-space projection, first-order convex-loss characterization, global-minimum condition |
| 05 | [Invariant Subspaces](05_Invariant_Subspaces.md) | Definition, eigenspaces / generalized eigenspaces / cyclic subspaces, block triangularization, link to diagonalizability, flags & upper-triangular form |
| 06 | [Diagonalization](06_Diagonalization.md) | Similarity transforms, diagonalizability conditions, spectral theorem, full Fibonacci derivation with Binet's formula, general linear recurrences |
| 07 | [Gradient Descent](07_Gradient_Descent.md) | Fixed-step GD, step-size bound $\eta \le 2/\lambda_{\max}$, eigenvalue convergence analysis, condition number, generalized convex-Lipschitz $O(1/\sqrt{T})$ bound |
| 08 | [Steepest Descent](08_Steepest_Descent.md) | Exact line-search algorithm, residual orthogonality / zig-zag, convergence conditions, rate vs. condition number |
| 09 | [Optimization Problems](09_Optimization_Problems.md) | $H$-norm constrained optimization, Cholesky reshaping of geometry, $H$-orthogonal projector construction |
| 10 | [Principal Components Analysis](10_PCA.md) | Variance maximization = residual minimization duality, covariance eigenvectors as PCs, projection operator, $R^2$ variance explained, worked examples |

---

## Conceptual Map

```
Inner Products / Orthogonality  ──►  Projections, Least Squares
            │
            ▼
   Quadratic Forms  ◄──►  Positive Definite Matrices  ◄──►  Convexity
            │                        │                         │
            ▼                        ▼                         ▼
   Invariant Subspaces  ──►  Diagonalization (spectral theorem)
            │
            ▼
   Gradient Descent  ──►  Steepest Descent  ──►  Constrained Optimization
            │
            ▼
              Principal Components Analysis (PCA)
```

---

## Recurring Themes

- **Symmetric Positive Definite (SPD) matrices** are everywhere: they guarantee convexity, unique minima, valid inner products, and stable factorizations.
- **Eigenvalues** drive everything — convergence speed (condition number), ellipsoid shapes, PCA variance, and recurrence growth rates.
- **Projection** is the unifying geometric operation behind least squares, best approximation, constrained optimization, and PCA.
