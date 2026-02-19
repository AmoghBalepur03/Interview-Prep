# Energy-Based Models: Hopfield Networks, Boltzmann Machines, and RBMs

Energy-based models define a scalar **energy** for each configuration of the network and learn so that desired patterns sit at **low-energy minima**. This note covers Hopfield networks (associative memory), Boltzmann machines (stochastic, with hidden units), and Restricted Boltzmann Machines (RBMs).

---

## 1. Hopfield Networks

A Hopfield network is a form of **associative (content-addressable) memory**.

**Structure:**

- Made of neurons, all **connected to each other** with weights $w_{ij}$ connecting neurons $n_i$ and $n_j$.
- Each neuron has an associated **threshold** $b_i$.
- All neurons hold a value of **$+1$ or $-1$** (bipolar).

**Behavior:** the network is first **prepared to store** various patterns/memories. Afterward, given **partial or even corrupted** data about a pattern, it can **recognize** it — it eventually **settles down** and restores the **closest** stored pattern.

### Synchronous vs. Asynchronous Updates

There are two ways to update:

- **Synchronous:** update all neurons at the same time.
- **Asynchronous:** update one by one — this is the **standard** way.

Constraints in Hopfield networks:

- **No self-connections:** $w_{ii} = 0$ for all $i$ (no recurrent self-loops).
- **Symmetric weights:** $w_{ij} = w_{ji}$.

### Energy Function

Hopfield networks have a global measure of success (analogous to the error function of regular networks) called the **energy**:

$$
E = -\frac{1}{2}\sum_{i}\sum_{j} w_{ij}\, s_i\, s_j + \sum_i b_i\, s_i
$$

where $s_i \in \{-1, +1\}$ is the state of neuron $i$. As learning/updating progresses, the energy $E$ either **stays the same or diminishes** — this is how Hopfield networks reach **local minima**. **Each local minimum is a memory** of some training sample(s). (A "flip-flop" Hopfield network is a small example storing a couple of stable states.)

### Problems with Hopfield Networks

- **Limited capacity:** about **$0.14N$** patterns for $N$ neurons.
- **Ghost patterns:** reversed (negated) images are also stable states.
- **Spurious patterns:** bad separation of patterns creates unintended minima.
- **Retrieval is not error-free.**

---

## 2. Boltzmann Machines

At first glance very similar to Hopfield networks, but with two key additions:

- They have **input neurons and hidden neurons**, all interconnected with weights.
- These weights are **non-recurrent and symmetrical**.

**How it works:** hidden units are initialized **at random** and build a **hidden representation** to mimic the inputs. This forms **two probability distributions** (the data distribution and the model's distribution), which can be compared with the **Kullback-Leibler (KL) divergence**. Training adjusts weights to minimize this divergence.

Boltzmann machines are **stochastic** (neurons turn on/off probabilistically based on energy), unlike the deterministic Hopfield update. The hidden units let them model richer distributions, but full Boltzmann machines are **slow to train** (dense connectivity).

---

## 3. Restricted Boltzmann Machines (RBM)

Structurally, RBMs are just Boltzmann machines **with no connections between neurons of the same layer** (no hidden-to-hidden and no visible-to-visible connections).

- This seemingly minor restriction is significant: it makes it possible to use a **modification of backpropagation** (efficient training, e.g. **Contrastive Divergence**).
- The RBM therefore has **two layers**: a **visible** layer and a **hidden** layer.
- The **visible layer** (true for Boltzmann machines in general) is where we **put in inputs and read out outputs**.

**Training:** because there are no within-layer connections, given the visible units the hidden units are **conditionally independent** (and vice versa). This lets us sample efficiently and train with Contrastive Divergence (a few steps of Gibbs sampling) rather than the intractable full Boltzmann learning. RBMs were historically important for **greedy layer-wise pretraining** of deep networks (stacking into Deep Belief Networks).

---

## 4. Comparison

| Model | Hidden units? | Connectivity | Dynamics | Training |
|---|---|---|---|---|
| **Hopfield** | No | Fully connected (symmetric, no self) | Deterministic, energy-descent | Hebbian storage |
| **Boltzmann** | Yes | Fully connected (symmetric) | Stochastic | Minimize KL (slow) |
| **RBM** | Yes | Bipartite (no within-layer) | Stochastic | Contrastive Divergence (fast) |

---

## Key Takeaways

- **Energy-based models** store patterns as **low-energy minima**; recall = settling into the nearest minimum.
- **Hopfield networks:** bipolar ($\pm1$) associative memory, symmetric weights, $w_{ii}=0$, asynchronous updates; energy never increases. Limited to ~$0.14N$ patterns and suffer ghost/spurious states.
- **Boltzmann machines:** add stochastic hidden units; compare data vs. model distributions via **KL divergence**; powerful but slow.
- **RBMs:** restrict to a **bipartite** visible/hidden structure → conditional independence within a layer → efficient training (Contrastive Divergence); foundational for deep belief nets.
