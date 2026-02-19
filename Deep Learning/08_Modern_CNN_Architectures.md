# Modern CNN Architectures

This note traces the evolution of convolutional architectures — from LeNet (1995) through the ImageNet revolution (AlexNet 2012) to VGG, NiN, GoogLeNet, BatchNorm, ResNet, DenseNet, EfficientNet, and the efficient mobile blocks (depthwise-separable, inverted residuals, squeeze-and-excitation).

---

## 1. LeNet (1995)

The original successful CNN. At a high level, LeNet-5 has two parts:

1. A **convolutional encoder**: two convolutional blocks. Each block = a convolutional layer (5×5 kernel) + a **sigmoid** activation + an **average pooling** (2×2) operation. The first conv layer has **6** output channels, the second **16**.
2. A **dense block**: three fully connected layers ending in the classifier.

---

## 2. ImageNet and the Current Trend

**ImageNet** (released 2009) is one of the largest and most influential datasets in computer vision — a standardized, massive collection of labeled images for training and benchmarking object recognition. The **ILSVRC** subset:

- 1,281,167 training images, 50,000 validation, 100,000 test,
- **1000 object classes** (~1000 images each).
- Key metrics: **top-1** and **top-5** accuracy.

The challenge drove a paradigm shift: in **2012**, the deep CNN **AlexNet** dramatically outperformed all others, sparking the modern deep-learning revolution for vision.

**Current trend (empirical findings):**

- **Deeper models work better.**
- Increasing the **number of parameters** without increasing **depth** is **not** effective at improving test performance.
- Shallow models overfit at ~20M parameters, while deep ones benefit from 60M+.
- A model performs better when **architected to reflect a composition of simpler functions** rather than a single complex function.

---

## 3. AlexNet (2012)

An 8-layer CNN that won ILSVRC 2012.

- Much **deeper** than LeNet-5.
- Used **ReLU** instead of sigmoid as the activation (faster training, less saturation).
- Has **10× more** convolution channels than LeNet.
- Originally used a **dual data stream** design so each of two GPUs stored/computed half the model.

---

## 4. VGG (2014)

Introduced the idea of building networks from a repeated **building block**:

1. A convolutional layer with **padding** to maintain resolution,
2. A **nonlinearity** (ReLU),
3. A **pooling** layer (max-pooling) to reduce resolution.

VGG showed in detailed analysis that **deep and narrow** networks (many small 3×3 convs) significantly **outperform** shallow, wide ones.

---

## 5. Network in Network (NiN, 2013)

**Motivation:** the fully connected layers at the end of VGG consume a **tremendous** number of parameters, and you can't add FC layers earlier to increase nonlinearity.

**NiN's two insights:**

1. Use **1×1 convolutions** to add local nonlinearities across the channel activations (a fully connected layer applied independently at each pixel location).
2. Use **global average pooling** to integrate across all locations in the last representation layer (replacing the giant FC head).

NiN uses the same initial convolution sizes as AlexNet but **avoids fully connected layers altogether**.

---

## 6. GoogLeNet (2015)

- Arguably the first network with a clear distinction among **stem** (data ingest), **body** (data processing), and **head** (prediction).
- Instead of trying to pick the single best convolution size (1×1 to 11×11), it simply **concatenated multi-branch convolutions** — the **Inception** module runs several kernel sizes in parallel and concatenates their outputs (using 1×1 convs to keep the cost down).

---

## 7. Batch Normalization

A popular, effective technique that consistently **accelerates convergence** of deep networks. It confers three benefits: **preprocessing, numerical stability, and regularization**.

In each training iteration, batch norm:

1. **Normalizes** the layer inputs by subtracting their **mean** and dividing by their **standard deviation**, both estimated from the **current minibatch**.
2. Applies a learnable **scale** ($\gamma$) and **offset** ($\beta$) to recover the lost degrees of freedom.

$$
\hat x = \frac{x - \mu_B}{\sqrt{\sigma_B^2 + \varepsilon}}, \qquad y = \gamma \hat x + \beta
$$

It derives its name precisely because the normalization is based on **batch statistics**.

**Limitations:** when the **batch size is small**, the sample mean/std are not representative and the network learns poorly. It is also **less suited for sequence models** (variable-length sequences, small effective batches per length) — motivating **Layer Normalization** (see the Transformers note).

---

