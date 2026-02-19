# Autoencoders and Variational Autoencoders (VAE)

Autoencoders learn to **compress and reconstruct** data without labels (unsupervised representation learning). This note covers autoencoder types, the connection to PCA, the U-Net encoder-decoder, transposed convolution (learnable upsampling), and the **Variational Autoencoder** with the reparameterization trick.

---

## 1. Autoencoders

An autoencoder is a neural network trained to **reconstruct its input**: an **encoder** compresses the input into a low-dimensional **latent code**, and a **decoder** reconstructs the input from that code.

**The bottleneck constraint:** if the hidden layer has **at least as many** neurons as the input/output layers, the autoencoder is in danger of learning the trivial **identity function**. So a **simple autoencoder** must have **fewer** neurons in the hidden layer than in the input/output layers — forcing it to learn a compressed, meaningful representation.

### Autoencoder architectures

- **Simple (undercomplete) autoencoder:** hidden layer smaller than input → forced compression.
- **Sparse autoencoder:** constrain the hidden neurons to be **at most double** the input neurons, but add a **heavy dropout** (e.g. 0.7). They digest inputs like simple autoencoders but additionally learn redundancies, offering a more "diluted" and bigger vector that is even simpler to process.
- **Denoising autoencoder:** create a **noisy copy** of the input; the **targets are the clean** (noise-free) inputs. This forces the model to learn robust features (to remove noise) rather than copy.
- **Contractive autoencoder:** add **explicit regularization** (a penalty on the sensitivity of the encoding to input changes), making the representation robust to small input perturbations.

### Applications

Autoencoders are used for **semi-supervised learning** (pretrain on unlabeled data, then fine-tune with few labels), dimensionality reduction, denoising, anomaly detection, and as generative building blocks.

---

## 2. Connection to PCA

**Principal Component Analysis (PCA)** reduces dimensionality while preserving crucial information by transforming the original variables into a set of new, **uncorrelated** variables called principal components.

A **linear** autoencoder (no nonlinear activations) with a squared-error loss learns essentially the **same subspace as PCA** — the latent code spans the principal components. The advantages of (nonlinear) autoencoders over PCA:

- **Nonlinearity:** can capture **nonlinear** manifolds that PCA (a linear projection) cannot.
- **Outliers:** flexible architectures and losses can be made more robust than PCA to nonlinearity and outliers.

So an autoencoder is a nonlinear generalization of PCA, learning a distributed representation similar to (but richer than) principal components.

---

## 3. U-Net

U-Net is a **U-shaped encoder-decoder** architecture, consisting of four **encoder** blocks and four **decoder** blocks connected via a **bridge**.

- **Encoder:** the feature extractor — learns an abstract representation of the input image through a sequence of encoder blocks (downsampling).
- **Decoder:** takes the abstract representation and generates a (semantic segmentation) **mask** by upsampling.
- **Skip connections:** provide features from **earlier** layers that would otherwise be lost due to the depth of the network — concatenating encoder features into the matching decoder stage. This preserves fine spatial detail for precise segmentation.

---

## 4. Upsampling and Transposed Convolution

The decoder must **upsample** (increase spatial resolution). Options:

- **Nearest-neighbor interpolation:** copy the nearest pixel value (fast, blocky).
- **Bilinear interpolation:** weighted average of nearby pixels (smoother).
- **Transposed convolution:** a **learnable** upsampling operation.

### Transposed Convolution (a.k.a. "deconvolution")

Regular convolution can be expressed as a **matrix multiplication**: instead of a 3×3 kernel, express it as a (sparse) matrix (e.g. 4×16) and the input as a vector (e.g. 16×1); the reshaped output equals the normal convolution result.

**Transposed convolution forms the same connectivity as normal convolution but in the backward direction** — it maintains the same 1-to-9 relationship because of how it lays out the weights (using the transpose of the convolution matrix). The best way to perform up-convolutions:

1. **Expand and duplicate** each element from the input feature map to the same size as the filter (this up-samples the input).
2. Apply the **filter** over each of these expanded regions.

If we rearrange the intermediate vectors, we get matrices identical to multiplying the kernel with each individual input element (extra slots filled with zeros), which combine to yield the final upsampled output. Unlike fixed interpolation, the transposed-conv kernel is **learned**.

---

## 5. Variational Autoencoder (VAE)

A regular autoencoder's latent space has **no structure** — encoded points can be scattered arbitrarily, so sampling a random latent point and decoding it usually gives garbage. The VAE fixes this for **generation**.

**Definition:** a VAE is an autoencoder whose training is **regularized** to avoid overfitting and to ensure the latent space has good properties that **enable a generative process**.

### The VAE process

1. The input is **encoded as a distribution** over the latent space (the encoder outputs a mean $\mu_x$ and variance $\sigma_x$).
2. A **point is sampled** from that distribution.
3. The sampled point is **decoded**, and the reconstruction error is computed.
4. The reconstruction error is **backpropagated** through the network.

### The VAE Loss Function

Two terms:

$$
\text{loss} = \underbrace{\|x - \hat x\|^2}_{\text{reconstruction}} + \underbrace{\text{KL}\big[\,\mathcal{N}(\mu_x, \sigma_x)\,\|\,\mathcal{N}(0, I)\,\big]}_{\text{regularization}}
$$

- **Reconstruction loss** $\|x - \hat x\|^2$: forces the model to accurately reconstruct the input (measures how close the reconstruction is to the original).
- **KL-divergence term:** measures how far the learned latent distribution $\mathcal{N}(\mu_x, \sigma_x)$ is from a **standard normal** $\mathcal{N}(0, I)$. It encourages the latent variables to be **well-spread** and to allow **smooth sampling** (so nearby latent points decode to similar, valid outputs).

KL-divergence:

$$
\text{KL}(P\|Q) = \sum_n P(n)\log\frac{P(n)}{Q(n)}
$$

where $P$ is the learned distribution for the input and $Q$ is the prior $\mathcal{N}(0, I)$. Enforcing the latent distributions to be close to a standard normal is what gives the latent space its smooth, sample-able structure.

### The Reparameterization Trick

**Problem:** sampling $z \sim \mathcal{N}(\mu_x, \sigma_x)$ is a **stochastic** operation — you cannot backpropagate gradients through random sampling.

**Trick:** move the randomness **outside** the differentiable path by writing:

$$
z = \mu_x + \sigma_x \odot \epsilon, \qquad \epsilon \sim \mathcal{N}(0, I)
$$

Now $\epsilon$ is the only random part (an external input), and $z$ is a **deterministic, differentiable** function of $\mu_x$ and $\sigma_x$. Gradients can flow through $\mu_x$ and $\sigma_x$ to train the encoder, making the VAE trainable end-to-end with backpropagation.

---

## Key Takeaways

- **Autoencoders** learn compressed representations by reconstructing input; the **bottleneck** (smaller hidden layer) prevents learning the identity. Variants: **sparse** (dropout), **denoising** (clean targets from noisy inputs), **contractive** (explicit regularization).
- A **linear** autoencoder ≈ **PCA**; nonlinear autoencoders generalize PCA to nonlinear manifolds and are more robust to outliers.
- **U-Net** is an encoder-decoder with **skip connections** for segmentation; the decoder **upsamples** via interpolation or learnable **transposed convolution**.
- A **VAE** encodes inputs as **distributions**, regularizes the latent space toward $\mathcal{N}(0, I)$ via a **KL term** (+ reconstruction loss), and uses the **reparameterization trick** ($z = \mu + \sigma\epsilon$) to backpropagate through sampling — enabling generation.
