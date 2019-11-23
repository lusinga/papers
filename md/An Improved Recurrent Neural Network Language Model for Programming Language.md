# An Improved Recurrent Neural Network Language Model for Programming Language

## Abstract

Language models are applied to the programming language. However, the existing language models may be confused with different tokens with the same name in different scopes and can generate the syntax error code. In this paper, we proposed a grammar language model to solve these two problems. The model is an improved recurrent neural network language model. The improved recurrent neural network language model has the scope-awared input feature and the grammar output mask. Weevaluated our model and existing language models on a C99 code dataset. Our model gets a perplexity value of 2.91 and a top-1 accuracy rate of 74.23% which is much better than other models.

语言模型适用于编程语言。然而，现有的语言模型可能会与不同范围内同名的不同标记混淆，并可能生成语法错误代码。在本文中，我们提出了一个语法语言模型来解决这两个问题。该模型是一种改进的递归神经网络语言模型。改进的递归神经网络语言模型具有范围分明的输入特征和语法输出掩模。我们在 C99 代码数据集上评估了我们的模型和现有的语言模型。我们的模型获得了 2.91 的困惑值和 74.23% 的 top-1 准确率，这比其他模型要好得多。

## I. INTRODUCTION

Recently, natural language processing(NLP) methods have been applied on the software engineering, such as code completion[1], [2], language migration [3], code synthesis [4], code summarization [5], [6] and so on.

最近，自然语言处理 (NLP) 方法已经应用于软件工程，如代码完成 [1]，[2]，语言迁移 [3], 代码合成 [4] 、代码摘要 [5] 、 [6] 等等。

Language model(LM) is one of the most widely-used NLP methods applied to programming language. A language model is a probability distribution over sequences of words. Given a sequence whose length is n, it will assign probability $P(x_1, x_2, ..., x_n)$ to the sequence. For compute this probability, a decomposition is applied to the target probability $P(x_1, x_2, ... ,x_n) = \prod_{i=1}^{i=n}P(x_i|x1,...,x_{i-1})$, so language model needs to compute the distribution of $P(x_t | x_1, ... , x_{t-1})$ where $x_1,...,x_{t-1}$ is the previous context and $x_t$ is a token from a vocabulary D. The recent best language model is the neural network language model [7], [8]. In NLP,neural network language model has been shown to be able to learn the high-level structure of the language [9]. The conditional recurrent language model has become the standard decoder of the sequence to sequence model [10].

语言模型 (LM) 是应用最广泛的 NLP 方法之一。语言模型是单词序列的概率分布。给定一个长度为 n 的序列，它将分配概率 $$ P (x_1，x_2，.，x_n) $$ 给序列。为了计算这个概率，分解应用于目标概率 $ P (x_1，x_2，.，x_n) = \ prod _ {i = 1} {i = n} P (x_i | x1，.，x _ {i-1}) $, 所以语言模型需要计算 $ P (x_t | x_1，.,X _ {t-1}) $ 其中 $ x _ 1，.，x _ {t-1} $ 是以前的上下文，$ x _ t $ 是词汇 D 的标记。最近最好的语言模型是神经网络语言模型 [7]，[8]。在自然语言处理中，神经网络语言模型被证明能够学习语言的高级结构 [9]。条件循环语言模型已经成为序列到序列模型的标准解码器 [10]。

Applying Neural Network Language Model(NNLM) to the
programming language is straightforward since we can simply
take the code as a sequence of lexical tokens [11]. However,
there are two problems in this view. Firstly, this model cannot distinguish two different tokens with the same name but in different scopes since the NNLM only takes the code as the lexical token sequence. For example, in Fig. 1a, there are two pairs of tokens with the name x and the name y in the main scope and the minus scope. If we take the code as a sequence of lexical tokens, the tokens with the same name will be the same token. However, we know that in this code the token x in the main scope is corresponding to the token y in the minus scope. Secondly, the grammar in programming language is well defined and more important than the grammar in natural language. A minor grammar error in programming language can make the code compile failed. NNLM always gives the grammar error code a positive probability whose probability should be zero. For example, in the Fig. 1b, the penultimate right brace is lost. The probability of this code should be zero, but NNLM gives it a positive probability.

