# Distributed word representations

## Meaning representations
Co-occurrence matrix:

![Co-occurrence matrix](./img/2021-03-31-co-occurrence-matrix.png)

Meaning can be present in such a matrix.
* If a word co-occurs often with "excellent," it likely is a positive word; if it co-occurs often with "terrible," it likely denotes something negative

## Guiding hypothesis for vector-space models
The meaning of a word is derived from its use in a language. If two words have similar vectors in a co-occurrence matrix, they tend to have similar meanings ([Turney and Pantel, 2010](https://arxiv.org/abs/1003.1141)).

## Feature representations of data
* *the movie was horrible* becomes [4, 0, 0.25] (4 words, 0 proper names, 0.25 concentration of negative words)
* Reduces noisy data to restricted feature set

## What do we define co-occurrence as?
For a sentence, e.g. *from swerve of shore to bend of bay, brings*

Consider the word "to"
* Window: how many words around "to" (in both directions) do we want to focus on?
* Scaling: how to weight words in the window?
    - Flat: treat everything equally
    - Inverse: word is weighted 1/n if it is distance n from the target word

Larger, flatter windows capture more semantic information, whereas smaller, more scaled windows capture more syntactic information

Can also consider different unit sizes - words, sentences, etc

## Constructing data
* Tokenization
* Annotation
* Tagging
* Parsing
* Feature selection

## Matrix design

### word x word
* Rows and columns represent individual words
* Value `a_ij` in a matrix represents how many times words `i` and `j` co-occur with each other in a given set of documents
* Very dense (lots of nonzero entries)! Density increases with more documents in the corpus
* Dimensionality remains fixed as we bring in new data as long as we pre-decide on vocabulary

### word x document
* Rows represent words; columns represent documents
* Value `a_ij` in a matrix represents how many times word `i` occurs in document `j`
* Very sparse: may be hard to compute certain operations, but easy storage

### word x discourse context
* Rows represent words; columns represent discourse context labels
    - Labels are assigned by human annotators based on what type of context the sentence is (i.e., acceptance dialogue, rejecting part of previous statement, phrase completion, etc)
* Value `a_ij` in a matrix represents how many times word `i` occurs in discource context `j`

### Other designs
* word x search proximity
* adj x modified noun
* word x dependency relations

Note: Models like GloVe and word2vec provide packaged solutions that pre-chose from these design choices. 

## Vector comparison (similarity)
Within the context of this example:

![Example](./img/2021-03-31-comparison-example.png)

Note that B and C are close in distance (frequency info), but A and B have a similar bias (syntactic/semantic info)

### Euclidean

For vectors `u`, `v` of `n` dimensions:

![euc_eq](https://latex.codecogs.com/png.latex?%5Cbg_white%20euc%28u%2C%20v%29%20%3D%20%5Csqrt%7B%5Csum_%7Bi%3D1%7D%5En%20%7Cu_i%20-%20v_i%7C%5E2%7D)

This measures the straight-line distance between `u` and `v` capturing the pure distance aspect of similarity

Note: Length normalization

![Length norm](./img/2021-03-31-length-norm.png)

This captures the bias aspect of similarity

### Cosine
For vectors `u`, `v` of `n` dimensions:

![cos_eq](https://latex.codecogs.com/png.latex?%5Cbg_white%20cosdist%28u%2C%20v%29%20%3D%201%20-%20%5Cfrac%7Bu_i%5ET%20v_i%7D%7B%7C%7Cu%7C%7C_2%20%7C%7Cv%7C%7C_2%7D)

* Division by the length effectively normalizes vectors
* Captures the bias aspect of similarity
* Not considered a proper distance metric because it fails the triangle inequality; however, the following does:

![newcos_eq](https://latex.codecogs.com/png.latex?%5Cbg_white%20newcosdist%28u%2C%20v%29%20%3D%20%5Cfrac%7B%5Ccos%5E%7B-1%7D%5Cleft%28%5Cfrac%7Bu_i%5ET%20v_i%7D%7B%7C%7Cu%7C%7C_2%20%7C%7Cv%7C%7C_2%7D%5Cright%29%7D%7B%5Cpi%7D)

* But the correlation between these two metrics is nearly perfect, so in practice, use the simpler one

### Other metrics
* Matching
* Dice
* Jaccard
* KL (distance between probability distributions)
* Overlap

## Reweighting
Goal: Amplify important data useful for generalization, because raw counts/frequency are poor proxy for semantic information

### Normalization
* L2 norming (see above)
* Probability distribution: divide values by sum of all values

### Observed/Expected

![Observed/Expected](./img/2021-03-31-observed-expected.png)

**Intuition**: Keeps words in idioms co-occurring more than expected; other word pairs co-occur less than expected

### Pointwise Mutual Information (PMI)

![PMI](https://latex.codecogs.com/png.latex?\bg_white%20pmi(X,%20i,%20j)%20=%20ln\left(\frac{X_{ij}}{\text{expected}(X,%20i,%20j)}\right)%20=%20ln\left(\frac{P(X_{ij})}{P(X_{i*})P(X_{*j})}\right))

This is the log of observed count divided by expected count.

### Positive PMI

PMI undefined when `X_{ij} = 0`. So:

![PPMI](https://latex.codecogs.com/png.latex?\bg_white%20ppmi(X,%20i,%20j)%20=%20max(0,%20pmi(X,i,j)))

### TF-IDF

For a corpus of documents D:

![TF-IDF](./img/2021-03-31-tf-idf.png)

## Dimensionality reduction

### Latent Semantic Analysis
* Also known as Trucated Singular Value Decomposition (Truncated SVD)
* Standard baseline, difficult to beat

Intuition:
* Fitting a linear model onto data encourages dimensionality reduction (since we can project data onto the model); this captures greatest source of variation in the data
* We can continue adding linear models to capture other sources of variation

Method:

Any matrix of real numbers can be written as

![SVD](https://latex.codecogs.com/png.latex?\bg_white%20A%20=%20TSD^T)

where `S` is a diagonal matrix of singular values and `T` and `D^T` are orthogonal. In NLP, `T` is the term matrix and `D^T` is the document matrix.

Dimensionality reduction comes from being selective about which singular values and terms to include (i.e., capturing only a few sources of variation in the data).

### Autoencoders
* Flexible class of deep learning architectures for learning reduced dimensional representations

Basic autoencoder model:

![Autoencoder](./img/2021-03-31-autoencoder.png)

### GloVe
* Goal is to learn vectors for words such that their dot product is proportional to their log probability of co-occurrence

![GloVe Objective](./img/2021-03-31-glove-objective.png)