# LSTM and GRU (Gated Recurrent Networks)

Vanilla RNNs struggle with **long-term dependencies** because gradients vanish/explode over many time steps. Gated RNNs — the **LSTM** and the **GRU** — solve this by adding learnable **gates** that control what information to keep, forget, and output. This note also covers deep/bidirectional RNNs and the CNN-LSTM / ConvLSTM hybrids.

---

## 1. LSTM — Long Short-Term Memory

The key distinction between vanilla RNNs and LSTMs is that LSTMs support **gating of the hidden state**. An LSTM cell maintains **two** states:

- The **cell internal state** $C_t$ (the long-term memory, a "conveyor belt" of information),
- The **hidden state** $H_t$ (the short-term/working memory and the output to other layers).

Three fully connected layers with **sigmoid** activations compute the **input**, **forget**, and **output** gates (each in $(0,1)$, acting as soft switches). Intuitively, these three gates decide **what percentage** of a value should be stored/retained/exposed.

### The gates

Given input $X_t$ and previous hidden state $H_{t-1}$:

$$
F_t = \sigma\big(X_t W_{xf} + H_{t-1} W_{hf} + b_f\big) \quad \text{(forget gate)}
$$
$$
I_t = \sigma\big(X_t W_{xi} + H_{t-1} W_{hi} + b_i\big) \quad \text{(input gate)}
$$
$$
O_t = \sigma\big(X_t W_{xo} + H_{t-1} W_{ho} + b_o\big) \quad \text{(output gate)}
$$

- **Forget gate $F_t$:** decides **what percentage of the previous long-term memory** $C_{t-1}$ to hold (keep).
- **Input gate $I_t$:** decides **how much of the new candidate memory** to add to the old long-term memory.
- **Output gate $O_t$:** decides how much of the cell's internal state is exposed as the hidden state.

### The input node (candidate memory)

$$
\tilde C_t = \tanh\big(X_t W_{xc} + H_{t-1} W_{hc} + b_c\big)
$$

This is the **new candidate** information (using tanh, so values are in $(-1, 1)$).

### Memory cell internal state update

The input gate governs how much new data we take in (via $\tilde C_t$), and the forget gate governs how much old internal state $C_{t-1}$ we retain:

$$
C_t = F_t \odot C_{t-1} + I_t \odot \tilde C_t
$$

($\odot$ = elementwise product.) This **additive** update (rather than repeated multiplication) is what lets gradients flow over long time spans — solving the vanishing-gradient problem.

### Hidden state (output)

$$
H_t = O_t \odot \tanh(C_t)
$$

When the output gate is close to **1**, the memory cell's internal state impacts subsequent layers **uninhibited**; when close to **0**, the current memory is **prevented** from impacting other layers at the current time step.

### Summary of LSTM intuition

The three sigmoid gates each output a "percentage" in $(0,1)$:
- $F_t$ → how much old long-term memory to keep,
- $I_t$ → how much new memory to add,
- $O_t$ → how much memory to reveal as output.

---

## 2. GRU — Gated Recurrent Unit

A simpler, cheaper gated RNN with only **two** gates (no separate cell state). It merges the cell and hidden state and uses a **reset** gate and an **update** gate.

$$
R_t = \sigma\big(X_t W_{xr} + H_{t-1} W_{hr} + b_r\big) \quad \text{(reset gate)}
$$
$$
Z_t = \sigma\big(X_t W_{xz} + H_{t-1} W_{hz} + b_z\big) \quad \text{(update gate)}
$$

- **Reset gate $R_t$:** controls how much of the **previous state** we still want to remember when forming the candidate.
- **Update gate $Z_t$:** controls how much of the new state is just a **copy of the old** one.

### Candidate hidden state

The influence of previous states is reduced by the elementwise product of $R_t$ and $H_{t-1}$:

$$
\tilde H_t = \tanh\big(X_t W_{xh} + (R_t \odot H_{t-1}) W_{hh} + b_h\big)
$$

### Hidden state update

Incorporate the update gate $Z_t$ via an elementwise **convex combination** of the old state $H_{t-1}$ and the new candidate $\tilde H_t$:

$$
H_t = Z_t \odot H_{t-1} + (1 - Z_t) \odot \tilde H_t
$$

$Z_t$ determines the extent to which the new hidden state matches the old state versus resembles the new candidate. When $Z_t \to 1$, the state is essentially copied (skipping the update → preserving long-term info); when $Z_t \to 0$, it is fully refreshed.

### LSTM vs. GRU

- **LSTM:** 3 gates (input/forget/output) + separate cell state; more parameters, more expressive.
- **GRU:** 2 gates (reset/update), no separate cell state; fewer parameters, faster, often comparable performance.