将神经网络语言模型 (NNLM) 应用于编程语言是简单的，因为我们可以简单地将代码作为一系列词汇标记 [11]。然而，这种观点有两个问题。首先，该模型不能区分两个同名但范围不同的标记，因为 NNLM 只将代码作为词汇标记序列。例如，在图 1a 中，主范围和减号范围中有两对名称为 x 和名称为 y 的标记。如果我们把代码作为一个词法标记序列，同名的标记将是相同的标记。然而，我们知道在这个代码中，主范围中的令牌 x 对应于负范围中的令牌 y。其次，编程语言中的语法定义明确，比自然语言中的语法更重要。编程语言中的一个小语法错误会导致代码编译失败。NNLM 总是给语法错误代码一个概率为零的正概率。例如，在图 1b 中，倒数第二个右括号丢失。此代码的概率应该为零，但 NNLM 给它一个正概率。

To solve the above problems, we proposed a grammar language model. The grammar language model is a recurrent language model with two new techniques – the scope-awared input feature and the grammar output mask. We maintain a scope stack. The stack contains feature vectors for each scope. A scope-awared token feature vector is calculated from the original token embedding feature and the corresponding scope feature vector. The scope-awared token feature vector is the input of our grammar language model. The grammar of most programming languages is LL(k) grammar. A parse table can be generated from the LL(k) grammar. When parsing a token sequence, the lookahead tokens should be in the parse table key set if and only if the token sequence are syntax right, so we can create a grammar output mask to rule out all syntax error tokens from the LM output distribution.

为了解决上述问题，我们提出了一个语法语言模型。语法语言模型是一个带有两种新技术的循环语言模型 -- 带有范围的输入特征和语法输出屏蔽。我们维护一个范围堆栈。堆栈包含每个范围的特征向量。从原始令牌嵌入特征和相应的范围特征向量计算出一个具有范围的令牌特征向量。带有范围的标记特征向量是我们语法语言模型的输入。大多数编程语言的语法是 LL (k) 语法。解析表可以从 LL (k) 语法生成。解析令牌序列时，当且仅当令牌序列语法正确时，前瞻令牌应该在解析表密钥集中, 因此，我们可以创建一个语法输出屏蔽，从 LM 输出分布中排除所有语法错误标记。

We trained our model on a C dataset collected from the CodeForces which is an online judge website. The grammar language model gets a perplexity value of 2.91 and a top-1 accuracy rate of 74.23%.

我们在一个从 CodeForces 收集的 C 数据集上训练我们的模型，CodeForces 是一个在线判断网站。语法语言模型的困惑值为 2.91，top-1 准确率为 74.23%。

## II. RELATED WORK

Statistical language models are used in many natural language technologies. N-gram models are widely used in statistical natural language processing due to their simplicity and good modeling performance, such as automatic speech recognition (ASR) [12], machine translation [13], [14], and spelling correction [15]–[17].

统计语言模型被用于许多自然语言技术。N-gram 模型由于其简单和良好的建模性能而被广泛应用于统计自然语言处理中，例如自动语音识别 (ASR) [12] 、机器翻译 [13] 、 [14] 和拼写更正 [15]-[17]。

Recently, neural network has been successfully applied to natural language processing. Xu et al. [18] firstly used artificial neural networks to learn the language model, which is proved to have better prerformence than standard statistical methods. Bengio et al. [19] generated a distributed representation of words along with the probability function for word sequences. A sequence of words gets high probability if the words have a nearby representation. Recurrent neural network language
models (RNNLMs) have recently demonstrated state-of-theart performance across a variety of tasks. Mikolov et al. [7],
[8] created and improved recurrent neural network based language model (RNNLM) using a hidden layer to store data
context. Context can cycle inside these networks for arbitrarily long time. Bengio et al. [20] showed that learning longterm dependencies by stochastic gradient descent can be quite difficult. Arisoy et al. [21] proposed deep-neural-networksbased language models (DNN LMs) to capture higher-level discriminative information about input feature. In addition to n-gram model, Sutskever et al. [10] presented a general end-to-end approach to sequence learning. Sequence-to-sequence model contains two multilayered RNNs. One encodes the input sequence to a vector of a fixed dimensionality and the other decodes the target sequence from the vector. Some language and translation models have added the soft attention or memory mechanisms [22]–[24]. The pointer mechanism is shown to be helpful in tasks like summarization [25], neural machine translation [26], code generation [27] and language modeling [28].

