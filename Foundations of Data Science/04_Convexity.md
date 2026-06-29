# Convexity

Convexity is the property that makes optimization tractable: for a convex function, any local minimum is a **global** minimum, and first-order (gradient = 0) conditions are sufficient for optimality.

---

## 1. Definition

A function $f$ is **convex** if for any $x_1, x_2 \in \mathbb{R}^n$ and $\lambda \in [0,1]$:

$$
f\big(\lambda x_1 + (1-\lambda) x_2\big) \le \lambda f(x_1) + (1-\lambda) f(x_2)
$$

Geometrically: the line segment (chord) between any two points on the graph lies **above** the graph.

A quadratic form is convex over $\mathbb{R}^n$ and has a global (local) minimum **if the matrix $A$ is positive semidefinite (PSD) or positive definite (PD)**.

---

## 2. Convexity of a Quadratic Form (Proof)

Consider $f(x) = x^T A x$. We test the convexity inequality along the segment between $x_1$ and $x_2$. Write the point on the segment as:

$$
f\big(x_1 + \lambda(x_2 - x_1)\big) = f\big((1-\lambda)x_1 + \lambda x_2\big)
$$

Expanding $f(x_2 + \lambda(x_1 - x_2)) = \big(x_2 + \lambda(x_1 - x_2)\big)^T A \big(x_2 + \lambda(x_1 - x_2)\big)$:

$$
= x_2^T A x_2 + \lambda x_1^T A x_2 + \lambda x_2^T A x_1 - \lambda x_2^T A x_2 + \lambda^2 (x_1 - x_2)^T A (x_1 - x_2)
$$

Regrouping the terms yields exactly:

$$
f\big(\lambda x_1 + (1-\lambda) x_2\big) \le \lambda f(x_1) + (1-\lambda) f(x_2)
$$

provided the leftover quadratic term $\lambda(\lambda-1)(x_1-x_2)^T A (x_1-x_2) \le 0$, which holds when $A$ is PSD (since $\lambda(\lambda-1) \le 0$ on $[0,1]$).

> **Conclusion:** If $A$ is symmetric and positive semidefinite, the quadratic form is convex, and the function admits a global minimum. For $x^T A x = 1$, the set of points is an **ellipsoid** (if $A$ is PD).

---

## 3. Geometric Intuition: When Does a Matrix "Flip" a Vector?

**Scalar analogy.** For a real number $a$:

- If $a > 0$, scalar multiplication does **not** flip the sign/direction of a vector.
- The dot product $a^T b$: if $a > 0$, then the result is on the **same side** as $b$ relative to the origin.
- If $a < 0$, multiplication flips to the **opposite side**; if $a > 0$, **same side**.

**Matrix version.** When does a matrix flip a vector?

- If $A$ is **Positive Definite (PD)**, $A$ does **not** flip $x$ about the origin.
- In other words, $x^T A x > 0$ for all $x \neq 0$.
- **Eigenvalues** cannot be negative; a negative eigenvalue would mean some direction gets flipped.

**Geometric view:** If the angle between $x$ and $Ax$ is **less than $90°$ (acute)**, their dot product $x^T A x$ is positive; if more than $90°$, it is negative.

> **Key Theorem:** A quadratic form $Q(x) = x^T A x$ is convex **if and only if** $A$ is Symmetric Positive (Semi)Definite (SPD/SPSD).

---

## 4. From Univariate to Multivariate Minimization

### 4.1 Univariate Quadratic Function

$$
f(x) = \tfrac12 a x^2 - b x + c
$$

- First derivative: $f'(x) = a x - b$ — optimality at $a x = b$.
- Second derivative: $f''(x) = a$.
- If $a > 0$, $f(x)$ is minimized at $x^{\ast} = b/a$.

### 4.2 Multivariate Analog

$$
f(x) = \tfrac12 x^T A x - b^T x + c
$$

**Claim:** If $A$ is SPD, $f(x)$ is minimized where $A x = b$.

$$
x^{\ast} = \arg\min_x \left[ \tfrac12 x^T A x - b^T x + c \right]
$$

- **First-order condition:** $A x = b$.
- The minimum **exists and is unique** exactly when $A$ is SPD.

**Gradient and Hessian:**

