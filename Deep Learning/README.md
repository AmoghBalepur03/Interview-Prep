# Deep Learning — Complete Notes (CS F425)

Detailed, topic-wise notes covering the full **Deep Learning** course — from the foundations of neural networks through CNNs, RNNs/LSTMs, attention, and Transformers, to LLMs, explainability, and energy-based models. Compiled from the course lectures (18 lectures + handout) and the detailed Intro/CNN notes, with all mathematics in LaTeX, worked examples, and completed derivations.

---

## Reading Order

The notes are numbered to follow the natural learning progression. Each file is self-contained but builds on earlier ones.

### Part I — Foundations
1. [01 — Introduction and Neural Networks](01_Introduction_and_Neural_Networks.md) — NN benefits, McCulloch-Pitts neuron, logic gates/XOR, Rosenblatt's perceptron, Hebbian learning, perceptron convergence theorem.
2. [02 — Linear & Logistic Regression and Loss](02_Linear_Logistic_Regression_and_Loss.md) — linear regression, (mini-batch) SGD, Naive Bayes, logistic regression, MLP, softmax, cross-entropy / BCE.
3. [03 — Backpropagation](03_Backpropagation.md) — delta rule, sigmoid perceptron, full MLP backprop via the chain rule.

### Part II — Training Deep Networks
4. [04 — Training Deep Networks](04_Training_Deep_Networks.md) — vanishing/exploding gradients, Xavier/He/LeCun init, gradient clipping, over/underfitting, train/val/test, debugging playbook, confusion matrix & metrics.
5. [05 — Regularization and Optimization](05_Regularization_and_Optimization.md) — L1/L2 (weight decay), dropout, momentum, RMSProp, Adam.

### Part III — Computer Vision
6. [06 — Image Processing Fundamentals](06_Image_Processing_Fundamentals.md) — point operations, histogram equalization, linear/nonlinear filters, features, aperture problem, descriptors.
7. [07 — Convolutional Neural Networks](07_Convolutional_Neural_Networks.md) — convolution vs. cross-correlation, channels, 1×1 convs, receptive field, padding, stride, pooling, **CNN backpropagation**.
8. [08 — Modern CNN Architectures](08_Modern_CNN_Architectures.md) — LeNet, AlexNet, VGG, NiN, GoogLeNet, BatchNorm, ResNet, DenseNet, EfficientNet, depthwise-separable / inverted-residual / SE blocks.
9. [09 — Data Augmentation and Transfer Learning](09_Data_Augmentation_and_Transfer_Learning.md) — geometric/photometric augmentation, neural style transfer, transfer-learning strategies.

### Part IV — Sequence Models
10. [10 — Sequence Models and RNNs](10_Sequence_Models_and_RNNs.md) — sequence types, Markov/n-grams, HMM + **Viterbi** worked example, RNN, Elman/Jordan, tanh, BPTT, truncated BPTT, gradient clipping.
11. [11 — LSTM and GRU](11_LSTM_and_GRU.md) — **LSTM gates** (input/forget/output) & cell state, **GRU** (reset/update), deep & bidirectional RNNs, CNN-LSTM, ConvLSTM.
12. [12 — Autoencoders and VAE](12_Autoencoders_and_VAE.md) — AE types (sparse/denoising/contractive), PCA link, U-Net, transposed convolution, **VAE** + reparameterization trick.
13. [13 — Word Embeddings](13_Word_Embeddings.md) — one-hot limits, embeddings, **Word2Vec** (CBOW & Skip-gram).

### Part V — Attention & Transformers
14. [14 — Seq2Seq and Attention](14_Seq2Seq_and_Attention.md) — encoder-decoder, teacher forcing, greedy/beam search, BLEU, attention (Q/K/V), scoring functions, Bahdanau attention.
15. [15 — Transformers](15_Transformers.md) — self-attention, **multi-head attention**, positional encoding, position-wise FFN, Add & Norm (layer norm), masked self-attention, full encoder-decoder walkthrough.
16. [16 — Vision Transformers and LLMs](16_Vision_Transformers_and_LLMs.md) — **ViT** (patch embedding, [CLS], position embeddings, encoder, inductive bias, variants, hybrid/high-res fine-tuning), ViViT (4 variants), large-scale pretraining, **BERT / T5 / GPT**, GPT-3 prompting, fine-tuning.
19. [19 — Two-Tower and Dual Encoders](19_Two_Tower_and_Dual_Encoders.md) — dual-encoder/Siamese architecture, contrastive training (in-batch negatives, InfoNCE), two-tower vs. cross-encoder, **CLIP**, DPR, Sentence-BERT, recommender user/item towers.

### Part VI — Beyond
17. [17 — Explainability and Adversarial](17_Explainability_and_Adversarial.md) — Husky-vs-Wolf, CNN visualization, Grad-CAM, occlusion, adversarial attacks (FGSM).
18. [18 — Energy-Based Models](18_Energy_Based_Models.md) — Hopfield networks, Boltzmann machines, RBMs.

---

## Concept Map

```
Perceptron → MLP → Backpropagation
                       │
        ┌──────────────┼───────────────┐
   Training tricks   Vision          Sequences
   (init, reg,      (CNN → modern    (RNN → LSTM/GRU)
    optimizers)      architectures)        │
                       │                Embeddings
                       │                    │
                       └──── Attention ─────┘
                                │
                          Transformers
                                │
                ┌───────────────┼───────────────┐
              ViT/ViViT    BERT / T5 / GPT   Two-Tower / CLIP
                                            (retrieval, recommenders)

   Cross-cutting: Autoencoders/VAE · Explainability · Energy-based models
```

---

## Recurring Themes

- **The chain rule is everything:** backprop (note 3), CNN backprop (7), BPTT (10/11) are all the same gradient computation applied to different graphs.
- **Vanishing/exploding gradients** recur from sigmoids (3/4) to deep nets (4) to RNNs (10) — and the fixes (good init, clipping, residual connections, LSTM/GRU additive memory, layer norm) all attack the same problem.
- **Residual / skip connections** appear in ResNet (8), DenseNet (8), U-Net (12), and Transformer Add & Norm (15) — gradient highways.
- **Attention** evolves from a fix for the seq2seq bottleneck (14) into the entire architecture (15) and then all of modern AI (16).
- **Normalization** (BatchNorm in 8, LayerNorm in 15) stabilizes training by controlling activation distributions.

---

## Source Material

- **CS F425 Deep Learning** — 18 lecture decks + course handout.
- Detailed **Intro** and **CNNs** lecture notes.
- Reference texts: *Dive into Deep Learning* (Zhang et al.), *Introduction to Deep Learning* (Skansi), *Neural Networks and Learning Machines* (Haykin), *Fundamentals of Neural Networks* (Fausett).
