# Sequence Models and Recurrent Neural Networks (RNNs)

Sequence data — text, time series, speech, video frames — has **order that matters**. This note builds up from the sequence-modeling problem and classical Markov/HMM approaches to the Recurrent Neural Network (RNN), its variants (Elman/Jordan), training via Backpropagation Through Time, and evaluation with perplexity.

---

## 1. The Sequence Modeling Problem

The key change of perspective: inputs are now an **ordered list** of feature vectors $x_1, \dots, x_T$, where each $x_t \in \mathbb{R}^d$ is indexed by a **time step** $t \in \mathbb{Z}^+$.

For most sequence models we do **not** require independence or even stationarity of the sequence; we require only that the sequences themselves are sampled from some **fixed underlying distribution over entire sequences**.

### Types of RNN (input/output structure)

| RNN Type | Input | Output | Example Use Case |
|---|---|---|---|
| One-to-One | Single element | Single element | Digit recognition |
| One-to-Many | Single element | Sequence | Image captioning |
| Many-to-One | Sequence | Single element | Sentiment classification |
| Many-to-Many (Aligned) | Sequence | Sequence | Part-of-speech tagging |
| Many-to-Many (Unaligned) | Sequence | Sequence | Machine translation |

- **Fixed target from sequence input:** e.g. sentiment classification of a movie review.
- **Sequence target from fixed input:** e.g. image captioning.
- **Sequence-to-sequence:** predict sequential targets from sequential inputs. Two forms:
  - **Aligned:** input at each step aligns with a corresponding target (e.g. POS tagging).
  - **Unaligned:** no step-for-step correspondence (e.g. machine translation).

RNNs flexibly map all combinations of sequential and fixed-size inputs/outputs; the choice depends on the alignment and structure your task needs.

> **Aside — 3D convolutions:** a 3D conv applies a 3D filter that moves in 3 directions (x, y, z), producing a volumetric output. Useful for event detection in videos and 3D medical images (a CNN-based alternative to RNNs for some spatiotemporal data).

---

## 2. Markov Models and n-grams

The probability of a sequence factorizes via the chain rule:

$$
P(x_1, \dots, x_T) = \prod_{t=1}^{T} P(x_t \mid x_1, \dots, x_{t-1})
$$

A **Markov model** makes the assumption that the future depends only on a limited window of the past:

- **First-order Markov:** $P(x_t \mid x_1, \dots, x_{t-1}) = P(x_t \mid x_{t-1})$.
- **n-gram models:** condition on the previous $n-1$ tokens (bigram, trigram, …). Simple and fast, but cannot capture long-range dependencies and suffer data sparsity for large $n$.

---

## 3. Hidden Markov Models (HMM)

A statistical model describing the probabilistic relationship between a sequence of **observations** and a sequence of **hidden states**. The states are "hidden" because they are not directly observable — only the outputs (observations) they produce are visible. (This is what distinguishes HMMs from plain Markov chains.)

An HMM is defined by:

- Initial state probabilities $\pi$,
- Transition matrix $A$ (state → state),
- Emission matrix $B$ (state → observation).

### Disadvantages of HMMs

- **Restrictive / simplistic:** assume hidden states are discrete and finite, and that observations are conditionally independent given the hidden states (often untrue).
- **Prone to over/underfitting:** require careful choice of the number of hidden states and priors.
- **Sensitive / brittle:** rely on the quality and quantity of observed data; suffer from data sparsity, noise, or missing values.

### The Viterbi Algorithm — Worked Example

Find the **most likely hidden state sequence** for a set of observations using dynamic programming.

**Setup:**
- Hidden states $S = \{\text{Rainy (R)}, \text{Sunny (S)}\}$; observations $O = \{\text{Walk (W)}, \text{Shop (Sh)}, \text{Clean (C)}\}$.
- Initial: $P(R) = 0.6$, $P(S) = 0.4$.
- Transitions: $P(R\to R)=0.7,\ P(R\to S)=0.3,\ P(S\to R)=0.4,\ P(S\to S)=0.6$.
- Emissions: $P(W|R)=0.1, P(Sh|R)=0.4, P(C|R)=0.5$; $P(W|S)=0.6, P(Sh|S)=0.3, P(C|S)=0.1$.
- Observation sequence: $[\text{Sh}, \text{C}, \text{W}]$.

