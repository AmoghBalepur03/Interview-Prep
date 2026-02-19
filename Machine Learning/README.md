# Machine Learning — Notes

A complete set of notes covering the core supervised and unsupervised machine learning algorithms, from linear regression to neural networks. Each topic is a self-contained Markdown file with full derivations, formulas, and LaTeX-formatted math.

> **Tip:** These render best in any Markdown viewer with LaTeX support (e.g. VS Code with a math preview extension, Obsidian, or GitHub).

---

## Reading Order

The files are numbered in a logical learning sequence — regression first, then classification, distance/clustering methods, margin-based methods, ensembles, and finally neural networks.

| # | Topic | What it covers |
|---|---|---|
| 01 | [Linear Regression](01_Linear_Regression.md) | Simple & multiple regression, OLS closed form, SE / t-test / CI / F-test / $R^2$ / RSE, forward-backward-stepwise selection, interaction effects, the six model problems (nonlinearity, autocorrelation, heteroscedasticity, outliers, leverage, multicollinearity/VIF), SGD, covariance |
| 02 | [Classification](02_Classification.md) | Why not linear regression, logistic regression (sigmoid, odds, logit, MLE), multinomial & softmax, generative vs discriminative, Bayes' theorem, Naive Bayes, LDA/QDA, confusion matrix, precision/recall, ROC & AUC |
| 03 | [KNN and Clustering](03_KNN_and_Clustering.md) | KNN algorithm & distance metrics, choosing $k$, K-Means (WCSS, E/M steps), Elbow & Silhouette methods, K-Means++, EM with Gaussian Mixture Models, K-Means as a special case of EM |
| 04 | [Support Vector Machines](04_SVM.md) | Hyperplanes, maximal margin classifier, support vectors, soft-margin SVC (slack $\xi$, budget $C$), the kernel trick, dual form, common kernels (linear/poly/RBF/sigmoid), multiclass strategies |
| 05 | [Tree-Based Models](05_Tree_Based_Models.md) | Decision trees (ID3/C4.5/CART), regression trees (recursive binary splitting, RSS), classification trees (Gini, entropy, error rate), cost-complexity pruning, bagging & OOB, random forests, boosting, AdaBoost, BART, stacking |
| 06 | [Neural Networks](06_Neural_Networks.md) | Feed-forward architecture, activations (sigmoid, ReLU), loss functions (MSE, cross-entropy), deep network computation, forward/backward flow, backpropagation (chain rule), optimization & regularization |

---

## Conceptual Map

```
                    Supervised Learning
        ┌───────────────────┴───────────────────┐
   Regression                              Classification
        │                          ┌────────────┼─────────────┐
 Linear Regression          Logistic /      Generative      Margin-based
 (OLS, inference)           Softmax         (LDA/QDA/NB)     (SVM + kernels)
        │                                                        │
        └──────────────► Ensembles (Trees) ◄────────────────────┘
                    Bagging / Random Forest / Boosting
                                │
                         Neural Networks
                       (deep nonlinear models)

   Unsupervised:  KNN (instance-based) · K-Means · EM / GMM
```

---

## Recurring Themes

- **Bias-variance trade-off** is everywhere: model flexibility (tree depth, $k$ in KNN, $C$ in SVM, network size) trades fitting power against generalization.
- **Maximum likelihood / loss minimization** underlies logistic regression, GMMs, and neural networks.
- **Ensembling** (bagging, random forests, boosting, stacking) reduces variance and/or bias by combining many weak learners.
- **Regularization & validation** (cross-validation, pruning, dropout, OOB error) are the tools that keep models from overfitting.