---

## 3. Deep Recurrent Neural Networks

We can build RNNs that are deep not only in the **time** direction but also in the **input-to-output** direction, by **stacking** RNN layers on top of each other. The hidden states of layer $\ell$ become the inputs to layer $\ell + 1$ at each time step, letting the network learn more abstract temporal representations.

---

## 4. Bidirectional Recurrent Neural Networks

For many tasks the likely value of a missing token depends on **both** what comes before **and** after it. A bidirectional RNN runs **two** RNNs — one **forward** (left → right) and one **backward** (right → left) — and **concatenates** their hidden states at each position.

This gives each position access to the **full context** (past and future). Useful for tasks where the entire sequence is available (e.g. filling in a blank, NER, POS tagging). It is **not** suitable for autoregressive generation (where the future isn't known yet).

---

## 5. CNN-LSTM Architecture

A hybrid that combines **CNNs** (for spatial features) and **LSTMs** (for temporal sequences). Appropriate for problems that have **both** spatial structure (2D pixels in an image, 1D structure of words) **and** temporal structure (order of frames in a video, words in text).

**How it works (step by step):**

1. **Input:** a sequence of data with spatial dimensions (e.g. 40 video frames, or a sequence of feature vectors).
2. **Feature extraction with CNN:** each frame is passed **independently** through a CNN (e.g. VGG16) to extract high-level features, condensing rich pixel data into a feature vector. (For 1D text, 1D CNNs are used.) → For each video you get a sequence of feature vectors, e.g. shape `(#samples, 40, 25088)` (40 frames, each a 25088-dim vector).
3. **Temporal modeling with LSTM:** the sequence of feature vectors is fed step-by-step into an LSTM, which "remembers" information across steps and learns how features at one time relate to others. Output is a condensed hidden state capturing **both** spatial and temporal dimensions (e.g. 320 LSTM units).
4. **Dense / output layer:** the final LSTM output goes to a fully connected layer (often **softmax**) to produce the class (e.g. basketball, biking, walking).

**Good for:** video classification / human action recognition, image captioning (and seq2seq), speech recognition, text/event modeling.

---

## 6. ConvLSTM

ConvLSTM replaces the matrix multiplications inside the LSTM with **convolution operators** in the **state-to-state** and **input-to-state** transitions. If we view the states as hidden representations of moving objects, a ConvLSTM with a **larger** transitional kernel can capture **faster** motions, while one with a **smaller** kernel captures **slower** motions. (Originally proposed for precipitation nowcasting.) Unlike CNN-LSTM (which flattens CNN features then feeds an LSTM), ConvLSTM preserves spatial structure **throughout** the recurrence.

---

## 7. Backpropagation Through Time (BPTT) for Gated RNNs

Gated RNNs are still trained with BPTT. Using a GRU computational graph as the example:

- **Inputs** to the layer (e.g. $X_t, H_{t-1}$) — gradients flow back to these.
- **Intermediate variables** computed in the gates (e.g. $R_t, Z_t, \tilde H_t$).
- **Gradients** = derivative of each step in the computation graph w.r.t. the loss.

We update the output weight $V$ (hidden → output) and the recurrent weight $W$ by summing gradient contributions across all time steps. For **LSTM backprop**, the total output difference at a cell combines:
- $\Delta T$ — the output difference computed by any **subsequent layer**, and
- $\Delta_{out}$ — the output difference computed by the **next time-step** LSTM.

The additive cell-state path ($C_t = F_t \odot C_{t-1} + I_t \odot \tilde C_t$) provides an uninterrupted gradient highway, which is precisely why LSTMs/GRUs learn long-range dependencies far better than vanilla RNNs.

---

## Key Takeaways

- **LSTM** = cell state $C_t$ + hidden state $H_t$ controlled by **3 sigmoid gates**: forget ($F_t$, keep old memory), input ($I_t$, add new memory), output ($O_t$, expose memory). The **additive** cell update $C_t = F_t \odot C_{t-1} + I_t \odot \tilde C_t$ defeats vanishing gradients.
- **GRU** = simpler, **2 gates** (reset, update), no separate cell state; $H_t = Z_t \odot H_{t-1} + (1-Z_t)\odot \tilde H_t$. Fewer parameters, often comparable to LSTM.
- **Stack** RNNs for depth; use **bidirectional** RNNs when the full sequence is available (not for generation).
- **CNN-LSTM** extracts spatial features per frame (CNN) then models the sequence (LSTM); **ConvLSTM** keeps convolutions inside the recurrence for spatiotemporal data.
- All are trained with **BPTT**; the gated additive memory path is the key gradient highway.
