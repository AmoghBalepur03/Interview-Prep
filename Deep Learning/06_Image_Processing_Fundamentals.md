# Image Processing Fundamentals

Before CNNs, classical computer vision processed images with hand-designed operations: point operations, histogram methods, linear/nonlinear filters, and feature detectors. These ideas (especially convolution-as-filtering and the notion of "good features") directly motivate the design of convolutional networks.

---

## 1. Point Operations

The simplest form of image processing: each output pixel depends **only** on the corresponding input pixel.

$$
g(i, j) = h\big(f(i, j)\big)
$$

The new image $g$ is created by applying a function $h$ to every pixel $(i, j)$ of the original image $f$.

### Linear point operations (gain and bias)

The most common point process is a linear transform:

$$
g(x) = a\, f(x) + b
$$

- **Gain $a > 0$:** controls **contrast** ($a > 1$ increases contrast, $a < 1$ decreases it).
- **Bias $b$:** controls **brightness** ($b > 0$ brightens, $b < 0$ darkens).

The two commonly used point processes are multiplication and addition with a constant.

---

## 2. Color Transforms

How do we automatically pick the best gain/bias? Two strategies:

- Find the **darkest and brightest** pixel values and map them to pure black and pure white (contrast stretching).
- Find the **average** value, push it toward middle gray, and expand the range so it more closely fills the displayable values.

---

## 3. Histogram Equalization

A **histogram of intensity values** lets us compute statistics (min, max, average intensity).

**Goal:** find an intensity mapping function $f(I)$ such that the resulting histogram is **flat** (uniform usage of all intensity levels → maximizes global contrast).

**Method:** use the **cumulative distribution** $c(I)$ of intensities and apply $f(I) = c(I)$ to the original image.

$$
c(I) = \sum_{k=0}^{I} p(k), \qquad g = f(I) = c(I)
$$

### Local (Adaptive) Histogram Equalization

A single global curve fails when an image has a wide range of luminance values. Instead, subdivide the image into $M \times M$ pixel blocks and equalize **each sub-block** separately.

To eliminate **blocking artifacts**, use a **moving window**: recompute the histogram for every $M \times M$ block centered at each pixel. (This is the idea behind CLAHE.)

---

## 4. Linear Filtering

Locally adaptive histogram equalization is an example of a **neighborhood operator** (local operator). The most common neighborhood operator is a **linear filter**: an output pixel's value is a **weighted sum** of input pixel values in a neighborhood.

$$
g(i, j) = \sum_{k, l} f(i + k,\ j + l)\, h(k, l)
$$

where $h$ is the filter/kernel. This is exactly **correlation / convolution** — the operation at the heart of CNNs.

### Separable Linear Filters

A 2D filter is **separable** if it can be written as the outer product of two 1D filters: $h(k, l) = h_1(k)\,h_2(l)$. Then a 2D convolution can be done as two 1D convolutions (one along rows, one along columns), reducing cost from $O(M^2)$ to $O(2M)$ per pixel. (E.g. the 2D Gaussian and the box/Sobel filters are separable.)

---

## 5. Nonlinear Filters

Filters where the output is **not** a simple weighted sum.

### The Median Filter

The most important nonlinear filter. For each pixel, look at its $M \times M$ neighborhood, **sort** all pixel values, and output the **median** (the middle value).

- **Example:** a 3×3 patch with a very bright pixel (255) that is noise (a "salt" pixel). A linear (blur) filter would smear this bright value into its neighbors; the median filter completely removes it (choosing, say, 101 instead).
- **Use case:** excellent for removing **salt-and-pepper noise** while **preserving sharp edges** — something linear blur filters cannot do.

---

## 6. Morphology

Morphological operations (erosion, dilation, opening, closing) operate on shapes, often applied **after thresholding** to clean up binary images — removing small specks, filling holes, and smoothing object boundaries using a structuring element.

---

## 7. Points, Patches, and the Aperture Problem

What makes a patch "interesting" enough to reliably find again in another image?

- **Textureless patches:** nearly impossible to localize. A uniform patch (e.g. a white wall) looks the same everywhere it moves.
- **Edge patches:** easier to find, but suffer the **aperture problem** — you can only align/track motion in the direction **normal** (perpendicular) to the edge; motion **along** the edge is invisible (like a pencil sliding along its length).
- **Corner patches:** patches with gradients in **at least two significantly different orientations** (corners, spots, textured areas) are the **easiest to localize** — their motion is unambiguous in all directions. These are stable features.

---

## 8. Feature Detection, Descriptors, and Matching

### Feature detectors

The simplest matching criterion for comparing two image patches is their (weighted) **summed square difference (SSD)**:

$$
E(u) = \sum_{i} w(x_i)\,\big[\,I_1(x_i + u) - I_0(x_i)\,\big]^2
$$

A patch is a good feature if this error rises sharply for **any** shift $u$ (i.e. it is a corner). This leads to corner detectors (Harris, etc.).

### Feature descriptors

Once a good keypoint is detected, we describe it so it can be matched. A common descriptor is a **histogram of all the gradient orientations** in the patch (the basis of SIFT/HOG): robust to small shifts and illumination changes.

### Matching

Compare descriptors between images (e.g. by nearest neighbor in descriptor space) to find correspondences — the foundation of stitching, tracking, and structure-from-motion.

---

## Key Takeaways

- **Point operations** ($g = af + b$) adjust contrast (gain) and brightness (bias) pixel-by-pixel.
- **Histogram equalization** flattens the intensity histogram via the CDF; do it locally (with a moving window) for images with varied lighting.
- **Linear filters** = weighted-sum neighborhoods = convolution (the core CNN op); **separable** filters split a 2D conv into two cheap 1D convs.
- The **median filter** (nonlinear) removes salt-and-pepper noise while keeping edges sharp.
- Good features are **corners** (gradients in 2+ directions); edges suffer the **aperture problem**, textureless regions are unlocalizable. Describe keypoints with gradient-orientation histograms and match across images.
