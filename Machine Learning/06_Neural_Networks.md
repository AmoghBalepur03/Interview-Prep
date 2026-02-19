# Neural Networks

A neural network builds a **nonlinear** function $f(X)$ to predict a response $Y$ by transforming input features $X = (X_1, X_2, \dots, X_p)$ through a series of layers. Stacking nonlinear transformations lets the network model complex relationships far beyond linear methods.

---

## 1. Overview

The network consists of:

- **Input layer:** receives the original features.
- **Hidden layers:** each contains units (neurons) that compute nonlinear transformations (activations) of weighted sums of inputs.
- **Output layer:** combines hidden-layer activations to produce predictions.

Neural networks can be seen as **layered compositions of functions**, where the nonlinearity at each neuron enables modeling complex relationships.

---

## 2. Single-Layer (Feed-Forward) Neural Network

A single hidden layer with $K$ units computes:

$$
a_k = g\!\left(w_{k0} + \sum_{j=1}^{p} w_{kj} X_j\right), \qquad k = 1, \dots, K
$$

$$
f(X) = \beta_0 + \sum_{k=1}^{K} \beta_k\, a_k
$$

where $g(\cdot)$ is a nonlinear **activation function**. The full set of parameters includes:

- **Input-to-hidden weights** $W_{kj}$.
- **Hidden bias weights** $w_{k0}$.
- **Hidden-to-output weights** $\beta_k$.
- **Output bias** $\beta_0$.

---

## 3. Activation Functions

### Sigmoid (logistic)

$$
g(z) = \frac{1}{1 + e^{-z}}
$$

- Historically common but suffers from **gradient vanishing**.
- Around 0 the gradient is steep; at the far ends the gradient is too small.
- Its derivative is bounded by at most **0.25**, therefore **no exploding gradients** (but vanishing ones in deep nets).

### ReLU (Rectified Linear Unit)

$$
g(z) = \max(0, z)
$$

- Currently **preferred** for computational efficiency and ability to mitigate gradient vanishing.
- **"Dying ReLU" problem:** if many neurons output 0 (when $z \le 0$), their gradient is also zero → neurons can stop learning if stuck in the zero-gradient regime.
- Can cause **exploding gradients** if many are positive and weights are big.

These nonlinear activations enable each neuron to transform input features flexibly, allowing the network to learn complex functions. (Other common ones: **tanh**, **Leaky ReLU**, **softmax** for output.)

---

## 4. Training Neural Networks

Parameters (weights and biases) are learned by **minimizing a loss function**.

- **Regression** — mean squared error:
$$
\sum_{i=1}^{n} (y_i - f(x_i))^2
$$

- **Classification** — cross-entropy loss (with suitable output activations, e.g. softmax):
$$
-\sum_{i=1}^{m} \sum_{c=1}^{n} y_{ic} \log \hat y_{ic}
$$
where $\hat y$ is the predicted probability of a class, $n$ = number of classes, $m$ = number of samples.

Optimization uses **gradient-based methods** such as stochastic gradient descent (SGD) and its variants.

---

## 5. Deep Neural Networks

### Architecture

A typical multilayer network has:

- **Input layer:** receives raw input features. For image data such as MNIST, this might be pixel grayscale values flattened into a vector.
- **Hidden layers:** one or more layers; each neuron performs a weighted sum followed by a nonlinear activation. These layers build successively more abstract / composite features.
- **Output layer:** produces the final prediction. For classification, often probabilities over classes.

### Computation Within Layers

Consider a general feedforward network with:

- **Input layer** $L_0$ with $p$ features (input vector $X \in \mathbb{R}^p$).
- **Hidden layers** $L_1, L_2, \dots, L_H$ with $K_1, K_2, \dots, K_H$ units respectively.
- **Output layer** $L_{H+1}$ with $K_{H+1}$ units (number of outputs).

For layer $\ell$ with weight matrix $W^{(\ell)}$ and bias $b^{(\ell)}$, the forward computation is:

$$
z^{(\ell)} = W^{(\ell)} a^{(\ell-1)} + b^{(\ell)}, \qquad a^{(\ell)} = g\big(z^{(\ell)}\big)
$$

with $a^{(0)} = X$ and the final output $\hat y = a^{(H+1)}$ (often after a softmax for classification).

---

## 6. Full Training Flow

1. **Forward pass:** data is passed through the network from input to output.
2. **Loss calculation:** a loss function quantifies the difference between predictions and true labels.
3. **Backward pass:** error derivatives (gradients) with respect to each weight and bias are computed by propagating the error backward through the network.
4. **Weight update:** using the gradients, an optimizer (e.g. gradient descent) updates weights and biases to minimize the loss.

---

## 7. Backpropagation

Backpropagation efficiently computes gradients by applying the **chain rule** layer by layer, from the output back to the input. The gradient at layer $\ell$ reuses the gradient computed at layer $\ell + 1$:

$$
\delta^{(L)} = \nabla_{\hat y}\,\text{Loss} \odot g'\big(z^{(L)}\big), \qquad
\delta^{(\ell)} = \big(W^{(\ell+1)T} \delta^{(\ell+1)}\big) \odot g'\big(z^{(\ell)}\big)
$$

$$
\frac{\partial \text{Loss}}{\partial W^{(\ell)}} = \delta^{(\ell)} \, a^{(\ell-1)T}, \qquad
\frac{\partial \text{Loss}}{\partial b^{(\ell)}} = \delta^{(\ell)}
$$

### Practical considerations

- **Activation functions:** backpropagation requires **differentiable** activations — sigmoid, tanh, ReLU (with subgradient at 0), and softmax.
- **Initialization:** training starts with random initial parameters; backpropagation guides the network to better parameters by minimizing loss.
- **Optimization:** SGD, Adam, or RMSprop.
- **Batch processing:** gradients are often computed on **mini-batches** to balance accuracy and computational efficiency.
- **Regularization:** techniques like **dropout** or **weight decay** complement backpropagation to prevent overfitting.

---

## Key Takeaways

- A neural network is a composition of layers, each applying a linear map ($Wx + b$) followed by a nonlinear activation $g$.
- **ReLU** is the default activation (efficient, avoids vanishing gradients, but can "die"); **sigmoid** vanishes but never explodes.
- Train by minimizing a loss (MSE for regression, cross-entropy for classification) with gradient-based optimizers.
- **Backpropagation** = chain rule applied backward, reusing downstream gradients ($\delta$) to efficiently compute all weight/bias gradients; combine with mini-batches, good initialization, and regularization.
