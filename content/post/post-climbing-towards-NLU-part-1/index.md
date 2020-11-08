---
# Documentation: https://sourcethemes.com/academic/docs/managing-content/

title: "Paper Summary - Climbing Towards NLU: On Meaning, Form, and Understanding in the Age of Data (Part One)"
subtitle: "What does meaning really mean?"
summary: "What does meaning really mean?"
authors: [admin]
tags: []
categories: []
date: 2020-08-02T16:11:56-03:00
lastmod: 2020-08-02T16:11:56-03:00
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
---

## Introduction

[Emily M. Bender](https://faculty.washington.edu/ebender/publications.html) and [Alexander Koller](http://www.coli.uni-saarland.de/~koller/) received the Best Theme Paper award at ACL 2020 for their position paper "[Climbing Towards NLU: On Meaning, Form, and Understanding in the Age of Data](https://www.aclweb.org/anthology/2020.acl-main.463/)" at ACL 2020. They posit that language models, trained purely on form, cannot learn the conventional meaning and communicative intent of a linguistic expression.

This blog post summarizes the definitions of form, conventional meaning, and communicative intent in the paper and visualizes the concept of 'understanding the meaning of a linguistic expression.'

## Definitions
### Form

> We take *form* to be any observable realization of language: marks on a page, pixels or bytes in a digital representation
of text, or movements of the articulators

Form essentially refers to the language expression that we can see or hear.

### Conventional Meaning

> The conventional meaning of an expression (word, phrase, sentence) is what is constant across all of its possible contexts of use. Conventional meaning is an abstract object that represents the communicative potential of a form, given the linguistic system it is drawn from.

The *conventional meaning* of a word is the meaning derived from its linguistic form. It is fixed by convention (i.e., the linguistic system) – loosely speaking, the conventional meaning of a word can be read off from a dictionary.

### Communicative intent

> When humans use language, we do so for a purpose: We do not talk for the joy of moving our articulators, but in order to achieve some communicative intent. $...$ Communicative intents are about something that is outside of language.

Communicative intent motivates a speaker to perform a speech act and grounds the speech act in the speaker's world. For example, when we hear the command, "please close the window," we know that the speaker is staying in a room where there is an open window, and the speaker wants us to close the window.

## Conclusion: What does it mean to *understand*?

> We take *meaning* to be the relation $M \in E \times I $ which contains pairs $(e, i)$ of natural language expressions $e$ and the communicative intents $i$ they can be used to evoke. $...$ Each linguistic system (say, English) provides a relation
$C \in E \times S $, which contains pairs $(e, s)$ of expressions $e$ and their conventional meanings $s$. $...$ Upon hearing $e$, the listener then reconstructs $s$ and uses their own knowledge of the communicative situation and their hypotheses about the speaker’s state of mind and intention in an attempt to deduce $i$.

{{< figure src="illustration.png" title="An illustration of what the girl understands from the boy's response. " lightbox="true" class="img-md-75">}}

In the short conversation illustrated above, the *form* is 'no,' and the girl understands that its conventional meaning is a negative response to her question. She then infers that his intent, in this context, is to inform her that he does not want to revisit Hawaii. She further hypothesizes the reason, even though the boy can deny it because he never says so.

To say that we understand an utterance, we need to be able to decipher the conventional meaning from the form and the communicative intent behind it. Simply put, we must know what the utterance refers to, and we must be able to relate the utterance to the speaker's state of mind or intention (which is not expressed in the form).
