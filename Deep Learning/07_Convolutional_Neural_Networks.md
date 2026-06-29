# Convolutional Neural Networks (CNNs)

CNNs are the workhorse of computer vision. They replace dense connectivity with **shared, local filters**, which gives translation invariance and dramatically fewer parameters. This note covers the convolution operation, channels, 1×1 convolutions, padding, stride, pooling, and how backpropagation works through a convolutional layer.

---

## 1. Why CNNs? Invariance and Parameter Sharing

An object (e.g. a cat) is a cat regardless of **where** it appears in the image. A CNN, by construction, is **less concerned with the exact position** of the object than a dense network. We also want robustness to:

- **Size / scale** (object large or small),
- **Illumination** (changing lighting),
- **Rotation / viewpoint** (object rotated or seen from different angles).

**CNNs use filters** (small sets of weights) that are **shared across all spatial locations** of the image. By contrast, **dense (fully connected)** networks have separate weights for every pixel-neuron pair, which:

- **Explodes the number of parameters** for images.
- Does **not exploit spatial structure** or translation invariance — features learned in one part of the image won't generalize to another part.

---

## 2. The Convolution Operation

**Continuous:**
$$
(f \ast g)(x) = \int f(z)\, g(x - z)\, dz
$$

**Discrete:**
$$
(f \ast g)(i) = \sum_a f(a)\, g(i - a)
$$

This is the **"flip and slide"** definition: one function ($g$, the kernel) is flipped and slid over the other ($f$, the signal). At each position, the integral/sum measures the **overlap** between $f$ and the flipped $g$.

### 2D Convolution

For images, we slide a 2D kernel over the 2D image, computing a weighted sum at each location:

$$
O(i, j) = \sum_{m}\sum_{n} I(i + m,\ j + n)\, K(m, n)
$$

### Cross-Correlation vs. Convolution

True convolution **flips** the kernel; **cross-correlation** does not. In deep learning, kernels are **learned from data**, so the outputs of convolutional layers are **unaffected** by whether we use strict convolution or cross-correlation — the network just learns the (possibly flipped) kernel. Most frameworks implement cross-correlation but call it "convolution."

### Edge Detection Example

A simple kernel like $[1, -1]$ detects vertical edges: the output is $+1$ for an edge going from white to black, $-1$ for black to white, and $0$ everywhere the intensity is constant.

---

## 3. Multiple Input and Output Channels

### Multiple Input Channels (e.g. RGB)

A separate 2D kernel is applied to **each input channel** (R, G, B), and the results are **summed** to produce a single output value at each location. The kernel is therefore **3D**: shape $(k_h, k_w, \text{input\_channels})$. For an RGB image with a 3×3 kernel, the true kernel shape is $(3, 3, 3)$. This lets the network learn features that combine information across color channels.

### Multiple Output Channels

To produce multiple **feature maps**, simply use **multiple different kernels** — each produces its own 2D output map, and these maps are **stacked** along a new dimension to form the output volume of shape $(\text{height}, \text{width}, \text{output\_channels})$.

Each output channel can learn to detect a different feature (horizontal edges, vertical edges, red blobs, …), which dramatically increases the layer's representational power. As we go **deeper**, we typically increase channel depth while **downsampling** spatially — trading spatial resolution for channel richness. (Channels are optimized to be **jointly** useful: a feature like "edge" may correspond to a direction in channel space rather than a single channel.)

---

## 4. 1×1 Convolutions

A convolution with a $1\times1$ kernel. It **cannot** integrate spatial information (it looks at one pixel at a time), but it operates **across all channels**. Uses:

- **Dimensionality reduction:** cheaply reduce the number of channels (e.g. 256 → 64).
- **Add nonlinearity:** a 1×1 conv followed by ReLU adds cheap nonlinearity.
- **Pooling across channels:** learns a weighted linear combination of input channels at each pixel.

A 1×1 conv is essentially a fully connected layer applied independently at each pixel location. It is a key building block in efficient architectures like GoogLeNet and ResNet.

---

## 5. Feature Map and Receptive Field

