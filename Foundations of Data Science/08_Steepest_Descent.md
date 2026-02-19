# Steepest Descent

Steepest descent is gradient descent with an **optimal step size chosen at every iteration** by exact line search (unlike plain gradient descent, which uses a fixed learning rate). At each step it moves along the negative gradient as far as the function keeps decreasing.

---

## 1. Algorithm

For minimizing $f(x) = \tfrac12 x^T A x - b^T x$ (with $A$ SPD), each iteration does:

1. **Compute the residual / negative gradient (search direction):**
$$
r^{(k)} = b - A x^{(k)} = -\nabla f(x^{(k)})
$$

2. **Exact line search** — choose the step size $\alpha_k$ that minimizes $f$ along the ray $x^{(k)} + \alpha\, r^{(k)}$:
$$
\alpha_k = \arg\min_{\alpha} f\big(x^{(k)} + \alpha\, r^{(k)}\big)
$$
For the quadratic above, this has a **closed form**:
$$
\alpha_k = \frac{r^{(k)T} r^{(k)}}{r^{(k)T} A\, r^{(k)}}
$$

3. **Update:**
$$
x^{(k+1)} = x^{(k)} + \alpha_k\, r^{(k)}
$$

4. **Repeat** until $\|r^{(k)}\|$ is below a tolerance.

> **Orthogonality of successive directions:** with exact line search, consecutive residuals are orthogonal, $r^{(k+1)T} r^{(k)} = 0$. This is what produces the characteristic **zig-zag** path of steepest descent in ill-conditioned (elongated) bowls.

### General (non-quadratic) form

For a general differentiable $f$:

1. Direction: $d^{(k)} = -\nabla f(x^{(k)})$.
2. Step: $\alpha_k = \arg\min_\alpha f(x^{(k)} + \alpha d^{(k)})$ (via exact line search, or backtracking/Armijo if exact is unavailable).
3. Update: $x^{(k+1)} = x^{(k)} + \alpha_k d^{(k)}$.

---

## 2. Will Steepest Descent Always Converge?

Steepest descent (gradient descent) is guaranteed to converge under these conditions:

- The objective function $f$ must be **convex and continuously differentiable** on an open domain.
- The gradient $\nabla f$ should be **Lipschitz continuous** — there exists a constant $L > 0$ such that for all $x, y$:
$$
\|\nabla f(x) - \nabla f(y)\| \le L\|x - y\|
$$
  The slope cannot change faster than a rate proportional to $\beta / L$ times the change in input.
- If $f$ is twice differentiable, **$\beta$-smoothness** is equivalent to the Hessian having eigenvalues **at most $\beta$** everywhere:
$$
0 \preceq \nabla^2 f(x) \preceq \beta I
$$
- A suitable **step-size rule** must be used: exact line search, backtracking / Armijo rule, or a fixed sufficiently small constant.
- For **strict (strong) convexity** (the function curves upward everywhere), convergence is not only guaranteed but **linear (geometric rate)**.
- If the Hessian is **positive definite** (rather than just semi-definite), the function is **strongly convex**, and convergence is guaranteed for step sizes within a certain interval.

---

## 3. Convergence Rate and the Condition Number

> **NOTE:**
> - The speed of convergence in gradient/steepest descent is heavily influenced by the **eigenvalues of the Hessian** (for quadratic or twice-differentiable functions).
> - The **ratio of eigenvalues** reflects the "shape" of the problem (ellipse axes), linking directly to the **condition number**.

For the quadratic case with $\kappa = \lambda_{\max}/\lambda_{\min}$, steepest descent with exact line search satisfies the error bound (in the energy / $A$-norm):

$$
\|e^{(k+1)}\|_A \le \left(\frac{\kappa - 1}{\kappa + 1}\right) \|e^{(k)}\|_A
$$

equivalently, for the function-value gap, a per-iteration factor of $\left(\frac{\kappa - 1}{\kappa + 1}\right)^2$.

**Interpretation:**

- $\kappa \approx 1$ (well-conditioned, circular level sets): factor $\approx 0$ — converges in very few steps.
- $\kappa \gg 1$ (ill-conditioned, elongated valley): factor $\approx 1$ — painfully slow zig-zagging across the valley.

This is precisely why preconditioning (reshaping the problem to reduce $\kappa$) and methods like conjugate gradient are introduced.

---

## Key Takeaways

- Steepest descent = negative-gradient direction + **exact line-search** step size each iteration.
- For the quadratic $f$, the optimal step is $\alpha_k = \frac{r^T r}{r^T A r}$, and successive residuals are orthogonal (zig-zag behavior).
- Convergence requires convexity + Lipschitz gradient (smoothness); strong convexity gives a linear rate.
- Convergence speed is governed by the condition number: rate factor $\frac{\kappa - 1}{\kappa + 1}$ per step.
