# Linear Regression

Linear regression models the relationship between a response variable $Y$ and one or more predictors $X$, assuming the relationship is approximately linear: $Y$ can be expressed as a linear function of $X$ plus random error.

---

## 1. Introduction to Linear Regression

**Simple linear regression** predicts $Y$ from a single predictor $X$:

$$
Y \approx \beta_0 + \beta_1 X
$$

- $\beta_0$ (**intercept**): the expected value of $Y$ when $X = 0$.
- $\beta_1$ (**slope**): the average change in $Y$ for a one-unit increase in $X$.

### Cost function (MSE)

$$
\text{MSE} = \frac{1}{n}\sum_{i=1}^n \big(y_i - \hat y_i\big)^2 = \frac{1}{n}\sum_{i=1}^n \big(y_i - \beta_0 - \beta_1 x_i\big)^2
$$

> **NOTE:** For MSE we divide by $n$ for **stability during gradient descent** — otherwise the gradient scales with $n$ (larger datasets would give larger gradients and require re-tuning the learning rate).

---

## 2. Statistical Model and Assumptions

The underlying **true model** is assumed to be:

$$
Y = \beta_0 + \beta_1 X + \varepsilon
$$

where $\varepsilon$ is a random error term with **mean zero**, representing the unexplained noise or variation not captured by the linear relationship.

**Key assumptions:** linearity, independent errors, constant error variance (homoscedasticity), and (for inference) normally distributed errors.

---

## 3. Measuring Accuracy of Estimates

The least-squares estimates have **standard errors** (assuming errors are uncorrelated with variance $\sigma^2$):

$$
\text{SE}(\hat\beta_1)^2 = \frac{\sigma^2}{\sum_{i=1}^n (x_i - \bar x)^2}, \qquad
\text{SE}(\hat\beta_0)^2 = \sigma^2\left[\frac{1}{n} + \frac{\bar x^2}{\sum_{i=1}^n (x_i - \bar x)^2}\right]
$$

The error variance is estimated by the **Residual Standard Error (RSE)**:

$$
\sigma \approx \text{RSE} = \sqrt{\frac{\text{RSS}}{n-2}}, \qquad \text{RSS} = \sum_{i=1}^n (y_i - \hat y_i)^2
$$

> **NOTES:**
> - Here $\sigma$ is basically the RSE value, as it is an **unbiased estimator** for the standard deviation of the errors.
> - The **more variation in $x$** (larger $\sum (x_i - \bar x)^2$), the **better** you can estimate how $y$ changes with $x$ (smaller $\text{SE}(\hat\beta_1)$).
> - The **intercept** is generally **less reliable** unless the data includes values of $x$ close to 0.

### 3.1 Confidence Intervals for $\beta_1$

A confidence interval (CI) provides a range of plausible values for the true coefficient $\beta_1$. The common **95% CI**:

$$
\hat\beta_1 \pm 2 \cdot \text{SE}(\hat\beta_1)
$$

**Interpretation:** if you repeat the sampling and fitting process many times, approximately 95% of the constructed intervals would contain the true $\beta_1$.

### 3.2 Hypothesis Testing for $\beta_1$

Hypothesis testing assesses whether there is statistical evidence that $\beta_1$ differs from zero (i.e. the predictor has an effect).

**Hypotheses:**

- **Null ($H_0$):** $\beta_1 = 0$ (no relationship between $X$ and $Y$).
- **Alternative ($H_a$):** $\beta_1 \neq 0$ (there is some relationship).

**Test statistic:**

$$
t = \frac{\hat\beta_1 - 0}{\text{SE}(\hat\beta_1)}
$$

This $t$-statistic measures how many standard errors $\hat\beta_1$ is away from zero. It follows a **$t$-distribution with $n-2$ degrees of freedom** ($n$ = sample size).

**p-value:** the probability of observing a $t$-value as extreme or more extreme than computed, assuming $H_0$ is true. For a two-tailed test:

$$
\text{p-value} = 2 \cdot P(T \ge |t|)
$$

where $T$ follows a $t$-distribution with the given degrees of freedom. **Reject $H_0$ if** $|t| > t_{\alpha/2,\, df}$, i.e. if the statistic falls in the rejection region.

A small p-value (commonly $< 0.05$) indicates strong evidence against the null hypothesis, so you reject $H_0$ and conclude the predictor has a significant effect.

---

## 4. Assessing Overall Model Fit

### $R^2$ Statistic

$R^2$ measures **how much of the variation in the dependent variable $Y$ can be explained** by the independent variable(s) $X$ using the regression model:

$$
R^2 = 1 - \frac{\text{RSS}}{\text{TSS}} = \frac{\text{TSS} - \text{RSS}}{\text{TSS}}, \qquad \text{TSS} = \sum_{i=1}^n (y_i - \bar y)^2
$$

