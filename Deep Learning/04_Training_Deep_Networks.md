# Training Deep Networks

Training deep networks is hard: gradients vanish or explode, models over/underfit, and things break in subtle ways. This note covers the core problems and their fixes — gradient pathologies, smart weight initialization, the bias-variance picture, evaluation protocol, practical debugging, and classification metrics.

---

## 1. Vanishing and Exploding Gradients

In deep networks, the gradient is a **product** of many factors (one per layer, from the chain rule). This product can shrink or grow exponentially with depth.

- **Vanishing gradients:** gradients get smaller and smaller as they propagate down to the lower (earlier) layers. The gradient-descent update leaves the lower-layer weights virtually unchanged, so training **never converges** to a good solution.
- **Exploding gradients:** the opposite — gradients grow bigger and bigger, so many layers get insanely large weight updates and the algorithm **diverges** (loss becomes NaN or infinity).

### Cause of vanishing gradients

- **Logistic sigmoid activation:** saturates at 0 or 1, where its derivative $\sigma'(z) = \sigma(z)(1-\sigma(z))$ is extremely close to 0 (max only 0.25). Many such factors multiplied together vanish.
- **Naive weight initialization:** the once-popular scheme of random init from a normal distribution with mean 0 and std 1 makes the variance of activations grow/shrink across layers.

### Solution for exploding gradients: Gradient Clipping

Clip the gradients during backpropagation so they never exceed a set threshold. This prevents updates from being too large — but it can **alter the gradient's direction**.

$$
\text{if } \|g\| > \tau: \quad g \leftarrow \tau \cdot \frac{g}{\|g\|}
$$

---

## 2. Xavier / Glorot Initialization

**Principle:** initialize weights so that the **variance of the outputs** of each layer equals the **variance of its inputs**. This keeps signals from exploding or vanishing as they pass through the network during the forward (and backward) pass.

**Key terms:**

- **fan-in:** number of input connections to a layer.
- **fan-out:** number of output connections from a layer.
- **fan_avg** $= \tfrac12(\text{fan-in} + \text{fan-out})$.

**Glorot / Xavier initialization** (recommended for sigmoid / tanh / softmax):

- **Normal:** mean $= 0$, variance $= \dfrac{1}{\text{fan\_avg}}$.
- **Uniform:** range $\left[-\sqrt{\dfrac{3}{\text{fan\_avg}}},\ +\sqrt{\dfrac{3}{\text{fan\_avg}}}\right]$.

**Other schemes** (the optimal init depends on the activation):

| Scheme | Activation | Variance |
|---|---|---|
| Glorot/Xavier | sigmoid, tanh, softmax | $1/\text{fan\_avg}$ |
| **He** | ReLU (and variants) | $2/\text{fan\_in}$ |
| **LeCun** | SELU | $1/\text{fan\_in}$ |

---

## 3. Underfitting vs. Overfitting

- **Underfitting:** the model cannot even reduce the **training** error → the model is too simple (high bias).
- **Overfitting:** the training error is significantly lower than the **validation** error → the model memorizes the training set and fails to generalize (high variance).

The goal is the sweet spot in between — low training error **and** low validation error.

---

## 4. Evaluation: Train / Validation / Test Splits

- **Training set:** the sample of data used to **fit** the model.
- **Validation set:** used for an unbiased evaluation **while tuning hyperparameters** (model selection).
- **Test set:** used for an unbiased evaluation of the **final** model (reported performance).

The test set must be touched only once, at the very end; otherwise you leak information and overestimate performance.

---

## 5. Practical Debugging Playbook

**My Model Won't Train:**
- Check your data against a schema to detect bad examples.
- If training looks unstable (loss oscillates wildly), **reduce the learning rate** to stop the model bouncing around parameter space.
- **Overfit a tiny dataset:** simplify to ~10 examples you know the model can fit; if you can't get very low loss, the model (not the data) is broken.
- **Start simple:** begin with a very simple model that beats a baseline, then incrementally add complexity.

**My Loss Exploded:** usually caused by anomalous input values — NaNs in input, exploding gradient from anomalous data, division by zero, or $\log(0)$ / log of negatives. Fix by debugging the data pipeline, adding safeguards, and shuffling so outliers are evenly distributed across batches.

**Validation Loss is Too High** (overfitting): reduce model capacity, add regularization, and check that train/val splits are statistically equivalent (from the same distribution).

**My Model Gets Stuck:** loss decreasing in a repetitive, step-like pattern usually means the input data is **not shuffled** — the model sees batches of very similar data in sequence. Ensure shuffling removes repetitive structure.

**My Metrics are Contradictory:** e.g. loss decreases but recall is stuck at 0. Common in **imbalanced** datasets (e.g. 99% class A, 1% class B): the model always predicts the majority class, minimizing loss but getting 0% recall on the minority class.

---

## 6. Classification Metrics & Confusion Matrix

A **confusion matrix** tabulates predictions against reality. Example:

|  | Classifier says X | Classifier says O |
|---|---|---|
| **In reality X** | 5 (TP) | 2 (FN) |
| **In reality O** | 1 (FP) | 6 (TN) |

$$
\text{Accuracy} = \frac{TP + TN}{TP + TN + FP + FN} = \frac{5 + 6}{14} = 0.785
$$
$$
\text{Precision} = \frac{TP}{TP + FP} = \frac{5}{5 + 1} = 0.833
$$
$$
\text{Recall} = \frac{TP}{TP + FN} = \frac{5}{5 + 2} = 0.714
$$

- **Precision:** of those predicted positive, how many truly are (penalizes false positives).
- **Recall (Sensitivity):** of the true positives, how many we caught (penalizes false negatives).
- **F1:** harmonic mean of precision and recall, $F1 = 2\cdot\frac{P\cdot R}{P + R}$.

---

## Key Takeaways

- Deep networks suffer vanishing/exploding gradients because the chain rule multiplies many factors; clip to tame explosions, initialize smartly to tame both.
- Use **Glorot/Xavier** init for sigmoid/tanh, **He** for ReLU, **LeCun** for SELU — match the init to the activation.
- Diagnose under- vs over-fitting via the train/validation gap; keep an untouched test set for final reporting.
- Debug systematically: verify data, lower the LR, overfit 10 examples, start simple, shuffle data.
- In imbalanced problems, accuracy lies — always look at precision/recall from the confusion matrix.
