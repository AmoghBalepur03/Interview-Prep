# Two-Tower (Dual-Encoder) Architectures

The **two-tower** model (a.k.a. **dual encoder** or, when weights are shared, a **Siamese network**) is the workhorse architecture for **large-scale retrieval and recommendation**. It learns to embed two kinds of inputs — e.g. (query, document), (image, text), (user, item) — into a **shared vector space** so that **relevant pairs are close** and irrelevant pairs are far apart. Because the two encoders are independent, candidate embeddings can be **precomputed and indexed**, making retrieval over **billions** of items fast.

---

## 1. The Architecture

Two separate neural networks ("towers") encode two inputs into fixed-length vectors in a **common embedding space**:

```
   Input A (e.g. query / user / image)        Input B (e.g. doc / item / text)
              │                                          │
        ┌─────▼─────┐                              ┌─────▼─────┐
        │  Tower A  │   (encoder f_A)              │  Tower B  │   (encoder f_B)
        └─────┬─────┘                              └─────┬─────┘
              │  a = f_A(A) ∈ R^d                        │  b = f_B(B) ∈ R^d
              └───────────────►  sim(a, b)  ◄────────────┘
                          (dot product / cosine)
```

- Each tower can be **any** encoder appropriate to its modality: a Transformer/BERT for text, a ViT or ResNet for images, an MLP over features for users/items.
- The towers output embeddings of the **same dimension** $d$ so they live in one space.
- The **only interaction** between the two inputs happens at the very end, through a cheap **similarity score** — there is **no cross-attention** between towers.

### Similarity scoring

$$
\text{sim}(a, b) = a^\top b \quad \text{(dot product)} \qquad \text{or} \qquad \frac{a^\top b}{\|a\|\,\|b\|} \quad \text{(cosine)}
$$

Cosine similarity = dot product of **L2-normalized** embeddings. Normalization makes scores scale-invariant and bounded in $[-1, 1]$, which stabilizes training and retrieval.

### Shared vs. separate towers

- **Siamese (shared weights):** the same encoder is used for both inputs — appropriate when both inputs are the **same modality** and symmetric (e.g. sentence–sentence similarity, Sentence-BERT).
- **Separate weights (true dual encoder):** different encoders — needed when the inputs are **different modalities** (image vs. text) or asymmetric roles (query vs. document, user vs. item).

---

## 2. Why Two Towers? The Decoupling Advantage

The defining property: **the candidate (item/document) embedding does not depend on the query.** This enables a two-phase serving pattern:

1. **Offline (indexing):** run Tower B over **all** candidates once, store the embeddings in an **Approximate Nearest Neighbor (ANN)** index (e.g. FAISS, ScaNN, HNSW).
2. **Online (query time):** run Tower A on the incoming query to get one embedding, then do a fast **nearest-neighbor search** in the index.

At query time you run **only the query tower once** plus a sublinear ANN lookup — instead of scoring the query against every candidate with a full model. This is what makes two-tower models scale to **billions of items** with millisecond latency.

---

## 3. Training: Contrastive / Metric Learning

Two-tower models are trained so that **positive pairs** (relevant) have high similarity and **negative pairs** (irrelevant) have low similarity.

### In-batch negatives (sampled softmax)

The standard, efficient trick: within a mini-batch of $B$ positive pairs $\{(a_i, b_i)\}$, treat $b_i$ as the positive for $a_i$ and **all other $b_j$ ($j \ne i$) in the batch as negatives**. This reuses the batch to get $B-1$ "free" negatives per example. The loss is a **softmax over similarities** (an InfoNCE / cross-entropy loss):

$$
\mathcal{L} = -\frac{1}{B}\sum_{i=1}^{B} \log \frac{\exp\big(\text{sim}(a_i, b_i)/\tau\big)}{\sum_{j=1}^{B} \exp\big(\text{sim}(a_i, b_j)/\tau\big)}
$$

- $\tau$ is a **temperature** that sharpens/softens the distribution (often learnable).
- Larger batches → more negatives → better representations (a key reason these models are trained with very large batches).

### Other losses

- **Contrastive loss:** push positive pairs together, pull negatives apart beyond a margin $m$:
$$
\mathcal{L} = y\,\|a - b\|^2 + (1 - y)\,\max(0,\ m - \|a - b\|)^2
$$
- **Triplet loss:** an anchor $a$, a positive $p$, and a negative $n$, with margin $m$:
$$
\mathcal{L} = \max\big(0,\ \|a - p\|^2 - \|a - n\|^2 + m\big)
$$

### Hard negative mining

Random in-batch negatives are often "too easy." Mixing in **hard negatives** (candidates that are similar but wrong) sharpens the decision boundary and substantially improves retrieval quality.

---

## 4. Two-Tower vs. Cross-Encoder

