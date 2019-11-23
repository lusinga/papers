# Sequence Model Design for Code Completion in the Modern IDE

## ABSTRACT

Code completion plays a prominent role in modern integrated development environments (IDEs). Machine learning has become
ubiquitous in analogous natural language writing and search software, surfacing more relevant autocompletions and search suggestions in fewer keystrokes. Prior research has reported training high-accuracy, deep neural networks for modeling source code, but little attention has been given to the practical constraints imposed by interactive developer tools.

代码完成在现代集成开发环境 (IDEs) 中扮演着重要的角色。机器学习已经在类似的自然语言写作和搜索软件中无处不在，在更少的击键中出现了更多相关的自动完成和搜索建议。先前的研究报告了训练高精度的深度神经网络来建模源代码，但是很少关注交互式开发工具带来的实际限制。

In particular, neural language models for source code modeling like the one described in Maybe Deep Neural Networks are the Best
Choice for Modeling Source Code[20] are framed around code completion, but only report accuracy of next-token prediction. However,
in order for a language model (LM) to work well within real-world code completion systems, it must also
- always make suggestions that produce valid code that typechecks, to support code completion’s role in correctnesschecking,
- return instantaneous results to help programmers code more efficiently in fewer keystrokes, and
- be small enough to fit comfortably on disk and in memory on developer workstations, since virtually all modern IDEs run locally and support offline usage.

特别是，源代码建模的神经语言模型，比如深神经网络中描述的模型，是源代码建模的最佳选择 [20] 是围绕代码完成构建的, 但只报告下一个标记预测的准确性。然而，为了使语言模型 (LM) 能够在现实世界的代码完成系统中很好地工作，它还必须
-总是提出建议，产生有效的代码进行打字检查，以支持代码完成在正确检查中的作用,
-返回即时结果，以帮助程序员以更少的击键更有效地编码，以及
-足够小，可以舒适地安装在磁盘和开发人员工作站的内存中，因为几乎所有现代 ide 都在本地运行，并支持离线使用。

To meet these additional requirements, we propose a novel design for predicting top-k next tokens that combines static analysis’ ability to enumerate all valid keywords and in-scope identifiers with the ability of a language model to place a probability distribution over them. Our model mixes character-level input representation with token output to represent out-of-vocabulary (OOV) tokens meaningfully and minimize prediction latency. OOV tokens can be predicted through detection of local repetition common in software. This design achieves state-of-art accuracy in source code modeling and fits the constraints imposed by real-world code completion implementations in modern IDEs.

为了满足这些额外的要求, 我们提出了一种预测 top-k next 令牌的新设计，该设计结合了静态分析枚举所有有效关键词和范围内标识符的能力和语言模型放置概率分布的能力他们。我们的模型将字符级输入表示与令牌输出混合，以有意义地表示词汇外 (OOV) 令牌，并最大限度地减少预测延迟。OOV 令牌可以通过检测软件中常见的本地重复来预测。该设计实现了源代码建模的最新准确性，并符合现代 ide 中真实世界代码完成实现的约束。

## 1 INTRODUCTION

Code completion is a tremendously popular tool for coding assistance, implemented across a wide range of programming languages and environments. In An Empirical Investigation of Code Completion Usage by Professional Software Developers, Marasoiu et al. map out the diversity of use cases it fulfills for programmers, including correctness checking, typing assistance, and API search [24]. A study of programmers’ behaviors within the Eclipse IDE found that autocomplete was used up to several times per minute [28], as often as copy-paste! Historically, completion suggestions have been based primarily on static analysis and, as a result, suffered from low relevance [9]. Applying the constraints imposed by a programming language’s grammar and type system produces all valid suggestions but says nothing about which are likely.

