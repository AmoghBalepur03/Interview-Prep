# Recommender Systems

Recommenders predict which **items** (products, movies, songs, posts) a **user** will like, from past interactions. They power a huge share of engagement and revenue at consumer companies, and are a staple of applied-ML interviews. This note covers the **classical** approaches — content-based, neighborhood collaborative filtering, and matrix factorization — plus implicit feedback, evaluation, and the practical issues that lead into modern neural retrieval.

> Complements `Deep Learning/19_Two_Tower_and_Dual_Encoders.md` (the neural, large-scale version) and `Machine Learning/09_ML_System_Design.md` (how a recommender is built end-to-end).

---

## 1. The Problem Setup

We have $m$ users and $n$ items and a sparse **interaction matrix** $R \in \mathbb{R}^{m \times n}$, where $r_{ui}$ is user $u$'s feedback on item $i$. Most entries are **missing** — the task is to predict them (or to rank the unobserved items).

- **Explicit feedback:** ratings (1–5 stars). Accurate but sparse; users rarely rate.
- **Implicit feedback:** clicks, watches, purchases, dwell time. Abundant but **only positives are observed** — a non-click is *not* a confirmed negative (the user may not have seen it).

The defining challenge is **sparsity** (often >99% missing) and the **long tail** (a few popular items, many rare ones).

---

## 2. Content-Based Filtering

Recommend items **similar to ones the user liked**, using item **features**.

- Represent each item by a feature vector (e.g. genre, tags, **TF-IDF** of text).
- Build a **user profile** as the (weighted) average of the feature vectors of items they liked.
- Score items by similarity to the profile, e.g. **cosine similarity**:

$$
\mathrm{sim}(a, b) = \frac{a \cdot b}{\lVert a\rVert\, \lVert b\rVert}
$$

**Pros:** no need for other users' data; handles **new items** (if they have features); explainable ("because you liked X").
**Cons:** needs good item features; **over-specialization** (filter bubble — only ever recommends more of the same); cannot help a brand-**new user** with no history.

---

## 3. Collaborative Filtering (CF)

Use the **wisdom of the crowd**: patterns across many users' interactions, **no item features required**. "Users who agreed in the past will agree in the future."

### 3a. User-Based Neighborhood CF

To predict $r_{ui}$: find users similar to $u$ who rated $i$, and take a similarity-weighted average of their (mean-centered) ratings:

$$
\hat r_{ui} = \bar r_u + \frac{\displaystyle\sum_{v \in N(u; i)} \mathrm{sim}(u, v)\,(r_{vi} - \bar r_v)}{\displaystyle\sum_{v \in N(u; i)} \lvert \mathrm{sim}(u, v)\rvert}
$$

Centering by each user's mean $\bar r_u$ removes **rating bias** (some users rate generously). Similarity is **cosine** or **Pearson correlation** (cosine on mean-centered ratings).

### 3b. Item-Based Neighborhood CF

Symmetric idea, but compute **item–item** similarities and predict from the user's own ratings of similar items:

$$
\hat r_{ui} = \frac{\displaystyle\sum_{j \in N(i; u)} \mathrm{sim}(i, j)\, r_{uj}}{\displaystyle\sum_{j \in N(i; u)} \lvert \mathrm{sim}(i, j)\rvert}
$$

