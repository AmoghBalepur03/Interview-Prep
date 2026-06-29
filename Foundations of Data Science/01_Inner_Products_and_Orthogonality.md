# Inner Products and Orthogonality

An **inner product** is a mathematical operation that generalizes the notion of the dot product in Euclidean space to more abstract vector spaces. It provides a way to define geometric concepts such as **length**, **angle**, and **orthogonality** in vector spaces beyond $\mathbb{R}^n$.

---

## 1. Properties (Axioms of an Inner Product)

An inner product $\langle \cdot, \cdot \rangle$ on a vector space $V$ over a field $F$ (real or complex) satisfies:

| Property | Statement |
|---|---|
| **Conjugate Symmetry** | $\langle u, v \rangle = \overline{\langle v, u \rangle}$ (for real spaces this is just $\langle u,v\rangle = \langle v,u\rangle$) |
| **Linearity in the First Argument** | $\langle cu + v, w \rangle = c\langle u, w \rangle + \langle v, w \rangle$ |
| **Positive Definiteness** | $\langle v, v \rangle \ge 0$, and $\langle v, v \rangle = 0 \iff v = 0$ |

### Derived concepts

- **Norm:** $\|v\| = \sqrt{\langle v, v \rangle}$
- **Orthogonality:** $u \perp v \iff \langle u, v \rangle = 0$
- **Cauchy–Schwarz (CS) inequality:** $|\langle u, v \rangle| \le \|u\| \, \|v\|$
- **Triangle inequality:** $\|u + v\| \le \|u\| + \|v\|$
- **Parallelogram law:** $\|u + v\|^2 + \|u - v\|^2 = 2\left(\|u\|^2 + \|v\|^2\right)$

### Function inner product

For functions $f, g \in C[a,b]$ (continuous functions on $[a,b]$), a natural inner product is:

$$
\langle f, g \rangle = \int_a^b f(x)\, g(x)\, dx
$$

This turns the space of continuous functions into an inner product space, so all the geometric notions above (length, angle, orthogonality, projection) apply to functions too.

---

## 2. Orthonormal Basis (ONB)

A set of vectors $\{e_1, \dots, e_m\}$ is an **Orthonormal Basis (ONB)** if:

- $\{e_1, \dots, e_m\}$ is **linearly independent (L.I.)**, and
- $\langle e_i, e_j \rangle = \begin{cases} 0 & i \neq j \\ 1 & i = j \end{cases}$ — i.e. $\|e_i\| = 1 \ \forall i = 1, \dots, m$ (orthonormal).

### Proposition 1 (Pythagoras for orthonormal lists)

If $\{e_1, \dots, e_m\}$ is an orthonormal list of vectors in $V$, then for all scalars $a_1, \dots, a_m \in F$:

$$
\|a_1 e_1 + \cdots + a_m e_m\|^2 = |a_1|^2 + \cdots + |a_m|^2
$$

### Proposition 2 (Orthonormal $\Rightarrow$ Linearly Independent)

Every orthonormal list of vectors is L.I.

**Proof:** Suppose $a_1 e_1 + \cdots + a_m e_m = 0$. By Prop 1,

$$
|a_1|^2 + \cdots + |a_m|^2 = \|0\|^2 = 0 \implies a_1 = \cdots = a_m = 0.
$$

Therefore the list is linearly independent. $\blacksquare$

---

## 3. Gram–Schmidt Process

> **Key result:** Every orthonormal list of vectors in $V$ can be extended to an orthonormal basis of $V$.

**Gram–Schmidt theorem:** If $(v_1, \dots, v_m)$ is a linearly independent list of vectors in $V$, then there exists an **orthonormal** list $(e_1, \dots, e_m)$ such that

$$
\mathrm{span}(v_1, \dots, v_j) = \mathrm{span}(e_1, \dots, e_j) \quad \text{for each } j = 1, \dots, m.
$$

### The algorithm

Start with $e_1 = \dfrac{v_1}{\|v_1\|}$. Then for $j = 2, \dots, m$:

1. **Subtract projections** onto the already-built orthonormal vectors:

$$
u_j = v_j - \sum_{i=1}^{j-1} \langle v_j, e_i \rangle \, e_i
$$

2. **Normalize:**

$$
e_j = \frac{u_j}{\|u_j\|}
$$

This guarantees $e_j \perp e_i$ for all $i < j$, and the spans are preserved at every step.

---

## 4. Minimization Problems — Orthogonal Complement and Projection

### 4.1 Orthogonal Complement ($U^\perp$)

For any subset $U \subseteq V$ (where $V$ is an inner product space):

$$
U^\perp = \{ v \in V \mid \langle v, u \rangle = 0 \text{ for all } u \in U \}
$$

$U^\perp$ is **always a subspace** of $V$.

**Key facts:**

- $V^\perp = \{0\}$ and $\{0\}^\perp = V$.
- If $U_1 \subseteq U_2$, then $U_1^\perp \supseteq U_2^\perp$ (order-reversing).

### 4.2 Direct Sum Decomposition

**Theorem:** If $U$ is a subspace of $V$, then

$$
V = U \oplus U^\perp
$$

Every $v \in V$ can be written **uniquely** as $v = u + w$, with $u \in U$ and $w \in U^\perp$.

### 4.3 Orthogonal Projection ($P_U$)

For $v \in V$, $P_U v$ is the unique $u \in U$ given by the decomposition $v = u + w$.

**Properties:**

- $\mathrm{range}(P_U) = U$
- $\mathrm{null}(P_U) = U^\perp$
- $P_U^2 = P_U$ (idempotent)
- $\|P_U v\| \le \|v\|$

**Formula:** If $(e_1, \dots, e_m)$ is an orthonormal basis of $U$:

$$
P_U v = \langle v, e_1 \rangle e_1 + \cdots + \langle v, e_m \rangle e_m
$$

### 4.4 Minimization Problem (Best Approximation)

For a subspace $U$ and $v \in V$, $P_U v$ is the **closest point in $U$ to $v$**:

$$
\|v - P_U v\| \le \|v - u\| \quad \text{for all } u \in U
$$

with **equality only when** $u = P_U v$.

**Proof (via Pythagorean theorem):** Write $v - u = \underbrace{(v - P_U v)}_{\in\, U^\perp} + \underbrace{(P_U v - u)}_{\in\, U}$. Since these two pieces are orthogonal,

$$
\|v - u\|^2 = \|v - P_U v\|^2 + \|P_U v - u\|^2 \ge \|v - P_U v\|^2.
$$

The right term vanishes iff $u = P_U v$. $\blacksquare$

---

## 5. Application: Polynomial Approximation

**Problem:** Approximate $\sin x$ on $[-\pi, \pi]$ by a degree-5 polynomial $u(x)$, minimizing

$$
\int_{-\pi}^{\pi} |\sin x - u(x)|^2 \, dx.
$$

**Setup:** Use $C[-\pi, \pi]$ as an inner product space with $\langle f, g \rangle = \int_{-\pi}^{\pi} f(x) g(x)\, dx$, and let $U = \mathrm{span}\{1, x, x^2, x^3, x^4, x^5\}$.

**Solution:** Compute $P_U(\sin x)$ — the orthogonal projection of $\sin x$ onto $U$ — using an orthonormal basis of $U$ obtained via Gram–Schmidt (these are scaled Legendre polynomials on $[-\pi,\pi]$).

**Result:** This least-squares polynomial **outperforms the Taylor series** approximation for $|x| > 2$, because Taylor matches derivatives at a single point ($x=0$) while the projection minimizes error across the whole interval.

---

## Key Takeaways

- An inner product gives any vector space a notion of length, angle, and orthogonality.
- Orthonormal bases make computations trivial (Pythagoras + projection formula).
- Gram–Schmidt turns any independent list into an orthonormal one with the same spans.
- $V = U \oplus U^\perp$ underlies all least-squares / best-approximation problems: the closest point in a subspace is the orthogonal projection.
