# Maybe Deep Neural Networks are the Best Choice for Modeling Source Code

## ABSTRACT

Statistical language modeling techniques have successfully been applied to source code, yielding a variety of new software development tools, such as tools for code suggestion and improving readability. A major issue with these techniques is that code introduces new vocabulary at a far higher rate than natural language, as new identifier names proliferate. But traditional language models limit the vocabulary to a fixed set of common words. For code, this strong assumption has been shown to have a significant negative effect on predictive performance. But the open vocabulary version of the neural network language models for code have not been introduced in the literature. We present a new open-vocabulary neural language model for code that is not limited to a fixed vocabulary of identifier names.We employ a segmentation into subword units, subsequences of tokens chosen based on a compression criterion, following previous work in machine translation. Our network achieves best in class performance, outperforming even the state-of-the-art methods of Hellendoorn and Devanbu that are designed specifically to model code. Furthermore, we present a simplemethod for dynamically adapting themodel to a newtest project, resulting in increased performance. We showcase our methodology on code corpora in three different languages of over a billion tokens each, hundreds of times larger than in previous work. To our knowledge, this is the largest neural language model for code that has been reported.

统计语言建模技术已经成功地应用到源代码中，产生了各种新的软件开发工具，例如用于代码建议和提高可读性的工具。这些技术的一个主要问题是，随着新的标识符名称的激增，代码引入新词汇表的速度远高于自然语言。但是传统的语言模型将词汇表限制为一组固定的常用词汇。对于代码来说，这种强烈的假设已经被证明对预测性能有显著的负面影响。但是对于代码的神经网络语言模型的开放词汇版本在文献中还没有介绍。我们为代码提供了一个新的开放词汇表神经语言模型，它不局限于标识符名称的固定词汇表。我们采用分段到子词单位，标记的子序列选择基于压缩标准，在机器翻译之前的工作。我们的网络达到了最佳的课堂性能，甚至超过了最先进的方法Hellendoorn和Devanbu，它们是专门为模型代码设计的。此外，我们提出了一种简单的方法，动态调整模型以适应新的测试项目，从而提高性能。我们用三种不同的语言在代码库中展示了我们的方法，每种语言都有超过10亿的标记，比之前的工作大了数百倍。据我们所知，这是已报道的最大的用于代码的神经语言模型。

## 1 INTRODUCTION

Large corpora of open source software projects present an opportunity for creating new software development tools based on machine learning [4].Many of thesemethods are based on the hypothesis that much of software is natural, that is, because software is written for humans to read, it displays some of the same statistical properties as natural language. To quantify the degree of naturalness of a piece of software, Hindle et al [40] propose the use of statistical language modeling. A language model is a probability distribution over strings; by training a language model (LM) on a large corpus of well-written code, we hope that the LM will assign high probability to new code that is similar to the training set, in other words, code that is well-written, easy to read, and natural. There is now a large literature on language modeling for code [6, 12, 22, 37, 40, 58, 69].

大型的开源软件项目集为基于机器学习[4]创建新的软件开发工具提供了机会。这些方法中的许多都是基于这样一种假设，即许多软件是自然的，也就是说，因为软件是为人类阅读而编写的，所以它会显示一些与自然语言相同的统计特性。为了量化一个软件的自然程度，Hindle等[40]提出使用统计语言建模。语言模型是字符串的概率分布;通过在大量编写良好的代码语料库上训练语言模型(LM)，我们希望LM能够为与训练集相似的新代码分配高概率，换句话说，即编写良好、易于阅读和自然的代码。现在有大量关于代码语言建模的文献[6,12,22,37,40,58,69]。