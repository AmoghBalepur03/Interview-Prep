# Tree-Based Models

Decision trees split the feature space into regions and predict a constant per region. They are interpretable but high-variance — which motivates **ensembles** (bagging, random forests, boosting) that combine many trees into strong predictors.

---

## 1. What is a Decision Tree?

A decision tree classifies based on a single discrete target feature. Each **internal node** performs a Boolean test on an input feature (multi-way tests can be converted into a series of Boolean tests). The **edges** are labeled with feature values, and each **leaf node** specifies a value for the target feature.

### Types of Decision Tree

- **ID3:** measures how mixed up the data is at a node using **entropy**, then chooses the feature that clarifies the data the most.
- **C4.5:** improved ID3 that handles missing data and continuous attributes.
- **CART:** uses **Gini impurity** to decide splits (and supports regression).

---

# Part I — Regression Trees

## 2. Motivating Example (Baseball Salaries)

Predict a baseball player's **log salary** using two predictors:

- **Years:** number of years in the major leagues.
- **Hits:** number of hits in the previous year.

The tree:

- Top split: **Years < 4.5** vs **Years ≥ 4.5**.
  - For **Years < 4.5**: predicted salary $= e^{5.107} = \$165{,}174$.
  - For **Years ≥ 4.5**: a second split on **Hits < 117.5** vs **Hits ≥ 117.5**:
    - **Hits < 117.5:** $e^{5.999} = \$402{,}834$.
    - **Hits ≥ 117.5:** $e^{6.740} = \$845{,}346$.

This creates **three regions (terminal nodes / leaves):**

- $R_1 = \{X \mid \text{Years} < 4.5\}$
- $R_2 = \{X \mid \text{Years} \ge 4.5,\ \text{Hits} < 117.5\}$
- $R_3 = \{X \mid \text{Years} \ge 4.5,\ \text{Hits} \ge 117.5\}$

**Interpretation:** Years is the most important factor. For less-experienced players, hits have little effect on salary; among experienced players, hits significantly affect salary. The tree is simple, easy to interpret, and visualize.

## 3. Core Concepts in Regression Trees

### 1. Prediction Through Stratification
- Predictor space is divided into $J$ non-overlapping regions $(R_1, R_2, \dots, R_J)$.
- For every observation in a region $R_j$, the prediction is the **mean response** for that region.

### 2. Building Regression Trees: Recursive Binary Splitting
Partitioning is **top-down and greedy**:

- At each step, choose the best predictor and cutpoint to **minimize the RSS**.
- Each internal node is a split; leaves represent regions with constant predictions.
- If there are too many features → use PCA / dimensionality reduction, or fit many trees (random forest).

**Why recursive binary splitting is efficient:**

- **Limited candidate splits:** for continuous features, only certain splits are considered, so at most $(n-1)$ candidates per feature.
- **Sort once, scan in linear time:** feature values per node are sorted once ($O(n \log n)$), then all splits are evaluated with a single linear scan using **incremental RSS (impurity) updates** — avoiding recomputation.
- **Data size shrinks with depth:** deeper nodes work with fewer samples, so cost per node drops quickly.
- **No exponential search:** greedy — picks the best split at the current node, reducing complexity from exponential to roughly $O(t \cdot n \log n \cdot d)$ ($t$ = features, $n$ = samples, $d$ = depth).
- **Implementation optimizations:** modern libraries cache sorted feature orders and use efficient partitioning.

### 3. RSS Minimization
For a pair (predictor $j$, cutpoint $s$), define regions:

$$
R_1(j, s) = \{X \mid X_j < s\}, \qquad R_2(j, s) = \{X \mid X_j \ge s\}
$$

Minimize:

$$
\sum_{i: x_i \in R_1(j,s)} (y_i - \hat y_{R_1})^2 + \sum_{i: x_i \in R_2(j,s)} (y_i - \hat y_{R_2})^2
$$

where $\hat y_{R_1}, \hat y_{R_2}$ are the mean responses in each region. Repeat on the resulting regions until a stopping criterion (e.g. no region with more than 5 observations).

## 4. Advantages of Decision Trees

- Simple to visualize and interpret.
- Work with both numerical and categorical data without much preprocessing.
- No normalization or scaling needed.
- Automatically identify the most important features.
- Handle non-linear relationships.

## 5. Tree Pruning in Regression Trees

A fully grown tree $T_0$ (from recursive binary splitting) often fits the training data very well but **overfits**. **Pruning** simplifies the tree (removing splits, merging terminal nodes) to improve generalization.

### Pruning Strategies

- **Early stopping:** stop growing when the RSS decrease after a split falls below a threshold. Often **too myopic** — a small split early might enable much bigger reductions further down.
- **Cost Complexity Pruning (Weakest Link Pruning):** grow a large tree $T_0$, then prune back to an optimal subtree balancing fit and complexity.