- **Feature map:** the output of a convolutional layer. Each feature map summarizes the presence of a particular pattern (edge, texture, …) detected by a learnable filter across all spatial locations. Multiple filters → multiple feature maps.
- **Receptive field:** all input elements (from all previous layers) that can influence a given neuron's value. **Shallow** layers have a **small** receptive field (set by kernel size); **stacking** layers **enlarges** the receptive field, letting deeper neurons "see" larger, more complex patterns. To detect features over a broader area, build a **deeper** network.

---

## 6. Padding

In convolution, the kernel must be placed over every part of the image, **including the borders**. **Padding** adds extra values around the input border so the filter can process edge pixels without missing them or shrinking the output too much.

**Padding types:**

- **Zero:** pixels outside the image are 0. Most common ("zero padding"). Preserves size, simple, but can introduce artificial black borders.
- **Constant (border color):** outside pixels set to a chosen constant (not necessarily 0).
- **Clamp (replicate / clamp to edge):** border pixels repeat the value of the nearest edge pixel. Avoids sharp jumps at edges.
- **Cyclic (wrap / tile):** image wraps like a torus (left edge takes from right, top from bottom). Useful for periodic patterns.
- **Mirror (reflect):** outside pixels reflect across the edge. Minimizes edge effects and preserves patterns.

**Output size** with input $n$, kernel $k$, padding $p$, stride $s$:
$$
\text{out} = \left\lfloor \frac{n + 2p - k}{s} \right\rfloor + 1
$$

---

## 7. Stride

Sometimes — for computational efficiency or to **downsample** — we move the window more than one element at a time, **skipping** intermediate locations. This is the **stride**. A larger stride reduces the output size and is especially useful with large kernels (which already capture a large area).

---

## 8. Pooling

Like convolutional layers, pooling slides a fixed-shape window over the input according to its stride, computing **one output per location**. Key differences from convolution:

- Pooling has **no parameters** (no kernel) — it is **deterministic** (e.g. **max** pooling outputs the maximum, **average** pooling the mean).
- Pooling operates on **each channel separately**, rather than summing across channels as convolution does.

We can pad and adjust the stride of pooling to get a desired output shape. Pooling provides a degree of **translation invariance** and reduces spatial resolution (and computation).

---

## 9. Backpropagation in a Convolutional Layer

To train a CNN we need the gradient of the loss $L$ w.r.t. (a) the **filter** weights and (b) the **input** (to pass gradients to earlier layers).

Setup: convolution between input $X$ and filter $F$ gives output $O$ (horizontal/vertical stride = 1).

### Gradient w.r.t. the filter

Expanding the chain rule over all output positions and substituting the local gradients, the gradient of the loss w.r.t. the filter turns out to be a **convolution between the input $X$ and the loss gradient $\partial L / \partial O$**:

$$
\frac{\partial L}{\partial F} = X \ast \frac{\partial L}{\partial O}
$$

### Gradient w.r.t. the input

Similarly expanding the chain rule for the input, the gradient w.r.t. the input is a **"full" convolution** between the **180°-rotated filter** $F$ and the loss gradient $\partial L / \partial O$:

$$
\frac{\partial L}{\partial X} = \text{rot}_{180}(F) \;\ast_{\text{full}}\; \frac{\partial L}{\partial O}
$$

**Full convolution** differs from **valid** convolution by padding so the output size matches the input size — ensuring every input pixel's gradient **accumulates** contributions from all related output gradients.

> Beautiful symmetry: the **forward** pass is a (valid) convolution, and the **backward** pass (input gradient) is a **full** convolution with the **flipped** kernel.

---

## Key Takeaways

- CNNs exploit **translation invariance** + **parameter sharing** → far fewer parameters than dense nets and features that generalize across the image.
- Convolution = flip-and-slide weighted sum; in DL, conv vs. cross-correlation doesn't matter (kernels are learned).
- Kernels span **all input channels** (3D); using many kernels yields many **output channels** (feature maps). **1×1 convs** mix channels cheaply and add nonlinearity.
- **Padding** controls output size and edge handling; **stride** downsamples; **pooling** (max/avg) downsamples without parameters, per channel.
- Receptive field grows with depth — go **deeper** to see larger patterns.
- CNN backprop: filter gradient $= X \ast \partial L/\partial O$; input gradient $=$ full conv of the **180°-rotated** filter with $\partial L/\partial O$.
