# Regularization and Optimization

Two families of techniques make deep training work: **regularization** (lowering model complexity to prevent overfitting) and **optimization** (smarter gradient-descent variants that converge faster and more stably).

---

# Part I — Regularization

Regularization refers to a set of techniques that lower the complexity of a neural network during training, and thus prevent overfitting.

## 1. L2 Regularization (Ridge / Weight Decay)

The regularization term is the **sum of squared weights**:

$$
\text{RegTerm} = \frac{\alpha}{2}\|W\|_2^2 = \frac{\alpha}{2}\sum_j w_j^2
$$

where $\alpha$ is the regularization strength.

**Effects:**

- Encourages weights to become **small but not exactly zero**.
- Spreads the model's influence across **many** input features rather than relying heavily on a few.
- Stabilizes training and reduces variance without sacrificing too much bias.

**Weight update rule** — with learning rate $\epsilon$ and loss gradient $\nabla L$:

$$
W_{\text{new}} = (1 - \epsilon\alpha)\,W_{\text{old}} - \epsilon\,\nabla L
$$

Weights are multiplied by a factor $(1 - \epsilon\alpha) < 1$ (decay) **before** the gradient step — hence the name **weight decay**. Independent of the loss gradient, we shrink the weights a little each step, preventing them from growing large.

## 2. L1 Regularization (Lasso)

The regularization term is the **sum of absolute values** of weights:

$$
\text{RegTerm} = \alpha\|W\|_1 = \alpha\sum_j |w_j|
$$

**Effects:**

- Encourages **sparsity** — pushes many weights **exactly to zero**.
- This performs **feature selection**, as features with zero weights are effectively ignored.
- Useful for a simpler, more interpretable model with fewer active features.

**Why L1 gives sparsity but L2 does not:** for L2, the penalty curve becomes flat near zero (gradient $\to 0$), so weights decrease but rarely reach zero. For L1, the gradient is constant ($\pm\alpha$) right up to zero, so weights are forced **all the way** to zero.

## 3. L1 vs L2 Summary

| Aspect | L2 Regularization | L1 Regularization |
|---|---|---|
| Penalty | Sum of squares of weights | Sum of absolute weights |
| Effect on weights | Small but not zero | Many become exactly zero (sparse) |
| Feature selection | No | Yes |
| Gradient behavior | Smooth, differentiable everywhere | Non-smooth at zero (drives sparsity) |
| Use case | Reduce overfitting without feature elimination | Feature selection + sparsity |

## 4. Choosing $\alpha$

Strike the right balance between low complexity and accuracy:

- $\alpha$ **too high** → model too simple → **underfitting** (won't learn enough to make useful predictions).
- $\alpha$ **too low** → model too complex → **overfitting** (memorizes training quirks, fails to generalize).

## 5. Dropout

A powerful and unique regularization technique.

**How it works:** during training, each neuron (including input neurons) has probability $P$ of being temporarily **dropped out** (set to zero) on each forward/backward pass.

**Effect:** prevents the network from becoming overly reliant on any single neuron, forcing it to learn **redundant, robust** representations. It is like training a large **ensemble** of different "thinned" networks simultaneously and averaging them. A simpler network per step means less complexity and reduced overfitting.

**At test time:** dropout is turned **off**, and outputs are scaled by $(1 - P)$ to account for all neurons now being active (so expected activations match training).

---

# Part II — Optimization

Smarter optimizers improve on plain SGD by adapting the step using gradient history.

## 6. Momentum

**Intuition — a ball rolling down a hill:** it accelerates in directions where the slope (gradient) consistently points the same way, and dampens oscillations in directions where the slope rapidly changes (zig-zagging).

**Mechanism** — maintain a **velocity** vector $m$:

$$
m = \beta m - \eta\,\nabla L, \qquad \theta = \theta + m
$$

- $\beta \in [0, 1)$ is the momentum hyperparameter (how much previous velocity is retained).
- $\eta$ is the learning rate.

**Effect:** velocity **accumulates** gradients over time, so in directions with consistently aligned gradients the update grows larger (faster convergence); in directions where the gradient flips sign frequently, accumulated momentum cancels and reduces oscillations. Especially useful in **ravines** / ill-conditioned loss surfaces (steep in one direction, gentle in another).

## 7. RMSProp (Adaptive Learning Rates)

RMSProp keeps a running average of **squared** gradients (the second moment) and scales each parameter's step by its inverse square root:

$$
s_t = \rho\, s_{t-1} + (1 - \rho)\,(\nabla L)^2, \qquad \theta \leftarrow \theta - \frac{\eta}{\sqrt{s_t} + \varepsilon}\,\nabla L
$$

**Interpretation:** parameters with **large, noisy** gradients have larger second-moment estimates $s_t$ → **smaller** effective learning rates. Parameters with **small, consistent** gradients get **larger** effective learning rates. This per-parameter adaptation handles features at different scales.

## 8. Adam (Adaptive Moment Estimation)

Adam **combines** the smoothing/acceleration of momentum (first moment) with the per-parameter adaptive step sizes of RMSProp (second moment):

$$
m_t = \beta_1 m_{t-1} + (1 - \beta_1)\nabla L, \qquad s_t = \beta_2 s_{t-1} + (1 - \beta_2)(\nabla L)^2
$$

with bias-corrected estimates $\hat m_t = \dfrac{m_t}{1 - \beta_1^t}$, $\hat s_t = \dfrac{s_t}{1 - \beta_2^t}$, and update:

$$
\theta \leftarrow \theta - \frac{\eta}{\sqrt{\hat s_t} + \varepsilon}\,\hat m_t
$$

Adam is the de facto default optimizer for most deep learning today.

---

## Key Takeaways

- **L2 (weight decay)** shrinks weights smoothly toward (but not to) zero; **L1 (Lasso)** drives many weights exactly to zero → sparsity / feature selection.
- Tune $\alpha$ carefully: too high underfits, too low overfits.
- **Dropout** = train an implicit ensemble of thinned networks; turn off and scale by $(1-P)$ at test time.
- **Momentum** accelerates and damps oscillations; **RMSProp** adapts per-parameter learning rates; **Adam** combines both and is the usual default.
