# Backpropagation

Backpropagation is the algorithm that makes deep learning possible: it efficiently computes the gradient of the loss with respect to **every** weight in the network by applying the **chain rule** backward, layer by layer. At its core, "backpropagation of errors is basically just gradient descent."

---

## 1. The Delta Rule (from SSE)

Consider a perceptron performing **regression** with the squared-error loss:

$$
E = \tfrac12 \big(y(n) - t(n)\big)^2
$$

where $y(n)$ is the network output and $t(n)$ the target. To find how $E$ changes with weight $w_i$, the chain rule splits the problem into two pieces — how $E$ changes with the output $y$, and how $y$ changes with $w_i$:

$$
\frac{\partial E}{\partial w_i} = \frac{\partial E}{\partial y(n)} \cdot \frac{\partial y(n)}{\partial w_i}
$$

For a linear unit $y = \sum_i w_i x_i$, we have $\frac{\partial y}{\partial w_i} = x_i$ and $\frac{\partial E}{\partial y} = (y - t)$, giving the **delta rule**:

$$
\frac{\partial E}{\partial w_i} = (y - t)\, x_i \qquad\Longrightarrow\qquad w_i \leftarrow w_i - \eta\,(y - t)\,x_i
$$

This is exactly the perceptron convergence update derived from the SSE.

---

## 2. The Sigmoid Perceptron

When the neuron has a **sigmoid** activation $y = \sigma(z)$ with $z = \sum_i w_i x_i + b$, we add one more chain-rule factor for the activation:

$$
\frac{\partial E}{\partial w_i} = \underbrace{\frac{\partial E}{\partial y}}_{(y - t)} \cdot \underbrace{\frac{\partial y}{\partial z}}_{\sigma'(z)} \cdot \underbrace{\frac{\partial z}{\partial w_i}}_{x_i}
$$

The key derivative of the sigmoid is:

$$
\sigma'(z) = \sigma(z)\big(1 - \sigma(z)\big) = y(1 - y)
$$

So the weight update for a sigmoid perceptron is:

$$
w_i \leftarrow w_i - \eta\,(y - t)\, y(1 - y)\, x_i
$$

> Note the $y(1-y)$ factor is at most $0.25$ and approaches $0$ when the neuron saturates ($y \to 0$ or $1$). This is the seed of the **vanishing gradient** problem in deep sigmoid networks.

---

## 3. Backpropagation Through an MLP

Backpropagation is "the same thing but applied more than once to backpropagate the errors through the layers." We define an **error signal** (delta) at each layer and propagate it backward.

### Notation

For a network with layers $\ell = 1, \dots, L$:

$$
z^{(\ell)} = W^{(\ell)} a^{(\ell-1)} + b^{(\ell)}, \qquad a^{(\ell)} = g\big(z^{(\ell)}\big)
$$

with $a^{(0)} = x$ (input) and $a^{(L)} = \hat y$ (output).

### Step 1 — Output layer error

$$
\delta^{(L)} = \frac{\partial E}{\partial z^{(L)}} = \nabla_{a^{(L)}} E \;\odot\; g'\big(z^{(L)}\big)
$$

(For sigmoid output + SSE this is $(\hat y - t)\odot \hat y \odot (1 - \hat y)$; for softmax + cross-entropy it simplifies beautifully to $\delta^{(L)} = \hat y - t$.)

### Step 2 — Backpropagate the error

The error at layer $\ell$ is obtained from the error at layer $\ell + 1$:

$$
\delta^{(\ell)} = \Big( W^{(\ell+1)T} \delta^{(\ell+1)} \Big) \odot g'\big(z^{(\ell)}\big)
$$

This is the heart of backprop — the downstream error is "pulled back" through the transpose of the weights and modulated by the local activation derivative.

### Step 3 — Gradients for the update

$$
\frac{\partial E}{\partial W^{(\ell)}} = \delta^{(\ell)} \, a^{(\ell-1)T}, \qquad \frac{\partial E}{\partial b^{(\ell)}} = \delta^{(\ell)}
$$

### Step 4 — Update (gradient descent)

$$
W^{(\ell)} \leftarrow W^{(\ell)} - \eta\, \frac{\partial E}{\partial W^{(\ell)}}, \qquad b^{(\ell)} \leftarrow b^{(\ell)} - \eta\, \frac{\partial E}{\partial b^{(\ell)}}
$$

---

## 4. The Full Training Loop

1. **Forward pass:** propagate the input through the network to compute all $z^{(\ell)}, a^{(\ell)}$ and the output $\hat y$.
2. **Loss:** compute $E(\hat y, t)$.
3. **Backward pass:** compute $\delta^{(L)}$, then recursively $\delta^{(\ell)}$ down to layer 1, accumulating weight/bias gradients.
4. **Update:** adjust all parameters by gradient descent (or a fancier optimizer).
5. Repeat over mini-batches and epochs.

### Why it's efficient

Backprop computes **all** gradients in a single backward pass that costs about the same as the forward pass, by **reusing** the downstream $\delta$ values rather than recomputing derivatives from scratch for each weight. This is dynamic programming on the computational graph.

---

## Key Takeaways

- Backprop = repeated application of the **chain rule** backward through the network; it's just gradient descent with an efficient gradient computation.
- The **delta rule** $(y - t)x$ is the single-layer special case; sigmoid units add a $\sigma'(z) = y(1-y)$ factor.
- The recursion $\delta^{(\ell)} = (W^{(\ell+1)T}\delta^{(\ell+1)}) \odot g'(z^{(\ell)})$ propagates error; gradients are $\delta^{(\ell)} a^{(\ell-1)T}$.
- Softmax + cross-entropy gives the clean output error $\delta^{(L)} = \hat y - t$.
- The repeated $g'$ factors are exactly why very deep networks suffer vanishing/exploding gradients (next note).