代码完成是一个非常受欢迎的编码辅助工具，在各种编程语言和环境中实现。在对专业软件开发人员代码完成使用情况的实证调查中，Marasoiu 等人绘制了它为程序员提供的用例的多样性，包括正确性检查、打字帮助, 和 API 搜索 [24]。一项对 Eclipse IDE 中程序员行为的研究发现，自动完成每分钟被使用几次 [28]，就像复制粘贴一样频繁!从历史上看，完成建议主要基于静态分析，因此相关性较低。应用编程语言的语法和类型系统施加的约束会产生所有有效的建议，但不会透露哪些建议是可能的。

### 1.1 Language modeling

In order to provide more relevant autocomplete results, researchers have looked to exploit the naturalness of source code through language modeling [14, 30]. Given a token sequence S = t1t2 · · · tn, a language model (LM) estimates the probability p(S) as a product of conditional probabilities 
p(S) = Îni=1 p(ti |t1, t2, ..., ti−1)

## 8 COMPARISON TO STATE OF THE ART

Learning Python Code Suggestion with a Sparse Pointer Network [7] and Code Completion with Neural Attention and Pointer Networks
[21] are two recent studies that demonstrated state-of-art accuracy predicting next tokens in dynamically typed programming languages. They each report top-1 accuracy on Python codebases. The former also reports top-5 accuracy and the latter includes results from applying the PHOG: Probabilistic Model for Code [8] model to their Python corpus. Our partial token LM exceeded both results when trained and evaluated on the open-source GitHub Dart corpus.

学习 Python 代码建议与稀疏指针网络 [7] 和代码完成与神经注意力和指针网络 [21] 是两个最近的研究，证明了最先进的准确性预测下一个令牌动态类型编程语言。他们都报告了 Python 代码库的 top-1 准确性。前者还报告了前 5 名的准确性，后者包括将 PHOG: 代码 [8] 模型的概率模型应用于他们的 Python 语料库的结果。当在开源 GitHub Dart 语料库上进行训练和评估时，我们的部分令牌 LM 超过了这两个结果。

Unfortunately the corpora and kinds of tokens which are included in source code model accuracy measurements vary greatly
between studies. While some models might predict all tokens including punctuation, literals, and declaration names, others might not include any of these token types. This lack of consensus makes direct comparison a challenge. For instance, Raychev et al. [34] report 0.9 top-3 accuracy but only on 20 test samples all of which are method invocations. On the other hand, Karampatsis et al. predict all token types in a large random sample of open-source Java corpora [20].

不幸的是，包含在源代码模型准确性测量中的语料库和令牌种类在不同的研究中差异很大。虽然一些模型可能预测所有标记，包括标点符号、文字和声明名称，但其他模型可能不包括任何这些标记类型。这种缺乏共识使得直接比较成为一个挑战。例如，Raychev 等人 [34] 报告了 0.9 的 top-3 准确性，但仅在 20 个测试样本上，所有这些都是方法调用。另一方面，Karampatsis 等人在一个大的开源 Java 语料库随机样本中预测所有令牌类型 [20]。

Even worse, several studies have highlighted the prevalence of duplication in codebases commonly used for training and evaluating
source code models [4, 23]. Allamanis finds that the presence of duplicate examples between training and test datasets leads to
accuracy metrics inflated up to 100% [4]. Our preprocessing step in which duplicate examples are removed from each of the corpora
limits the impact of this problem.

更糟糕的是，一些研究强调了常用于训练和评估源代码模型 [4,23] 的代码库中普遍存在重复。Allamanis 发现训练和测试数据集之间存在重复的例子，导致准确性指标膨胀到 100% [4]。我们的预处理步骤是从每个语料库中删除重复的例子，这限制了这个问题的影响。

### 8.1 Performance of repetition detection

The secondary network that detects repetition of input sequence tokens scored 0.9051 precision and 0.9071 recall on test data. 3.311% of the keywords, identifiers, and literals in the GitHub Dart corpus are repetitions of OOV tokens occurring within the input sequence. Another 30.545% belong to the token vocabulary and are not repetitions. This implies that repetition detection predicts a true positive that the base LM assigns zero probability in 3.003% of examples. It will make a false positive prediction and incorrectly reassign probability mass from a non-repeat, in-vocabulary token 2.899% of the time.

