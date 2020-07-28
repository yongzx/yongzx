---
# Documentation: https://sourcethemes.com/academic/docs/managing-content/

title: "Google Summer of Code 2020: Series 2"
subtitle: "Preliminary Results for Frame Semantics Annotations Projection"
summary: "Preliminary Results for Frame Semantics Annotations Projection"
authors: [admin]
tags: []
categories: []
date: 2020-07-26T19:56:48-03:00
lastmod: 2020-07-26T19:56:48-03:00
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
### 1. Introduction

My Google Summer of Code 2020 [project](https://summerofcode.withgoogle.com/projects/#4816633052266496) is to perform the cross-lingual transfer of frame semantics annotations. This blog post first summarizes the results that we have gotten so far (in my first phase) and then discusses our attempts at projecting frame elements.

---
## Summary of Results from Phase 1
### 2. Annotation Projection of Lexical Units

#### Methods

1. **Translation-based approach (no projection)**: Identify lexical units in another language by translating the words into English using the Extended Open Multilingual WordNet and filtering them using the Berkeley FrameNet. See my [previous post](http://yongzx.me/post/post-gsoc20-1/#6-baseline-model) for more detailed explanations.
2. **Embedding-based approach**: Identify lexical units in the target sentence whose mBERT contextualized representations are closest to those of lexical units in the source sentence. See my [previous post](http://yongzx.me/post/post-gsoc20-1/#7-proposed-embedding-based-method) for more detailed explanations.

#### Metrics

1. **Precision**: Precision measures the proportion of correctly identified lexical units out of all the *identified* lexical units.
2. **Recall**: Recall measures the proportion of correctly identified lexical units out of all the words that are lexical units.
3. **F1-score**: The harmonic mean of precision and recall.

#### Results

| Approaches \ Languages | EN | DE | PT |
| --- | :---:  | :---:  | :---:  |
| Translation-based (no projection)| 0.55 | 0.50 | 0.78 |
| Embedding-based | 0.54 | 0.64 | 0.83 |
Table 1: Precision for the Corpus-based and Embedding-based methods.

| Approaches \ Languages | EN | DE | PT |
| --- | :---:  | :---:  | :---:  |
| Translation-based (no projection)| 0.70 | 0.55 | 0.56 |
| Embedding-based | 0.72 | 0.51 | 0.51 |
Table 2: Recall for the Corpus-based and Embedding-based methods.

| Approaches \ Languages | EN | DE | PT |
| --- | :---:  | :---:  | :---:  |
| Translation-based (no projection)| 0.63 | 0.55 | 0.67 |
| Embedding-based | 0.64 | 0.62 | 0.66 |
Table 3: F1-scores for the Corpus-based and Embedding-based methods.

For the sake of brevity, see my [previous post](http://yongzx.me/post/post-gsoc20-1/#8-results) for the discussion of the results.

### 3. Annotation Projection of Semantic Frames

#### Methods

1. **Classifier (no projection)**: Use a simple multinomial log-linear classifier to predict the best-fitting frame out of all the FrameNet frames for a lexical unit. See my [previous post](http://yongzx.me/post/post-gsoc20-1/#9-baseline-model) for more detailed explanations.
2. **Classifier + Reduced Search Space**: Use the same classifier to predict the best-fitting frame, but restrict the search space to the frames related to the annotated counterpart in the source sentence. See my [previous post](http://yongzx.me/post/post-gsoc20-1/#10-proposed-modification) for more detailed explanations.

#### Metrics

1. **Precision**: Precision measures the proportion of correctly identified semantic frames out of all the *identified* semantic frames.
2. **Recall**: Recall measures the proportion of correct semantic frames that are identified.
3. **F1-score**: The harmonic mean of precision and recall.
4. **Frame-Semantic Spread Activation Evaluation Measure (FSEM)**: FSEM measures how far away the identified frame is from the correct frame in the network of FrameNet.

#### Results

| Approaches \ Languages | EN | DE | PT |
| --- | :---:  | :---:  | :---:  |
| Classifier (no projection) | 0.11 | 0.10 | 0.12 |
| Classifier + Reduced Search Space | 0.48 | 0.45 | 0.65 |
Table 4: Precision for the two semantic frames projection baseline methods.

| Approaches \ Languages | EN | DE | PT |
| --- | :---:  | :---:  | :---:  |
| Classifier (no projection) | 0.10 | 0.08 | 0.09 |
| Classifier + Reduced Search Space | 0.11 | 0.10 | 0.09 |
Table 5: Recall for the two semantic frames projection baseline methods.

| Approaches \ Languages | EN | DE | PT |
| --- | :---:  | :---:  | :---:  |
| Classifier (no projection) | 0.10 | 0.09 | 0.10 |
| Classifier + Reduced Search Space | 0.47 | 0.45 | 0.40 |
Table 6: F1-scores for the two semantic frames projection baseline methods.

| Approaches \ Languages | EN | DE | PT |
| --- | :---:  | :---:  | :---:  |
| Classifier (no projection) | 0.06 | 0.05 | 0.05 |
| Classifier + Reduced Search Space | 0.16 | 0.08 | 0.07 |
Table 7: FSEM for the two semantic frames projection baseline methods.

For the sake of brevity, see my [previous post](http://localhost:1313/post/post-gsoc20-1/#11-results) for the discussion of the results.

---
## Summary and Discussion of Results from Phase 2
### 4. Annotation Projection of Frame Elements

#### Methods

1. **Word-to-Word Alignment**: Use [`fast_align`](https://github.com/clab/fast_align) to align the words in the source sentence to those in the target sentence. Subsequently, transfer the frame element labels to the target sentence using the alignment.

{{< figure src="word_alignment.png" title="Word alignment between a Portuguese sentence and its English parallel counterpart." numbered="true" lightbox="true" class="img-md-75">}}

2. **Constituent-to-Word Alignment**: Parse sentences with a multilingual constituency parser such as [Berkeley Neural Parser](https://github.com/nikitakit/self-attentive-parser) and [Stanford CoreNLP](https://nlp.stanford.edu/software/srparser.html) (through [Stanza](https://stanfordnlp.github.io/stanza/corenlp_client.html)) to obtain the constituents. Label a constituent with the frame element label if all the words in the constituent are considered frame elements.

    Next, create the alignments between the constituents of the source sentence &mdash;&mdash; in this case, we only focus on terminal categories and the bottommost non-terminal categories in the constituency-based parse trees &mdash;&mdash; and the words in the target sentence. That is, there is an alignment between the source constituent and the target word if the target word is aligned with a word in the source constituent. Finally, transfer the frame element labels to the target sentence using the alignment.

3. **Constituent-to-Constituent Alignment**: This is similar to the Constituent-to-Word Alignment above, but instead of aligning source constituents to target words, we align source constituents to the constituents of the target sentence based on the Jaccard similarity index of the word alignment between the source and target constituents (Pado & Lapata, 2019)[^1].

[^1]: Padó, S., & Lapata, M. (2009). Cross-lingual annotation projection for semantic roles. Journal of Artificial Intelligence Research, 36, 307-340.


#### Metrics

1. **Hard Hamming Loss**: Hard hamming loss measures the fraction of frame elements that are incorrectly labeled because the label is in the wrong position.
2. **Soft Hamming Loss**: Soft hamming loss measures the fraction of frame elements that are incorrectly labeled, but the label is at the correct position.
3. **Total Hamming Loss**: Total hamming loss measures the overall fraction of frame elements that are incorrectly labeled. The ideal loss is 0.
3. **Exact Match Ratio**: Exact match ratio measures the percentage of sentences that have all their frame elements labeled correctly. The ideal ratio is 1.
4. **Distribution of Matching Number of Frame Elements**: The distribution reveals the disparity in the number of labeled words. Ideally, we should obtain a normal distribution centered at 0. Right-skewness means that there are missing frame element labels in the target sentence, whereas left-skewness means that words that do not play any semantic role are labeled with frame elements.

#### Results

The table and the figures below only present the annotation projection result from English to German because many English sentences are fully annotated whereas German sentences are not, and we cannot obtain constituents of Portuguese sentences to date.

| Approaches \ Metrics | Hard Hamming Loss | Soft Hamming Loss | Total Hamming Loss | Exact Match Ratio |
| --- | --- | --- | --- | --- |
| Word-to-Word Alignment | 0.16 | 0.02 | 0.18 | 0.20 |
| Constituent-to-Word Alignment | 0.16 | 0.01 | 0.17 | 0.28 |
| Constituent-to-Constituent Alignment | 0.24 | 0.00 | 0.24 | 0.04 |
Table 8: Results for Frame Elements Projection from EN to DE.

{{< figure src="en2de_w2w.png" title="Distribution of the matching number of frame elements for annotation projection ($\text{EN} \rightarrow \text{DE}$) via word-to-word alignment." numbered="true" lightbox="true" class="img-md-50">}}

{{< figure src="en2de_c2w.png" title="Distribution of the matching number of frame elements for annotation projection ($\text{EN} \rightarrow \text{DE}$) via constituent-to-word alignment." numbered="true" lightbox="true" class="img-md-50">}}

{{< figure src="en2de_c2c.png" title="Distribution of the matching number of frame elements for annotation projection ($\text{EN} \rightarrow \text{DE}$) via constituent-to-constituent alignment." numbered="true" lightbox="true" class="img-md-50">}}

Empirical results show that the constituent-to-word alignment best projects frame elements from English sentences to German sentences. The main reasons are that (1) frame elements span across multiple words in English source sentences, which benefits and (2) many German words in the target sentences are compound lexemes, thereby reducing the effectiveness of constituent-to-constituent alignment.

The unimodal distributions in the figures are right-skewed: annotation projection via various alignments results in *under-labeled* sentences. In other words, many words that should be labeled as frame elements are not labeled &mdash;&mdash; low recall.