**Item-based is usually preferred in production** (Amazon's "customers who bought…"): item–item similarities are **more stable** over time, there are typically fewer items than users, and the similarity matrix can be **precomputed** offline.

**Limits of neighborhood CF:** the similarity matrix is $O(m^2)$ or $O(n^2)$; extreme sparsity makes overlaps tiny; it doesn't generalize across the latent structure → motivates matrix factorization.

---

## 4. Matrix Factorization (Model-Based CF)

Assume interactions are explained by $k$ **latent factors**. Learn a user matrix $P \in \mathbb{R}^{m \times k}$ and item matrix $Q \in \mathbb{R}^{n \times k}$ so that:

$$
\hat r_{ui} = p_u^{\top} q_i
$$

Each $p_u$ (user's affinity to factors) and $q_i$ (item's loading on factors) live in a shared latent space (e.g. "action vs romance", "mainstream vs niche") **learned from data**, not hand-designed.

### Bias terms

Real ratings have strong user/item offsets, so the standard model adds **biases**:

$$
\hat r_{ui} = \mu + b_u + b_i + p_u^{\top} q_i
$$

where $\mu$ = global mean, $b_u$ = user bias, $b_i$ = item bias. (Biases alone are a strong baseline.)

### Objective (regularized squared error over observed entries $K$)

$$
\min_{P, Q, b}\ \sum_{(u,i)\in K}\Big(r_{ui} - \mu - b_u - b_i - p_u^{\top} q_i\Big)^2 + \lambda\Big(\lVert p_u\rVert^2 + \lVert q_i\rVert^2 + b_u^2 + b_i^2\Big)
$$

**Why not plain SVD?** Classical SVD needs a fully-observed matrix; imputing zeros is biased and expensive. We instead optimize **only over observed entries** ("Funk SVD", popularized in the Netflix Prize).

### Optimization

**Stochastic Gradient Descent.** With error $e_{ui} = r_{ui} - \hat r_{ui}$, for each observed $(u,i)$:

$$
p_u \leftarrow p_u + \gamma\,(e_{ui}\, q_i - \lambda\, p_u), \qquad q_i \leftarrow q_i + \gamma\,(e_{ui}\, p_u - \lambda\, q_i)
$$
$$
b_u \leftarrow b_u + \gamma\,(e_{ui} - \lambda\, b_u), \qquad b_i \leftarrow b_i + \gamma\,(e_{ui} - \lambda\, b_i)
$$

**Alternating Least Squares (ALS).** Fixing $Q$ makes the problem **quadratic and convex in $P$** (and vice versa), with a closed-form per-user solve:

$$
p_u = \big(Q_{I_u}^{\top} Q_{I_u} + \lambda I\big)^{-1} Q_{I_u}^{\top}\, r_u
$$

where $I_u$ are the items $u$ rated. Alternate $P$- and $Q$-steps. ALS is **embarrassingly parallel** (each user/item solves independently) — preferred for large, implicit datasets.

---

## 5. Implicit Feedback

With only positive signals (clicks/plays), reframe the problem.

### Weighted ALS (Hu, Koren, Volinsky)

Split each observation into a binary **preference** and a **confidence**:

$$
p_{ui} = \mathbb{1}[r_{ui} > 0], \qquad c_{ui} = 1 + \alpha\, r_{ui}
$$

More interactions ⇒ higher confidence the preference is real. Optimize over **all** $u,i$ pairs (observed positives + unobserved treated as weak negatives), weighted by confidence:

$$
\min_{P,Q}\ \sum_{u, i} c_{ui}\,\big(p_{ui} - p_u^{\top} q_i\big)^2 + \lambda\big(\lVert P\rVert^2 + \lVert Q\rVert^2\big)
$$

### Bayesian Personalized Ranking (BPR)

Optimize **ranking** directly: a user prefers an observed item $i$ over an unobserved item $j$. With $\hat x_{uij} = \hat r_{ui} - \hat r_{uj}$, maximize:

$$
\sum_{(u,i,j)} \ln \sigma(\hat x_{uij}) - \lambda\,\lVert \Theta \rVert^2
$$

This is a pairwise (AUC-like) objective — better suited to top-N ranking than pointwise squared error.

---

## 6. Hybrid Models & Factorization Machines

- **Hybrid:** combine content + CF to fix each other's weaknesses (e.g. weighted blend, or CF with content as a fallback for cold items). Most real systems are hybrid.
- **Factorization Machines (FM):** generalize MF to **arbitrary features** (user, item, context, side info) with pairwise interactions $\hat y(x) = w_0 + \sum_i w_i x_i + \sum_{i<j}\langle v_i, v_j\rangle x_i x_j$ — MF is the special case with only user/item id features. Great for **context-aware** and **cold-start** recs.

---

## 7. The Cold-Start Problem

- **New item:** no interactions → CF fails. Use **content-based** features or popularity.
- **New user:** no history → use **onboarding** (ask preferences), demographics, or popular/diverse items; switch to personalized once signal accrues.
- **New system:** bootstrap with content/popularity; explore to gather data (bandits).

---

## 8. Evaluation

### Rating prediction (explicit)
RMSE / MAE between predicted and held-out ratings.

### Top-N ranking (what actually matters)
Hold out interactions (temporal split or leave-one-out) and score the ranked list:

- **Precision@k / Recall@k:** fraction of the top-$k$ that are relevant / fraction of relevant items retrieved in the top-$k$.
- **MAP** (Mean Average Precision): averages precision at each relevant hit — rewards ranking hits higher.
- **MRR** (Mean Reciprocal Rank): $\frac{1}{\lvert Q\rvert}\sum 1/\mathrm{rank}_{\text{first hit}}$ — for "find the one right answer."
- **NDCG** (the standard for graded relevance):

$$
\mathrm{DCG@k} = \sum_{i=1}^{k} \frac{2^{\mathrm{rel}_i} - 1}{\log_2(i + 1)}, \qquad \mathrm{NDCG@k} = \frac{\mathrm{DCG@k}}{\mathrm{IDCG@k}}
$$

The $\log$ discount rewards putting relevant items near the top; dividing by the ideal DCG normalizes to $[0,1]$.

### Beyond accuracy
**Coverage** (catalog fraction recommended), **diversity**, **novelty/serendipity**, and **popularity bias** — accuracy alone produces boring, head-heavy lists. Validate offline, then confirm with an **online A/B test** (note 11), since offline metrics correlate imperfectly with engagement.

---

## 9. Practical Issues → Modern Systems

- **Popularity bias & feedback loops:** the system recommends popular items → they get more interactions → look even more popular. Mitigate with debiasing / exploration.
- **Position bias:** higher-ranked items get clicked regardless of relevance; correct with inverse-propensity weighting.
- **Scalability:** exact top-N over millions of items is infeasible → **two-stage**: cheap **candidate generation** (e.g. ANN over MF/two-tower embeddings) then a heavier **ranker**.
- **Bridge to deep learning:** MF's $p_u^{\top} q_i$ is exactly a **two-tower** dot product; neural recommenders (NCF, two-tower, DLRM, sequential/transformer recommenders) extend it with deep encoders and side features. See note 19.

---

## Key Takeaways

- **Three families:** **content-based** (item features, handles new items, over-specializes), **neighborhood CF** (user/item similarity, no features needed; item-based is more stable/scalable), and **matrix factorization** (latent factors $\hat r_{ui} = \mu + b_u + b_i + p_u^{\top} q_i$).
- **MF training:** regularized squared error over **observed** entries via **SGD** or **ALS** (closed-form, parallel); add **biases** — they're a strong baseline.
- **Implicit feedback:** only positives are known → **weighted ALS** (preference + confidence) or **BPR** (pairwise ranking).
- **Cold start** is the central weakness of CF → use content/hybrid/FM/bandits.
- **Evaluate ranking** with Precision@k, Recall@k, MAP, MRR, **NDCG** (plus coverage/diversity), and confirm with an online A/B test.
- MF's dot product generalizes directly to **two-tower / neural** recommenders for web-scale retrieval.
