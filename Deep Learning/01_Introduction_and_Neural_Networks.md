# Introduction and Neural Networks

Deep Learning is the modern reincarnation of neural networks, driven by (a) the availability of large amounts of data, (b) powerful GPUs, (c) software libraries that make deep network implementation easy, and (d) heavy industrial research involvement. This note covers the historical foundations: from the first artificial neuron to the perceptron and its learning rules.

---

## 1. What is a Neural Network?

A neural network is a computational model loosely inspired by the brain — a network of simple processing units (neurons) connected by weighted links. By adjusting the weights, the network learns to map inputs to outputs.

### Benefits of Neural Networks

- **Nonlinearity:** can model nonlinear relationships (a network of nonlinear neurons is itself nonlinear).
- **Input-output mapping:** learns the mapping directly from labeled examples (supervised learning).
- **Adaptivity:** weights can adapt/retrain as the environment changes.
- **Evidential response:** can output not just a decision but a measure of confidence.
- **Parallel processing:** computations are inherently parallel (massively so on GPUs).

### Where Deep Learning fails

DL is not infallible — real-world failures highlight brittleness: sports-camera "ball" detectors that track a referee's bald head, biased jaywalking detectors, abandoned AI drive-throughs, chatbots inventing false policies, etc. Models can fail badly out of distribution or when they latch onto spurious correlations (see also the Husky-vs-Wolf experiment in the explainability note).

---

## 2. McCulloch–Pitts Neuron (1943)

The first mathematical model of a neuron:

- **Activation is binary** (output is 0 or 1).
- Neurons are connected by **fixed weights** (no learning).
- Each connection is either **excitatory** or **inhibitory**.
- Uses a **threshold activation**: fires if the weighted sum exceeds a threshold.
- Operates in a **single time unit** (discrete time steps).

$$
y = \begin{cases} 1 & \text{if } \sum_i w_i x_i \ge \theta \\ 0 & \text{otherwise} \end{cases}
$$

### Logical Computations with Neurons

With appropriate weights and thresholds, a single MP neuron can compute basic logic gates. With $\sum w_i x_i$ and a threshold:

| $X_1, X_2$ | $\sum WX$ | AND | OR |
|---|---|---|---|
| 0, 0 | 0 | 0 | 0 |
| 0, 1 | 1 | 0 | 1 |
| 1, 0 | 1 | 0 | 1 |
| 1, 1 | 2 | 1 | 1 |

(AND fires only at threshold 2; OR fires at threshold 1.)

### XOR and Nonlinearity

**XOR is not linearly separable** — no single line (single neuron) can separate the XOR outputs. This is a fundamental limitation of a single-layer model. XOR can only be computed by **combining logic gates** (multiple layers), e.g. $\text{XOR}(x_1, x_2) = \text{AND}(\text{OR}(x_1, x_2),\ \text{NAND}(x_1, x_2))$. This is the historical motivation for multilayer networks.

### Modelling Perception of Hot and Cold

A classic demonstration of the MP model's use of **discrete time steps**: a known physiological phenomenon is that a brief cold stimulus is perceived as heat, while a sustained cold stimulus is perceived as cold.

- **Cold is perceived** if cold is applied for **two consecutive time steps**.
- **Hot is perceived** if hot is applied, OR if a cold stimulus is applied briefly (1 time step) and then removed.

A small MP network with time-delayed connections reproduces this behavior, showing how temporal logic can be encoded.

---

## 3. Rosenblatt's Perceptron (1958)

The perceptron added **learnable weights** to the MP neuron. An extra **bias** feature is generally added ($x_0 = 1$, always outputs 1) so the threshold becomes a learnable parameter.

$$
y = f\!\left(b + \sum_{i=1}^n w_i x_i\right), \qquad f(z) = \begin{cases} 1 & z \ge 0 \\ 0 & z < 0 \end{cases}
$$

### Bias

