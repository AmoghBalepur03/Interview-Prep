# Linear & Logistic Regression and Loss Functions

Before deep networks, we need the building blocks: linear regression (the simplest predictor), gradient descent (how we train), Naive Bayes (a probabilistic baseline), logistic regression (a single neuron classifier), the multilayer perceptron, and the loss functions (SSE, softmax + cross-entropy) that drive learning.

---

## 1. Linear Regression Model

The assumption of **linearity** means the expected value of the target is a weighted sum of the features:

$$
\hat y = b + w_1 x_1 + w_2 x_2 + \cdots + w_d x_d = b + w^T x
$$

### Loss Function

A **loss function** quantifies the distance between the real and predicted target values. For regression, the squared error per sample:

$$
\ell^{(i)} = \tfrac12 \big(\hat y^{(i)} - y^{(i)}\big)^2
$$

---

## 2. Gradient Descent and Mini-Batch SGD

**Gradient descent** iteratively reduces the error by updating parameters in the direction that incrementally lowers the loss:

$$
\theta \leftarrow \theta - \eta\, \nabla_\theta L(\theta)
$$

### Gradient Descent vs. Stochastic Gradient Descent

- **(Batch) Gradient Descent:** computes the gradient over the **entire** dataset each step — accurate but expensive per step.
- **Stochastic Gradient Descent (SGD):** uses **one** sample per step — cheap and noisy (noise helps escape local minima).
- **Mini-Batch SGD:** the practical compromise — compute the gradient on a small **batch** of samples. Balances accuracy and speed, and exploits vectorized hardware.

$$
\theta \leftarrow \theta - \frac{\eta}{|B|} \sum_{i \in B} \nabla_\theta \ell^{(i)}(\theta)
$$

> Powers-of-2 batch sizes align with memory → fewer cache misses and better GPU/TPU throughput.

---

## 3. Naive Bayes (1961)

A probabilistic classifier built on Bayes' theorem with the **conditional independence** assumption — all features are conditionally independent given the class.

- $P(t)$ — prior probability of target (class) $t$.
- $P(f)$ — prior probability of a feature.
- $P(f \mid t)$ — probability of feature $f$ given target $t$.
- $P(t \mid f)$ — what we want: probability of target $t$ given the feature.

For an $n$-dimensional feature vector, the independence assumption gives:

$$
P(t \mid f_1, \dots, f_n) \propto P(t) \prod_{j=1}^{n} P(f_j \mid t)
$$

### Worked Example — Classifying Fruits

Features: Color (Yellow/Red), Shape (Round/Not Round), Size (Small/Large); classes Apple / Banana. With 7 training instances (3 apples, 4 bananas):

**Step 1 — Priors:** $P(\text{Apple}) = 3/7$, $P(\text{Banana}) = 4/7$.

**Step 2 — Likelihoods** (e.g. for Apple): $P(\text{Red} \mid \text{Apple}) = 3/3 = 1.0$, $P(\text{Yellow} \mid \text{Apple}) = 0/3 = 0$, etc.

**Step 3 — Laplace Smoothing** (fix zero probabilities by adding 1 to every count): with 2 possible values per feature,

$$
P(\text{Color}=\text{Red} \mid \text{Apple}) = \frac{3 + 1}{3 + 2} = \frac{4}{5}, \qquad
P(\text{Yellow} \mid \text{Apple}) = \frac{0 + 1}{3 + 2} = \frac{1}{5}
$$

**Step 4 — Predict** a new fruit (Yellow, Round, Large):

$$
\text{Score}_{\text{Apple}} = \tfrac{3}{7} \cdot \tfrac{1}{5} \cdot \tfrac{3}{5} \cdot \tfrac{3}{5} = \tfrac{27}{875} \approx 0.0309
$$
$$
\text{Score}_{\text{Banana}} = \tfrac{4}{7} \cdot \tfrac{5}{6} \cdot \tfrac{1}{2} \cdot \tfrac{1}{2} = \tfrac{20}{168} \approx 0.1191
$$

**Step 5 — Compare:** Banana's score is higher → classify as **Banana**. (We can ignore the denominator $P(f)$ since it is common to both.)

