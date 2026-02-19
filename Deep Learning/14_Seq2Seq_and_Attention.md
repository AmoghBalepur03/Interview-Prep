# Sequence-to-Sequence Learning and Attention

Sequence-to-sequence (seq2seq) models map an input sequence to an output sequence (e.g. machine translation). This note covers the encoder-decoder architecture, teacher forcing, decoding strategies (greedy/beam search), BLEU evaluation, and the **attention mechanism** (queries/keys/values, scoring functions, and Bahdanau attention) — the bridge to Transformers.

---

## 1. Sequence-to-Sequence Learning for Machine Translation

In machine translation, the input and output are **variable-length, unaligned** sequences. The **encoder-decoder** architecture handles this:

- The **encoder RNN** takes a variable-length sequence as input and transforms it into a **fixed-shape hidden state** (a context vector summarizing the source).
- The **decoder RNN** predicts each successive target token given **both** the input sequence (via the context) and the **preceding** tokens in the output.

The final hidden state of the encoder RNN is used to **initialize** the decoder's hidden state (only at the first decoding step).

---

## 2. Teacher Forcing

A training technique for the decoder. The special **beginning-of-sequence** token and the original target sequence (**excluding** the final token) are concatenated as the **input** to the decoder, while the decoder's **labels** are the original target sequence **shifted by one** token:

```
input : <bos>  Ils  regardent  .
labels:  Ils  regardent  .  <eos>
```

So the decoder is always fed the **ground-truth** previous token during training (rather than its own possibly-wrong prediction). As in language modeling, apply **softmax** to obtain the distribution and compute the **cross-entropy loss** for optimization.

**Alternative:** feed the **predicted** token from the previous step as the current input (free-running). Teacher forcing trains faster and more stably, but creates "exposure bias" (train/inference mismatch).

---

## 3. Decoding / Prediction Strategies

At inference there are no ground-truth tokens, so we must search for a good output sequence.

### Greedy Search

At each step, simply pick the token assigned the **highest probability** by the decoder. As in training, the first input is the `<bos>` token. Fast, but locally greedy → can miss the globally best sequence.

### Exhaustive Search

Enumerate **all** possible output sequences with their conditional probabilities and output the highest-scoring one. **Optimal** but computationally **intractable** (vocabulary size to the power of sequence length).

### Beam Search

A compromise between the **efficiency** of greedy search and the **optimality** of exhaustive search. Characterized by a single hyperparameter, the **beam size $k$**: at each step, keep the **$k$ most probable** partial sequences (hypotheses), expand each, and again keep the top $k$. Larger $k$ → better quality but more computation.

---

## 4. Evaluation: BLEU

**Bilingual Evaluation Understudy (BLEU)** evaluates a predicted sequence against a target.

Define $p_n$ as the **precision of an $n$-gram**: the ratio of the number of **matched** $n$-grams (in both predicted and target sequences) to the number of $n$-grams in the **predicted** sequence.

$$
\text{BLEU} = \exp\!\left(\min\Big(0,\ 1 - \tfrac{\text{len}_{\text{label}}}{\text{len}_{\text{pred}}}\Big)\right) \prod_{n=1}^{k} p_n^{\,1/2^n}
$$

The first factor is a **brevity penalty** (penalizes predictions that are too short); the product rewards matching higher-order $n$-grams (which capture fluency). Higher BLEU is better.

---

## 5. The Attention Mechanism

### Motivation

In a plain seq2seq RNN, **all** relevant information about the source sequence is squeezed into a **single fixed-dimensional** state by the encoder. Before too long, there simply isn't enough "space" in this intermediate representation to store everything important — a bottleneck, especially for long sentences.

**Attention** introduces a more abstract, **smooth notion of "retrieving"** information. Instead of one fixed context, the decoder can **look back** at all encoder states and focus on the relevant ones at each step.

### Why context matters (illustration)

The word "bank" means different things in "I swam across the river to the **bank**" vs. "I deposited **cash** at the **bank**." This can only be resolved by looking at the **context** provided by other words. Some words matter more: "swam"/"river" indicate the river bank; "cash" indicates the financial institution. Attention learns these importance weights.

### The Three Components: Query, Keys, Values

- **Query ($Q$):** the request for information — what the network is currently interested in. (Each position/token can act as a query.)
- **Keys ($K$):** "labels" or pointers describing pieces of information stored in memory; associated with the available information (the context / other words).
- **Values ($V$):** the actual information/content at each memory location. When a key is selected via attention, its associated value is retrieved.

The attention output is a **weighted sum of the Values**, where the weights come from the **similarity between the Query and each Key** (after softmax normalization).

### Attention Pooling by Similarity

At their core, **Nadaraya–Watson** estimators rely on a **similarity kernel** $\alpha(q, k)$ relating queries $q$ to keys $k$. The attention output is a weighted average of values:

$$
\text{Attention}(q) = \sum_i \alpha(q, k_i)\, v_i, \qquad \sum_i \alpha(q, k_i) = 1
$$

The weights $\alpha(q, k_i)$ are obtained by applying **softmax** to the attention scores.

### Attention Scoring Functions

How do we compute the score relating a query to a key?

- **Scaled dot-product attention:** distance functions are slightly more expensive than dot products, so use the dot product. To ensure the variance of the dot product remains ~1 regardless of vector length $d$, **scale** by $\sqrt{d}$:

$$
a(q, k) = \frac{q^\top k}{\sqrt{d}}
$$

- **Additive attention:** when queries and keys have **different** dimensions, use a small MLP with a tanh nonlinearity:

$$
a(q, k) = w_v^\top \tanh(W_q q + W_k k)
$$

The full scaled dot-product attention over matrices $Q, K, V$:

$$
\text{Attention}(Q, K, V) = \text{softmax}\!\left(\frac{QK^\top}{\sqrt{d_k}}\right) V
$$

---

## 6. Bahdanau Attention

The original attention mechanism for neural machine translation. The key idea: instead of keeping the context variable $c$ (summarizing the source sentence) **fixed**, we **dynamically update** it as a function of **both**:

- the original text (the **encoder** hidden states $h_t$), and
- the text already generated (the **decoder** hidden state $s_{t'-1}$).

At each decoding step $t'$, the decoder computes attention weights over all encoder hidden states (using the previous decoder state as the query) and forms a **fresh context vector** $c_{t'}$ as the weighted sum of encoder states:

$$
c_{t'} = \sum_t \alpha(s_{t'-1}, h_t)\, h_t
$$

This lets the decoder **attend** to the most relevant source words when generating each target word, removing the fixed-context bottleneck and dramatically improving long-sentence translation.

---

## Key Takeaways

- **Seq2seq** = encoder RNN compresses the source into a context; decoder RNN generates the target token-by-token. **Teacher forcing** feeds ground-truth previous tokens during training.
- Decode with **greedy** (fast, local), **exhaustive** (optimal, intractable), or **beam search** (top-$k$ compromise). Evaluate translation with **BLEU** ($n$-gram precision + brevity penalty).
- **Attention** removes the fixed-context bottleneck: output = softmax-weighted sum of **Values**, weighted by **Query–Key** similarity.
- **Scaled dot-product** score $q^\top k/\sqrt{d}$ (keeps variance ~1); **additive** score for mismatched dimensions.
- **Bahdanau attention** builds a **dynamic** context per decoding step from encoder states — the conceptual seed of the Transformer.
