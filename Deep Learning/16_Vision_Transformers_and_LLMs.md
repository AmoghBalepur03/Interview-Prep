# Vision Transformers and Large Language Models

Once the Transformer proved dominant in NLP, it spread to **vision** (ViT, ViViT) and scaled into **Large Language Models** (BERT, T5, GPT). This note covers applying Transformers to images/video and the three LLM paradigms (encoder-only, encoder-decoder, decoder-only), plus prompting and fine-tuning.

---

## 1. Vision Transformer (ViT)

*"An Image is Worth 16×16 Words"* (Dosovitskiy et al., 2020). The core idea: apply a **standard Transformer encoder directly to images**, with the fewest possible image-specific modifications. Without specific constraints on patch size, ViTs **extract patches from images**, treat each patch as a **token**, and feed them into a **Transformer encoder** to obtain a **global representation**, which is finally transformed for **classification**.

### 1.1 Patch Embedding (image → tokens)

A standard Transformer expects a **1D sequence of token embeddings**, but an image is a 2D grid. ViT bridges this by splitting the image into patches:

- An image $x \in \mathbb{R}^{H \times W \times C}$ (height, width, channels) is split into fixed-size, **non-overlapping** patches of size $P \times P$.
- This yields $N$ patches, where:

$$
N = \frac{H \cdot W}{P^2}
$$

$N$ is the effective **sequence length** fed to the Transformer. For a $224 \times 224$ image with patch size $P = 16$: $N = (224 \cdot 224)/16^2 = 196$ patches.

- Each patch is **flattened** into a vector of size $P^2 \cdot C$ (e.g. $16^2 \cdot 3 = 768$), then **linearly projected** by a learnable matrix $E$ to the model dimension $D$ (the "patch embedding"):

$$
z_0^{(p)} = x_p\, E, \qquad E \in \mathbb{R}^{(P^2 C) \times D}
$$

This linear projection is equivalent to a **convolution** with kernel size $P$ and stride $P$ — a common implementation trick.

### 1.2 Class Token and Position Embeddings

- **[CLS] token:** a learnable embedding $z_{\text{class}}$ is **prepended** to the sequence of patch embeddings (borrowed from BERT). Its representation at the **output** of the encoder serves as the **global image representation** used for classification. (Sequence length becomes $N + 1$.)
- **Position embeddings:** since self-attention is permutation-invariant, ViT **adds learnable 1D position embeddings** $E_{\text{pos}}$ to retain positional/spatial information (standard learnable 1D embeddings work about as well as 2D-aware ones).

$$
z_0 = [\,z_{\text{class}};\ x_1 E;\ x_2 E;\ \dots;\ x_N E\,] + E_{\text{pos}}, \qquad E_{\text{pos}} \in \mathbb{R}^{(N+1) \times D}
$$

### 1.3 Transformer Encoder

The token sequence $z_0$ passes through $L$ identical Transformer encoder layers. ViT uses the **pre-norm** variant (LayerNorm **before** each sub-layer) with residual connections, and **GELU** in the MLP:

$$
z'_\ell = \text{MSA}\big(\text{LN}(z_{\ell-1})\big) + z_{\ell-1}
$$
$$
z_\ell = \text{MLP}\big(\text{LN}(z'_\ell)\big) + z'_\ell
$$

where **MSA** = multi-head self-attention and the **MLP** = two linear layers with a GELU non-linearity. (See note 15 for the attention math.)

### 1.4 Classification Head

The final representation of the [CLS] token, $z_L^{(0)}$, is layer-normalized and fed to an **MLP head**:

- **Pre-training:** an MLP with one hidden layer.
- **Fine-tuning:** a single linear layer → number of target classes.

$$
y = \text{MLP-Head}\big(\text{LN}(z_L^{(0)})\big)
$$

### 1.5 Inductive Bias and Data Requirements

This is ViT's most important practical lesson:

- **CNNs** have strong image-specific **inductive biases** baked into every layer: **locality** (nearby pixels relate), **translation equivariance**, and a 2D neighborhood structure.
- **ViT** has **much less** image-specific inductive bias — only the patch-cutting step and position embeddings are spatial; the global self-attention treats patches as a generic set. The model must **learn** spatial relationships from data.

**Consequence:** when trained on a **mid-sized** dataset (e.g. ImageNet-1k alone), ViT **underperforms** comparable ResNets, precisely because it lacks these biases. But when **pre-trained on large datasets** (ImageNet-21k with ~14M images, or the proprietary JFT-300M with ~300M images) and then transferred, ViT **matches or beats** state-of-the-art CNNs at lower pre-training compute. **Scale substitutes for inductive bias.**

### 1.6 Model Variants

ViT follows BERT's sizing, named `ViT-{Size}/{Patch}` (e.g. **ViT-B/16** = Base model, 16×16 patches):

| Model | Layers $L$ | Hidden $D$ | MLP size | Heads | Params |
|---|---|---|---|---|---|
| ViT-Base | 12 | 768 | 3072 | 12 | ~86M |
| ViT-Large | 24 | 1024 | 4096 | 16 | ~307M |
| ViT-Huge | 32 | 1280 | 5120 | 16 | ~632M |

**Smaller patch size → longer sequence → more compute, but higher accuracy** (ViT-L/16 is more expensive than ViT-L/32, since $N \propto 1/P^2$).

### 1.7 Fine-tuning at Higher Resolution & Hybrid ViT

- **Higher-resolution fine-tuning:** it's often beneficial to fine-tune at a **higher resolution** than pre-training. Keeping the patch size fixed, a higher resolution yields **more patches** (longer sequence). The pre-trained position embeddings no longer match, so they are **2D-interpolated** to the new grid size.
- **Hybrid ViT:** instead of raw image patches, feed **CNN feature-map patches** (e.g. from a ResNet stem) into the Transformer. This injects some convolutional inductive bias and can help in lower-data regimes.

### 1.8 ViT vs. CNN — Summary

| | CNN | ViT |
|---|---|---|
| Inductive bias | Strong (locality, translation equivariance) | Weak (mostly learned) |
| Receptive field | Grows with depth (local → global) | **Global from layer 1** (self-attention) |
| Data efficiency | Good on small/medium data | Needs **large-scale** pre-training |
| Scaling | Saturates | Keeps improving with size/data |
| Compute | $O(\text{pixels})$ per layer | $O(N^2)$ in number of patches |

ViT shows that, with enough data, a **pure-attention** model can **match or beat CNNs** on image classification — convolutions are not strictly necessary. It is also the **image tower** in many multimodal two-tower models like CLIP (see note 19).

---

## 2. ViViT — Video Vision Transformer

Extends ViT to **video** by extracting **tubelets**: each token captures information about how a patch changes **over time** in the video sample (a spatio-temporal patch). ViViT comes in **4 variants** trading off accuracy and compute:

### Variant 1 — Spatio-Temporal Attention

Each token is passed through a patch embedding layer, a position encoding is added, and **all** tokens (across space and time) go through a **standard Transformer encoder**. Most expensive (attention over all spatio-temporal tokens jointly).

### Variant 2 — Factorized Encoder

First split the video into small **clips**. Each clip passes through a **spatial transformer**, giving an encoding vector per clip. These per-clip vectors, plus a **CLS token**, get position embeddings and pass through a **temporal transformer encoder** (similar to the standard Transformer encoder). Each clip is divided into tubelets, each treated as a token.

### Variant 3 — Factorized Self-Attention

The **first** multi-head self-attention layer computes attention between all tokens from the **same spatial index** (within the same clip); **then** the **temporal** self-attention layer computes attention between tokens from **different temporal indices**.

### Variant 4 — Factorized Dot-Product Attention

**Half** the heads in the MSA layer compute dot-product self-attention between tokens from the **same spatial index**, while the **remaining** heads compute it between tokens from **different temporal indices**. Combines spatial and temporal attention within a single layer at lower cost.

---

## 3. Large-Scale Pre-Training with Transformers

Key observations driving LLMs:

- The Transformer architecture performs **better with increased model size and training compute** — superior **scaling** behavior.
- Transformers are extensively **pretrained** on a wealth of text.
- Transformers can be used in **three modes**: **encoder-only**, **encoder-decoder**, and **decoder-only**.

---

## 4. Encoder-Only: BERT

**BERT (Bidirectional Encoder Representations from Transformers).**

- Pretrained on text sequences using **Masked Language Modeling (MLM)** — randomly mask some tokens and train the model to predict them, using **bidirectional** context (both left and right).
- Since the representation depends on **all** input tokens, it is further projected into classification labels.
- The pretrained BERT can be **fine-tuned** to downstream encoding tasks involving single text or text pairs.

**Fine-tuning BERT for sequence-level and token-level applications:**

- **Single text classification:** use the [CLS] representation → classifier (e.g. sentiment).
- **Text pair classification:** feed two sentences → classify their relationship (e.g. entailment).
- **Text tagging:** per-token classification (e.g. POS tagging, NER).
- **Question answering:** predict the start/end span of the answer in a passage.

BERT is best for **understanding** tasks (not generation), because it is an encoder using bidirectional context.

---

## 5. Encoder-Decoder: T5

**T5 (Text-to-Text Transfer Transformer)** unifies many tasks as the **same text-to-text problem**: every task (translation, summarization, classification, QA) is cast as "generate target text conditional on input text."

**Fine-tuning T5:**

1. The T5 input includes a **task description** (a text prefix, e.g. "translate English to German: …").
2. T5 can generate sequences of **arbitrary length** via its Transformer **decoder**.
3. **No additional task-specific layers** are required — the same model handles all tasks.

---

## 6. Decoder-Only: GPT

**GPT (Generative Pre-Training).** Decoder-only Transformers have become the de facto architecture in large-scale language modeling.

- The **target sequence is the input sequence shifted by one token** (next-token prediction).
- The attention pattern in the Transformer decoder enforces that **each token can only attend to its past tokens** (causal/masked self-attention) — preserving the autoregressive property.

### GPT-3 and Prompting

GPT-3 demonstrated using **the same language model for multiple tasks without updating the model** — via **prompting** (in-context learning):

- **Zero-shot prompting:** give only a task description, no examples. Well-suited for **simple tasks, exploratory queries, or tasks needing only general knowledge**. Doesn't work well for complex tasks requiring context or a very specific output form.
- **Few-shot prompting:** give a few examples in the prompt. Useful when the model needs to "learn" a new concept or when a **precise output form** is required. A natural choice with **very limited data** (too little to train on) that could help the model solve a task.
- (**One-shot** is the in-between: a single example.)

---

## 7. The Three Transformer Modes — Summary

| Mode | Example | Attention | Best For |
|---|---|---|---|
| **Encoder-only** | BERT | Bidirectional | Understanding (classification, tagging, QA) |
| **Encoder-decoder** | T5 | Encoder bidir. + decoder causal + cross | Conditional generation (translation, summarization) |
| **Decoder-only** | GPT | Causal (left-to-right) | Open-ended generation, in-context learning |

---

## Key Takeaways

- **ViT** splits an image into $N = HW/P^2$ **patches**, linearly projects each to a token, prepends a **[CLS]** token, adds **learnable position embeddings**, and runs a standard (pre-norm, GELU) Transformer encoder; the [CLS] output → MLP head. It has **weak inductive bias**, so it needs **large-scale pre-training** (ImageNet-21k/JFT-300M) to beat CNNs — scale substitutes for inductive bias. Variants: ViT-B/L/H; smaller patch = more accuracy + compute.
- **ViViT** extends this to video via **tubelets**, with 4 variants (joint spatio-temporal; factorized encoder; factorized self-attention; factorized dot-product) trading accuracy for efficiency.
- Transformers **scale** well with size/compute and come in three modes: **encoder-only (BERT)**, **encoder-decoder (T5)**, **decoder-only (GPT)**.
- **BERT** = bidirectional, masked-LM pretraining, fine-tuned for understanding tasks. **T5** = everything as text-to-text. **GPT** = causal next-token prediction.
- **GPT-3** popularized **prompting**: zero-shot (description only) vs. few-shot (a few examples) in-context learning, no weight updates.