最近，神经网络已经成功地应用于自然语言处理。徐等人 [18] 首先使用人工神经网络来学习语言模型，这被证明比标准的统计方法具有更好的预形式。Bengio 等人 [19] 生成了单词序列的分布式表示以及单词序列的概率函数。如果单词有附近的表示，单词序列获得高概率。循环神经网络语言模型 (RNNLMs) 最近在各种任务中展示了最先进的性能。Mikolov 等人 [7]，[8] 创建并改进了基于递归神经网络的语言模型 (rnlm)，使用隐藏层存储数据上下文。上下文可以在这些网络中循环任意长时间。Bengio 等人 [20] 表明，通过随机梯度下降来学习长期依赖关系可能相当困难。Arisoy 等人 [21] 提出了基于深度神经网络的语言模型 (DNN LMs) 来捕获关于输入特征的更高级别的歧视性信息。除了 n-gram 模型，Sutskever 等人 [10] 提出了一种通用的端到端的序列学习方法。序列到序列模型包含两个多层 rnn。一个将输入序列编码为固定维度的向量，另一个从向量中解码目标序列。一些语言和翻译模型增加了软注意力或记忆机制 [22]-[24]。指针机制被证明在总结 [25] 、神经机器翻译 [26] 、代码生成 [27] 和语言建模 [28] 等任务中很有帮助。

Because of the good performance in natural language technologies, language models are also used in code completion and synthesis. Hindle et al. [2] used n-gram model Manning et al. [29] on lexical tokens to show that source code has repetition and predictability. Source code can be usefully captured by language models originally developed in the field of statistical natural language processing (NLP). Much papers combine different features of program with language model to solve code completion. Tu et al. [30] improved n-gram model with caching capability for recently seen tokens. Raychev et al. [31] captured common sequences of API calls with n-gram to predict API call. Nguyen et al. [1], [32] incorporated syntactic and semantic information into code tokens to increase accuracy of code suggestion. Bhoopchand et al. [33] further proposed a special mechanism called sparse pointer for better predicting out-of-vocabulary words in code completion. To solve the gradient vanishing problem, Li et al. [34] applied attention mechanism [22] to deal with longrange dependencies. Probabilistic grammars are also employed for code completion [35]–[37]. Bielik generalized probabilistic context free grammars (PCFGs) by allowing conditioning of a production rule beyond the parent nonterminal to capture rich contexts relevant to programs. Raychev improved the work of Bielik by learning a decision tree over abstract syntax trees to condition the prediction of a program element.

由于自然语言技术的良好性能，语言模型也用于代码完成和合成。Hindle 等人 [2] 在词汇标记上使用 n-gram 模型 Manning 等人 [29] 来表明源代码具有重复和可预测性。最初在统计自然语言处理 (NLP) 领域开发的语言模型可以有效地捕获源代码。许多论文将程序的不同特性与语言模型相结合来解决代码完成问题。Tu 等人 [30] 改进了 n-gram 模型，具有最近看到的令牌的缓存能力。Raychev 等人 [31] 用 n-gram 捕获了 API 调用的常见序列，以预测 API 调用。Nguyen 等人 [1]，[32] 将语法和语义信息纳入代码标记，以提高代码建议的准确性。Bhoophand 等人 [33] 进一步提出了一种特殊的机制，称为稀疏指针，用于更好地预测代码完成时的词汇外单词。为了解决梯度消失问题，Li 等人 [34] 应用注意机制 [22] 来处理长期依赖。概率语法也被用于代码完成 [35]-[37]。Bielik 广义概率上下文无关文法 (PCFGs)，通过允许对父非终端之外的生产规则的调节来捕获与程序相关的丰富上下文。Raychev 通过在抽象语法树上学习决策树来为程序元素的预测提供条件，从而改进了 Bielik 的工作。

## III. GRAMMAR LANGUAGE MODEL

## IV. EMPIRICAL EXPERIMENT

## V. CONCLUSION

In this paper, we proposed a grammar language model for programming language. Different from the previous models, our model utilizes the grammar information to distinguish the different tokens with the same name in different scopes and rules out the syntax error tokens from the prediction. The model is based on the traditional RNNLM, so we believe our model can be used in any place where the RNNLM works.

在本文中，我们提出了一个用于编程语言的语法语言模型。与以前的模型不同，我们的模型利用语法信息来区分不同范围内同名的不同标记，并从预测中排除语法错误标记。该模型基于传统的RNNLM，所以我们相信我们的模型可以在RNNLM工作的任何地方使用。
