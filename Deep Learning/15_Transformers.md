# Transformers

The Transformer ("Attention Is All You Need", 2017) discards recurrence and convolution entirely, relying **solely on attention**. It is the architecture behind modern LLMs (BERT, GPT, T5) and Vision Transformers. This note covers self-attention, multi-head attention, the full encoder-decoder architecture, positional encoding, the position-wise FFN, Add & Norm (layer normalization), and masked self-attention.

---

## 1. Self-Attention

In **self-attention**, the queries, keys, and values **all come from the same sequence** — each token attends to every other token (including itself) in the same input. This lets every position directly gather context from the whole sequence in **one** step.

For an input sentence of $n$ words (e.g. "The cat sat on the mat"), for each word/position the model:

1. Forms a **Query** vector (for the current word),
2. Computes a similarity/attention **score** with the **Key** vector of **every** word in the sequence (including itself),
3. Applies **softmax** to the scores, then takes a **weighted sum of all the Value vectors**.

This weighted sum becomes the output representation for that word — it "attends to" information from the **whole sentence**, not just local neighbors.

$$
Q = XW_Q,\quad K = XW_K,\quad V = XW_V, \qquad \text{SelfAttn}(X) = \text{softmax}\!\left(\frac{QK^\top}{\sqrt{d_k}}\right)V
$$

### Worked self-attention example

Inputs: Input1 = [1,0,1,0], Input2 = [0,2,0,2], Input3 = [1,1,1,1]. Weight matrices:

- **Key:** [[0,0,1],[1,1,0],[0,1,0],[1,1,0]]
- **Query:** [[1,0,1],[1,0,0],[0,0,1],[0,1,1]]
- **Value:** [[0,2,0],[0,3,0],[1,0,3],[1,1,0]]

The procedure to compute the output for Input1:

1. Compute Keys for all inputs ($K = X W_K$), Queries ($Q = X W_Q$), and Values ($V = X W_V$).
2. Compute the score: dot product of Input1's query with **every** key.
3. Softmax the scores → attention weights.
4. Multiply each value by its weight and **sum** → the output representation for Input1.

(Repeat with each input acting as the query to get all outputs.)

### Comparing CNNs, RNNs, and Self-Attention

| Property | CNN | RNN | Self-Attention |
|---|---|---|---|
| Path length between distant positions | $O(n/k)$ (needs depth) | $O(n)$ (sequential) | $O(1)$ (direct) |
| Sequential operations | $O(1)$ | $O(n)$ | $O(1)$ |
| Computation per layer | $O(knd^2)$ | $O(nd^2)$ | $O(n^2 d)$ |
| Parallelizable | Yes | **No** (sequential in time) | **Yes** |

Self-attention's killer features: **maximum path length is $O(1)$** (any token directly reaches any other → easy long-range dependencies) and it is **fully parallelizable** (unlike RNNs). The cost is $O(n^2)$ in sequence length.

---

## 2. Multi-Head Attention

It may be beneficial to allow the attention mechanism to **jointly use different representation subspaces** of queries, keys, and values. Instead of one attention function, multi-head attention runs **$h$ attention "heads"** in parallel, each with its own learned $W_Q^{(i)}, W_K^{(i)}, W_V^{(i)}$ projections:

$$
\text{head}_i = \text{Attention}(QW_Q^{(i)}, KW_K^{(i)}, VW_V^{(i)})
$$
$$
\text{MultiHead}(Q,K,V) = \text{Concat}(\text{head}_1, \dots, \text{head}_h)\,W_O
$$

Each head can attend to different relationships (e.g. one to syntax, one to coreference), and the concatenated results are projected back by $W_O$.

---

## 3. Positional Encoding

Because the Transformer has **no recurrence and no convolution**, it has no inherent notion of **order** — self-attention is permutation-equivariant. We must inject information about token positions. **Positional encodings** are added to the token embeddings.

### Requirements for a positional encoding