### Cost Complexity Pruning

Introduce a tuning parameter $\alpha$ controlling the trade-off between complexity and training error. The objective for a subtree $T$ with $|T|$ terminal nodes:

$$
C_\alpha(T) = \sum_{m=1}^{|T|} \sum_{x_i \in R_m} (y_i - \hat y_{R_m})^2 + \alpha |T|
$$

- The first term is the **RSS** for the subtree.
- The second term **penalizes complexity** proportional to the number of terminal nodes.
- When $\alpha = 0$, penalty is zero → best subtree is the full tree $T_0$.
- As $\alpha$ increases, the complexity penalty grows → smaller trees.
- This produces a **nested sequence of subtrees**, each optimal for a specific $\alpha$.

To prune: for the final tree $T_0$, choose an internal node, remove it and all its descendants, and compute $C_\alpha(T)$; choose the one with the lowest $C_\alpha(T)$. Repeat as required.

### Selecting the Optimal Tree

- Use **cross-validation** to estimate prediction error for each candidate subtree.
- Choose the $\alpha$ (and corresponding subtree) that minimizes the cross-validated **MSE**.
- Once the best $\alpha$ is chosen, the corresponding subtree is the final model.

**Example:** In the Hitters baseball data, a large tree was grown; varying $\alpha$ generated different subtree sizes; six-fold CV identified that the subtree with **three terminal nodes** minimized CV error — balancing simplicity and accuracy better than the unpruned tree.

---

# Part II — Classification Trees

## 6. Prediction
- Each terminal node predicts the **most common class** among training observations in that node.
- Class proportions in the node provide insight into **prediction confidence**.

## 7. Building
Recursive binary splitting is used similarly to regression trees. However, **RSS is not appropriate** for qualitative outcomes, so we use impurity measures (computed **for each node $m$**). Let $\hat p_{mk}$ be the proportion of training observations in node $m$ belonging to class $k$.

### Splitting Criteria

**Classification Error Rate:**

$$
E = 1 - \max_k (\hat p_{mk})
$$

where $\max_k(\hat p_{mk})$ is the proportion of the most common class. Not sensitive to changes in class distribution unless they change the majority class — so it is rarely used for growing trees.

**Gini Index** — the probability that a randomly chosen sample from the node would be misclassified if randomly labeled according to the node's class distribution:

$$
G = \sum_{k=1}^{K} \hat p_{mk}(1 - \hat p_{mk})
$$

**Entropy** — measures the amount of disorder / uncertainty:

$$
D = -\sum_{k=1}^{K} \hat p_{mk} \log_2 \hat p_{mk}
$$

Gini and entropy are both more sensitive to node purity than the error rate, so they are preferred for growing trees.

## 8. Pruning Classification Trees
- Like regression trees, pruning avoids overfitting.
- The same **cost complexity pruning** framework applies, typically using classification error or impurity in the pruning criterion.
- **Cross-validation** helps select the optimal subtree.

---

# Part III — Ensembling Models

Ensemble methods combine multiple **weak learners** into a stronger predictive model. For trees, ensembles improve accuracy and reduce instability.

## 9. Why Error Rate Reduces

Averaging many independent models reduces **variance**. If trees were perfectly independent with variance $\sigma^2$, averaging $B$ of them gives variance $\sigma^2/B$. In practice trees are correlated (correlation $\rho$), so the averaged variance is $\rho\sigma^2 + \frac{1-\rho}{B}\sigma^2$ — which is why **decorrelating** trees (random forests) helps further.

## 10. Bagging (Bootstrap Aggregating)

Bagging reduces the **high variance** of decision trees (which results from sensitivity to data perturbations) by averaging many trees built on different bootstrapped samples.

**Process:**

1. Generate $B$ bootstrapped training sets by sampling **with replacement** from the original data.
2. Fit a separate decision tree to each bootstrapped set **without pruning** (deep trees: low bias, high variance).
3. **Average** the predictions (regression) or take a **majority vote** (classification).

Averaging reduces variance without substantially increasing bias, improving test accuracy.

**Key advantages:**

- Simple to implement.
- Very effective at stabilizing unstable models like decision trees.
- Does **not overfit** as $B$ increases; large $B$ is practical.

### Out-of-Bag (OOB) Error Estimation

- Each bootstrapped sample leaves out about one-third of the data; these **OOB samples** give unbiased test-error estimates — no separate validation set or CV needed.
- Because we sample with replacement $n$ times, the probability a particular point is **not** chosen in any of the $n$ draws is:
$$
\left(1 - \frac{1}{n}\right)^n \xrightarrow{n \to \infty} \frac{1}{e} \approx 0.3679
$$
- OOB error closely approximates leave-one-out cross-validation error.