The bias $b$ shifts the decision boundary away from the origin. Without a bias, the separating hyperplane $\sum w_i x_i = 0$ must pass through the origin; the bias gives the model the freedom to position the boundary anywhere.

---

## 4. Hebbian Learning

> "Neurons that fire together, wire together."

Hebb's rule strengthens the weight between two neurons when they are active simultaneously. The simplest weight update:

$$
w_i \leftarrow w_i + \Delta w_i, \qquad \Delta w_i = \eta \, x_i \, y
$$

### Hebb's rule simplified (perceptron learning for an OR gate)

The perceptron learning rule updates only the **active** weights using the error $\text{Diff} = (Y - Y')$ between the target $Y$ and the prediction $Y'$:

$$
w_i \leftarrow w_i + (Y - Y')\quad\text{(for active inputs)}
$$

Worked OR-gate trace (start from weights $0, 0, 0$, threshold via bias):

| $X_1, X_2$ | weights $W_1, W_2, b$ | $\sum WX + b$ | $Y'$ | $Y$ | Update |
|---|---|---|---|---|---|
| 0, 0 | 0, 0, 0 | 0 | 1 | 0 | Diff = −1, active = $b$ → $b = -1$ |
| 0, 1 | 0, 0, −1 | −1 | 0 | 1 | Diff = +1, active = $W_2$ → $W_2 = 1$ |
| 1, 0 | 0, 1, −1 | −1 | 0 | 1 | Diff = +1, active = $W_1$ → $W_1 = 1$ |
| 1, 1 | 1, 1, −1 | 1 | 1 | 1 | correct, no update |

Final solution weights: $W_1 = 1,\ W_2 = 1,\ b = -1$ (which correctly computes OR).

---

## 5. The Perceptron Convergence Theorem

**Statement:** If the training data is **linearly separable**, the perceptron learning algorithm is guaranteed to find a separating hyperplane in a **finite** number of steps.

**Algorithm (Perceptron Convergence):**

1. Initialize weights (often zero).
2. For each training sample $(x, y)$, compute prediction $y' = f(w^T x)$.
3. If misclassified, update: $w \leftarrow w + \eta\,(y - y')\,x$.
4. Repeat over the dataset until no misclassifications (convergence).

The proof bounds the number of updates in terms of the margin of separation and the radius of the data — it is finite as long as a separating hyperplane exists.

### Learning-rate parameter $\eta$

The learning rate is a **hyperparameter** controlling how quickly the model updates its parameters. Too large → unstable / overshooting; too small → very slow convergence.

---

## 6. Perceptron Convergence vs. Backpropagation

| Perceptron Convergence (online / sample-by-sample) | Backpropagation (batch) |
|---|---|
| Adjusts weights on a **sample-by-sample** basis | Adjusts weights **after presenting all** training samples |
| Cost = total **instantaneous** error | Cost = **average** error |
| Search is **stochastic** → less likely to get trapped in a local minimum | Accurate cost estimate w.r.t. weights **guarantees convergence** to a local minimum |
| **No parallelization** of learning | **Parallelizable** learning |
| **Much less storage** required | **Demanding** storage requirements |
| **Cannot** extend to multiple layers | **Works** with deep neural networks |

This contrast motivates why deep learning relies on (mini-batch) gradient descent + backpropagation rather than the original perceptron rule.

---

## Key Takeaways

- The MP neuron (1943) is a binary, fixed-weight, threshold unit; it can do AND/OR but a single unit **cannot** do XOR (not linearly separable) — motivating multilayer networks.
- Rosenblatt's perceptron (1958) adds learnable weights + bias; Hebbian / perceptron learning updates active weights by the error.
- The **Perceptron Convergence Theorem** guarantees a solution in finite steps **if** the data is linearly separable.
- Perceptron learning is online and can't go deep; backpropagation (batch gradient descent) is what scales to deep networks.
