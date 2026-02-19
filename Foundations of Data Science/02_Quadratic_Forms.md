# Quadratic Forms

A **quadratic form** is a homogeneous degree-2 polynomial in several variables, written compactly using a symmetric matrix. They are the foundation for understanding optimization landscapes, convexity, and least squares.

---

## 1. Definition

A quadratic form in two variables is:

$$
F(x, y) = a x^2 + 2b xy + c y^2
$$

This can be written in **matrix notation** as:

$$
F(x, y) = \begin{pmatrix} x & y \end{pmatrix}
\begin{pmatrix} a & b \\ b & c \end{pmatrix}
\begin{pmatrix} x \\ y \end{pmatrix}
= x^T A x
$$

where $A = \begin{pmatrix} a & b \\ b & c \end{pmatrix}$ is **symmetric** ($A = A^T$). In general, for $x \in \mathbb{R}^n$ and symmetric $A \in \mathbb{R}^{n\times n}$, the quadratic form is $F(x) = x^T A x$.

---

## 2. Critical Points

To find stationary points, set the gradient to zero:

$$
\frac{\partial F}{\partial x} = 2ax + 2by = 0
$$

$$
\frac{\partial F}{\partial y} = 2cy + 2bx = 0
$$

For general quadratic forms, the stationary point is at the **origin**, unless there is an additional linear or constant term.

---

## 3. Conditions for Positive Definiteness (2×2 case)

Given $f(x, y) = a x^2 + 2b xy + c y^2$, the matrix $A$ is **positive definite** iff:

- $a > 0$
- $\det(A) = ac - b^2 > 0$

Positive definiteness guarantees:

- **Strict convexity** of the quadratic form.
- A **unique global minimum** at the stationary point.

---

## 4. Geometric Interpretation and Behavior

| Type | Surface shape | Optimum |
|---|---|---|
| **PD** (positive definite) | Bowl-shaped ("soup bowl") | Unique global **minimum** |
| **ND** (negative definite) | Inverted bowl | Unique global **maximum** |
| **Indefinite** | Saddle shape (e.g. $x^2 - y^2$) | Saddle point (neither) |
| **PSD** (positive semidefinite) | Valley with flat directions | Infinitely many minima |

---

## 5. Hessian

The **Hessian matrix** $H$ of a twice-differentiable scalar function $f : \mathbb{R}^n \to \mathbb{R}$ is the square matrix of its second-order partial derivatives:

$$
H = \nabla^2 f = \begin{pmatrix}
\dfrac{\partial^2 f}{\partial x_1^2} & \cdots & \dfrac{\partial^2 f}{\partial x_1 \partial x_n} \\
\vdots & \ddots & \vdots \\
\dfrac{\partial^2 f}{\partial x_n \partial x_1} & \cdots & \dfrac{\partial^2 f}{\partial x_n^2}
\end{pmatrix}
$$

- **Gradient $\nabla f$:** points in the direction of steepest ascent.
- **Hessian $H$:** describes how the gradient changes (i.e. the **curvature**).

For a quadratic form $f(x) = \tfrac12 x^T A x$, the Hessian is exactly $A$. So definiteness of the Hessian determines convexity.

---

## 6. Least Squares

Given an **overdetermined** linear system:

$$
R x = b
$$

where $R$ is $m \times n$ (typically $m > n$), the system may have no exact solution. The **least squares solution** minimizes the sum of squared residuals:

$$
\min_x \|R x - b\|^2
$$

### Derivation

$$
Y = \tfrac12 \|Rx - b\|^2 = \tfrac12 \langle Rx - b,\; Rx - b \rangle
$$

$$
Y' = R^T R x - R^T b \qquad\Longrightarrow\qquad Y'' = R^T R
$$

Since $R^T R$ is **PD** (when $R$ has full column rank), the cost has a **unique solution**.

**Useful facts:**

$$
\operatorname{Null}(R^T R) = \operatorname{Null}(R), \qquad \operatorname{Rank}(R^T R) = \operatorname{Rank}(R)
$$

---

## 7. Matrix Formulation and SPD Structure

To find $x$, form the **normal equations**:

$$
R^T R\, x = R^T b
$$

Here $R^T R$ is always:

- **Symmetric**, and
- **SPD (Symmetric Positive Definite)** if $R$ has full column rank (linearly independent columns).

**Why SPD?** For any nonzero $x$:

$$
x^T R^T R x = (Rx)^T (Rx) = \|Rx\|^2 \ge 0,
$$

and it equals zero only if $Rx = 0$, which forces $x = 0$ (since columns of $R$ are independent). Therefore $R^T R$ is positive definite.

---

## 8. Solution via Cholesky / QR Factorization

Because $R^T R$ is SPD, the normal equations can be solved efficiently using:

- **Cholesky decomposition:** $R^T R = L L^T$
- **QR decomposition** (more numerically robust): $R = Q R'$, with $Q$ orthogonal and $R'$ upper triangular.

The SPD structure ensures **existence, uniqueness, and numerical stability** of the solution.

### Cholesky solution (step by step)

1. Compute $R^T R$ and $R^T b$.
2. Compute Cholesky factorization: $R^T R = L L^T$.
3. Solve $L y = R^T b$ (**forward substitution**).
4. Solve $L^T x = y$ (**back substitution**).
5. $x$ is the least squares solution.

> **Variant:** Cholesky can also be split as $R^T R = (L\sqrt{D})(\sqrt{D}\,L^T)$, where $D$ is the diagonal matrix of pivots (the $LDL^T$ form).

---

## 9. Quadratic Form and Convexity

The least squares cost function

$$
f(x) = \|Rx - b\|^2 = x^T R^T R x - 2 b^T R x + b^T b
$$

is **strictly convex** if $R^T R$ is SPD. Therefore it has a **unique global minimum**.

---

## 10. Geometric Interpretation

Solving least squares finds the **orthogonal projection of $b$** onto the subspace spanned by the columns of $R$. The projection matrix is:

$$
P = R (R^T R)^{-1} R^T
$$

---

## Key Points

- If a matrix has linearly independent columns, $R^T R$ is SPD and invertible.
- The local convexity of the least-squares "loss landscape" is guaranteed by positive definiteness of $R^T R$.
- $f(x) = x^T A x$ with symmetric $A$ unifies quadratic forms, Hessians, and least squares — definiteness of $A$ decides the shape (bowl, saddle, etc.) and whether a unique minimum exists.
