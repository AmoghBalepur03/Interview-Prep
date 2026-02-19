# Gradient Descent

Gradient descent is the workhorse first-order optimization algorithm. Unlike steepest descent, the **learning rate is fixed** (not re-optimized at each step). The central questions are: how to choose the step size, and how fast does the error shrink.

---

## 1. The Update Rule

$$
x_{k+1} = x_k - \eta\, \nabla f(x_k)
$$

where $\eta$ is the (fixed) **learning rate** / step size.

For **least squares** $f(x) = \|Ax - y\|^2$:

$$
\nabla f(x) = 2 A^T (A x - y)
$$

---

## 2. How to Decide the Learning Rate

For gradient descent to make progress we need monotone decrease: $f(x_{k+1}) \le f(x_k)$.

Substitute the update $x_{k+1} = x_k - \eta\, \nabla f(x_k)$ and use the **Taylor expansion** (with gradient $g = \nabla f(x_k)$ and Hessian $H$):

$$
f(x_{k+1}) = f(x_k - \eta g) \approx f(x_k) - \eta\, g^T g + \tfrac12 \eta^2\, g^T H g
$$

For descent we require the change to be negative, i.e. the quadratic correction must not overwhelm the linear decrease:

$$
\tfrac12 \eta^2\, g^T H g \le \eta\, g^T g
\quad\Longrightarrow\quad
\eta \le \frac{2\, g^T g}{g^T H g}
$$

Now $g^T H g \le \lambda_{\max}\, (g^T g)$ (Rayleigh quotient bound), so a **safe, $g$-independent** step size is:

$$
\boxed{\ \eta \le \frac{2}{\lambda_{\max}}\ }
$$

---

## 3. Convergence on a Quadratic (Eigenvalue Analysis)

Let $J(\theta) = \tfrac12 \theta^T A \theta$, so $\nabla J(\theta) = A\theta$. The update is:

$$
\theta_{k+1} = \theta_k - \alpha \nabla J(\theta_k) = (I - \alpha A)\theta_k
$$

After $k$ iterations:

$$
\theta_k = (I - \alpha A)^k \theta_0
$$

Since $A$ is symmetric, $A = Q \Lambda Q^T$, so:

$$
\theta_k = Q (I - \alpha \Lambda)^k Q^T \theta_0
\quad\Longrightarrow\quad
(\theta_k)_i = (1 - \alpha \lambda_i)^k (\theta_0)_i
$$

**For convergence**, each mode must decay: $|1 - \alpha \lambda_i| < 1$ for all $i$. This requires:

$$
\alpha \le \frac{2}{\lambda_{\max}}
$$

Each coordinate along an eigenvector is scaled by $(1 - \alpha \lambda_i)$ every iteration — convergence is **mode-by-mode**, slowest along the eigenvector with the most extreme $|1 - \alpha\lambda_i|$.

---

## 4. Least Squares via Gradient Descent

Solve the linear system $A x = b$ (with $A$ symmetric positive definite, SPD). This is equivalent to minimizing:

$$
f(x) = \tfrac12 x^T A x - x^T b
$$

The gradient $\nabla f(x) = A x - b$, so the stationary point is at $A x^* = b$.

- **Energy norm:** $\|x\|_A = \sqrt{\langle x, A x \rangle}$.

### Error and residual recursion

Define the **error** at step $k$: $\ e^{(k)} = x^* - x^{(k)}$, and the **residual**:

$$
r^{(k)} = b - A x^{(k)} = A e^{(k)}
$$

The error evolves as:

$$
e^{(k+1)} = x^* - x^{(k+1)} = x^* - x^{(k)} - \alpha^{(k)} r^{(k)} = e^{(k)} - \alpha^{(k)} A e^{(k)} = (I - \alpha^{(k)} A)\, e^{(k)}
$$

So each coordinate along an eigenvector is scaled by $(1 - \alpha \lambda_i)$.

### Contraction factor

The **contraction factor** (worst shrinkage of error norm per iteration) is:

$$
\rho(\alpha) = \max_i |1 - \alpha \lambda_i|
$$

This is either $|1 - \alpha \lambda_{\min}|$ or $|1 - \alpha \lambda_{\max}|$ (the extremes).

### Optimal fixed step size

We want to minimize the worst-case contraction over all eigendirections. Since $\lambda$ ranges in $[\lambda_{\min}, \lambda_{\max}]$, the optimum balances the two extremes:

$$
|1 - \alpha \lambda_{\min}| = |1 - \alpha \lambda_{\max}|
\quad\Longrightarrow\quad
\alpha^* = \frac{2}{\lambda_{\min} + \lambda_{\max}}
$$

Plugging $\alpha^*$ back into the contraction factor gives:

$$
\rho^* = |1 - \alpha^* \lambda_{\max}| = |1 - \alpha^* \lambda_{\min}| = \frac{\lambda_{\max} - \lambda_{\min}}{\lambda_{\max} + \lambda_{\min}} = \frac{\kappa - 1}{\kappa + 1}
$$