- In **simple** linear regression, $R^2 = (\text{correlation between } X \text{ and } Y)^2$.
- In **multiple** regression, $R^2 = \operatorname{Cor}(y, \hat y)^2$.

---

## 5. Multiple Linear Regression (MLR)

General MLR model:

$$
Y = \beta_0 + \beta_1 X_1 + \beta_2 X_2 + \cdots + \beta_p X_p + \varepsilon
$$

where $X_j$ is the $j$-th predictor, $\beta_j$ is the average effect of a one-unit increase in $X_j$ **while holding all other predictors fixed**, and $\varepsilon$ is the error term.

**Closed-form (normal equations) solution:**

$$
\hat\beta = (X^T X)^{-1} X^T Y
$$

### Hypothesis Testing — the F-statistic

The **F-statistic** tests the **overall significance** of the regression model ($H_0: \beta_1 = \cdots = \beta_p = 0$):

$$
F = \frac{(\text{TSS} - \text{RSS})/p}{\text{RSS}/(n - p - 1)}
$$

A large $F$ (with small p-value) indicates that at least one predictor is useful.

| Use a **t-test** when | Use an **F-test** when |
|---|---|
| Comparing the means of two groups (or testing a single coefficient) | Comparing variances of two or more groups, or testing **overall** significance in regression / multiple-group comparisons |

**Residual Standard Error (MLR):**

$$
\text{RSE} = \sqrt{\frac{\text{RSS}}{n - p - 1}}
$$

> **NOTES on $(X^T X)^{-1}$:**
> - $X$ is the training data, of shape $n \times (p+1)$.
> - The $j$-th diagonal element of $(X^T X)^{-1}$ represents the **precision / variance inflation** of $\hat\beta_j$ (low variance directions make this value large).
> - If predictors are highly correlated (**multicollinearity**), $X^T X$ is "close to singular," making its inverse large → diagonal elements big → **standard errors of coefficients blow up**.

---

## 6. Feature Selection in MLR

### 6.1 Forward Selection

Start with the **null model** (intercept only, no predictors).

1. Fit a simple regression for each predictor individually. Choose the predictor whose inclusion most reduces the RSS, or whose p-value is lowest. (Compute $t_j$, then $P(|T| > t_j)$ with $n - p - 1$ df, giving the p-value.) A small p-value means $X_j$ has strong evidence of association with $Y$ after adjusting for predictors already in the model.
2. Add the selected predictor to the model.
3. At each subsequent step, consider adding each remaining predictor one at a time; fit the new model and compute RSS / p-value.
4. Add the predictor giving the greatest improvement (lowest RSS / most significant p-value), provided it meets a predefined criterion (e.g. $p < 0.05$).
5. Repeat until no additional predictor improves the model beyond the threshold.

- **Pros:** Simple, avoids fitting all possible models; useful with many predictors when only a few matter.
- **Cons:** Greedy — a variable included early can cause other important variables to be missed later because their effect is masked once correlated variables are in the model.

### 6.2 Backward Elimination

Start with the **full model** (all candidate predictors).

1. Look at the p-value for each predictor; identify the one with the **largest** p-value (least significant).
2. Remove this predictor.
3. Refit without it and re-evaluate p-values.
4. Repeat, removing one predictor at a time, until all remaining predictors have p-values below the threshold ($p < 0.05$).

- **Pros:** Works well when you expect most variables to be important and only a few are irrelevant.
- **Cons:** Can't be used when $p > n$ (more predictors than observations). A variable that appeared insignificant early might become significant later once others are removed.

### 6.3 Stepwise (Mixed) Selection

Combines forward and backward approaches. Start with the null model (or a few variables). At each step:

- Try **adding** every variable not currently in the model (forward); if adding any helps (meets significance), add the best one.
- Also try **removing** any variable whose p-value has drifted above a threshold (backward).
- Alternate adding and removing at each step.
- Stop when no further additions or eliminations improve the model.

- **Pros:** More flexible; corrects mistakes where a variable added early becomes redundant later.
- **Cons:** Still greedy; may not find the true optimal subset, especially with highly correlated predictors.

---

## 7. Synergy / Interaction Effects

Consider two predictors $X_1$ and $X_2$. The interaction model adds a **product term**:

$$
Y = \beta_0 + \beta_1 X_1 + \beta_2 X_2 + \beta_3 (X_1 \times X_2) + \varepsilon
$$

- $\beta_1$: main effect of $X_1$; $\beta_2$: main effect of $X_2$; $\beta_3$: **interaction effect**.

Rewriting:

$$
Y = \beta_0 + (\beta_1 + \beta_3 X_2)\, X_1 + \beta_2 X_2 + \varepsilon
$$

