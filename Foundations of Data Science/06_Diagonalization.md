# Diagonalization

Diagonalization rewrites a matrix in terms of its eigenvalues and eigenvectors, turning hard operations (powers, exponentials, recurrences) into simple element-wise operations on a diagonal matrix.

---

## 1. Similarity Transformations

Two matrices $A$ and $B$ are **similar** if there exists an invertible matrix $P$ such that:

$$
B = P^{-1} A P
$$

**Key property — similar matrices share:**

- Eigenvalues
- Determinant
- Trace
- Rank

(They represent the *same* linear operator in different bases.)

---

## 2. Diagonalizability

A matrix $A$ is **diagonalizable** if it is similar to a diagonal matrix $D$:

$$
A = P D P^{-1}
$$

where $D = \mathrm{diag}(\lambda_1, \lambda_2, \dots, \lambda_n)$ and $P$ contains the **eigenvectors as columns**.

**Condition:** An $n \times n$ matrix $A$ is diagonalizable **if and only if** it has $n$ linearly independent eigenvectors.

### Symmetric matrices ($A = A^T$)

- **Always diagonalizable.**
- Has **orthogonal eigenvectors.**
- Can be written as $A = Q D Q^T$, where $Q$ is **orthogonal** ($Q^T Q = I$). This is the **Spectral Theorem**.

### Diagonalization Algorithm

1. Find eigenvalues by solving $\det(A - \lambda I) = 0$.
2. For each eigenvalue, find the eigenvectors by solving $(A - \lambda I)v = 0$.
3. Form $P$ with eigenvectors as columns and $D$ with eigenvalues on the diagonal.
4. Then $A = P D P^{-1}$.

### Why diagonalization helps with powers

$$
A^n = (P D P^{-1})^n = P D^n P^{-1}, \qquad D^n = \mathrm{diag}(\lambda_1^n, \dots, \lambda_n^n)
$$

Raising a diagonal matrix to a power is just raising each diagonal entry to that power — extremely cheap.

---

## 3. Application: Fibonacci Numbers

The Fibonacci recurrence is:

$$
f_{n+2} = f_{n+1} + f_n, \qquad f_0 = 0,\ f_1 = 1
$$

### Step 1 — Write as a matrix equation

Define the **Fibonacci vector** $v_n = \begin{pmatrix} f_{n+1} \\ f_n \end{pmatrix}$. The recurrence becomes:

$$
\begin{pmatrix} f_{n+2} \\ f_{n+1} \end{pmatrix} =
\begin{pmatrix} 1 & 1 \\ 1 & 0 \end{pmatrix}
\begin{pmatrix} f_{n+1} \\ f_n \end{pmatrix}
\quad\Longrightarrow\quad
v_{n+1} = M v_n, \quad M = \begin{pmatrix} 1 & 1 \\ 1 & 0 \end{pmatrix}
$$

Which implies $v_n = M^n v_0$, with $v_0 = \begin{pmatrix} 1 \\ 0 \end{pmatrix}$.

### Step 2 — Diagonalize $M$

To compute $M^n$, diagonalize $M = B D B^{-1}$, where $D$ holds the eigenvalues and $B$ holds the corresponding eigenvectors as columns.

**Eigenvalues** from $\det(M - \lambda I) = 0$:

$$
\det \begin{pmatrix} 1 - \lambda & 1 \\ 1 & -\lambda \end{pmatrix} = \lambda^2 - \lambda - 1 = 0
$$

$$
\lambda = \frac{1 \pm \sqrt{5}}{2} \quad\Longrightarrow\quad
\varphi = \frac{1 + \sqrt5}{2} \approx 1.618, \qquad
\psi = \frac{1 - \sqrt5}{2} \approx -0.618
$$

($\varphi$ is the **golden ratio**.)

**Eigenvectors:** for each $\lambda$, $(M - \lambda I)v = 0$ gives $v_\lambda = \begin{pmatrix} \lambda \\ 1 \end{pmatrix}$, so

$$
B = \begin{pmatrix} \varphi & \psi \\ 1 & 1 \end{pmatrix}, \qquad D = \begin{pmatrix} \varphi & 0 \\ 0 & \psi \end{pmatrix}
$$

### Step 3 — Compute $M^n$ and extract the closed form

$$
M^n = B D^n B^{-1} = B \begin{pmatrix} \varphi^n & 0 \\ 0 & \psi^n \end{pmatrix} B^{-1}
$$

Carrying out the multiplication and reading off the bottom entry of $v_n = M^n v_0$ gives **Binet's formula**:

$$
\boxed{\,f_n = \frac{\varphi^n - \psi^n}{\sqrt5} = \frac{1}{\sqrt5}\left[\left(\frac{1+\sqrt5}{2}\right)^n - \left(\frac{1-\sqrt5}{2}\right)^n\right]\,}
$$

Since $|\psi| < 1$, the $\psi^n$ term vanishes for large $n$, so $f_n \approx \varphi^n / \sqrt5$ — Fibonacci numbers grow geometrically at rate $\varphi$.

---

## 4. General Steps for Any Linear Recurrence Relation

### Step 1 — Formulate the recurrence as a matrix equation

For a $k$-th order linear recurrence, define a vector $v_n$ containing $k$ consecutive terms. Write the recurrence as

$$
v_{n+1} = M v_n
$$

where $M$ is a $k \times k$ matrix (the **companion matrix**).

### Step 2 — Diagonalize the matrix $M$

- Find the eigenvalues $\lambda$ from $\det(M - \lambda I) = 0$.
- Find eigenvectors for each eigenvalue.
- Form $B$ (eigenvectors as columns) and $D$ (diagonal matrix of eigenvalues).

### Step 3 — Compute $M^n$

$$
M^n = B D^n B^{-1}
$$

### Step 4 — Apply initial conditions

Multiply $M^n$ by the initial vector $v_0$ to obtain the closed-form expression for the $n$-th term:

$$
v_n = M^n v_0
$$

---

## Key Takeaways

- Diagonalization $A = P D P^{-1}$ decouples a matrix into independent eigen-directions.
- Symmetric matrices are always orthogonally diagonalizable: $A = Q D Q^T$ (Spectral Theorem).
- Powers become trivial: $A^n = P D^n P^{-1}$ — the key to solving linear recurrences in closed form.
- The Fibonacci example yields Binet's formula, with growth governed by the golden ratio $\varphi$.