检测输入序列标记重复的二级网络对测试数据的精确度和召回率分别为 0.9051 和 0.9071。GitHub Dart 语料库中 3.311% 的关键词、标识符和文字是输入序列中 OOV 标记的重复。另外 30.545% 属于象征性词汇，不重复。这意味着重复检测预测一个真正的阳性，即基本 LM 在 3.003% 的例子中分配零概率。它会做出假阳性预测，并在 2.899% 的时间里错误地从非重复的词汇令牌中重新分配概率质量。

### 8.2 Model size

After an order of magnitude size reduction from post-training quantization, the token input model’s size is 114M. The character input model clocks in at 65M. Both models are large but fall within an acceptable range to distribute alongside developer tools targeting programmer workstations.

经过训练后量化的数量级大小缩减后，令牌输入模型的大小为 114 M。字符输入模型时钟在 65 米。这两种模型都很大，但都在可接受的范围内，可以与面向程序员工作站的开发工具一起分发。

### 8.3 Prediction latency

The 10,000 requests prediction benchmark was run before and after applying post-training quantization with TensorFlow Lite.
Before quantization, the benchmark completed in 30 minutes and the average request time was 179 milliseconds. After quantization,
the total time was reduced to 18 minutes and the average request time shortened to 109ms. 75.33% of the requests completed in under 110ms, right around our 100ms latency target. Since prediction latency grows with vocabulary size, our benchmark results suggest that the model is as large as it can be without degrading the user experience. It is also clear from these results that similar models needing to make multiple subword predictions are too slow for code completion.

在使用 TensorFlow Lite 应用训练后量化之前和之后运行 10,000 请求预测基准。量化前，基准测试在 30 分钟内完成，平均请求时间为 179 毫秒。量化后，总时间缩短至 18 分钟，平均请求时间缩短至 109 ms。75.33% 的请求在 110ms 以下完成，就在我们的 100ms 延迟目标附近。由于预测延迟随着词汇表的大小而增加，我们的基准测试结果表明，该模型尽可能大，而不会降低用户体验。从这些结果中也可以清楚地看出，需要进行多个子词预测的类似模型对代码完成来说太慢了。

## 9 THREATS TO VALIDITY

As Hellendoorn et al. caution in their study of real-world code completions, the benchmarks we use to measure code completion
models and their similarity to real-world usage scenarios can have significant impact on reported accuracy and usefulness in programming assistance [13]. In particular, our models were exclusively trained on already written and reviewed code. This is a common practice in software language modeling, but there is an implicit assumption that these models will generalize well to actively developed code.

