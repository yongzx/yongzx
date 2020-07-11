---
# Documentation: https://sourcethemes.com/academic/docs/managing-content/

title: "How Linguistically Diverse is the NLP World?"
subtitle: "A quick distillation of the ACL 2020 publication '[The State and Fate of Linguistic Diversity and Inclusion in the NLP World](https://www.aclweb.org/anthology/2020.acl-main.560.pdf)'"
summary: "A quick distillation of the ACL 2020 publication '[The State and Fate of Linguistic Diversity and Inclusion in the NLP World](https://www.aclweb.org/anthology/2020.acl-main.560.pdf)'"
authors: [admin]
tags: []
categories: []
date: 2020-07-09T21:59:05-03:00
lastmod: 2020-07-09T21:59:05-03:00
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
Most labeled and unlabeled research resources are concentrated in a small proportion languages that make up less than 6% of the languages in our world.

---
## Introduction
Joshi et al. (2020)[^1] investigated the distribution of research resources and typological features for different languages. They also quantified the inclusiveness of NLP conferences in publishing research on different languages.

[^1]: Joshi, P., Santy, S., Budhiraja, A., Bali, K., Choudhury, M. (2020). *The State and Fate of Linguistic Diversity and Inclusion in the NLP World.* Proceedings of the 58th Annual Meeting of the Association for Computational Linguistics.

## Main Findings

### 1. Skewed Distribution of Language Resources

{{< figure src="taxonomy.png" title="Visualization of taxonomy of languages based on language resources. Figure taken from Joshi et al. (2020)." numbered="true" lightbox="true" class="img-md-50">}}

[Figure 1](#figure-visualization-of-taxonomy-of-languages-based-on-language-resources-figure-taken-from-joshi-et-al-2020) shows that we can categorize languages in our world into six classes based on the size of their labeled resources (in LDC and ELRA Map repositories) and unlabeled resources (i.e., Wikipedia pages). Languages from Class 0, to which the authors comically refer as *The Left-Behinds*, has almost no data for NLP systems to work with. On the other hand, the languages from Class 5 enjoy NLP breakthroughs because of the abundant resources readily available for research.

The full language taxonomy can be found [here](https://microsoft.github.io/linguisticdiversity/assets/lang2tax.txt).

{{< figure src="bar_total_lang.png" title="Percentage of total languages for each language class." numbered="true" lightbox="true" class="img-md-75">}}
{{< figure src="bar_total_speakers.png" title="Total number of speakers for each language class." numbered="true" lightbox="true" class="img-md-75">}}

One alarming finding is that, while there are billions of speakers for both Class 0 languages (such as Dahalo and Warlpiri) and Class 5 languages (such as English and German), the former class, which severely lacks resources, comprises 88% of the total languages in our world. In contrast, the latter class that is abundant with research resources **only** encompasses 0.28% of world languages.

### 2. Excluded Representation of Typological Features

Joshi et al. (2020) found that certain typological features that exist in low-resource languages (from Class 0 and 1) are not represented in resource-rich languages. They called these features as "ignored" typological features.

As transfer learning works better when two languages are typologically similar[^2], the authors showed that translations from English to low-resource languages with 'ignored' typological features were worse than translations to the counterparts without 'ignored' typological features in the same language family.

[^2]: Lin, Y.H., Chen, C.Y., Lee, J., Li, Z., Zhang, Y., Xia, M., Rijhwani, S., He, J., Zhang, Z., Ma, X., Anastasopoulos, A., Littell, P., & Neubig, G. (2019). Choosing Transfer Languages for Cross-Lingual Learning. In Proceedings of the 57th Annual Meeting of the Association for Computational Linguistics (pp. 3125–3135). Association for Computational Linguistics.

### 3. Varying Inclusiveness of NLP Conferences

Joshi et al. (2020) evaluated the diversity of researched languages in NLP conferences and presented the following analyses:
1. The authors used language occurrence entropy to measure the spread of the distribution of the researched languages.

    {{< figure src="conferences_inclusion.png" title="Diversity of languages mentioned in publications (measured by language occurrence entropy) over the years for different conferences. Figure taken from Joshi et al. (2020)" numbered="true" lightbox="true" class="img-md-75">}}

    **Results**: [Figure 4](#figure-diversity-of-languages-mentioned-in-publications-measured-by-language-occurrence-entropy-over-the-years-for-different-conferences-figure-taken-from-joshi-et-al-2020) shows a general rise over the years in the entropy for many conferences, which indicates that multilingual research becomes more linguistically inclusive. Newer conferences (conferences with later start dates) are also more language inclusive, and among all conferences, LREC and WS are the most inclusive.

2. The authors employed the class-wise Mean Reciprocal Rank to score each language class (from the taxonomy as mentioned earlier) based on their frequency of occurrences in a conference.

    **Results**: They found that publications involving low-resource languages are underrepresented in conferences such as CoNLL, TACL, and SEMEVAL.

3. The authors created embeddings for each author, language, conference (with a particular iteration). Like how Word2Vec embeddings are generated, these embeddings are inputs to a skip-gram model that is trained to predict randomly sampled words from the title and abstract of a paper (associated with the author, the language in the paper, and the conference where the paper is published).

    {{< figure src="model.png" title="Model architecture to learn embeddings for authors, languages and conferences. Figure adapted from Joshi et al. (2020)" numbered="true" lightbox="true" class="img-md-75">}}

    {{< figure src="viz_learnt_embeddings.png" title="t-SNE visualization of the learnt conference and language embeddings. Figure taken from Joshi et al. (2020)" numbered="true" lightbox="true">}}

    **Results**: [Figure 6](#figure-t-sne-visualization-of-the-learnt-conference-and-language-embeddings-figure-taken-from-joshi-et-al-2020) shows that LREC and WS embeddings are closer to the cluster of language embeddings than the other conferences. Moreover, we can observe that the NLP research for low-resource languages is still lacking; otherwise, we would expect conference embeddings to be at the bottom half of the Figure 6 and overlap with embeddings for languages from Class 1 and 2.

4. The authors used cosine distance between language embeddings and author embeddings to rank how important a language is to a particular author (out of all the languages that the author has worked with).

    **Results**: First, some low-resource languages have focused research groups, but some do not (despite having million of speakers). Second, resource-rich languages have a diverse range of authors working on them.

## Conclusion: Why Diversity Matters?

The inclusion of different languages in the NLP research community is lacking. The consequence is that "the rich get richer and the poor get poorer" as native speakers of low-resource languages are drawn to work on resource-rich languages due to the lower barrier of entry. Furthermore, some low-resource languages are typologically different from resource-rich languages, thereby jeopardizing the performance of transfer learning for these languages.
