---
# Documentation: https://sourcethemes.com/academic/docs/managing-content/

title: "Understanding SenseBERT"
subtitle: ""
summary: "A quick distillation of the paper SenseBERT: Driving Some Sense into BERT"
authors: [admin]
tags: []
categories: []
date: 2020-06-30T22:27:12-03:00
lastmod: 2020-07-05T22:27:12-03:00
featured: false
draft: false

# Featured image
# To use, add an image named `featured.jpg/png` to your page's folder.
# Focal points: Smart, Center, TopLeft, Top, TopRight, Left, Right, BottomLeft, Bottom, BottomRight.
image:
  caption: ""
  focal_point: ""
  preview_only: false

# Projects (optional).
#   Associate this post with one or more of your projects.
#   Simply enter your project's folder or file name without extension.
#   E.g. `projects = ["internal-project"]` references `content/project/deep-learning/index.md`.
#   Otherwise, set `projects = []`.
projects: []
toc: true
---

---
## One-Sentence Summary
Levine et al. (2020)[^1] created SenseBERT &mdash; BERT that learns word sense information &mdash; by training the BERT model with augmented input embeddings to predict the masked words and their WordNet supersenses.

[^1]: Levine, Yoav, et al. (2020). SenseBERT: Driving Some Sense into BERT. Proceedings of the 58th Annual Meeting of the Association for Computational Linguistics.
---

## Introduction

### BERT and Its Problem

BERT[^2] is a language model that uses Masked Language Models (MLM) and Next Sentence Prediction (NSP) to learn text representation. MLM trains BERT on the word level: masks some words in a sentence and trains the model to predict the masked words using neighboring words. On the other hand, NSP trains BERT on the sentence level as BERT learns to predict if a sentence comes after another sentence.

[^2]: Devlin, J., Chang, M. W., Lee, K., & Toutanova, K. (2018). Bert: Pre-training of deep bidirectional transformers for language understanding. arXiv preprint arXiv:1810.04805.