---

## 4. Logistic Regression (A Single Neuron Classifier)

Logistic regression computes a linear score and squashes it through the **sigmoid** to produce a probability:

$$
z = b + w_1 x_1 + w_2 x_2 + w_3 x_3, \qquad \hat y = \sigma(z) = \frac{1}{1 + e^{-z}}
$$

### Example

With $w = (0.1,\ 0.35,\ 0.7)$ and bias $b = 0.66$:

$$
\hat y_A = \sigma(0.66 + 0.1\cdot0.2 + 0.35\cdot0.5 + 0.7\cdot0.91) = \sigma(1.492) = 0.8163
$$
$$
\hat y_B = \sigma(0.66 + 0.1\cdot0.4 + 0.35\cdot0.01 + 0.7\cdot0.5) = \sigma(1.0535) = 0.7414
$$
$$
\hat y_C = \sigma(0.66 + 0.1\cdot0.3 + 0.35\cdot1.1 + 0.7\cdot0.8) = \sigma(1.635) = 0.8368
$$

---

## 5. From Linear Models to the Multilayer Perceptron (MLP)

We overcome the limitations of linear models (e.g. XOR) by adding **hidden layers**. The simplest way is to stack many fully connected layers, each feeding into the one above, until we generate outputs. This architecture is the **multilayer perceptron (MLP)**.

A hidden layer applies a linear map followed by a **nonlinear** activation; without the nonlinearity, stacked linear layers collapse into a single linear map.

---

## 6. The Softmax

For multi-class outputs, raw scores ("logits") have no guarantee of being nonnegative or summing to 1, so they cannot be read as probabilities. **Softmax** fixes this:

$$
\hat y_k = \text{softmax}(z)_k = \frac{e^{z_k}}{\sum_{l} e^{z_l}}
$$

The outputs are now all positive and sum to 1 — a valid probability distribution over classes.

---

## 7. Loss Functions for Classification

### 7.1 Why not Sum of Squared Error (SSE)?

SSE is the right loss for regression but poor for classification: it does not heavily penalize **confident but wrong** predictions, and combined with sigmoid/softmax it leads to flat gradients. A good classification loss should massively penalize predicting probability 0.9 when the truth is 0, but barely penalize predicting 0.51 when the truth is 1.

### 7.2 Cross-Entropy Loss (Derivation)

1. **Maximum Likelihood:** maximize the probability of the correct labels, $P(Y \mid X) = \prod_i P(y_i \mid x_i)$.
2. **Negative Log-Likelihood:** products are awkward (and underflow), so take the negative log to turn it into a sum to minimize:
$$
-\log P(Y \mid X) = \sum_i -\log P(y_i \mid x_i)
$$
3. **Cross-entropy** (multi-class), with one-hot label $y$:
$$
L = -\sum_{k} y_k \log \hat y_k
$$

> We scale the loss by the number of examples $n$, since scaling does not change the location of the minimum.

### 7.3 Binary Cross-Entropy

For two classes, we only predict one probability (the other is $1 - \hat y$):

$$
L = -\big[\, y \log \hat y + (1 - y)\log(1 - \hat y)\,\big]
$$

- If $y = 1$: loss is $-\log \hat y$ — small when $\hat y \to 1$, huge when $\hat y \to 0$.
- If $y = 0$: loss is $-\log(1 - \hat y)$ — same punishing logic.

The graphs of $-\log(x)$ and $-\log(1-x)$ show the loss approaching **infinity** as the prediction approaches the wrong answer, which is exactly the desired behavior. The log also prevents the numerical underflow of multiplying many small probabilities.

---

## Key Takeaways

- Linear regression + (mini-batch) SGD is the template for all neural-network training: define a loss, follow the negative gradient.
- Naive Bayes is a fast probabilistic baseline; Laplace smoothing avoids zero-probability collapse.
- Logistic regression = one neuron with a sigmoid = a probabilistic binary classifier; stacking neurons with nonlinearities gives the MLP.
- Use **softmax + cross-entropy** (not SSE) for classification — it heavily penalizes confident wrong predictions and gives healthy gradients.
