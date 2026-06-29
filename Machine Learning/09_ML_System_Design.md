# ML System Design / Applied ML Lifecycle

The **ML system design** round is the defining interview for Applied Scientist / Senior DS roles. It is open-ended: "Design the recommendation system for X / fraud detection for Y." Success is **not** naming a fancy model — it's showing a **structured, end-to-end** thought process that ties a business problem to data, models, serving, and measurement, while reasoning about trade-offs. This note gives a reusable framework, the recurring building blocks, and worked canonical designs.

> Builds on `08_Recommender_Systems.md`, `Deep Learning/19_Two_Tower_and_Dual_Encoders.md` (retrieval), and `Foundations/11_Hypothesis_Testing_and_AB_Testing.md` (online eval).

---

## 1. The Framework (the spine of every answer)

Walk the interviewer through these nine stages, looping back as needed:

```
Problem framing → Metrics → Data & labels → Features → Model
       → Offline eval → Online eval → Serving → Monitoring
```

Always **start by clarifying scope** (don't jump to models): who are the users, what's the scale (QPS, # items, # users), what's the latency budget, what's the business goal, are there constraints (privacy, fairness, cost)?

---

## 2. Step 1 — Problem Framing

- **Translate business → ML objective.** "Increase engagement" → "rank feed items by predicted watch-time," or "reduce fraud loss" → "binary classifier for transaction fraud with a cost-sensitive threshold."
- **Is ML even needed?** A rule/heuristic baseline may suffice; ML is justified when patterns are complex and data is abundant.
- **Problem type:** classification, regression, **ranking/recommendation**, retrieval, detection, forecasting, generation. This dictates models and metrics.
- **Framing choices matter:** e.g. recommendations can be framed as rating prediction, binary click prediction, or learning-to-rank — pick what aligns with the objective and available labels.

---

## 3. Step 2 — Metrics

- **Offline metrics** (computed on held-out data, used to iterate): must be a good **proxy** for the online goal.
- **Online / business metrics** (measured in an A/B test): the truth. Define **one OEC** (Overall Evaluation Criterion) plus **guardrails** (latency, revenue, complaints) that must not regress.
- **Map problem → metric:**
  - Regression: RMSE, MAE, MAPE, quantile loss.
  - Classification: precision/recall/F1, ROC-AUC, **PR-AUC** (imbalanced), **log-loss/Brier** (calibration).
  - Ranking/recsys: **NDCG, MAP, MRR, Precision@k, Recall@k**.
- The gap between offline and online metrics is a central theme — a model can win offline and lose online (proxy mismatch, feedback loops, latency).

---

## 4. Step 3 — Data & Labels

- **Sources:** logs (clicks, impressions, transactions), user/item attributes, context (time, device), third-party. Discuss volume and freshness.
- **Labels:**
  - **Explicit** (ratings, thumbs) — clean but scarce.
  - **Implicit** (clicks, purchases, dwell) — abundant but biased (position/selection bias, no true negatives).
  - **Human labeling / weak supervision / heuristics** when needed; label cost and quality.
  - **Label delay:** fraud/charge-backs and conversions arrive days later → affects training freshness.
- **Negative sampling:** for implicit/retrieval, how to choose negatives (random, popularity-corrected, hard negatives).
- **Splits:** use a **temporal split** (train on past, validate on future) to mimic deployment and avoid leakage; never random-split time-ordered data.
- **Class imbalance / sampling:** down/upsample, but keep evaluation on the true distribution and **recalibrate**.

---

## 5. Step 4 — Features

- **Types:** numeric, categorical (one-hot / **embeddings** / hashing for high cardinality), text (TF-IDF / embeddings), image, **cross features** (user×item), aggregations (counts, rates over windows), graph features.
- **Transformations:** scaling/normalization, log transforms, bucketization, missing-value handling.
- **Feature store:** a central, versioned source so the **same feature logic runs in training and serving**.
- **Training–serving skew:** the #1 silent killer — features computed differently (or with future info) offline vs online. Avoid **leakage** (no post-outcome or future data in features).
- **Freshness:** real-time features (last-click) vs batch features (30-day rate) — trade latency/infra for signal.

---

## 6. Step 5 — Modeling

- **Baseline first:** popularity/logistic regression/GBM — fast, interpretable, sets the bar. Only add complexity if it pays off.
- **Model choice by constraints:** GBMs (XGBoost/LightGBM) dominate tabular; deep nets for text/image/sequence and huge sparse data (embeddings); **two-tower** for retrieval.
- **Trade-offs:** accuracy vs **latency/throughput**, training cost, interpretability, maintainability.
- **Handle imbalance / cold start** (notes 05, 08).
- **Iterate:** error analysis on slices, then targeted features/model changes.

---

## 7. Step 6 — Offline Evaluation

- Use the **right metric** (Step 2) and a **temporal/grouped validation** scheme (k-fold, time-series CV, group CV to prevent leakage across user/session).
- **Slice metrics** by segment (new vs returning users, country, device) — aggregate numbers hide failures.
- **Calibration:** when the *probability* matters (ads bidding, fraud cost, expected value), check reliability and apply **Platt scaling / isotonic regression**. A model can rank well (good AUC) but be badly calibrated.
- Establish that offline wins are likely to translate before spending traffic.

---

## 8. Step 7 — Online Evaluation

- **A/B test** the candidate vs production (note 11): define OEC + guardrails, compute sample size from the MDE, check **SRM**, run ≥1 week.
- **Ramp** gradually (1% → 5% → 50%) to limit blast radius; monitor guardrails.
- Watch for **novelty effects** and **interference** (marketplaces/social → cluster or switchback designs).
- Offline ↔ online: if they disagree, investigate proxy mismatch, feedback loops, or latency regressions.

---

## 9. Step 8 — Serving

- **Batch (offline) vs real-time (online) inference:** precompute recommendations nightly vs score per request. Trade freshness vs cost/latency.
- **Latency/throughput:** set a budget (e.g. p99 < 100 ms). Techniques: smaller/distilled models, **quantization**, caching, feature precomputation, request batching, GPU/accelerators.
- **Multi-stage architecture** (the dominant pattern at scale):

```
Billions of items
      │  candidate generation (cheap: ANN over two-tower / MF embeddings) → ~hundreds
      ▼
   ranking (rich model: GBDT / deep net with many features) → ~tens
      ▼
   re-ranking (business rules, diversity, dedup, freshness) → final list
```

This makes web-scale recommendation/search tractable: a fast recall stage narrows the space, a precise ranker orders the survivors.

---

## 10. Step 9 — Monitoring & Maintenance

- **Operational:** latency, error rate, throughput, resource cost.
- **Data quality:** schema/range checks, missing-feature spikes (often training–serving skew).
- **Drift:**
  - **Data/covariate drift** — input distribution $P(X)$ shifts.
  - **Concept drift** — relationship $P(Y\mid X)$ shifts (user behavior changes).
  - Detect via population stability index, distribution distances, or live metric decay.
- **Model performance decay:** track online metrics; set **retraining cadence** (scheduled or triggered by drift/decay).
- **Feedback loops:** the model's outputs become tomorrow's training data → bias amplification; inject **exploration** and log propensities for unbiased learning.
- **Rollback / shadow / canary:** deploy safely; keep the ability to revert.

---

## 11. Recurring Building Blocks (name-drop and use these)

- **Candidate generation vs ranking vs re-ranking** (§9) — the multi-stage backbone.
- **Position & selection bias / feedback loops** — correct with inverse-propensity weighting and exploration.
- **Cold start** — content/popularity/onboarding/bandits (note 08).
- **Calibration** — Platt/isotonic when probabilities feed decisions/bidding.
- **Class imbalance** — sampling + threshold tuning + PR-AUC.
- **Offline ranking metrics** (define crisply when asked):

$$
\mathrm{NDCG@k} = \frac{\mathrm{DCG@k}}{\mathrm{IDCG@k}}, \quad
\mathrm{DCG@k} = \sum_{i=1}^{k}\frac{2^{\mathrm{rel}_i}-1}{\log_2(i+1)};
\qquad \mathrm{MRR} = \frac{1}{\lvert Q\rvert}\sum_{q}\frac{1}{\mathrm{rank}_q}
$$

Precision@k = (relevant in top-k)/$k$; Recall@k = (relevant in top-k)/(total relevant); MAP = mean over queries of average precision.

---

## 12. Canonical Designs

### (a) Recommendation / Feed Ranking
- **Objective:** maximize long-term engagement (proxy: weighted clicks + watch-time, minus negative feedback).
- **Data/labels:** impressions + interactions (implicit); careful negatives.
- **Architecture:** two-tower **retrieval** → deep **ranker** (user, item, context, cross features) → re-rank for **diversity/freshness**.
- **Metrics:** NDCG offline; sessions/DAU/watch-time online (guardrail: report-rate).
- **Pitfalls:** popularity bias, feedback loops, clickbait (optimize dwell, not just clicks).

### (b) Search / Retrieval
- **Two-stage:** embedding **retrieval** (two-tower + ANN) for recall → **cross-encoder reranker** for precision (note 19).
- **Metrics:** NDCG/MRR; online: click-through, query success, reformulation rate.
- **Pitfalls:** synonymy/intent, freshness, tail queries.

### (c) Ads CTR Prediction
- **Objective:** predict $P(\text{click})$ (and conversion); **calibration is critical** because expected value = $\text{pCTR}\times\text{bid}$ drives the auction.
- **Model:** logistic regression / GBDT / **DLRM**-style deep nets; **billions** of sparse features via hashing + embeddings.
- **Metrics:** log-loss, PR-AUC, **calibration**; online: revenue, CTR (guardrail: user experience).
- **Pitfalls:** delayed conversions, position bias, feedback loops.

### (d) Fraud / Anomaly Detection
- **Objective:** catch fraud at acceptable false-positive cost; **extreme imbalance** (<<1% positive).
- **Model:** GBM / cost-sensitive learning; anomaly detection (Isolation Forest, autoencoders) for novel patterns; graph features for rings.
- **Metrics:** **PR-AUC**, precision/recall at an operating threshold, $/loss; not accuracy.
- **Serving:** real-time, very low latency; **label delay** (charge-backs) complicates training.
- **Pitfalls:** adversaries adapt (concept drift → frequent retraining); thresholding by **expected cost**.

### (e) ETA / Demand Forecasting
- **Objective:** predict a continuous quantity over time.
- **Model:** GBM with temporal/lag features, or classical time series (ARIMA) / deep sequence models; **quantile loss** when you need prediction intervals.
- **Metrics:** RMSE/MAE/MAPE; pinball loss for quantiles.
- **Pitfalls:** seasonality, holidays, distribution shift; evaluate with **time-series CV**.

---

## 13. How to Structure the Answer (interview tips)

1. **Clarify** requirements, scale, latency, and the business goal (2–3 min).
2. **State the ML problem framing and metrics** (offline proxy + online OEC + guardrails).
3. **Sketch the high-level architecture** (multi-stage), then go deep where asked.
4. **Talk data, labels, features** explicitly — interviewers weight this heavily.
5. **Reason about trade-offs** out loud (accuracy vs latency vs cost).
6. **Close the loop:** evaluation (offline → A/B), serving, **monitoring/drift/retraining**.
7. Mention **failure modes** proactively (skew, leakage, bias, feedback loops).

---

## Key Takeaways

- **Have a framework:** framing → metrics → data/labels → features → model → offline eval → online eval → serving → monitoring. Structure beats model-name-dropping.
- **Metrics:** one **OEC + guardrails**; choose offline proxies (NDCG/PR-AUC/log-loss) that track the online goal; mind the offline–online gap.
- **Data & features** win or lose the system: temporal splits, no leakage, a **feature store** to kill **training–serving skew**, thoughtful labels/negatives.
- **Serving at scale = multi-stage:** cheap candidate generation (ANN/two-tower) → rich ranking → re-ranking, under a latency budget (distillation/quantization/caching).
- **Production is never done:** monitor **data & concept drift**, watch **feedback loops**, retrain on a cadence, deploy via canary with rollback.
- **Canonical designs** (rec/feed, search, ads CTR, fraud, forecasting) reuse the same blocks: retrieval+ranking, calibration, imbalance handling, bias correction, A/B validation.