**Step 1 — Initialization (t=1, "Shop"):**
$$
\delta_1(R) = \pi(R)\cdot P(Sh|R) = 0.6 \times 0.4 = 0.24
$$
$$
\delta_1(S) = \pi(S)\cdot P(Sh|S) = 0.4 \times 0.3 = 0.12
$$

**Step 2 — Recursion (t=2, "Clean"):**
$$
\delta_2(R) = \max\{0.24\cdot0.7,\ 0.12\cdot0.4\}\cdot P(C|R) = \max\{0.168, 0.048\}\cdot 0.5 = 0.084
$$
$$
\delta_2(S) = \max\{0.24\cdot0.3,\ 0.12\cdot0.6\}\cdot P(C|S) = \max\{0.072, 0.072\}\cdot 0.1 = 0.0072
$$

**Step 3 — Recursion (t=3, "Walk"):**
$$
\delta_3(R) = \max\{0.084\cdot0.7,\ 0.0072\cdot0.4\}\cdot P(W|R) = 0.0588 \cdot 0.1 = 0.00588
$$
$$
\delta_3(S) = \max\{0.084\cdot0.3,\ 0.0072\cdot0.6\}\cdot P(W|S) = 0.0252 \cdot 0.6 = 0.01512
$$

**Step 4 — Termination & backtracking:** max final probability $= \max(0.00588, 0.01512) = 0.01512$ → last state **Sunny**. Backtracking through the stored backpointers gives previous state **R** (at t=2) and **R** (at t=1).

**Most likely hidden state sequence: Rainy, Rainy, Sunny.** Given the activities (Shop, Clean, Walk), the most probable weather was rainy, rainy, then sunny.

---

## 4. The Recurrent Neural Network (RNN)

An RNN is a neural network for **sequence data** where order matters. Unlike feedforward networks, RNNs have **connections that loop backward**, allowing information to persist ("memory"). The RNN processes input **one time step at a time**, maintaining a **hidden state** that is updated at each step.

The motivation: develop models that maintain a summary $h_t$ of past observations and update $h_t$ alongside the prediction $\hat x_t$. The model estimates $\hat x_t = P(x_t \mid h_t)$ and updates $h_t = g(h_{t-1}, x_{t-1})$. Since $h_t$ is **never observed**, these are called **latent autoregressive models**.

### Hidden State Update

$$
h_t = \phi\big(x_t W_{xh} + h_{t-1} W_{hh} + b_h\big)
$$

- $x_t$ — input at time $t$; $h_{t-1}$ — previous hidden state.
- $W_{xh}, W_{hh}$ — weight matrices for input and recurrent connections.
- $b_h$ — bias; $\phi$ — activation (typically **tanh** or ReLU).

The hidden state $h_t$ is a **summary** of the current input $x_t$ and **all** previous hidden states $h_{t-1}, h_{t-2}, \dots$ — giving the network memory of the past.

### Output

$$
o_t = h_t W_{hq} + b_q
$$

where $W_{hq}$ is the output weight matrix and $b_q$ the output bias. For prediction, $o_t$ is often passed through a **softmax** to give probabilities over the vocabulary (e.g. for language modeling: after processing "...the", predict the most likely next word).

### Summary of equations

| Step | Operation |
|---|---|
| Input | $x_{t-1}, x_t, x_{t+1}, \dots$ |
| Hidden update | $h_t = \phi(x_t W_{xh} + h_{t-1} W_{hh} + b_h)$ |
| Output | $o_t = h_t W_{hq} + b_q$ |

### Why RNNs are powerful

- **Capture temporal dependencies:** predictions depend not just on the current input but on everything before it.
- **Share parameters across time:** the **same** weights are used at every step → efficient for variable-length sequences.

**Typical applications:** language modeling (next-word prediction), speech recognition, time-series forecasting, sequence labeling (POS tagging).

---