The error norm contracts **geometrically** by this factor each step.

---

## 5. Condition Number

$$
\kappa = \frac{\lambda_{\max}}{\lambda_{\min}}
$$

- The condition number reflects how **sensitive** a system is to changes — errors in data or round-off — and how efficiently algorithms like gradient descent find the minimum.
- **Good conditioning** ($\kappa \approx 1$) occurs when the problem geometry is "isotropic": axes equally scaled, the function equally steep in all directions (circular level sets). GD converges fast.
- It measures how much small **relative errors** in the input (e.g. the right-hand side of a linear system) can cause relative errors in the output (the solution).
- **Ill-conditioned** matrices have large condition numbers (very large, or infinite if singular): small input errors blow up into large output errors — solutions become sensitive or unreliable, and GD zig-zags slowly down a narrow valley.

We want $(I - \eta A)^k$ to be both **(i) stable** ($|1-\eta\lambda_i| < 1$) and **(ii) small** (fast decay).

---

## 6. Generalization of Gradient Descent (Beyond Quadratics)

The assumption so far was that the Hessian is the **same** at all points. This need not be the case for general convex functions. We instead use weaker assumptions.

### Assumptions

1. **Convexity:**
$$
f(\lambda x + (1-\lambda)y) \le \lambda f(x) + (1-\lambda)f(y), \quad \forall\, 0 \le \lambda \le 1
$$
equivalently, $f(x) \ge f(y) + \nabla f(y)^T(x - y)$.

2. **Lipschitz continuity** (bounded gradients):
$$
|f(x) - f(y)| \le L\|x - y\| \quad\Longleftrightarrow\quad \|\nabla f(x)\| \le L
$$

3. **Bounded start:** the starting point is at most distance $R$ from a true solution, $\|x_1 - x^*\| \le R$.

### Convergence proof (sketch of the $O(1/\sqrt{T})$ bound)

Track the distance to the optimum. Using the identity

$$
a^T b = \tfrac12\left(\|a\|^2 + \|b\|^2 - \|a - b\|^2\right)
$$

with $a = x_k - x_{k+1} = \eta \nabla f(x_k)$ and $b = x_k - x^*$, one expands the gradient inequality $f(x_k) - f(x^*) \le \nabla f(x_k)^T (x_k - x^*)$ into:

$$
f(x_k) - f(x^*) \le \frac{\|x_k - x^*\|^2 - \|x_{k+1} - x^*\|^2}{2\eta} + \frac{\eta}{2}\|\nabla f(x_k)\|^2
$$

**Sum** from $k = 1$ to $T$. The distance terms **telescope**, leaving:

$$
\sum_{k=1}^{T} \big(f(x_k) - f(x^*)\big) \le \frac{\|x_1 - x^*\|^2}{2\eta} + \frac{\eta}{2}\sum_{k=1}^T \|\nabla f(x_k)\|^2
$$

Apply the **Lipschitz** bound $\|\nabla f(x_k)\| \le L$ and $\|x_1 - x^*\| \le R$:

$$
\sum_{k=1}^{T} \big(f(x_k) - f(x^*)\big) \le \frac{R^2}{2\eta} + \frac{\eta L^2 T}{2}
$$

Since the **minimum is at most the average**:

$$
\min_{k \le T} f(x_k) - f(x^*) \le \frac{1}{T}\sum_{k=1}^T \big(f(x_k) - f(x^*)\big) \le \frac{R^2}{2\eta T} + \frac{\eta L^2}{2}
$$

**Optimize over $\eta$** (take derivative w.r.t. $\eta$ and set to zero):

$$
-\frac{R^2}{2\eta^2 T} + \frac{L^2}{2} = 0 \quad\Longrightarrow\quad \eta^* = \frac{R}{L\sqrt{T}}
$$

Substituting back gives the classical bound:

$$
\boxed{\ \min_{k \le T} f(x_k) - f(x^*) \le \frac{R L}{\sqrt{T}}\ }
$$

i.e. the famous $O(1/\sqrt{T})$ convergence rate for (sub)gradient descent on general convex Lipschitz functions.

> **The fundamental trade-off:** progress per step comes from balancing step size $\eta$, initial distance $R$, gradient bound $L$, and number of steps $T$. This result is the basis for choosing the optimal fixed step size and deriving the $O(1/\sqrt{T})$ rate.

---

## Key Takeaways

- Fixed-step GD: $x_{k+1} = x_k - \eta \nabla f(x_k)$; stability requires $\eta \le 2/\lambda_{\max}$.
- On quadratics, error decays mode-by-mode by $(1 - \alpha\lambda_i)$; optimal $\alpha^* = 2/(\lambda_{\min}+\lambda_{\max})$ giving rate $\frac{\kappa-1}{\kappa+1}$.
- The condition number $\kappa = \lambda_{\max}/\lambda_{\min}$ governs speed: large $\kappa$ = slow zig-zag.
- For general convex Lipschitz $f$, GD achieves $O(1/\sqrt{T})$ with $\eta^* = R/(L\sqrt{T})$.
