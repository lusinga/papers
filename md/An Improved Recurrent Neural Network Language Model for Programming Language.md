# An Improved Recurrent Neural Network Language Model for Programming Language

## Abstract

Language models are applied to the programming language. However, the existing language models may be confused with different tokens with the same name in different scopes and can generate the syntax error code. In this paper, we proposed a grammar language model to solve these two problems. The model is an improved recurrent neural network language model. The improved recurrent neural network language model has the scope-awared input feature and the grammar output mask. Weevaluated our model and existing language models on a C99 code dataset. Our model gets a perplexity value of 2.91 and a top-1 accuracy rate of 74.23% which is much better than other models.

语言模型适用于编程语言。然而，现有的语言模型可能会与不同范围内同名的不同标记混淆，并可能生成语法错误代码。在本文中，我们提出了一个语法语言模型来解决这两个问题。该模型是一种改进的递归神经网络语言模型。改进的递归神经网络语言模型具有范围分明的输入特征和语法输出掩模。我们在 C99 代码数据集上评估了我们的模型和现有的语言模型。我们的模型获得了 2.91 的困惑值和 74.23% 的 top-1 准确率，这比其他模型要好得多。

## I. INTRODUCTION
Recently, natural language processing(NLP) methods have been applied on the software engineering, such as code completion[1], [2], language migration [3], code synthesis [4], code summarization [5], [6] and so on.

最近，自然语言处理 (NLP) 方法已经应用于软件工程，如代码完成 [1]，[2]，语言迁移 [3], 代码合成 [4] 、代码摘要 [5] 、 [6] 等等。

Language model(LM) is one of the most widely-used NLP methods applied to programming language. A language model is a probability distribution over sequences of words. Given a sequence whose length is n, it will assign probability $P(x_1, x_2, ..., x_n)$ to the sequence. For compute this probability, a decomposition is applied to the target probability $P(x_1, x_2, ... ,x_n) = \prod_{i=1}^{i=n}P(x_i|x1,...,x_{i-1})$, so language model needs to compute the distribution of $P(x_t | x_1, ... , x_{t-1})$ where $x_1,...,x_{t-1}$ is the previous context and $x_t$ is a token from a vocabulary D. The recent best language model is the neural network language model [7], [8]. In NLP,neural network language model has been shown to be able to learn the high-level structure of the language [9]. The conditional recurrent language model has become the standard decoder of the sequence to sequence model [10].

语言模型 (LM) 是应用最广泛的 NLP 方法之一。语言模型是单词序列的概率分布。给定一个长度为 n 的序列，它将分配概率 $ P (x_1，x_2，.，x_n) $ 给序列。为了计算这个概率，分解应用于目标概率 $ P (x_1，x_2，.，x_n) = \ prod _ {i = 1} {i = n} P (x_i | x1，.，x _ {i-1}) $, 所以语言模型需要计算 $ P (x_t | x_1，.,X _ {t-1}) $ 其中 $ x _ 1，.，x _ {t-1} $ 是以前的上下文，$ x _ t $ 是词汇 D 的标记。最近最好的语言模型是神经网络语言模型 [7]，[8]。在自然语言处理中，神经网络语言模型被证明能够学习语言的高级结构 [9]。条件循环语言模型已经成为序列到序列模型的标准解码器 [10]。

Applying Neural Network Language Model(NNLM) to the
programming language is straightforward since we can simply
take the code as a sequence of lexical tokens [11]. However,
there are two problems in this view. Firstly, this model cannot distinguish two different tokens with the same name but in different scopes since the NNLM only takes the code as the lexical token sequence. For example, in Fig. 1a, there are two pairs of tokens with the name x and the name y in the main scope and the minus scope. If we take the code as a sequence of lexical tokens, the tokens with the same name will be the same token. However, we know that in this code the token x in the main scope is corresponding to the token y in the minus scope. Secondly, the grammar in programming language is well defined and more important than the grammar in natural language. A minor grammar error in programming language can make the code compile failed. NNLM always gives the grammar error code a positive probability whose probability should be zero. For example, in the Fig. 1b, the penultimate right brace is lost. The probability of this code should be zero, but NNLM gives it a positive probability.

将神经网络语言模型 (NNLM) 应用于编程语言是简单的，因为我们可以简单地将代码作为一系列词汇标记 [11]。然而，这种观点有两个问题。首先，该模型不能区分两个同名但范围不同的标记，因为 NNLM 只将代码作为词汇标记序列。例如，在图 1a 中，主范围和减号范围中有两对名称为 x 和名称为 y 的标记。如果我们把代码作为一个词法标记序列，同名的标记将是相同的标记。然而，我们知道在这个代码中，主范围中的令牌 x 对应于负范围中的令牌 y。其次，编程语言中的语法定义明确，比自然语言中的语法更重要。编程语言中的一个小语法错误会导致代码编译失败。NNLM 总是给语法错误代码一个概率为零的正概率。例如，在图 1b 中，倒数第二个右括号丢失。此代码的概率应该为零，但 NNLM 给它一个正概率。

