# Supervised sentiment analysis

## Tokenization

### Whitespace tokenizer
Very simple, just splits sentences into words by spacing. Example:

```python
> whitespace_tokenizer("The quick fox jumped over the lazy dog.")
['The', 'quick', 'fox', 'jumped', 'over', 'the', 'lazy', 'dog.']
```

Note: simplest version will not take punctuation into account, which could be disruptive for using with VSMs

### Sentiment-aware tokenizer
Ideally, a tokenizer would
* Isolate emoticons
* Respects domain-specific markup (i.e., hashtags and `@`-mentions)
* Uses underlying markup
* Capture masked curses such as `f@#$%ing`
* Preserve meaningful capitalization
* Regularizes lengthening (i.e., `YAAAAAY` => `YAAY`)
* Captures multiword expressions such as idioms such as `out of this world`

ex:

```python
sentiment_tokenizer("@NLUers: can&#39;t wait for the Jun 9 #projects! YAAAAAAY!!! &gt;:-D http://stanford.edu/class/cs224u/.")
['@nluers', ':', 'can\'t', 'wait', 'for', 'the', 'Jun_9', '#projects', '!', 'YAAAY', '!', '!', '!', '>:-D', 'http://stanford.edu/class/cs224u/', '.']
```

Most of these criteria are met by `nltk.tokenize.casual.TweetTokenizer` (for Tweets)

## Other preprocessing techniques
* Part-of-speech tagging
    - Tag each word as verb/noun/adjective/adverb/... and pre-apply positive/negative sentiment to each word-POS pair
    - Limits: (Word, POS) pair can have two sentiments in different contexts
* Simple negation marking
    - Append a `_NEG` suffix to every word appearing between a negation and a clause-level punctuation mark
    - Example: `No one enjoys it.` becomes `['no', 'one_NEG', 'enjoys_NEG', 'it_NEG', '.']`

## Hyperparameter search
* Model parameters: values learned as part of optimizing the model
* Hyperparameters: parameters set outside of optimization
    - GloVe or LSA dimensionality
    - GloVe `x_max` and `alpha`
    - Regularization terms, hidden dimensionalities, learning rates, activation functions
* Must be done to properly get to "optimal" model
* Done only with train/development data

## Feature representation

### N-grams
* Unigrams: "bag-of-words" models
* Generalize to "bag-of-ngrams"
* Dependent on tokenization scheme
* Can be combined with preprocessing steps with `_NEG` marking
* Creates very large, very sparse feature representations
* Generally fails to directly model feature relationships

### Other ideas
* Lexicon-derived features
* Negation marking
* Modal adverbs
* Length-based features
* Thwarted expectations: ratio of positive to negative words

## RNN classifiers
![RNN](/notes/images/cs224u/2021-04-12-rnn.png)