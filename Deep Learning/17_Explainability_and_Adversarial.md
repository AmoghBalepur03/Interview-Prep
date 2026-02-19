# Explainability and Adversarial Attacks

Deep networks are powerful but **opaque**. This note covers why explainability matters (the Husky-vs-Wolf cautionary tale), techniques to **visualize and interpret** trained CNNs (filter visualization, activation maximization, Grad-CAM, occlusion), and how the same gradients can be used to **fool** networks (adversarial attacks).

---

## 1. Why Explainability? The Husky vs. Wolf Experiment

A logistic-regression classifier was trained on 20 hand-selected images where **all wolves had snow in the background** and **huskies did not**. On 60 additional images, the classifier predicted **"Wolf" if there was snow** (or a light background at the bottom) and **"Husky" otherwise** — regardless of the animal's color, position, or pose.

**The experiment:** present a balanced set of 10 test predictions, including one wolf **not** in snow (predicted Husky) and one husky **in** snow (predicted Wolf). Subjects are then asked whether they **trust** the algorithm.

**The lesson:** the model learned a **spurious correlation** (snow) rather than the actual concept (the animal). A high-accuracy model can be right **for the wrong reasons** — explainability tools are needed to **detect** this before deployment.

---

## 2. Visualizing Trained CNNs

### First-layer filters

For filters in the **first** convolutional layer, visualization is straightforward: they correspond to small patches in the **original input image space**, so we can visualize the network weights directly as small images (they typically look like edge/color detectors).

### Deeper layers

Subsequent layers are **harder** to interpret, because their inputs are **not** image patches but **groups of filter responses** from the previous layer.

### Activation-maximizing patches

Present a large number of image patches to the network and see which produce the **highest activation** in a particular hidden unit. This reveals a natural **hierarchical structure**:

- **Layer 1:** responds to **edges**.
- **Layer 2:** responds to **textures and simple shapes**.
- **Layer 3:** shows **components of objects** (e.g. wheels).
- **Layer 5:** shows **entire objects**.

### Numerical activation maximization

Extend the technique beyond selecting validation patches: perform a **numerical optimization over the input pixels** to **maximize** the activation of a particular unit. If the unit is an **output** unit, we find the image most representative of that class label.

- Because output units use **softmax**, it is better to maximize the **pre-activation** (logit) feeding the softmax rather than the class probability directly — this ensures the optimization depends on only **one** class.
- Some form of **regularization** is required to find solutions that look closer to natural images (otherwise they become noisy/adversarial-looking).

---

## 3. Grad-CAM (Gradient-weighted Class Activation Mapping)

Grad-CAM produces a **heatmap** showing which regions of an image were most important for a particular class prediction. It uses the **gradients** of the target class score flowing into the **final convolutional layer** to weight that layer's feature maps, then combines them (with a ReLU) into a coarse localization map. This highlights "where the network looked" to make its decision — directly useful for catching Husky/Wolf-style failures.

---

## 4. Occlusion-Based Explanation (Average Removal / Aggregation)

Probe importance by **removing** or **aggregating** parts of the image and observing the change in the model's output (here, for face verification).

- **Single removal:** remove a **circular region** of image $B$ centered at $(i, j)$ and see how the score changes.
- **Greedy removal ($S_1^-$):** repeat the single-removal procedure **iteratively**, removing the most impactful region each time.
- **Single aggregation:** **aggregate** a circular region of $B$ onto a black image (keep only that region).
- **Greedy aggregation:** repeat the single-aggregation procedure several times, each iteration adding the **most relevant** part of image $B$.

These produce maps of which regions are most responsible for a decision (model-agnostic, "black-box" explanation).

---

## 5. Adversarial Attacks

Gradients **w.r.t. the input image pixels** can also be used to **attack** convolutional networks. Adversarial attacks make **very small modifications** to an image — at a level **imperceptible to a human** — that cause the image to be **misclassified** by the network.

### Fast Gradient Sign Method (FGSM)

(Goodfellow, Shlens & Szegedy, 2014.) Change each pixel value in an image $x$ by a **fixed amount** $\epsilon$, with the **sign** determined by the gradient of an error function $E(x; t)$ w.r.t. the pixel values:

$$
x_{\text{adv}} = x + \epsilon \cdot \text{sign}\big(\nabla_x E(x; t)\big)
$$

By nudging **every** pixel in the direction that **increases** the loss, the cumulative effect flips the prediction while each individual change stays tiny. This exposes a serious robustness/security concern for deployed vision systems.

---

## Key Takeaways

- **Explainability matters:** the Husky/Wolf experiment shows a model can be accurate but rely on **spurious correlations** (snow) — trust requires understanding *why*.
- **First-layer filters** visualize directly (edges/colors); deeper units are probed via **activation-maximizing patches**, revealing a hierarchy edges → textures → parts → objects.
- **Activation maximization** optimizes the input to excite a unit (maximize the pre-softmax logit, with regularization). **Grad-CAM** uses class gradients to make a "where it looked" heatmap. **Occlusion** (removal/aggregation) gives model-agnostic importance maps.
- **Adversarial attacks** (e.g. **FGSM**, $x + \epsilon\,\text{sign}(\nabla_x E)$) use input gradients to cause imperceptible misclassification — a key robustness concern.