The crucial design trade-off in retrieval/ranking:

| | **Two-Tower (Dual Encoder)** | **Cross-Encoder** |
|---|---|---|
| Input handling | Encodes each input **separately** | Concatenates the pair and encodes **jointly** (full cross-attention) |
| Interaction | Only at the final similarity score | Deep token-level interaction across both inputs |
| Accuracy | Good | **Higher** (sees fine-grained interactions) |
| Can precompute candidates? | **Yes** (query-independent) | **No** (score depends on the pair) |
| Query-time cost | One query encode + ANN lookup | Re-run full model for **every** candidate → $O(N)$ |
| Use case | **Retrieval** over millions/billions | **Re-ranking** a small candidate set |

**Retrieve-then-rerank pipeline:** use a fast **two-tower retriever** to fetch the top-$k$ candidates from the whole corpus, then a slow, accurate **cross-encoder reranker** to reorder just those $k$. This combines scalability with accuracy.

---

## 5. Canonical Examples

### CLIP — Contrastive Language–Image Pre-training (multimodal two-tower)

The most famous modern two-tower model, and a direct bridge to ViT (note 16):

- **Image tower:** a **ViT** (or ResNet) encodes the image to a vector.
- **Text tower:** a Transformer encodes the caption to a vector.
- Both are projected to a **shared multimodal space** and L2-normalized.
- **Training:** contrastive InfoNCE on ~**400M (image, text)** pairs from the web. In a batch of $N$ images and $N$ texts, the model maximizes similarity of the $N$ **correct** image–text pairs and minimizes it for the $N^2 - N$ incorrect pairs (a symmetric cross-entropy over both rows and columns of the similarity matrix).
- **Zero-shot classification:** to classify an image, embed candidate class names as text prompts (e.g. "a photo of a {class}") with the text tower, embed the image with the image tower, and pick the **most similar** text — **no task-specific training** needed. This is two-tower retrieval applied to labels.

### Dense Passage Retrieval (DPR) — text retrieval for QA

- **Question encoder** (BERT) and **passage encoder** (BERT) are separate towers.
- Trained with in-batch negatives + hard negatives so a question's embedding is closest to its answer-bearing passage.
- Passages are pre-encoded and indexed; at query time the question is encoded once and retrieves passages by nearest neighbor — the retrieval stage of **retrieval-augmented** QA / RAG.

### Sentence-BERT — sentence embeddings (Siamese)

A **Siamese** BERT (shared weights) fine-tuned so semantically similar sentences have high cosine similarity. Makes semantic search / clustering over precomputed sentence vectors fast.

### Recommender Systems — user/item retrieval (course application)

The course lists **Recommender Systems** as an application; two-tower is the standard candidate-generation architecture (YouTube, Google Play):

- **User tower:** encodes user features + history → a user embedding.
- **Item tower:** encodes item features → an item embedding.
- Trained so a user is close to the items they engaged with (in-batch negatives over items).
- **Serving:** all item embeddings are indexed offline; at request time the user embedding retrieves the top-$k$ items via ANN — generating candidates from a catalog of millions in milliseconds. A heavier ranking model (often a cross-feature DNN) then re-ranks the retrieved candidates.

---

## 6. Advantages and Limitations

**Advantages**
- **Scalable retrieval:** precomputed candidate embeddings + ANN → sublinear search over huge corpora.
- **Modality-agnostic:** any encoder per tower (text, image, user features, audio).
- **Reusable embeddings:** the same item/passage index serves many queries and even multiple downstream tasks.

**Limitations**
- **No fine-grained interaction:** the single dot product can't model token-level interactions the way a cross-encoder can → lower ceiling on accuracy.
- **Embedding bottleneck:** all information is squeezed into one fixed vector per input.
- **Negative sampling sensitivity:** quality depends heavily on good (often hard) negatives and large batches.
- **Train/serve consistency:** the ANN approximation and embedding staleness (if items change) must be managed.

---

## Key Takeaways

- A **two-tower / dual-encoder** model embeds two inputs with **separate** encoders into a **shared space** and scores them with a cheap **dot product / cosine** — no cross-attention between towers.
- The **decoupling** lets you **precompute and ANN-index** candidate embeddings, so retrieval runs the query tower once + a fast nearest-neighbor lookup → scales to billions of items.
- Trained with **contrastive learning** (in-batch negatives + InfoNCE softmax, temperature $\tau$, hard negatives); also contrastive/triplet losses.
- **Cross-encoders** are more accurate (joint encoding) but can't precompute → use them only to **rerank** the top-$k$ from a two-tower **retriever**.
- Canonical models: **CLIP** (ViT image tower + text tower, zero-shot classification), **DPR** (QA retrieval), **Sentence-BERT** (Siamese), and **recommender** user/item towers.
