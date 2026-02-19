# Some Optimization Problems

This note works through a constrained linear optimization problem where the constraint is measured in a **weighted ($H$-norm) geometry**, and shows how to build the appropriate orthogonal projector.

---

## 1. The Optimization Problem

$$
\min_x\ c^T x
$$

**Subject to:**

- $A x = a$ (linear equality constraints),
- $\|x_0 - x\|_H \le \delta$ (stay within an $H$-norm ball of radius $\delta$ around a feasible point $x_0$),

where the **$H$-norm** is defined by

$$
\|z\|_H = \big(z^T H z\big)^{1/2}
$$

and $x_0 \in \mathbb{R}^n$ is a known feasible point with $A x_0 = a$. Here $H$ is symmetric positive definite, so it defines a valid inner product $\langle u, v\rangle_H = u^T H v$.

---

## 2. Reshaping the Geometry via $H = L L^T$

Factor the SPD matrix $H$ using its Cholesky decomposition:

$$
H = L L^T
$$

The factor $L$ acts as a **change of coordinates** that turns the $H$-norm into the ordinary Euclidean norm: $\|z\|_H = \|L^T z\|_2$. We exploit this to build a projector that is orthogonal **with respect to the $H$-inner product**.

---

## 3. Constructing the $H$-Orthogonal Projector

Let $Q$ be the **orthogonal projector onto the nullspace of $A L^{-T}$** (i.e. the set of all vectors mapped to $0$ by $A L^{-T}$), in the **usual Euclidean inner product**.

Define:

$$
P = L^{-T} Q\, L^{T}
$$

This matrix projects orthogonally — **with respect to the $H$-inner product** — onto the nullspace of $A$. Concretely:

- $P x$ lies in the **null space of $A$** (so it respects the constraint direction $Ax = a$ when applied to feasible displacements), and
- $x - Px$ is **minimal according to the $H$-norm inner product** (it is the $H$-orthogonal component).

The conjugation by $L^T$ (and its inverse $L^{-T}$) is what converts an ordinary Euclidean projector $Q$ into one that is orthogonal in the reshaped $H$-geometry.

---

## 4. Projection Properties

- $Q^2 = Q$ — the Euclidean projector is **idempotent**.
- $P^2 = P$ — the projection property is preserved under the conjugation:
$$
P^2 = (L^{-T} Q L^T)(L^{-T} Q L^T) = L^{-T} Q\, (L^T L^{-T})\, Q L^T = L^{-T} Q^2 L^T = L^{-T} Q L^T = P.
$$
- Both $Q$ and $P$ are **projection matrices**.

---

## Key Takeaways

- Constraints measured in a weighted $H$-norm call for an $H$-orthogonal projector, not the plain Euclidean one.
- The Cholesky factor $H = L L^T$ provides the coordinate change between Euclidean and $H$-geometry.
- The projector $P = L^{-T} Q L^T$ is idempotent and projects $H$-orthogonally onto $\operatorname{Null}(A)$: $Px$ stays in the null space while $x - Px$ is the minimal-$H$-norm complement.