$$
\nabla f(x) = A x - b, \qquad \nabla^2 f(x) = A
$$

So if $A$ is SPD, $f$ is convex. This links the optimization problem directly to matrix definiteness. Solving $A x = b$ is the same as finding the stationary point $x^{\ast}$ (intersection of planes in multivariable calculus).

> **NOTE — "$A$ is SPD" means:** $A = A^T$ (symmetric) **and** $x^T A x > 0$ for all $x \neq 0$ (positive definite).

### 4.3 Proof that $x^{\ast}$ (where $Ax^{\ast} = b$) is the minimizer

Let $x^{\ast}$ be the minimizer and perturb by $\epsilon$:

$$
f(x^{\ast} + \epsilon) = \tfrac12 (x^{\ast} + \epsilon)^T A (x^{\ast} + \epsilon) - b^T(x^{\ast} + \epsilon) + c
$$

$$
= \tfrac12 \big[ x^{\ast T} A x^{\ast} + x^{\ast T} A \epsilon + \epsilon^T A x^{\ast} + \epsilon^T A \epsilon \big] - b^T x^{\ast} - b^T \epsilon + c
$$

At the minimizer, $\nabla f(x^{\ast}) = A x^{\ast} - b = 0 \Rightarrow A x^{\ast} = b$. Plugging in, the cross terms cancel:

$$
x^{\ast T} A \epsilon - b^T \epsilon = (b^T \epsilon) - b^T \epsilon = 0
$$

Therefore:

$$
f(x^{\ast} + \epsilon) = f(x^{\ast}) + \tfrac12 \epsilon^T A \epsilon \;>\; f(x^{\ast})
$$

since $\epsilon^T A \epsilon > 0$ for $\epsilon \neq 0$ (because $A$ is SPD). Hence $x^{\ast}$ is the **unique global minimizer**. $\blacksquare$

---

## 5. Ellipsoids (Level Sets)

If $A$ is diagonal, $D = \mathrm{diag}(\lambda_1, \lambda_2, \dots)$:

$$
x^T A x = \lambda_1 x_1^2 + \lambda_2 x_2^2 + \cdots = 1
$$

- Each variable's axis is **stretched** according to its eigenvalue.
- Positive SPD matrices give **ellipsoids**; if $A = I$, we get a **sphere**.
- The **axes of the ellipsoid align with the eigenvectors** of $A$.
- The **size of each axis** is determined by its eigenvalue — inversely proportional to the **square root** of the eigenvalue ($\text{axis length} \propto 1/\sqrt{\lambda_i}$).
- Because $A = A^T$, the axes are **perpendicular** to each other.

> **NOTE:**
> - If $A$ is SPD, we can write it as $A = P D P^T$ (spectral decomposition).
> - Eigenvalues of $A^2$ are the squares of eigenvalues of $A$.
> - Arrange eigenvalues in increasing order of magnitude from $i = 1 \dots n$ to order axes from longest to shortest.

---

## 6. Projection onto the Null Space

$P$ is a matrix that projects any vector onto the **null space (kernel)** of another matrix $A$.

**Projection properties:**

- $\mathrm{Rank}(P) = \dim \mathrm{Null}(A)$
- $P(I - P) = 0$
- $P^2 = P$ (idempotent)
- On $\mathrm{Null}(A)$, $P = I$.

**Construction.** Let $A$ be a linear transform. The orthogonal projection onto the null space of $A$ is:

$$
\omega = x - A^T (A A^T)^{-1} A x, \qquad A\omega = 0
$$

Therefore the orthogonal projector is:

$$
P = I - A^T (A A^T)^{-1} A
$$

---

## 7. Convex Loss Functions (First-Order Characterization)

Let $f : \mathbb{R}^n \to \mathbb{R}$ be differentiable. Then $f$ is convex **if and only if**, for every $x, y \in \mathbb{R}^n$:

$$
f(y) \ge f(x) + \nabla f(x)^T (y - x)
$$

i.e. the function always lies **above its tangent plane**.

### Proof (from midpoint convexity to the gradient inequality)

If $f$ is convex, for any $x \neq y$: $\ f\!\left(\tfrac12(x+y)\right) \le \tfrac12 f(x) + \tfrac12 f(y)$.