- **Fixed** (deterministic) per position,
- **Cannot be large** values (shouldn't overwhelm the token embedding),
- Should allow the model to attend by **relative** positions.

### Sinusoidal positional encoding

For position $pos$ and dimension $i$:

$$
PE(pos, 2i) = \sin\!\left(\frac{pos}{10000^{2i/d}}\right), \qquad PE(pos, 2i+1) = \cos\!\left(\frac{pos}{10000^{2i/d}}\right)
$$

The frequency **monotonically decreases** along the encoding dimension. This relates to absolute positional information much like the **binary representations** of $0, 1, \dots, 7$: lower bits flip fast (high frequency), higher bits flip slowly (low frequency). The sinusoids are the continuous analogue, and crucially they let the model express **relative** positions (since $PE(pos+k)$ is a linear function of $PE(pos)$).

### Adding vs. concatenating

We **add** the positional encoding to the token embedding (same dimensionality). If we instead **concatenated**, we'd have to decide a priori the size $d_t$ for the token and $d_p$ for the position, with total $d = d_t + d_p$ — meaning $d > d_t$ and $d > d_p$, so we'd be **decreasing** the size devoted to the token in favor of positional info. Adding avoids this trade-off.

### Absolute vs. relative PE

- **Absolute PE:** encodes the absolute index (order) of each token.
- **Relative PE:** encodes the relative distance between tokens (relation) — often better for generalizing to longer sequences.

---

## 4. Position-wise Feed-Forward Network (FFN)

The position-wise FFN transforms the representation at **all sequence positions using the same MLP** — each position is multiplied by the **same weights** (so identical inputs at different positions produce identical outputs). It consists of two linear transformations separated by a **ReLU**:

$$
\text{FFN}(x) = \max(0,\ xW_1 + b_1)\,W_2 + b_2
$$

This introduces non-linearity and increases the model's capacity, applied independently per token.

---

## 5. Add & Norm (Residual Connection + Layer Normalization)

Each sub-layer (attention, FFN) is wrapped with a **residual connection** followed by **normalization**:

$$
\text{output} = \text{LayerNorm}\big(x + \text{Sublayer}(x)\big)
$$

**Why normalize?** The output of layer $k-1$ feeds layer $k$. If the inputs to a layer change drastically (shifting distributions), we run into **unstable gradients**. Normalization stabilizes the distributions to accelerate training. Normalization introduces two **learnable** parameters — a scale $\gamma$ and an offset $\beta$ — so if a certain fluctuation in distribution is actually useful, the network can learn the optimal $\gamma, \beta$.

### Why Layer Norm, not Batch Norm?

**Batch normalization** normalizes across the **batch** dimension, one mini-batch at a time. Its limitations for Transformers:

- When the **batch size is small**, the sample mean/std are not representative and the network can't learn meaningfully.
- It is **less suited for sequence models**: sequences may have **different lengths**, and longer sequences correspond to smaller effective batch sizes.

**Layer normalization** instead normalizes **across all features** for **each input** (each token) independently, removing the dependence on batch statistics. All neurons in a layer share the same distribution for a given input → ideal for variable-length sequences.

---

## 6. Masked Self-Attention (Decoder)

In the **decoder** self-attention, queries, keys, and values all come from the outputs of the previous decoder layer. However, each position is allowed to attend **only** to positions **up to and including itself** — future positions are **masked** out.

This **masked attention preserves the autoregressive property**: when predicting token $t$, the model can only use tokens $1, \dots, t$ that have already been generated, never "peeking" ahead. Implementationally, the scores for future positions are set to $-\infty$ before the softmax (so their weights become 0).

---

## 7. Full Transformer Architecture — Step by Step

### Encoder Side

1. **Input tokens:** the input sentence is **tokenized** into tokens (words/subwords).
2. **Embedding:** each token → a dense vector (embedding).
3. **Positional encoding:** added to embeddings to inject position information (no recurrence).
4. **Multi-head self-attention:** each token attends to all tokens (incl. itself), capturing relationships regardless of position; multiple heads attend to different subspaces.
5. **Add & Norm:** residual connection (add attention output to its input) + normalization.
6. **Position-wise FFN:** two linear layers + ReLU, applied independently per position.
7. **Add & Norm:** another residual + normalization after the FFN.
8. **Repeat** steps 4–7 for $n$ encoder layers (typically 6+) to deepen the contextual representations.

### Decoder Side

9. **Target token embedding:** the previously generated tokens (target so far) → embeddings.
10. **Positional encoding:** added to the target embeddings.
11. **Masked multi-head self-attention:** the decoder attends only to **previous** tokens (masking future) to keep the autoregressive property.
12. **Add & Norm.**
13. **Encoder-decoder (cross) multi-head attention:** the decoder attends over the **encoder's output**, focusing on relevant input information for generating the next token. (Here queries come from the decoder; keys/values from the encoder.)
14. **Add & Norm.**
15. **Position-wise FFN.**
16. **Add & Norm.**
17. **Repeat** steps 11–16 for $n$ decoder layers.

### Final Output Layer

18. **Fully connected + Softmax:** the decoder output passes through a linear layer + softmax → a probability distribution over the target vocabulary. During inference, the highest-probability token is selected as the next output.

### Summary of the flow

- **Encode** input tokens into contextual vectors with multiple layers of self-attention + FFN.
- At each **decoding** step, generate a target token by attending to previously generated tokens (masked self-attention) **and** the encoder output (cross-attention).
- **Residual connections + layer normalization** throughout stabilize training.
- Repeat encoder/decoder layers to deepen understanding; output tokens one by one until the full target sequence is generated.

This enables effective modeling of complex sequential tasks like machine translation and summarization.

---

## Key Takeaways

- **Self-attention** lets every token directly attend to every other token in $O(1)$ path length and is **fully parallel** (unlike RNNs); cost is $O(n^2 d)$.
- **Multi-head attention** runs several attention heads in parallel over different representation subspaces, then concatenates + projects.
- No recurrence → must add **positional encodings** (sinusoidal; added, not concatenated). Each layer has a **position-wise FFN** (2 linear + ReLU).
- **Add & Norm** = residual + **layer normalization** (chosen over batch norm because it's batch-independent and handles variable-length sequences).
- The **decoder** uses **masked** self-attention (autoregressive, no peeking) plus **cross-attention** over the encoder output. Final linear + softmax produces the next-token distribution.
