---
# Documentation: https://sourcethemes.com/academic/docs/managing-content/

title: "Paper Summary - How multilingual is Multilingual BERT?"
subtitle: "Evaluate Multilingual BERT's performance on  NER, POS tagging, and translation tasks."
summary: "Evaluate Multilingual BERT's performance on  NER, POS tagging, and translation tasks."
authors: [admin]
tags: []
categories: []
date: 2020-07-19T19:30:07-03:00
lastmod: 2020-07-19T19:30:07-03:00
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
## Introduction

This article presents the summary of the results from the paper "[How multilingual is Multilingual BERT?](https://arxiv.org/pdf/1906.01502.pdf)"[^1]. For the sake of brevity, I leave out the discussions and interpretations for the experimental results.

[^1]: Pires, T., Schlinger, E., & Garrette, D. (2019). How multilingual is Multilingual BERT?. arXiv preprint arXiv:1906.01502.

## What is Multilingual BERT (M-BERT)?
> :warning: **Disclaimer**: See this [Medium article](https://towardsdatascience.com/bert-explained-state-of-the-art-language-model-for-nlp-f8b21a9b6270) and this [blog article](http://jalammar.github.io/illustrated-bert/) to learn more about BERT.

Multilingual BERT (M-BERT)[^2] is a pre-trained language model that can generate contextualized representations for words from 104 languages. Architecture-wise, M-BERT is identical to BERT[^2], which consists of 12-layer transformer encoder layers. However, M-BERT is trained in an unsupervised manner on Wikipedia pages of 104 languages instead of monolingual English corpus.

[^2]: Jacob Devlin, Ming-Wei Chang, Kenton Lee, and Kristina Toutanova. 2019. BERT: Pre-training of deep bidirectional transformers for language understanding. In Proceedings of NAACL

## Experiments and Findings

### 1. Named Entity Recognition (NER)

**Task Definition**: Predict the named-entities using the last layer representations of M-BERT.

**Datasets**: CoNLL-2002[^3] and CoNLL-2003[^4]

[^3]: Erik F. Tjong Kim Sang. 2002. Introduction to the CoNLL-2002 shared task: Language-independent named entity recognition. In Proceedings of CoNLL.
[^4]: Erik F. Tjong Kim Sang and Fien De Meulder. 2003. Introduction to the CoNLL-2003 shared task: Language-independent named entity recognition. In Proceedings of CoNLL.

**Results**:

{{< figure src="NER.png" title="NER F1 results on the CoNLL data. Table taken from Pires et al. (2020)." numbered="true" lightbox="true" class="img-md-50">}}

1. The NER models perform the best when the training set for fine-tuning and test set are in the same language.
2. Interestingly, M-BERT generalizes well for language pairs with almost no lexical overlap.

### 2. Part-of-Speech (POS) Tagging

**Task Definition**: Predict the part-of-speech tags using the last layer representations of M-BERT.

**Datasets**: Universal Dependencies[^5]

[^5]: Joakim Nivre, Marie-Catherine de Marneffe, Filip Ginter, Yoav Goldberg, Jan Hajic, Christopher D. Manning, Ryan T. McDonald, Slav Petrov, Sampo Pyysalo, Natalia Silveira, Reut Tsarfaty, and Daniel Zeman. 2016. Universal dependencies v1: A multilingual treebank collection. In Proceedings of LREC

**Results**:

{{< figure src="POS.png" title="POS accuracy on a subset of UD languages. Table taken from Pires et al. (2020)." numbered="true" lightbox="true" class="img-md-50">}}

1. M-BERT generalizes well across languages under the zero-shot setting where it has not seen a single POS-tagged word in another language.
2. The cross-lingual performance deteriorates when languages exhibit different word order typologies such as the arrangement of subjects, verbs, and objects.

### 3. Part-of-Speech Tagging + Code-Switching and Transliteration

> **[Code-switching](https://en.wikipedia.org/wiki/Code-switching)**: The process of using more than one language in a single utterance.

> **[Transliteration](https://en.wikipedia.org/wiki/Transliteration)**: The process of transferring a word from the alphabet of one language to another such as [romanization](https://en.wikipedia.org/wiki/Romanization).

**Task Definition**: Predict the part-of-speech tags for words in code-switched and transliterated English and Hindi text using the last layer representations of M-BERT.

**Datasets**: Code-Switching Hindi/English UD corpus[^6]

[^6]: Irshad Bhat, Riyaz A. Bhat, Manish Shrivastava, and Dipti Sharma. 2018. Universal dependency parsing for Hindi-English code-switching. In Proceedings of NAACL.

**Results**:

{{< figure src="CS_transliteration.png" title="POS accuracy on the code-switched Hindi/English dataset, on script-corrected and transliterated tokens. Table taken from Pires et al. (2020)." numbered="true" lightbox="true" class="img-md-50">}}

1. M-BERT's POS-tagging performance is comparable for both monolingual and code-switched dataset when there's no transliteration (i.e., "corrected" scenario).
2. The POS-tagging performance of M-BERT suffers when the data are transliterated.

### 4. Nearest Neighbor Translation

**Task Definition**: Translate sentence in language $X$ to language $Y$ by projecting the sentence representation in language $X$ to the representation space for language $Y$ and finding the sentence in language $Y$ with the closest sentence representation.

**Datasets**: WMT16[^7]

[^7]: Ondrej Bojar, Yvette Graham, Amir Kamran, and Milos Stanojevic. 2016. Results of the WMT16' metrics shared task. In Proceedings of the First Conference on Machine Translation: Volume 2, Shared Task Papers.

**Results**:

{{< figure src="translation.png" title="Accuracy of nearest neighbor translation for EN-DE, EN-RU, and HI-UR. Figure taken from Pires et al. (2020)." numbered="true" lightbox="true" class="img-md-50">}}

1. M-BERT translates sentences the best when the sentence representations are taken from its middle layers.
2. When the sentence representation is obtained from the last few layers of M-BERT, the accuracy of the translation goes down. This result is surprising because Jahawar et al. (2019)[^8] shows that the monolingual BERT captures semantics information at the top layers.

[^8]: Ganesh Jawahar, Benoît Sagot, and Djamé Seddah. 2019. What Does BERT Learn about the Structure of Language?. In Proceedings of the 57th Annual Meeting of the Association for Computational Linguistics (pp. 3651–3657). Association for Computational Linguistics.

## Conclusion: When Does M-BERT Work Well, and Why?

M-BERT transfers well when the source and the target languages are typologically similar because the representations do not capture the structural transformation needed when transferring knowledge to languages with different typological features.

M-BERT generalizes across languages because different languages are close in the shared space when the Masked Language Modeling training maps the co-occurring word pieces of different languages to the same shared space.