Fix $y = x + h$ and substitute into the convexity definition:

$$
f\!\left(x + \tfrac{h}{2}\right) \le \tfrac12 f(x) + \tfrac12 f(x + h)
$$

Rearranging:

$$
f\!\left(x + \tfrac12 h\right) - f(x) \le \tfrac12 \big(f(x+h) - f(x)\big)
$$

Equivalently:

$$
f(x + h) - f(x) \ge 2\left(f\!\left(x + \tfrac12 h\right) - f(x)\right)
$$

**Recursive choice:** pick the next point as $x + h/2$ to obtain

$$
f(x+h) - f(x) \ge 4\left(f\!\left(x + \tfrac{h}{4}\right) - f(x)\right)
$$

and in general

$$
f(x+h) - f(x) \ge 2^k \left[ f\!\left(x + \tfrac{1}{2^k} h\right) - f(x) \right] \quad \text{for all } k.
$$

As $k \to \infty$, the right side $\to \nabla f(x)^T h$, giving:

$$
f(x+h) - f(x) \ge \nabla f(x)^T h
$$

Setting $h = y - x$:

$$
f(y) \ge f(x) + \nabla f(x)^T (y - x). \qquad \blacksquare
$$

### Converse direction (tangent inequality $\Rightarrow$ convex)

Consider any two points $\omega, z \in \mathbb{R}^n$ and scalar $0 \le \lambda \le 1$. Define $x = \lambda \omega + (1-\lambda) z$. By the first-order (Taylor) inequality:

$$
f(\omega) \ge f(x) + \nabla f(x)^T(\omega - x)
$$
$$
f(z) \ge f(x) + \nabla f(x)^T(z - x)
$$

Multiply the first by $\lambda$, the second by $1 - \lambda$, and add:

$$
\lambda f(\omega) + (1-\lambda) f(z) \ge f(x) + \lambda \nabla f(x)^T(\omega - x) + (1-\lambda)\nabla f(x)^T(z - x)
$$

Now, with $x = \lambda \omega + (1-\lambda) z$:

$$
\omega - x = (1-\lambda)(\omega - z), \qquad z - x = -\lambda(\omega - z)
$$

Substituting:

$$
\lambda \nabla f(x)^T(\omega - x) + (1-\lambda)\nabla f(x)^T(z - x) = \nabla f(x)^T\big[\lambda(\omega - x) + (1-\lambda)(z - x)\big] = 0
$$

(the bracket is zero by direct computation). Therefore:

$$
\lambda f(\omega) + (1-\lambda) f(z) \ge f\big(\lambda \omega + (1-\lambda) z\big),
$$

which is exactly the definition of convexity. $\blacksquare$

---

## 8. Global Minimizer Condition

If $f$ is convex and differentiable, then any point $x^{\ast}$ is a **global minimizer if and only if** $\nabla f(x^{\ast}) = 0$. (For any convex function, stationary points are global minima.)

**Proof:** For any $y$, by the first-order inequality:

$$
f(y) \ge f(x^{\ast}) + \nabla f(x^{\ast})^T (y - x^{\ast}) = f(x^{\ast}) + 0 = f(x^{\ast}). \qquad \blacksquare
$$

---

## 9. Example: log-cosh Loss is Convex

The loss $\mathrm{logcosh}(y - w^T x)$ is **convex in $w$**.

Let $f(w) = \mathrm{logcosh}(y - w^T x)$ and set $z = y - w^T x$. Since $\mathrm{logcosh}(z) = \log\cosh(z)$ has second derivative $\mathrm{sech}^2(z) = 1 - \tanh^2(z) \in (0, 1] \ge 0$, it is convex in $z$. Because $z$ is an affine (linear) function of $w$, and convexity is preserved under affine composition, $f(w)$ is convex in $w$.

---

## Key Takeaways

- Convexity = chord above graph = function above every tangent plane = local min is global min.
- A quadratic form $x^T A x$ is convex iff $A$ is symmetric PSD; strictly convex iff PD.
- For a convex differentiable $f$, $\nabla f(x^{\ast}) = 0$ is **necessary and sufficient** for a global minimum.
- Definiteness of the Hessian is the bridge between linear algebra (eigenvalues) and optimization (convexity, unique minima, ellipsoidal level sets).
