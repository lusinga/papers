# Are Deep Neural Networks the Best Choice for Modeling Source Code?

## ABSTRACT

Current statistical language modeling techniques, including deeplearning based models, have proven to be quite effective for source code. We argue here that the special properties of source code can be exploited for further improvements. In this work, we enhance established language modeling approaches to handle the special challenges of modeling source code, such as: frequent changes, larger, changing vocabularies, deeply nested scopes, etc.We present a fast, nested language modeling toolkit specifically designed for software, with the ability to add & remove text, and mix & swap out many models. Specifically, we improve upon prior cache-modeling work and present a model with a much more expansive, multi-level notion of locality that we show to be well-suited for modeling software. We present results on varying corpora in comparison with traditional N-gram, as well as RNN, and LSTM deep-learning language models, and release all our source code for public use. Our evaluations suggest that carefully adapting N-gram models for source code can yield performance that surpasses even RNN and LSTM based deep-learning models.

目前的统计语言建模技术，包括基于深度学习的模型，已被证明是非常有效的源代码。我们在这里认为源代码的特殊属性可以用于进一步的改进。在这项工作中,我们加强了语言的建模方法来处理建模源代码的特殊挑战,如:频繁变化,大,变化的词汇表,深深地嵌套范围,完美呈现出快速、嵌套专门为软件设计语言建模工具包,能够添加和删除文本和混合&交换了许多模型。具体来说，我们改进了以前的缓存建模工作，并提供了一个具有更广泛的、多层次的局部性概念的模型，我们认为它非常适合建模软件。与传统的N-gram、RNN和LSTM深度学习语言模型相比，我们展示了不同语料库的结果，并发布了我们所有的源代码供公众使用。我们的评估表明，为源代码仔细地调整N-gram模型可以产生甚至超过RNN和基于LSTM的深度学习模型的性能。

## 1 INTRODUCTION

There has been much interest in the idea of “naturalness": viz., modeling
and exploiting the repetitive nature of software using statistical
techniques from natural language processing (NLP) [17, 26, 38].