> :warning: **Disclaimer**: See this [Medium article](https://towardsdatascience.com/bert-explained-state-of-the-art-language-model-for-nlp-f8b21a9b6270) to learn more about BERT.

**Levine et al. (2020) found that, with the naive MLM, BERT does not learn word sense information.** In other words, the self-supervised MLM that operates on the word-form level cannot capture lexical semantics knowledge.


### What is Word Sense?

{{< figure src="example_sense.png" title="The word '**kick**' has different word senses depending on the sentence context." numbered="true" lightbox="true" class="img-md-75">}}

The term "word sense" is a fancy term for a word meaning. Some words only have one word sense and some have multiple. For example, in [Figure 1](#figure-the-word-kick-has-different-word-senses-depending-on-the-sentence-context), the word "kick" has two word senses: it can refer to the action of striking with the foot or the abstract concept of giving up a habit.

Levine et al. (2020) used the word senses &mdash; more specifically, supersenses &mdash; defined by WordNet. WordNet supersenses refer to coarse groupings of senses. For example, the supersense $\ntext{Religion}$ covers word senses (e.g., mosque and monastery) related to religious purposes.


## SenseBERT

### Modification 1: Input Embeddings

{{< figure src="modification_1.png" title="Illustration of the modification on the input embeddings. Part of the figure is taken from Devlin et al. (2018)." numbered="true" lightbox="true" class="img-md-75">}}

Levine et al. (2020) introduced an additional embedding for each BERT input embedding. The additional embedding would be trained to represent supersenses. The authors proposed mapping each word to its supersense through a trainable weight matrix $M$ and subsequently to the sense embedding through another trainable matrix $S$.


### Modification 2: Training Task for Supersense Prediction

{{< figure src="masked_input.png" title="Illustration of the training task. The black box represents the masked word in the sentence." numbered="true" lightbox="true" class="img-md-75">}}

Levine et al. (2020) trained the model to predict masked words (see this [Medium article](https://towardsdatascience.com/bert-explained-state-of-the-art-language-model-for-nlp-f8b21a9b6270) to learn more about MLM) and their supersenses. For brevity, I omit the explanation for MLM training because there are no novel changes to it.

#### Masking Strategy

The authors favored masking words with only one supersense so the model learns the mapping contexts to supersenses when randomly masking words in the sentence. Specifically, 50% of the single-supersensed words in each input sequence were masked.

When a subword token is masked, since word sense is a word-level information, the masked token is not used for supersense prediction training. For an out-of-vocabulary (OOV) token, when any of its subtoken is masked, all of the subtokens are masked together (known as the whole-word-masking strategy).

#### Output

For sense prediction, SenseBERT maps the BERT embeddings for each token to the words supersenses space (i.e., the sense-score vectors). After the softmax operation, each value in the sense-score vector corresponds to the predicted probability of the supersense.

#### Training Objective

$$\mathcal{L}_{\text{SLM}} = \mathcal{L}^{\text{allowed}} _{\text{SLM}} + \mathcal{L}^{\text{reg}} _{\text{SLM}}$$

$$\mathcal{L}^{\text{allowed}} _{\text{SLM}} = - \log \sum _{s\in A(w)} {p(s|\text{context})}$$

$$\mathcal{L}^{\text{reg}} _{\text{SLM}} = - \sum _{s\in A(w)} \frac{1}{|A(w)|} \log p(s|\text{context})$$

Levine et al. (2020) proposed a new training objective $\mathcal{L}_{\text{SLM}}$ for supersense prediction consisting of two loss terms. $p(s|\text{context})$ denotes the probability of a particular supersense $s$ for the word $w$ given the sentence context. $A(w)$ refers to the set of all supersenses in WordNet for the word $w$.

{{< figure src="loss_function_comparison.png" title="Comparison between multi-class cross-entropy loss and $\mathcal{L}^{\text{allowed}} _{\text{SLM}}$ in the example sentence (Levine et al., 2020)." numbered="true" lightbox="true" class="img-md-75">}}

The first loss term $\mathcal{L}^{\text{allowed}} _{\text{SLM}}$ is interesting. Ideally, we want to calculate a multi-class cross-entropy loss with respect to the correct supersense. However, $\mathcal{L}^{\text{allowed}} _{\text{SLM}}$ combines the cross-entropy loss with respect to _allowed supersenses_ : all the supersenses $s$ associated with the word $w$ in WordNet, which is $s \in A(w)$, regardless of the context. All the supersenses are weighted equally for polysemous words (words with multiple senses).

The authors argue that given enough training data, the model will learn the mapping of the sentence's context to the correct supersense of the masked word. While not explicitly mentioned in the publication, I believe that the success of the training is contingent on the masking strategy that favors monosemous words, which reduces the noise from incorrect labels for polysemous words. Note that $\mathcal{L}^{\text{allowed}} _{\text{SLM}}$ is reduced to the multi-class cross entropy loss when the model predicts monosemous words' senses.

The second loss term $\mathcal{L}^{\text{reg}} _{\text{SLM}}$ is a regularization term. Minimizing the regularization loss forces the predicted probability to distribute more uniformly over the allowed supersense. The reason is that the sum of logarithms in $\mathcal{L}^{\text{reg}} _{\text{SLM}}$ is the logarithm of the product of probabilities.

### Modification 3: Vocabulary Size

Levine et al. (2020) doubled the size of the vocabulary for BERT to 60K words by adding 30K new words from Wikipedia.



## Results
{{< figure src="result_clusters.png" title="Clustering of supersense vectors. Figure is taken from the original paper (Levine et al, 2020)." numbered="true" lightbox="true">}}

{{< figure src="result_WiC.png" title="Performance of different models on the Word-in-Context (WiC) task." numbered="true" lightbox="true" class="img-md-50">}}

1. Supersenses with the same part-of-speech or similar semantics are clustered together.
2. SenseBERT outperforms the original BERT in disambiguating supersenses.
3. SenseBERT achieves the state-of-the-art result for the Word-in-Context (WiC) task: a binary classification task that determines whether the same word in two different sentences share the same meaning.
4. GLUE benchmark tests show that incorporating word supersense information does not compromise SenseBERT’s language understanding ability.
