# Word Embeddings

To feed text to a neural network, words must become numbers. **Word embeddings** are dense vector representations that capture meaning, replacing sparse one-hot encodings. This note covers one-hot encoding, the idea of embeddings, and **Word2Vec** (CBOW and Skip-gram).

---

## 1. Converting Raw Text into Sequence Data

The text preprocessing pipeline:

1. **Tokenize:** split the strings into tokens (words or characters).
2. **Build the vocabulary:** determine the set of unique tokens in the training corpus.
3. **Index:** build a vocabulary dictionary associating each token with a numerical index.
4. **Encode:** convert the text into sequences of numerical indices.

---

## 2. One-Hot Encoding and its Limitation

A **one-hot vector** has all 0s except a single 1 at the position of the word's index. For a vocabulary of size $V$, each word is a $V$-dimensional vector with one hot entry.

**Limitation:** one-hot word vectors **cannot express similarity** between different words. Any two distinct one-hot vectors are **orthogonal**, so their cosine similarity is always 0 — "cat" and "kitten" are as dissimilar as "cat" and "airplane." One-hot vectors are also extremely high-dimensional and sparse.

---

## 3. What Are Word Embeddings?

Word embeddings are **numerical vector representations** of words in a continuous, multi-dimensional vector space:

- Each word is a **dense** vector of real numbers, capturing **semantic and syntactic** information.
- **Geometric relationships** between vectors represent linguistic relationships: words with similar meanings/usage appear **close** in the vector space.
- This contrasts with one-hot encoding (sparse, no notion of similarity).

Word2Vec maps each word to a **fixed-length vector**, and these vectors better express similarity and analogy relationships among words.

---

## 4. Word2Vec

A popular technique developed by a Google team (incl. Tomas Mikolov) in **2013**. It is a **shallow neural network** trained to learn word representations based on the **context** in which words appear: words used in similar contexts get similar embeddings. Both of its architectures use the **context** of a word (the words around it) as well as the word itself.

### 4.1 Continuous Bag of Words (CBOW)

Predicts a **target (center) word** from its **surrounding context words** — a "fill-in-the-blank" task.

- Example: in "The cat sat on the ___," predict "mat" from the context words.
- The **order** of context words does **not** matter (the "bag of words" assumption).
- Context word vectors are **averaged** in the calculation of the conditional probability.
- **Faster** to train and works well with **frequent** words.

For the sequence "the", "man", "loves", "his", "son" with center word **"loves"** and context window size 2, CBOW models the conditional probability of generating "loves" given the context {"the", "man", "his", "son"}:

$$
P(\text{"loves"} \mid \text{"the"}, \text{"man"}, \text{"his"}, \text{"son"})
$$

**Network structure:** the input layer receives word-index vectors → as many input neurons as unique words in the vocabulary. The number of **hidden neurons = embedding size**. The number of output neurons = number of input neurons (vocabulary size). The **input-to-hidden** connections are **linear** (no activation), and the **hidden-to-output** connections have **softmax** activations.

The maximum-likelihood estimation of CBOW is equivalent to **minimizing the negative log-likelihood** loss of predicting the center word from its averaged context.

### 4.2 Skip-gram

The mirror image of CBOW: uses the current **center word** to predict the **surrounding context words**.

- Each nearby context word is predicted, weighted more heavily if **closer** to the center word.
- Works **better for infrequent words** by learning more precise representations.

### CBOW vs. Skip-gram

| | CBOW | Skip-gram |
|---|---|---|
| Predicts | center word from context | context words from center word |
| Speed | faster | slower |
| Best for | frequent words | infrequent words |

---

## 5. Training Objective & Why Embeddings Are Useful

The Word2Vec network (≈ two layers) is trained on a large text corpus, adjusting embeddings to **maximize the probability** of predicting words in the correct context window. The resulting vectors organize the vocabulary so semantically/syntactically similar words are near each other.

**Why useful:**

- **Vector arithmetic on words:** $\text{vec}(\text{king}) - \text{vec}(\text{man}) + \text{vec}(\text{woman}) \approx \text{vec}(\text{queen})$.
- Capture analogies, synonyms, antonyms.
- Improve performance on NLP tasks: text classification, sentiment analysis, named-entity recognition, machine translation.
- Compact and efficient — far lower dimensionality than one-hot encoding.

(Other embedding methods include **GloVe** and **fastText**.)

---

## Key Takeaways

- Text → tokens → vocabulary indices → vectors. **One-hot** vectors are sparse and orthogonal (no similarity); **embeddings** are dense and encode meaning via geometry.
- **Word2Vec** learns embeddings by predicting context: **CBOW** (context → center word, averages context, faster, frequent words) and **Skip-gram** (center word → context, better for rare words).
- The embedding size = number of hidden neurons; training minimizes negative log-likelihood (softmax output).
- Embeddings enable analogy arithmetic (king − man + woman ≈ queen) and power virtually all modern NLP.
