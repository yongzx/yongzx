---
# Documentation: https://sourcethemes.com/academic/docs/managing-content/

title: "Google Summer of Code 2020: Series 1"
subtitle: ""
summary: "Cross-lingual transfer of annotated lexical units and semantic frames"
authors: [admin]
tags: []
categories: []
date: 2020-06-23T21:19:23-03:00
lastmod: 2020-06-23T21:19:23-03:00
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

This blog post summarizes what I did for the first phase of the Google Summer of Code 2020. My [project](https://summerofcode.withgoogle.com/projects/#4816633052266496) is to perform the cross-lingual transfer of frame semantics annotations, particularly lexical units and semantic frames in the first phase. I implemented a few modifications to a multilingual frame semantics parser and illustrated the results below.


### 2. What are Frame Semantics Annotations?
Frame semantics annotations refer to annotations based on FrameNet. [FrameNet](https://framenet.icsi.berkeley.edu/fndrupal/) is essentially a network of <span style="color:brown">semantic frames</span> (this is how the word "FrameNet" is coined!) where a frame describes the concept that a <span style="color:brown">lexical unit</span> evokes in a particular context. Put differently, a frame explains the event described by the sentence, and the lexical unit is the basis for such sentence-level understanding.

{{< figure src="sentence.png" title="A sentence with the annotated lexical unit and semantic frame." numbered="true" lightbox="true" class="img-md-50">}}

For example, from the sentence "Mary purchases a car from John.", we understand that a commercial transaction takes place because of the word "purchases". Therefore, the lexical unit refers to the word "purchases"[^1] and the evoked frame is $\text{Commerce_buy}$.
[^1]: Technically, the lexical unit would be "purchase.v" because a lexical unit consists of a lemma and a part-of-speech.

{{< figure src="sentence_with_fe.png" title="A sentence with the annotated lexical unit, semantic frame, and frame elements." numbered="true" lightbox="true" class="img-md-50">}}

Within the semantic frame annotation, there can be multiple <span style="color:green;">frame elements</span> which add more information about the event or concept. For example, there are usually a buyer, a seller, and the sold goods in a transaction, all of which are labeled in the example sentence (see [Figure 2](#figure-a-sentence-with-the-annotated-lexical-unit-semantic-frame-and-frame-elements)).

## Experimental Setup
### 3. Dataset

{{< figure src="annotation_report.png" title="Size of annotation sets per language for the annotated TED Talk transcripts." numbered="true" lightbox="true" class="img-md-75">}}

The dataset used (available upon request) is the TED Talk transcripts annotated under the [Global FrameNet](https://www.globalframenet.org/annotation)'s shared annotation initiative. Note that the dataset is parallel corpora: transcripts in other languages are translated from the English transcript.

[Figure 3](#figure-size-of-annotation-sets-per-language-for-the-annotated-ted-talk-transcripts) shows the current size of annotation sets per language. Due to copyright issues, we can only use the English (en), Portuguese (pt), and German (de) annotation sets. Our goal is to identify frame-evoking lexical units and semantic frames in the English, Portuguese, and German sentences.

### 4. Evaluation Metrics

We compared the performance of the models (see below) on identifying lexical units and semantic frames. The main metrics used are:
1. **Precision**: Precision measures the proportion of correctly identified lexical units or semantic frames out of all the identified lexical units or semantic frames by the model.

$$\text{Precision} = \frac{\text{Correctly identified LUs or frames}}{\text{All identified LUs or frames}}$$

2. **Recall**: Recall measures the ability of the model in retrieving lexical units or semantic frames.

$$\text{Recall} = \frac{\text{Correctly identified LUs or frames}}{\text{All LUs or frames that should be identified}}$$

3. **F1-score**: The harmonic mean of precision and recall. It's important to measure the harmonic mean because a model can have high precision and low recall --- for a sentence that evokes multiple semantic frames, the model only identifies just one frame --- and vice versa.

4. **Frame-Semantic Spread Activation Evaluation Measure (FSEM)**[^2]: Used for evaluating semantic frame identification in a graded manner: how far away the identified frame is from the correct frame in the network of FrameNet. The range of FSEM is between 0 and 1, where 0 indicates no relationship between the two frames, and 1 indicates that both frames are identical. The further apart the two frames, the lower the FSEM score.

[^2]: Czulo, O., Torrent, T. T., da Silva Matos, E. E., da Costa, A. D., & Kar, D. (2019, September). Designing a Frame-Semantic Machine Translation Evaluation Metric. In 2nd Workshop on Human-Informed Translation and Interpreting Technology (HiT-IT 2019)

### 5. Baseline Model

The baseline model is the any-language frame-semantic parser (Johannsen et al., 2015)[^3]. [Section 6](#6-baseline-model) and [Section 9](#9-baseline-model) outline the parser's inner workings for identifying lexical units and semantic frames respectively. A bulk of my time is spent on implementing the model because the codes are not given.
[^3]: Johannsen, A., Alonso, H. M., & Søgaard, A. (2015, September). Any-language frame-semantic parsing. In Proceedings of the 2015 Conference on Empirical Methods in Natural Language Processing (pp. 2062-2066).

## Annotating Lexical Units
### 6. Baseline Model

{{< figure src="baseline_LU.png" title="Lexical units identification with the baseline model." numbered="true" lightbox="true" class="img-md-75">}}


The figure above illustrates the mechanism of lexical unit identification with the baseline model.
1. **Pre-processing**: Lemmatizes and performs parts-of-speech (POS) tagging for the input sentence.
2. **POS-based filters**: Selects the lexical units whose POS is within the top 6 common POS in the entire dataset, which are nouns, proper nouns, verbs, adjectives, adverbs, and adpositions.
3. **OMW\***: This step maps the lexical units, English or non-English, into English synsets. This step is the most critical step (therefore the asterisk) because it allows us to check whether a non-English lexical unit "exists" within FrameNet.<br/>
The original implementation uses BabelNet for the mapping. However, given that BabelNet API has a tight restriction on the number of API calls per day, we used the Extended Open Multilingual WordNet (OMW)[^4] instead.
4. **FrameNet Filter**: Filter out the synsets that correspond to the lexical units existing in Berkeley FrameNet 1.7[^5]. The remaining words are the identified lexical units.

[^4]: Francis Bond and Ryan Foster (2013)
Linking and extending an open multilingual wordnet. In 51st Annual Meeting of the Association for Computational Linguistics: ACL-2013. Sofia. 1352–1362
[^5]: We used Berkeley FrameNet data release version 1.7 instead of 1.5 because the data in this experiment are annotated using lexical units and frames from Berkeley FrameNet 1.7.

### 7. Proposed Embedding-based Method
{{< figure src="modification_LU.png" title="Lexical units identification using annotated lexical units (bolded words) in a parallel corpus. Blue boxes represent the embeddings of the frame-evoking lexical units from the annotated sentence, whereas the red boxes represent the embeddings of word tokens in the unannotated sentence." numbered="true" lightbox="true" class="img-md-75">}}

The figure above shows an embedding-based method of identifying lexical units through parallel corpora. The model identifies the lexical units $l^{(x)}$ in the unannotated sentence that is the closest to the lexical units $l^{(x)}$ in the annotated, parallel sentence in the distributional semantic space.

We first used the pre-trained multilingual BERT[^6] (mBERT) language model to embed $l^{(a)}$ and all the word tokens in the unannotated sentence. Since frame-evoking lexical units can consist of multiple words, we also created $n$-gram word embeddings by summing the word embeddings within a hyperparameterized window size of $n$. $l^{(x)}$ are the $n$-gram words whose embeddings closest to $l^{(a)}$ with respect to cosine similarity.
[^6]: Devlin, J., Chang, M. W., Lee, K., & Toutanova, K. (2018). Bert: Pre-training of deep bidirectional transformers for language understanding. arXiv preprint arXiv:1810.04805.

### 8. Results

{{< figure src="result_LU.png" title="Comparison of F1-score between the baseline model and the proposed embedding-based method. The error bar represents the 95% confidence interval." numbered="true" lightbox="true" class="img-md-50">}}

The embedding-based method only performs better than the translation-based baseline method for the German annotation sets. The poorer baseline performance may be that the German annotation sets are smaller than the other two languages (see [Figure 3](#figure-size-of-annotation-sets-per-language-for-the-annotated-ted-talk-transcripts)), so the POS-based filter, which retains words with the most common parts-of-speech, can be inaccurate.

Our results corroborate previous findings[^7] that pre-trained contextualized embeddings are not good representations of distributional semantics as word tokens that share the same meaning in the same sentence (but different locations) may have different representations. Moreover, pre-trained contextualized embeddings incorporate language bias, which renders the embeddings context- and language-variant[^8].

We obtained the best result for the embedding-based method when unigram word embeddings were used. This finding implies that many lexical units consist of a single word token. In fact, all the lexical units in the German annotation sets are single words.

[^7]: Mickus, T., Paperno, D., Constant, M., & van Deemeter, K. (2019). What do you mean, BERT? Assessing BERT as a Distributional Semantics Model. arXiv preprint arXiv:1911.05758.

[^8]: Libovický, J., Rosa, R., & Fraser, A. (2019). How Language-Neutral is Multilingual BERT?. arXiv preprint arXiv:1911.03310.

## Annotating Semantic Frames
### 9. Baseline Model

{{< figure src="baseline_frame.png" title="Semantic frame identification with the baseline model." numbered="true" lightbox="true" class="img-md-75">}}

The baseline model uses a simple multinomial log-linear classifier for frame prediction for a particular frame-evoking lexical unit. The input representation for the lexical unit is a combination of parts-of-speech labels, dependency labels, lemmatized sequences of words, its WordNet relations with other frame-evoking lexical units in the sentence, its mBERT embedding, and its minimum and mean distances from the embeddings of the set of words obtained after the FrameNet Filter (see [Figure 4](#figure-lexical-units-identification-with-the-baseline-model])).

Given the input representation, the classifier chooses the best frame from all the frames in Berkeley FrameNet. We trained the classifier using all the data published by Johannsen et al. (2015).[^3]

### 10. Proposed Modification

{{< figure src="modification_frame.png" title="Search space for the best frame with different levels of depth." numbered="true" lightbox="true" class="img-md-75">}}

Given our task cross-lingual annotations transfer, we proposed restricting the search space for the best frame using the annotated frame in the parallel corpora. Since the semantic frames in the Berkeley FrameNet are related to each other (through frame-to-frame relations), we controlled the size of the search space through a hyperparameter `level`. This constraint strategy is tantamount to a breadth-first search in a graph. When the `level` is zero, the search space only contains the annotated frame in the parallel corpora. When the `level` is larger than zero, we realize the spread activation of the annotated frame through the network of semantic frames.

### 11. Results

{{< figure src="result_frame_f1.png" title="Comparison of F1-score between the baseline model and the proposed modification that reduces the search space for semantic frames. The error bar represents the 95% confidence interval." numbered="true" lightbox="true" class="img-md-50">}}

{{< figure src="result_frame_FSEM.png" title="Comparison of FSEM scores between the baseline model and the proposed modification that reduces the search space for semantic frames. The error bar represents the 95% confidence interval." numbered="true" lightbox="true" class="img-md-50">}}

The illustrations above show that, with the proposed modification that restricts the search space for semantic frame, the baseline classifier predicts more accurately. In essence, leveraging annotations in parallel corpora improves semantic frame identification.

However, the generally low FSEM scores (closer to 0) indicate that many inaccurately predicted semantic frames are not related to the correct frames. This empirical result suggests that the model is terrible at recognizing sentence-level context during frame identification.

## Conclusion

In the next phase, I would experiment the baseline parser on identifying frame elements. Overall, annotation transfer aside, I believe that multilingual frame-semantics requires a better parser that utilizes deep learning such as BiLSTM or Transformer-based techniques.