### Interpretability
- Bagged models lose the simple interpretability of a single tree.
- **Variable importance** can be assessed by the total decrease in **RSS** (regression) or **Gini index** (classification) due to splits on each variable, averaged over all trees.

## 11. Random Forests

**Improvement over bagging:** introduce additional randomness by considering only a **random subset $m$ of the $p$ predictors at each split**.

- Lower correlation between trees → greater variance reduction when averaging.
- **Typical choice:** $m \approx \sqrt{p}$ for classification.
- **Rationale:** in bagging, if one predictor is very strong, most trees use it repeatedly → correlated trees → limited variance reduction. Random forests **decorrelate** trees by forcing splits to consider only feature subsets, giving other predictors a chance.

**Benefits:**

- Reduces variance further than bagging.
- More thorough model-space exploration.
- Maintains reliable OOB error estimates.
- Robust with high-dimensional and correlated predictors.

**Empirical:** random forests typically outperform bagging; increasing $B$ stabilizes predictions without overfitting.

## 12. Boosting

Unlike bagging/random forests, boosting does **not resample** the data. Trees are grown **sequentially**, each trying to correct errors made by the ensemble of previously built trees.

**Characteristics:**

- The next tree focuses more on the **mistakes (remaining errors / residuals)** of the updated model.
- Trees are typically **small (shallow)**.
- Progressive focus on "hard" observations that previous trees handled poorly.
- Can substantially reduce **both bias and variance**.

**Risks:**

- Sensitive to **overfitting** if too many trees are added or the learning rate is too high.
- Requires careful tuning: number of trees, tree depth, learning rate.

## 13. Adaptive Boosting (AdaBoost)

- **Rounds ($k$):** decide how many weak learners to train.
- **Dataset ($D$):** $n$ training examples $(x)$ and labels $(y)$.
- **Initial weights ($w_1$):** every data point starts with equal weight $1/n$ — every example is equally important to the first model.

**Each round $t$:**

1. Train a weak learner on the weighted data; compute its weighted error $\varepsilon_t = \sum_i w_i\, \mathbb{1}[h_t(x_i) \neq y_i]$.
2. Compute its weight $\alpha_t = \tfrac12 \ln\!\frac{1 - \varepsilon_t}{\varepsilon_t}$ (better learners get more say).
3. **Update sample weights:** increase weights of misclassified points, decrease for correct ones: $w_i \leftarrow w_i\, e^{-\alpha_t y_i h_t(x_i)}$, then renormalize.
4. Final prediction: weighted vote $H(x) = \mathrm{sign}\!\left(\sum_t \alpha_t h_t(x)\right)$.

The reweighting makes each subsequent learner focus on examples the ensemble currently gets wrong.

## 14. Bayesian Additive Regression Trees (BART)

BART is a **Bayesian ensemble** that models the response as a **sum of many trees**, using a Bayesian framework with **prior distributions** to control tree structure.

**Advantages:**

- Avoids overfitting by limiting tree size and regularizing through Bayesian priors.
- Provides **uncertainty quantification** for predictions.
- Strong empirical performance with minimal tuning.

**Comparison to boosting:**

- Both build trees sequentially.
- BART modifies **existing** trees softly rather than fitting brand-new trees on residuals.
- BART's Bayesian nature provides principled uncertainty measures.

## 15. Stacking

**Naive stacking:** base models are trained on the full dataset, and their predictions become input features to train a secondary **meta-classifier**.

- **Problem:** highly prone to **overfitting**, because the meta-classifier learns from "seen" training-data predictions rather than unbiased, out-of-fold data.

**With cross-validation:** generate **out-of-fold** predictions for the base models (each prediction made by a model that did **not** see that point during training). Train the meta-model on these out-of-fold predictions — this removes the leakage and prevents overfitting.

---

## 16. General Notes

- In **imbalanced datasets**, classification trees can bias heavily toward the majority class, causing poor performance on minority classes.
- Decision trees struggle to capture complex (e.g. **XOR, parity**) or smooth relationships due to their piecewise-constant nature and greedy node splitting.
- Trees naturally handle categorical features (with appropriate encoding); missing data is typically handled by **surrogate splits** or imputation before modeling.

---

## Key Takeaways

- A single tree = recursive binary splitting (minimize RSS for regression; Gini/entropy for classification), then **prune** via cost-complexity ($\alpha$) chosen by CV.
- **Bagging** averages deep trees on bootstrap samples to cut variance; OOB ($\approx 1/e$ of data) gives a free validation estimate.
- **Random forests** decorrelate bagged trees by sampling $m \approx \sqrt{p}$ features per split → even lower variance.
- **Boosting / AdaBoost / BART** build trees sequentially to reduce bias and variance, focusing on hard cases; **stacking** combines diverse models via a meta-learner (use out-of-fold predictions to avoid overfitting).