正如 Hellendoorn 等人在研究现实世界的代码完成时所谨慎的那样, 我们用来衡量代码完成模型及其与现实世界使用场景的相似性的基准可以对编程帮助 [13 中报告的准确性和有用性产生重大影响。特别是，我们的模型专门在已经编写和审查的代码上进行培训。这是软件语言建模中的一种常见做法，但是有一个隐含的假设，即这些模型将很好地推广到积极开发的代码。

Along the same lines, one positive aspect of code completion solutions driven by type analysis is that they do not suffer from concept drift. Whereas static analysis will exhibit constant performance as new libraries, frameworks, and coding conventions evolve, it is likely that the quality of a solution based on language modeling would degrade. One example of this phenomenon from our study was a change to the Dart programming language’s style guide to recommend omitting the optional new keyword. Although some time has passed since this change was made, our models still suggest including new as part of constructor invocations. The reason for this is that most of the training examples still follow the previous style recommendation. While we can address this existing problem by applying a simple transformation to our training corpora, we cannot anticipate future evolutions in our models.

同样，类型分析驱动的代码完成解决方案的一个积极方面是它们不会遭受概念漂移。尽管随着新的库、框架和编码约定的发展，静态分析将表现出持续的性能，但基于语言建模的解决方案的质量可能会降低。我们研究中这种现象的一个例子是对 Dart 编程语言风格指南的修改，建议省略可选的新关键词。尽管这种改变已经过去了一段时间，但我们的模型仍然建议将 new 作为构造函数调用的一部分。原因是大多数训练示例仍然遵循以前的风格建议。虽然我们可以通过对我们的训练语料库进行简单的转换来解决这个现有的问题，但我们不能预测我们模型中的未来演变。

## 10 CONCLUSIONS

We introduced a new approach to source code modeling that combines a character-input, token-output LM with an auxiliary network
for local repetition detection and static analysis for prediction validation. Constraints on prediction latency, model size, and output validity were motivated from relevant user experience studies[24, 26, 28, 32].

我们引入了一种新的源代码建模方法，将字符输入、令牌输出 LM 与辅助网络相结合，用于本地重复检测和预测验证的静态分析。对预测延迟、模型大小和输出有效性的限制来自相关的用户体验研究 [24,26，28,32]。

The open vocabulary nature of source code calls for a model that can draw signal from new words at prediction time. And it is
critically important that a completion model can suggest new names as programmers write new functions and libraries. We considered
several prior attempts to address the open vocabulary problem in neural source code modeling [7, 20, 21]. Networks that predict
partial tokens are incompatible with the latency requirements of real-world code completion, and a token-level pointer network
cannot incorporate signal from OOV tokens.

源代码的开放词汇性质要求有一个模型，可以在预测时从新词中提取信号。当程序员编写新的函数和库时，完成模型可以建议新的名称是至关重要的。我们考虑了以前几次尝试来解决神经源代码建模 [7,20，21] 中的开放词汇问题。预测部分令牌的网络与现实世界代码完成的延迟要求不兼容，令牌级指针网络不能合并来自 OOV 令牌的信号。

Based on these considerations, we believe that a hybrid model which learns subtoken representations but predicts whole tokens and can predict new names through repetition detection is wellsuited to this task. The accuracy of such a model on large, diverse
corpora of Dart code meets or exceeds all comparable source code modeling results in the literature we reviewed.

基于这些考虑，我们认为，一种混合模型，它学习子令牌表示，但预测整个令牌，并可以通过重复检测预测新的名称，非常适合这项任务。在我们回顾的文献中，这种模型在大型、多样化的飞镖代码语料库上的准确性达到或超过了所有可比的源代码建模结果。

Our approach leverages a powerful combination between static analysis’ ability to enumerate valid keywords and identifiers in
scope and the capability from language modeling to place probability distributions over token sequences. In future work, we hope
to study the modeling impact of incorporating type and program context information available through static analysis as training
signal. Nguyen et al. found these elements significant in their n-gram source code model SLAMC [31], and it’s easy to imagine that
these improvements could translate to neural source code models.

我们的方法利用了静态分析在范围内枚举有效关键词和标识符的能力和语言建模在令牌序列上放置概率分布的能力之间的强大组合。在未来的工作中，我们希望研究将类型和程序上下文信息作为训练信号通过静态分析获得的建模影响。Nguyen 等人发现这些元素在他们的 n-gram 源代码模型 SLAMC [31] 中很重要，很容易想象这些改进可以转化为神经源代码模型。

A large token vocabulary remains a challenge to training and prediction performance as well as model size. One of the remediations
in our work was to limit the size of output vocabulary to 100k tokens, which also limits model accuracy. Although beam search over subword language model output at prediction time is a major drawback, a smaller output vocabulary of word pieces and the corresponding ability to generate identifier names not seen at training time makes such an architecture well worth exploring.

大量的令牌词汇对训练和预测性能以及模型大小仍然是一个挑战。我们工作中的一个补救措施是将输出词汇表的大小限制为 100k 令牌，这也限制了模型的准确性。尽管在预测时对子单词语言模型输出进行波束搜索是一个主要缺点, 较小的单词片段输出词汇表和相应的生成训练时看不到的标识符名称的能力使得这种架构非常值得探索。