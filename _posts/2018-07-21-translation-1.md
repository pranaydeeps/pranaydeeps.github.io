---
layout: post
title: Progress in Machine Translation - Pt. 1
categories: NLP
description: A multi-part post mainly focusing on the build up towards Neural Machine Translation without any parallel data. Part 1 explores the roots of basic translation and some of the progress through the years.
---

## Before NMT Systems

As can be seen from the post (link word embedding blog), it is possible to represent words in a mathematical vector form which represents their semantic meaning. Machine Translation is however, a task which expands way beyond the understanding of words. The model needs to learn not only the laws and syntax of multiple languages, but also how to convert from one to another. As expected, this task is highly demanding from a computational perspective. Owing to this, Translation systems were mostly phrase-based. This means that translation was based on small units called 'phrases' only. The Model would translate a phrase from one language to another, given similar samples based on simple probabilistic rules. 

As you would expect though, these systems though reliant for basic translation, were not syntactically very accurate. Multiple reasons can be pointed out. 

- Phrase Reordering would be required: When translating from one phrase to another, one also needs to worry about the position of the phrase in the sentence. Some languages have a different way of placing words than others. This was accounted for by building a second language model to reorder the phrases. However, that only adds to the computation.
- Loss of Context: Since the model is only looking at one phrase to translate and not it's adjoining phrases, there is a huge loss of context. Lots of words have multiple meaning depending on the context. From our example in the (Word Embeddings post). 'Please bear with me' has a different meaning for 'bear', whereas 'I see a big brown bear' has a seperate meaning for 'bear'.
- Morphological Errors: Our model is only translating phrases, hence it has no idea about the tense or any part of the structure of the source text. Hence, it would be impossible to correctly place morphological forms of words in the translation.

To correct these cons for phrase-based systems, came the NMT or Neural Machine Translation Systems. These systems centre around RNNs or Recurrent Neural Networks. The RNN Endoder-Decoder Architecture has been central to the development of not only Translation but a significant amount of Language and Sequence Modelling tasks over the years. 

## Understanding the RNN Encoder-Decoder

Any Endoder-Decoder Architecture centres around the idea that an intermediate representation can be learnt using the Encoder, which is sufficient to encode every bit of information presented to us, and the Decoder is capable of decoding this representation back to some meaningful form.

An RNN based Encoder-Decoder works on similar lines. One RNN encodes a sequence of symbols into a fixed lenght vector representation, and another RNN decoded representations into another sequence of symbols. Esentially, the encoder and decoder together are trying to jointly minimize the conditional probability of a target sequence, given a source sequence.
