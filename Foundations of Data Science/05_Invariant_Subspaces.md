# Invariant Subspaces

An invariant subspace is a subspace that a linear operator maps into itself. Invariant subspaces are the structural backbone behind triangularization, diagonalization, and the spectral theory of operators.

---

## 1. Definition

An **invariant subspace** $W$ of a linear operator $T : V \to V$ (or matrix $A$) satisfies:

$$
T(W) \subseteq W
$$

Equivalently, for every $w \in W$, $T(w) \in W$ — applying $T$ never takes a vector out of $W$.

---

## 2. Types of Invariant Subspaces

### Trivial Subspaces

$\{0\}$ and $V$ are always invariant under any $T$.

### Eigenspaces

An **eigenspace** is the set of all eigenvectors associated with a particular eigenvalue, plus the zero vector. For eigenvalue $\lambda$:

$$
E_\lambda = \{ v \in V \mid T(v) = \lambda v \} = \mathrm{Null}(T - \lambda I)
$$

Each eigenspace is invariant: if $T(v) = \lambda v$, then $T(v) = \lambda v \in E_\lambda$.

### Generalized Eigenspaces

For eigenvalue $\lambda$, the **generalized eigenspace** is

$$
G_\lambda = \mathrm{Null}\big((T - \lambda I)^k\big) \quad (\text{for } k = \dim V)
$$

which is also invariant under $T$. (These are essential when $T$ is *not* diagonalizable — they give the Jordan form.)

### Cyclic Subspaces

For a vector $v$, the subspace

$$
\mathrm{span}\{v,\ T(v),\ T^2(v),\ \dots\}
$$

is invariant under $T$ (applying $T$ just shifts to the next generator).

---

## 3. Properties of Invariant Subspaces

If $W_1$ and $W_2$ are invariant, then:

- $W_1 + W_2$ is invariant.
- $W_1 \cap W_2$ is invariant.

---

## 4. Matrix Block Triangularization

If $W$ is a $T$-invariant subspace of $V$, we can choose a basis

$$
B = \{ w_1, \dots, w_k, v_{k+1}, \dots, v_n \}
$$

where:

- $\{w_1, \dots, w_k\}$ is a basis for $W$, and
- $\{v_{k+1}, \dots, v_n\}$ extends it to a basis for $V$.

Then the matrix representation of $T$ in this basis has **block upper-triangular** form:

$$
[T]_B = \begin{pmatrix} A & B \\ 0 & C \end{pmatrix}
$$

The zero block in the lower-left appears precisely because $T(w_i) \in W$ — the images of basis vectors of $W$ have no components along $v_{k+1}, \dots, v_n$.

---

## 5. Diagonalizability and Invariant Subspaces

- **Diagonalizability:** $T$ is diagonalizable **iff** $V$ decomposes into a **direct sum of 1-dimensional invariant subspaces** (eigenspaces).
- **Implication:** If $T$ has enough eigenvectors, we can write

$$
V = E_{\lambda_1} \oplus \cdots \oplus E_{\lambda_m}
$$

where $E_{\lambda_i}$ is the eigenspace for eigenvalue $\lambda_i$.

---

## 6. Background: Sums and Direct Sums

- Suppose $U_1, \dots, U_m$ are subspaces of $V$. Then $U_1 + \cdots + U_m$ is the **smallest** subspace of $V$ containing all of $U_1, \dots, U_m$.
- The sum $U_1 + \cdots + U_m$ is called a **direct sum** if each element can be written in **only one way** as $u_1 + \cdots + u_m$ with each $u_j \in U_j$.
- **Criterion:** $U_1 + \cdots + U_m$ is a direct sum **iff** the only way to write $0 = u_1 + \cdots + u_m$ (with $u_j \in U_j$) is by taking each $u_j = 0$.

---

## 7. Proposition: Upper Triangular Matrices and Invariant Flags

Let $T \in \mathcal{L}(V)$ and let $(v_1, \dots, v_n)$ be a basis of $V$. The following are **equivalent**:

1. The matrix of $T$ with respect to $(v_1, \dots, v_n)$ is **upper triangular**.
2. $T v_k \in \mathrm{span}(v_1, \dots, v_k)$ for each $k = 1, \dots, n$.
3. $\mathrm{span}(v_1, \dots, v_k)$ is **invariant** under $T$ for each $k = 1, \dots, n$.

This chain of nested invariant subspaces $\mathrm{span}(v_1) \subset \mathrm{span}(v_1, v_2) \subset \cdots \subset V$ is called a **flag**, and upper-triangularity is exactly the statement that $T$ preserves this flag.

---

## Key Takeaways

- An invariant subspace is "closed" under the operator: $T(W) \subseteq W$.
- Eigenspaces, generalized eigenspaces, and cyclic subspaces are the canonical examples.
- Invariant subspaces let us block-triangularize (and, with enough eigenvectors, diagonalize) the operator.
- Upper-triangular form $\iff$ existence of a full flag of invariant subspaces.
