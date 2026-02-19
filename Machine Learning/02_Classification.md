# Classification

Classification predicts a **qualitative (categorical)** response variable. Unlike regression (quantitative responses), classification assigns observations to categories or classes — e.g. diagnosing medical conditions, detecting fraudulent transactions, or identifying deleterious DNA mutations.

---

## 1. Challenges with Linear Regression for Classification

Linear regression fits a hyperplane to predict continuous values, which is problematic for classification:

- **Inappropriate for multiple classes:** Mapping categories to numeric labels (e.g. 1 = stroke, 2 = drug overdose, 3 = seizure) implies a numeric **ordering** and **equal spacing** that is usually unjustified. (Equal spacing makes no sense: "overdose − stroke = 1" is meaningless.)
- **Predicted values don't bound probabilities:** With 0/1 encoding, linear regression can predict values outside $[0,1]$, which aren't valid probabilities.
- **Misaligned objectives:** Linear regression minimizes squared error (for continuous data), but classification needs probability estimates or decision boundaries that separate classes.

---

## 2. Logistic Regression

Logistic regression models the probability $p(X)$ that an observation belongs to class 1 (binary case) using the **logistic (sigmoid) function**:

$$
p(X) = \frac{e^{\beta_0 + \beta_1 X}}{1 + e^{\beta_0 + \beta_1 X}} = \frac{1}{1 + e^{-(\beta_0 + \beta_1 X)}}
$$

The output is bounded in $(0, 1)$ — a valid probability. Parameters $\beta_0, \beta_1$ are estimated by **Maximum Likelihood Estimation (MLE)**.

### Odds and the logit (log-odds)

The **log-odds (logit)** is a **linear** function of the predictors:

$$
\log\!\left(\frac{p(X)}{1 - p(X)}\right) = \beta_0 + \beta_1 X
$$

This produces an S-shaped probability curve, avoiding invalid predictions. The three views:

1. **Probability:** squashed between 0 and 1 → hard to model linearly.
2. **Odds:** asymmetrical — losing is between 0 and 1, winning is 1 to infinity.
3. **Logit:** symmetric — a "50/50" chance becomes exactly 0.
   - **Equality:** if the logit for winning is $+2$, the logit for losing is exactly $-2$.
   - **Linearity:** you can simply "add" or "subtract" evidence (coefficients) to change the outcome, just like standard linear regression.

### Maximum Likelihood Estimation

Choose $\beta$ to maximize the likelihood of the observed labels:

$$
\ell(\beta_0, \beta_1) = \prod_{i: y_i = 1} p(x_i) \prod_{i': y_{i'} = 0} \big(1 - p(x_{i'})\big)
$$

Equivalently, minimize the **cross-entropy / log-loss** $-\sum_i [y_i \log p(x_i) + (1-y_i)\log(1 - p(x_i))]$.

---

## 3. Multiple Logistic Regression

Estimates the probability of $Y$ belonging to class 1 (e.g. default = yes) given multiple predictors:

$$
p(X) = \frac{1}{1 + e^{-(\beta_0 + \beta_1 X_1 + \cdots + \beta_p X_p)}}
$$

---

## 4. Multinomial Logistic Regression

Generalizes binary logistic regression to $K > 2$ classes.

### Model structure

Choose one class as the **baseline** (often the $K$-th class) and model the log-odds of each other class $k = 1, \dots, K-1$ relative to the baseline:

$$
\log\!\left(\frac{\Pr(Y = k \mid X)}{\Pr(Y = K \mid X)}\right) = \beta_{k0} + \beta_{k1} X_1 + \cdots + \beta_{kp} X_p
$$

**Intuition:**

- **LHS:** how much more likely class $k$ is compared to the baseline class $K$ — in log-odds units.
- **RHS:** a linear function of the predictors.

> **NOTE:** $\beta$ is a $K \times p$ matrix for multinomial logistic regression.

---

## 5. Softmax

$$
\Pr(Y = k \mid X) = \frac{e^{\beta_{k0} + \beta_k^T X}}{\sum_{l=1}^{K} e^{\beta_{l0} + \beta_l^T X}}
$$

- **All classes treated equally:** no need to choose a baseline/reference category.
- Ensures all output probabilities are in $(0,1)$ and **sum to 1** (a valid probability distribution).
- The class with the **highest probability** is the predicted class.

---

## 6. Generative Models for Classification

### Discriminative methods (e.g. Logistic Regression)
- **What they do:** model the conditional probability $P(Y \mid X)$ **directly**.
- **Goal:** learn the decision boundary without explicitly modeling how $X$ is distributed.

### Generative methods (LDA, QDA, Naive Bayes)
- **What they do:** model the joint distribution $P(X, Y)$ by:
  - Estimating **class priors** $\pi_k = P(Y = k)$ = (number of samples of class $k$) / total.
  - Estimating the **class-conditional densities** $f_k(x) = P(X = x \mid Y = k)$.
  - Then applying **Bayes' theorem** to obtain $P(Y \mid X)$.

### Bayes' Theorem in Classification

$$
\Pr(Y = k \mid X = x) = \frac{\pi_k\, f_k(x)}{\sum_{l=1}^{K} \pi_l\, f_l(x)}
$$

- $\pi_k$ = (points in class $k$) / (total number of points).
- Assuming features are **independent** (Naive Bayes): $f_k(x) = f_{k1}(x_1) \times f_{k2}(x_2) \times \cdots \times f_{kp}(x_p)$.

**Intuition:**

- Imagine each class has its own "probability landscape" for $X$ (the shape of $f_k(x)$).
- We compute $f_k(x)$ from the underlying distribution (assuming Gaussian, exponential, etc.).
- We weigh each shape by how frequent the class is ($\pi_k$).
- At a given point $x$, whichever weighted shape gives the highest value is the chosen class.
- This is the **best possible classifier** (Bayes optimal) given enough points.

---

## 7. Linear Discriminant Analysis (LDA)

### Assumptions (Key)

- $(X \mid Y = k) \sim N(\mu_k, \Sigma)$: multivariate Gaussian with **class-specific mean** $\mu_k$ but a **common covariance matrix** $\Sigma$.
- Priors $\pi_k = P(Y=k)$ are known or estimated from data.
- Features have the same variance/covariance structure for all classes.

The principle: find a projection (a new axis) in feature space onto which data can be projected so the classes become as distinct as possible. LDA tries to:

- **Maximize** the distance between the means of different classes (**between-class variance**).
- **Minimize** the variance within each class (**within-class variance**).

### Algorithm

1. **Compute mean vectors** for each class (centroid in the original feature space).
2. **Compute scatter matrices:**
   - **Within-class scatter:** measures spread within each class.
   $$
   S_W = \sum_k \sum_i (x_i - \mu_k)(x_i - \mu_k)^T
   $$
   - **Between-class scatter:** measures scatter between class means.
   $$
   S_B = \sum_k N_k (\mu_k - \mu)(\mu_k - \mu)^T
   $$
3. **Solve the generalized eigenvalue problem:** compute eigenvectors of
$$
S_W^{-1} S_B\, w = \lambda w
$$
4. **Select linear discriminants:** sort eigenvectors by eigenvalue (descending), keep the top ones (most discriminative).
5. **Project data:** $P = w^T x_i$.

> **LDA vs QDA:** QDA (Quadratic Discriminant Analysis) relaxes the common-covariance assumption — each class gets its own $\Sigma_k$, producing **quadratic** decision boundaries (more flexible, more parameters).

---

## 8. Performance & the Confusion Matrix

A **confusion matrix** cross-tabulates predicted vs. actual classes:

|  | Predicted Positive | Predicted Negative |
|---|---|---|
| **Actual Positive** | TP (True Positive) | FN (False Negative) |
| **Actual Negative** | FP (False Positive) | TN (True Negative) |

### Common metrics

| Metric | Formula | Synonyms |
|---|---|---|
| **FPR** | $FP / N = FP/(FP+TN)$ | Type I error, $1 - $ Specificity |
| **TPR** | $TP / P = TP/(TP+FN)$ | Sensitivity, Recall, Power |
| **Precision (PPV)** | $TP / (TP + FP)$ | Positive predictive value |
| **NPV** | $TN / (TN + FN)$ | Negative predictive value |
| **Recall** | $TP / (TP + FN)$ | (= TPR / Sensitivity) |

Additional: **Accuracy** $= (TP+TN)/(P+N)$, **Specificity** $= TN/(TN+FP)$, **F1** $= 2 \cdot \frac{\text{Precision}\cdot\text{Recall}}{\text{Precision}+\text{Recall}}$.

---

## 9. ROC Curves and AUC

- **ROC curve:** plots **True Positive Rate (Sensitivity)** vs. **False Positive Rate (1 − Specificity)** for varying classification thresholds (the probability at which we classify as 1 vs. 0).
- **AUC (Area Under the Curve):** a scalar summary of ROC performance:
  - $1.0$ = perfect classifier.
  - $0.5$ = random guessing.

Lowering the threshold catches more positives (higher TPR) at the cost of more false positives (higher FPR) — the ROC curve traces this trade-off across all thresholds.

---

## Key Takeaways

- Logistic regression models the **log-odds** linearly; outputs valid probabilities via the sigmoid, fit by MLE.
- Multinomial / softmax extend this to $K$ classes (softmax treats all classes symmetrically).
- **Discriminative** models learn $P(Y\mid X)$ directly; **generative** models (LDA/QDA/Naive Bayes) model $P(X,Y)$ and apply Bayes' rule.
- Evaluate with the confusion matrix, precision/recall/specificity, and the threshold-independent ROC/AUC.