This shows the effect of $X_1$ on $Y$ **depends on the value of $X_2$**: the slope w.r.t. $X_1$ is $(\beta_1 + \beta_3 X_2)$. By symmetry, the effect of $X_2$ also depends on $X_1$.

---

## 8. Potential Problems in Linear Regression Models

### 1. Non-linearity of the Response–Predictor Relationships
- **Issue:** Model assumes linearity; if the true relationship is nonlinear, the model fails to capture it.
- **Detection:** Residual plots (residuals vs. fitted/predictor values) reveal U-shapes or systematic trends.
- **Remedies:** Transform predictors/response (log, square root, polynomial terms) or use nonlinear methods.

### 2. Correlation of Error Terms (Autocorrelation)
- **Issue:** Model assumes uncorrelated errors; violated often in time-series / longitudinal data.
- **Consequences:** Underestimated standard errors, overly narrow CIs and prediction intervals, inflated Type I error rates.
- **Detection:** Plot residuals against time/order; look for "tracking" or clustering of residual signs.
- **Remedies:** Time-series models, generalized least squares, or covariates explaining the dependency.
- **Intuition:** positive autocorrelation means nearby residuals move together → less independent information than assumed.

### 3. Non-constant Variance of Errors (Heteroscedasticity)
- **Issue:** Model assumes homoscedasticity (constant error variance). Heteroscedasticity = variance changes with magnitude.
- **Detection:** Residual plots show a "funnel shape" (spread grows/shrinks with fitted values).
- **Remedies:** Transform the response (e.g. log), weighted least squares, or robust standard errors.

### 4. Outliers
- **Issue:** Observations where the response is unexpectedly far from the prediction.
- **Consequences:** Skew coefficient estimates and increase residual variance.
- **Detection:** Studentized residuals (residual / its standard error); values beyond $\pm 3$ are suspect.
- **Remedies:** Check for data errors, remove or separately model outliers, or use robust regression.

### 5. High-Leverage Points
- **Issue:** Observations with predictor values far from the ordinary range.
- **Consequences:** Strongly influence the regression line, potentially distorting the fit.
- **Detection:** In simple regression, predictor values far from the mean. In multiple regression, the **leverage statistic** (average $\approx (p+1)/n$; much larger = high leverage).
- **Remedies:** Examine carefully to check if genuine; consider robust estimation.

### 6. Collinearity (Multicollinearity)
- **Issue:** Two or more predictors highly correlated, making individual effects hard to isolate.
- **Consequences:** Inflated standard errors → unreliable t-tests, wide CIs; unstable, counterintuitive coefficients.
- **Detection:**
  - Correlation matrices (pairwise correlations).
  - **Variance Inflation Factor (VIF):** $\text{VIF} > 5$ or $10$ is commonly problematic.
  - Contour plots of RSS show elongated valleys (many coefficient combinations yield similar fits).
- **Steps to calculate VIF:** regress predictor $X_j$ on all other predictors (auxiliary regression), get $R_j^2$, then
$$
\text{VIF}(\hat\beta_j) = \frac{1}{1 - R_j^2}
$$
- **Remedies:** Drop one collinear variable (using VIF / correlation matrix), combine variables into composite indices (e.g. PCA), use dimension reduction, or apply regularization.

---

## 9. Optimization Notes

### Stochastic Gradient Descent (SGD)

Instead of computing the gradient over the entire dataset (batch GD), SGD updates parameters using **one sample (or a mini-batch)** at a time:

$$
\beta \leftarrow \beta - \eta\, \nabla_\beta \,\text{loss}(y_i, \hat y_i)
$$

This is much faster per step and scales to large datasets, at the cost of noisier updates (which can also help escape poor local regions).

> **NOTE:** Using **powers of 2** for batch sizes → memory alignment is optimal → fewer cache misses, faster matrix multiplications, and better throughput on CUDA cores / TPUs.

### Covariance Calculation

$$
\operatorname{Cov}(X, Y) = \frac{1}{n}\sum_{i=1}^n (x_i - \bar x)(y_i - \bar y)
$$

This is the off-diagonal entry of the covariance matrix; the slope in simple regression is $\hat\beta_1 = \operatorname{Cov}(X,Y)/\operatorname{Var}(X)$.

---

## Key Takeaways

- Linear regression fits $Y \approx \beta_0 + \beta_1 X$ (or $X\beta$ in matrix form) by minimizing squared error; closed form $\hat\beta = (X^TX)^{-1}X^TY$.
- Inference (SE, t-test, CI, F-test, $R^2$) tells us which predictors matter and how well the model fits.
- Feature selection (forward / backward / stepwise) navigates the predictor subset space greedily.
- Watch out for the six problems: nonlinearity, autocorrelation, heteroscedasticity, outliers, leverage, and multicollinearity (diagnosed via VIF).