## 5. Elman and Jordan Networks

These are the foundational early RNN architectures, differing in **what** feeds the hidden-state update.

### Elman Network

Builds memory using the previous **hidden state**:

$$
h(t) = f_h\big(W_h^\top h(t-1) + W_x^\top x(t)\big), \qquad y(t) = f_o\big(w_o^\top h(t)\big)
$$

Expanding recursively, $h(t)$ depends on $h(t-1)$, which depends on $h(t-2)$, …, so the network incorporates information from **all** past time steps when computing $y(t)$.

### Jordan Network

The key difference: the hidden-state update uses the previous **output** $y(t-1)$ (not the previous hidden state):

$$
h(t) = f_h\big(W_y^\top y(t-1) + W_x^\top x(t)\big)
$$

### Comparison

| Model | Hidden State Update | Context Source |
|---|---|---|
| Elman | $h(t) \leftarrow h(t-1),\ x(t)$ | Hidden state |
| Jordan | $h(t) \leftarrow y(t-1),\ x(t)$ | Output |

Both process sequences and handle temporal dependencies; **Elman** networks (hidden state as memory) are more common in practice, while **Jordan** networks use their own past outputs as memory.

---

## 6. Activation: Tanh vs. Sigmoid

RNNs commonly use **tanh**. Both tanh and sigmoid are S-shaped functions that bound the activations (keeping outputs numerically stable and helping prevent exploding gradients).

$$
\sigma(x) = \frac{1}{1 + e^{-x}} \in (0, 1), \qquad \tanh(x) = \frac{e^x - e^{-x}}{e^x + e^{-x}} \in (-1, 1)
$$

- **Output range & symmetry:** tanh is **zero-centered** (−1 to 1), so negative/zero/positive values are equally represented → often **faster convergence**. Sigmoid (0 to 1) is **not** zero-centered → all-positive activations can bias the next layer and slow learning.
- **Gradients:** tanh's derivative is larger/more peaked than sigmoid's → **stronger gradients**, faster and more stable learning. Both still suffer vanishing gradients far from zero, but tanh's range keeps gradients effective longer.
- **Example** (input 0.3): sigmoid(0.3) ≈ 0.574 (gradient ≈ 0.244); tanh(0.3) ≈ 0.291 (gradient ≈ 0.917). The tanh output is closer to zero and its gradient is much larger — more useful for backprop.

> Rule: if we want strong gradients and big learning steps, prefer **tanh**.

---

## 7. Backpropagation Through Time (BPTT)

Training an RNN unrolls it across time and applies backpropagation to the unrolled graph — **Backpropagation Through Time**. Because inputs at the first time step must pass through a chain of $T$ layers (one per time step) to influence the final output, the gradients can **explode or vanish**.

### Gradient Clipping

The standard fix for exploding gradients in RNNs: **clip** the gradients, forcing the "clipped" gradients to take smaller values (capping the norm at a threshold). This prevents numerical instability at the cost of possibly changing the gradient direction.

### Truncated BPTT

Computing the **full** sum of gradients over all time steps is very slow, and gradients can blow up (subtle changes in initial conditions can affect the outcome a lot). The alternative is to **truncate** the sum after $\tau$ steps — **truncated backpropagation through time**. A consequence: the model focuses primarily on **short-term** influence rather than long-term consequences.

---

## Key Takeaways

- Sequence models exploit **order**; RNN input/output structures range from one-to-one to unaligned many-to-many (machine translation).
- Classical baselines: **Markov / n-grams** (limited history) and **HMMs** (hidden states inferred via the **Viterbi** dynamic-programming algorithm).
- An **RNN** maintains a hidden state $h_t = \phi(x_t W_{xh} + h_{t-1}W_{hh} + b_h)$ — a latent autoregressive memory — and shares weights across time.
- **Elman** uses the previous hidden state as memory; **Jordan** uses the previous output. **tanh** (zero-centered, stronger gradients) is the usual activation.
- Train with **BPTT**; tame exploding gradients via **clipping** and use **truncated BPTT** for efficiency (at the cost of long-range memory) — motivating LSTMs/GRUs next.