## 8. ResNet — Residual Networks

**The degradation problem:** increasing depth generally improves learning, but after a certain depth, **adding more layers makes performance worse** — even though a deeper net contains the shallower one as a subnetwork (so in theory it shouldn't be worse). This is **not** overfitting; it arises because deep models are **harder to optimize** (gradients vanish, explode, or become uninformative across many stacked nonlinearities).

**Solution — skip (shortcut) connections:** pass the input of a block directly to a deeper layer, bypassing intermediate layers. The block learns a **residual function**:

$$
y = F(x) + x
$$

- $x$: input to the block.
- $F(x)$: output of the stacked layers (e.g. BatchNorm → Conv → ReLU → BatchNorm → Conv).
- The skip connection adds $x$ to $F(x)$.

This makes it easy to learn the **identity** (just push $F(x) \to 0$), so extra layers never hurt. The loss landscape becomes **smoother, less steep, and less dependent on the starting point**, enabling networks with hundreds of layers.

---

## 9. DenseNet — Densely Connected Networks

What if we want to **capture** (not just add) information beyond two terms? DenseNet does this.

**Key difference — concatenation vs. addition:**

- In **ResNet**, outputs are **added** (summed) via skip connections → only the **sum** of previous features is available at each layer.
- In **DenseNet**, outputs are **concatenated** (stacked along the channel dimension, denoted $[\,,\,]$). Each layer receives the **original input and the outputs of all earlier layers** → every layer has access to a richer set of feature maps.

$$
x_\ell = H_\ell\big([\,x_0, x_1, \dots, x_{\ell-1}\,]\big)
$$

---

## 10. EfficientNet — Compound Scaling

EfficientNet addresses the trade-off between **model size, accuracy, and computational efficiency** with **compound scaling**: rather than scaling just one dimension, it scales **depth, width, and resolution together** in a balanced way.

- **Wider** models capture more complex patterns.
- **Deeper** models capture more intricate representations.
- **Higher-resolution** images provide more detailed information.

$$
\text{depth} = \alpha^\phi, \quad \text{width} = \beta^\phi, \quad \text{resolution} = \gamma^\phi
$$

The exponents ($\alpha, \beta, \gamma$) are found by an **empirical grid search** to give the best accuracy/efficiency trade-off, and $\phi$ controls the overall scaling budget.

---

## 11. Efficient Building Blocks (Mobile Architectures)

### Depthwise Separable Convolution

Factorizes a standard convolution into two cheaper steps:

1. **Depthwise:** apply convolution to **a single channel at a time** (unlike standard convs that mix all $M$ channels).
2. **Pointwise:** apply a **1×1 convolution** across the $M$ channels.

This drastically reduces computation and parameters versus a standard conv (used in MobileNet, Xception).

### Inverted Residual Blocks

Follow a **narrow → wide → narrow** structure (the opposite of a classic bottleneck):

1. **Expansion:** increase the number of feature maps with a 1×1 conv.
2. **Depthwise convolution:** a 3×3 depthwise conv bottleneck.
3. **Projection:** shrink feature maps back to the original number with a 1×1 conv.

The residual (skip) connection links the narrow ends (used in MobileNetV2).

### Squeeze-and-Excitation (SE) Block

A standard residual block weights **all channels equally** when forming output feature maps. SE blocks add a **content-aware mechanism** to weight each channel **adaptively**:

- **Squeeze:** global average pool each channel to a single number.
- **Excitation:** learn per-channel scalar weights (how relevant each channel is) and rescale the channels accordingly.

This lets the network emphasize informative channels and suppress less useful ones.

---

## Key Takeaways

- **LeNet** (conv + pool + FC) → **AlexNet** (deeper, ReLU, GPUs) → **VGG** (small 3×3 blocks, deep & narrow wins).
- **NiN** uses 1×1 convs + global average pooling to kill the huge FC head; **GoogLeNet/Inception** concatenates multi-scale branches.
- **BatchNorm** normalizes per-minibatch (+ learnable $\gamma, \beta$) to speed up and stabilize training; weak for small batches / sequences.
- **ResNet** solves the degradation problem with residual skip connections ($y = F(x) + x$); **DenseNet** concatenates all earlier features.
- **EfficientNet** balances depth/width/resolution via compound scaling; **depthwise-separable convs, inverted residuals, and SE blocks** make networks efficient and channel-aware.
