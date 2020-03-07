# Unsupervised Learning of API Aliasing Specifications

## ABSTRACT

Code completion plays a prominent role in modern integrated development environments (IDEs). Machine learning has become ubiquitous in analogous natural language writing and search software, surfacing more relevant autocompletions and search suggestions in fewer keystrokes. Prior research has reported training high-accuracy, deep neural networks for modeling source code, but little attention has been given to the practical constraints imposed by interactive developer tools.

代码完成在现代集成开发环境(ide)中扮演着重要的角色。在类似的自然语言写作和搜索软件中，机器学习已经变得无处不在，用更少的按键显示更多相关的自动补全和搜索建议。先前的研究报告了训练高精度、深度的神经网络用于建模源代码，但是很少注意交互式开发工具施加的实际约束。

In particular, neural language models for source code modeling like the one described in Maybe Deep Neural Networks are the Best Choice for Modeling Source Code[20] are framed around code completion, but only report accuracy of next-token prediction. However, in order for a language model (LM) to work well within real-world code completion systems, it must also 
- always make suggestions that produce valid code that typechecks, to support code completion’s role in correctnesschecking, 
- return instantaneous results to help programmers code more efficiently in fewer keystrokes, and
- be small enough to fit comfortably on disk and in memory on developer workstations, since virtually all modern IDEs run locally and support offline usage.

特别是，用于源代码建模的神经语言模型，如Deep neural Networks所描述的可能是建模源代码的最佳选择，[20]是围绕着代码完成而建立的，但只报告下一个标记预测的准确性。然而，为了使语言模型(LM)能够在真实的代码完成系统中很好地工作，它还必须
- 总是提出建议，产生有效的代码类型，以支持代码完成的作用，在纠正检查，
- 返回即时结果，以帮助程序员更有效地编码在更少的击键，和
- 足够小，以适应在磁盘和内存的开发人员工作站，因为几乎所有现代ide运行本地和支持离线使用。

To meet these additional requirements, we propose a novel design for predicting top-k next tokens that combines static analysis’ ability to enumerate all valid keywords and in-scope identifiers with the ability of a language model to place a probability distribution over them. Our model mixes character-level input representation with token output to represent out-of-vocabulary (OOV) tokens meaningfully and minimize prediction latency. OOV tokens can be predicted through detection of local repetition common in software. This design achieves state-of-art accuracy in source code modeling and fits the constraints imposed by real-world code completion implementations in modern IDEs.

为了满足这些额外的需求，我们提出了一种预测top-k next令牌的新设计，它结合了静态分析枚举所有有效关键字和范围内标识符的能力，以及语言模型对它们进行概率分布的能力。我们的模型将字符级的输入表示与令牌输出混合起来，以有意义地表示词汇表外(OOV)的令牌，并最小化预测延迟。OOV令牌可以通过检测软件中常见的本地重复来进行预测。这种设计在源代码建模方面达到了最先进的精度，并且符合现代ide中实际代码完成实现所施加的约束。

## 1 INTRODUCTION

Code completion is a tremendously popular tool for coding assistance, implemented across a wide range of programming languages and environments. In An Empirical Investigation of Code Completion Usage by Professional Software Developers, Marasoiu et al. map out the diversity of use cases it fulfills for programmers, including correctness checking, typing assistance, and API search [24]. A study of programmers’ behaviors within the Eclipse IDE found that autocomplete was used up to several times per minute [28], as often as copy-paste! Historically, completion suggestions have been based primarily on static analysis and, as a result, suffered from low relevance [9]. Applying the constraints imposed by a programming language’s grammar and type system produces all valid suggestions but says nothing about which are likely.

代码补全是一种非常流行的编码辅助工具，可以跨多种编程语言和环境实现。Marasoiu等人对专业软件开发人员的代码补全使用情况进行了实证研究，他们绘制出它为程序员实现的用例的多样性，包括正确性检查、类型帮助和API搜索[24]。一项对Eclipse IDE中程序员行为的研究发现，autocomplete每分钟使用[28]多达几次，与复制-粘贴一样频繁!从历史上看，完成建议主要基于静态分析，因此，相关性[9]较低。应用编程语言的语法和类型系统所施加的约束，会产生所有有效的建议，但对于哪些建议是可能的，却只字未提。

### 1.1 Language modeling

In order to provide more relevant autocomplete results, researchers have looked to exploit the naturalness of source code through language modeling [14, 30]. Given a token sequence $S =t_1,t_2,...,t_n$, a language model (LM) estimates the probability p(S) as a product of conditional probabilities

$p(S)=\prod_{i=1}^n p(t_i|t_1,t_2,...,t_{i-1})$

为了提供更多相关的自动完成结果，研究人员希望通过语言建模来利用源代码的自然性[14,30]。给定一个令牌序列$S =t_1,t_2，…，t_n$，一个语言模型(LM)估计概率p(S)作为一个乘积的条件概率

State-of-art LMs for natural language are typically based on recurrent neural networks (RNNs) and have shown remarkable prediction accuracy in tasks ranging from machine translation to speech recognition [25]. Figure 3 depicts the basic RNN configuration: an input sequence $x_1, x_2, ..., x_n$ that the network learns to encode into hidden states $h_1,h_2, ...,h_n$ and decode as output. Each hidden state is computed as a combination of the previous hidden state and the next input sequence value.

用于自然语言的最先进的LMs通常基于递归神经网络(RNNs)，并在从机器翻译到语音识别[25]等任务中显示出显著的预测准确性。图3描述了基本的RNN配置:一个输入序列$x_1, x_2，…， x_n$，网络学会将其编码成隐藏状态，$h_1,h_2，…，h_n$，解码为输出。每个隐藏状态都作为前一个隐藏状态和下一个输入序列值的组合进行计算。

In source code modeling, the input sequence $x_1, x_2, ..., x_n$ consists of vectors representing the previous n tokens, abstract syntax tree (AST) nodes, characters, or even partial tokens. Commonly these inputs are represented by their integer index into an input vocabulary V and the network will learn a dimensionality reduction $f (x) : IR|V | → IRm$ form << |V | through an embedding layer. Whereas one-hot encoded vectors x, x’ ∈ R|V | have x ⊥ x’, the more compact vector space Rm can capture semantic relationships among the transformed input vectors.

For the classification problem of selecting the high probability next word x from a vocabulary V , the RNN is often connected to a softmax output and optimized to minimize cross-entropy loss. At a high-level, the softmax probability function S(x) : IR|V | → IR|V | produces an output vector y so that Í|V | i=1 yi = 1. This function allows the network to learn during training to decode the final RNN hidden state as probabilities of each word x ∈ V .

One of the most important choices in building an LM for source code is how this output vocabulary V is constructed. As in the input sequence vocabulary, it could range from scanner tokens to individual characters. The model’s vocabulary has implications for what can be predicted and how quickly predictions can be made. For example, a character-level model with V = {0, 1, ..., 255} corresponding to ascii characters can output any arbitrary alphanumeric word, but requires numerous prediction requests. On the other hand, choosing V to be the set of keywords for a given programming language makes it so only keywords and nothing else can be predicted, but whole tokens can be predicted in a single request.

在为源代码构建LM时，最重要的选择之一是如何构造这个输出词汇表V。与输入序列词汇表一样，它的范围从扫描令牌到单个字符。该模型的词汇表对预测内容和预测速度有一定的影响。例如，一个具有V ={0,1，…255}对应的ascii字符可以输出任何任意的字母数字单词，但需要大量的预测请求。另一方面，选择V作为给定编程语言的关键字集，使得只能预测关键字而不能预测其他任何东西，但是可以在单个请求中预测整个令牌。

### 1.2 Modern IDE constraints

Popular IDEs such as Visual Studio, IntelliJ, and Eclipse have in common that support for various programming languages is provided through a plugin architecture. This enables programmers to augment their IDE with additional language-specific functionality by downloading and installing extensions. These plugins provide interactive functionality to assist programmers writing software and include features like syntax highlighting, reporting of errors and warnings, and code completion.

流行的ide(如Visual Studio、IntelliJ和Eclipse)都有一个共同点，即通过插件架构提供对各种编程语言的支持。这使程序员能够通过下载和安装扩展来使用额外的特定于语言的功能来增强他们的IDE。这些插件提供了交互功能来帮助程序员编写软件，包括语法突出显示、错误和警告报告以及代码完成等功能。

Since one of the values that autocomplete provides is typing assistance, developers are interested in instantaneous completion suggestions. The user experience literature states that completion results must arrive within 100ms of user action to be perceived as instantaneous [26, 32]. This latency upper bound puts limits on LM size, the amount of processing that can be done before and after model predictions, and the number of predictions that can be made within an autocomplete request.

因为autocomplete提供的一个值是类型帮助，所以开发人员对即时完成建议很感兴趣。用户体验文献指出，完成结果必须在用户操作的100毫秒内到达，才能被视为瞬间完成[26,32]。这个延迟上限限制了LM大小、在模型预测之前和之后可以完成的处理数量，以及在自动完成请求中可以进行的预测数量。

With regard to model size, deep neural networks for language modeling might contain hundreds of millions or even billions of parameters [19]. Since each parameter represents a decimal value, an LM can quickly exceed the memory and disk capacity of a developer machine. Furthermore, a key finding from Jozefowicz et al. was that, given sufficient training data, the accuracy of RNN LMs improves with increasing size until a larger model cannot fit in GPU memory. In the context of IDE tools, accuracy improvements must be weighed against the resource costs of deploying and running a larger model on programmer workstations.

在模型大小方面，用于语言建模的深度神经网络可能包含数亿甚至数十亿的参数[19]。由于每个参数表示一个十进制值，LM可能很快超过开发人员机器的内存和磁盘容量。此外，Jozefowicz等人的一个关键发现是，在提供足够的训练数据的情况下，RNN LMs的精度会随着尺寸的增大而提高，直到更大的模型无法装入GPU内存。在IDE工具的环境中，准确性的提高必须与在程序员工作站部署和运行更大模型的资源成本进行权衡。

Finally, programmers expect that accepting completion suggestions will, at the very least, produce programs that compile. As a matter of fact, Marasoiu et al. found in a study of Dart programmers’ usage of code completion that developers often leverage autocomplete as a quick check of their code’s correctness [24]. In general, an LM cannot guarantee valid output. Penalizing invalid suggestions more heavily than valid but incorrect ones at training time by incorporating type analysis is an option, but would only decrease the likelihood of invalid suggestions. To ensure that suggestions are always valid, the model should be asked to rank already validated tokens or else any invalid suggestions it produces must be filtered out post-prediction.

最后，程序员希望接受完成建议至少能生成可编译的程序。事实上，Marasoiu等人在一项关于Dart程序员使用代码补全的研究中发现，开发人员经常利用自动补全来快速检查他们的代码的正确性[24]。通常，LM不能保证有效的输出。在培训时，通过合并类型分析对无效建议的惩罚比对有效但不正确的建议的惩罚更重是一种选择，但只会降低无效建议的可能性。为了确保建议总是有效的，应该要求模型对已经验证过的令牌进行排序，否则它产生的任何无效建议都必须在预测后过滤掉。

### 1.3 Summary of contributions

- This work details and evaluates a design for incorporating the predictive power of language modeling within existing IDE code completion systems. 
- We discuss and compare prior work on neural source code modeling to address the open vocabulary problem. 
- State-of-art accuracy is reported for source code modeling on a dynamic programming language. 
- The largest of three corpora we studied is made available along with our source code [3].

- 这项工作的细节和评估的设计，纳入预测能力的语言建模现有的IDE代码完成系统。
- 我们讨论和比较以前的工作在神经源代码建模，以解决开放词汇表的问题。
- 最先进的准确性报告的源代码建模的动态编程语言。
- 我们研究的三个语料库中最大的是提供与我们的源代码[3]。

This paper is organized to first give a brief history of the research that influenced our design. We then delve into the specifics of the design with a focus on modeling. We cover input representation, neural architecture, and training configurations, contrasting the details and performance of a character-input model with a tokeninput model. The design section concludes with a discussion of how model results can be synthesized with the list of keywords and inscope identifiers produced by type analysis. We then characterize each of the datasets used for model training and evaluation, and report prediction accuracy in the context of comparable source code modeling results. An additional benchmark for prediction latency is reported to demonstrate the fitness of our approach for the IDE setting. Lastly, along with a concluding discussion, we consider threats to the validity of our work.

这篇论文的组织首先给出了一个简短的历史研究，影响了我们的设计。然后我们深入研究设计的细节，重点是建模。我们将介绍输入表示、神经结构和训练配置，并将字符输入模型的细节和性能与令牌输入模型进行对比。设计部分最后讨论了如何使用关键字列表和类型分析产生的范围标识符来综合模型结果。然后，我们描述用于模型训练和评估的每个数据集，并在可比的源代码建模结果上下文中报告预测的准确性。报告了一个用于预测延迟的附加基准测试，以证明我们的方法适合于IDE设置。最后，在最后的讨论中，我们考虑了对我们工作有效性的威胁。

## 2 BACKGROUND

The idea of leveraging machine learning to improve code completion suggestion ranking was proposed as early as 2009 in Bruch et al. [9], who considered the Eclipse IDE’s unfortunate prioritization of all java.lang.Object methods when calling a method on any inheriting object. Hindle et al. connected this idea to the concept of natural language modeling and developed an n-gram model for Java completion [14]. Raychev et al. and White et al. proposed replacing the n-gram model for code completion with RNN [34, 38], which was reaffirmed as a superior tool in the 2019 paper Maybe Deep Neural Networks are the Best Choice for Modeling Source Code [20].

早在2009年Bruch等人就提出了利用机器学习来提高代码完成建议排名的想法，他们认为Eclipse IDE对所有java.lang进行了不幸的优先排序。在对任何继承对象调用方法时使用对象方法。Hindle等人将这一想法与自然语言建模的概念联系起来，开发了一个用于Java完成[14]的n-gram模型。Raychev等和White等人提出用RNN代替n-gram模型进行代码补全[34,38]，这在2019年的论文中被再次确认为一种优越的工具，也许深度神经网络是建模源代码[20]的最佳选择。

### 2.1 Pointer networks

One of the major challenges researchers have encountered in applying LMs to software languages is that new words, especially identifier names, occur at a much faster rate than in natural language [5], leading to a higher incidence of OOV tokens. One remediation for this issue is a pointer network [37] where non-vocabulary tokens can be predicted by reference to an input sequence token. This strategy has been leveraged in multiple previous studies on source code modeling [7, 21]. There is a natural compatibility between pointer networks and source code because of the prevalence of locally repeated terms in software, but they do not address the problem of unknown token representation. The pointer network can only learn that a term appearing in one code location is likely to be repeated in another neighboring location.

研究人员在将LMs应用于软件语言时遇到的主要挑战之一是，新单词(尤其是标识符名称)的出现速度比自然语言[5]快得多，这导致OOV标记的发生率更高。解决这个问题的一个方法是指针网络[37]，在这个网络中，可以通过引用输入序列令牌来预测非词汇表令牌。这种策略已经在以前的多个源代码建模研究中得到了应用[7,21]。由于软件中局部重复术语的流行，指针网络和源代码之间存在一种天然的兼容性，但是它们不能解决未知的令牌表示的问题。指针网络只能知道在一个代码位置出现的术语可能在另一个相邻位置重复。

### 2.2 Subword LM

A more general idea from Karampatsis et al. is subword modeling [20], adapted from the work of Sennrich et al. on machine translation [36]. They represent source code through sequences of partial tokens and include special end-of-token symbols so that whole tokens can be constructed at prediction time. However, while this strategy solves the problem of OOV token representation, it introduces a new challenge at prediction time. Whereas a token-level LM only requires a single prediction, a subword model implies that a tree of partial, candidate suggestions must be searched to produce a list of high relevance results. Beam search [1] is a natural fit for this task and terminates more quickly than a complete search, but subword predictions would nonetheless need to be extraordinarily fast in order to fit within the 100ms response window.

Karampatsis等人的一个更一般的想法是subword modeling[20]，改编自Sennrich等人对机器翻译[36]的工作。它们通过部分令牌序列表示源代码，并包含特殊的令牌结束符号，以便在预测时构造整个令牌。然而，该策略在解决OOV令牌表示问题的同时，也在预测时间上带来了新的挑战。一个标记级LM只需要一个预测，而一个子单词模型意味着必须搜索一个局部的、候选的建议树，以生成一个高相关性结果的列表。波束搜索[1]自然适合这项任务，它的终止速度比完整搜索快得多，但是子单词预测仍然需要非常快才能适应100ms响应窗口。

TODO://

## 3 DESIGN

A hybrid strategy is to map input character sequences onto likely next tokens. Such a network combines the single-prediction speed of a token LM with the ability of a character-level model to meaningfully represent new words. The main drawback of this approach compared to a pure character-level LM like the one in Karampatsis et al. is that the network cannot predict novel words.

混合策略是将输入字符序列映射到可能的下一个标记上。这种网络结合了令牌LM的单次预测速度和字符级模型有意义地表示新单词的能力。与Karampatsis等人的纯字符级LM相比，这种方法的主要缺点是网络无法预测新单词。

## 8 COMPARISON TO STATE OF THE ART

Learning Python Code Suggestion with a Sparse Pointer Network [7] and Code Completion with Neural Attention and Pointer Networks [21] are two recent studies that demonstrated state-of-art accuracy predicting next tokens in dynamically typed programming languages. They each report top-1 accuracy on Python codebases. The former also reports top-5 accuracy and the latter includes results from applying the PHOG: Probabilistic Model for Code [8] model to their Python corpus. Our partial token LM exceeded both results when trained and evaluated on the open-source GitHub Dart corpus.

使用稀疏指针网络[7]学习Python代码建议和使用神经注意和指针网络[21]完成代码是最近的两项研究，它们展示了在动态类型编程语言中预测下一个标记的最先进的准确性。它们都报告了Python代码库的最高1级精度。前者还报告了前5名的准确性，后者包括将PHOG: Probabilistic Model for Code [8] Model应用到Python语料库的结果。我们的部分token LM在开源GitHub Dart语料库上进行培训和评估时，其结果都超过了这两项。

Unfortunately the corpora and kinds of tokens which are included in source code model accuracy measurements vary greatly between studies. While some models might predict all tokens including punctuation, literals, and declaration names, others might not include any of these token types. This lack of consensus makes direct comparison a challenge. For instance, Raychev et al. [34] report 0.9 top-3 accuracy but only on 20 test samples all of which are method invocations. On the other hand, Karampatsis et al. predict all token types in a large random sample of open-source Java corpora [20].

遗憾的是，在不同的研究中，包含在源代码模型精度测量中的语料库和标记的种类差异很大。虽然有些模型可能预测所有标记(包括标点、文字和声明名称)，但其他模型可能不包括任何这些标记类型。缺乏共识使得直接比较成为一个挑战。例如，Raychev等人的[34]报告了0.9的前3名精度，但是只有在20个测试样本中，所有这些都是方法调用。另一方面，Karampatsis等人预测了开源Java语料库[20]的大型随机样本中的所有令牌类型。

Even worse, several studies have highlighted the prevalence of duplication in codebases commonly used for training and evaluating source code models [4, 23]. Allamanis finds that the presence of duplicate examples between training and test datasets leads to accuracy metrics inflated up to 100% [4]. Our preprocessing step in which duplicate examples are removed from each of the corpora limits the impact of this problem.

更糟糕的是，一些研究已经强调了在通常用于培训和评估源代码模型的代码库中重复的普遍性[4,23]。Allamanis发现，训练数据集和测试数据集之间存在重复的例子，导致准确性指标膨胀到100%[4]。在我们的预处理步骤中，从每个语料库中删除重复的例子限制了这个问题的影响。

### 8.1 Performance of repetition detection

The secondary network that detects repetition of input sequence tokens scored 0.9051 precision and 0.9071 recall on test data. 3.311% of the keywords, identifiers, and literals in the GitHub Dart corpus are repetitions of OOV tokens occurring within the input sequence. Another 30.545% belong to the token vocabulary and are not repetitions. This implies that repetition detection predicts a true positive that the base LM assigns zero probability in 3.003% of examples. It will make a false positive prediction and incorrectly reassign probability mass from a non-repeat, in-vocabulary token 2.899% of the time.

检测重复输入序列令牌的辅助网络对测试数据的精确度和召回率分别为0.9051和0.9071。GitHub Dart语料库中的关键字、标识符和文本中有3.311%是在输入序列中出现的OOV标记的重复。另外30.545%属于标记性词汇，不属于重复。这意味着重复检测预测了一个真正的正数，即在3.003%的例子中，基LM赋值为零的概率。它将作出一个错误的阳性预测和不正确地重新分配概率质量从一个非重复，在词汇表标记2.899%的时间。

### 8.2 Model size

After an order of magnitude size reduction from post-training quantization, the token input model’s size is 114M. The character input model clocks in at 65M. Both models are large but fall within an acceptable range to distribute alongside developer tools targeting programmer workstations.

经过训练后量化一个数量级的大小缩减后，令牌输入模型的大小为114M。字符输入模型为65M。这两个模型都很大，但都在可接受的范围内，可以与针对程序员工作站的开发人员工具一起发布。

### 8.3 Prediction latency

The 10,000 requests prediction benchmark was run before and after applying post-training quantization with TensorFlow Lite. Before quantization, the benchmark completed in 30 minutes and the average request time was 179 milliseconds. After quantization, the total time was reduced to 18 minutes and the average request time shortened to 109ms. 75.33% of the requests completed in under 110ms, right around our 100ms latency target. Since prediction latency grows with vocabulary size, our benchmark results suggest that the model is as large as it can be without degrading the user experience. It is also clear from these results that similar models needing to make multiple subword predictions are too slow for code completion.

在使用TensorFlow Lite应用训练后量化之前和之后运行10,000个请求预测基准。在量化之前，基准测试在30分钟内完成，平均请求时间为179毫秒。量化后，总时间减少到18分钟，平均请求时间缩短到109ms。75.33%的请求是在110ms以下完成的，接近我们100ms的延迟目标。由于预测延迟随词汇量的增加而增加，我们的基准测试结果表明，模型尽可能大，同时又不会降低用户体验。从这些结果还可以清楚地看出，需要进行多个子单词预测的类似模型对于代码完成来说太慢了。

## 9 THREATS TO VALIDITY

As Hellendoorn et al. caution in their study of real-world code completions, the benchmarks we use to measure code completion models and their similarity to real-world usage scenarios can have significant impact on reported accuracy and usefulness in programming assistance [13]. In particular, our models were exclusively trained on already written and reviewed code. This is a common practice in software language modeling, but there is an implicit assumption that these models will generalize well to actively developed code.

Hellendoorn等人在研究真实世界的代码完成情况时警告说，我们用来衡量代码完成模型及其与真实世界使用场景的相似性的基准可能会对报告的准确性和编程辅助[13]的有用性产生重大影响。特别是，我们的模型专门针对已经编写和审查的代码进行培训。这是软件语言建模中的一个常见实践，但是有一个隐含的假设，即这些模型将很好地泛化到主动开发的代码中。

Along the same lines, one positive aspect of code completion solutions driven by type analysis is that they do not suffer from concept drift. Whereas static analysis will exhibit constant performance as new libraries, frameworks, and coding conventions evolve, it is likely that the quality of a solution based on language modeling would degrade. One example of this phenomenon from our study was a change to the Dart programming language’s style guide to recommend omitting the optional new keyword. Although some time has passed since this change was made, our models still suggest including new as part of constructor invocations. The reason for this is that most of the training examples still follow the previous style recommendation. While we can address this existing problem by applying a simple transformation to our training corpora, we cannot anticipate future evolutions in our models.

同样，由类型分析驱动的代码完成解决方案的一个积极方面是，它们不受概念漂移的影响。虽然随着新的库、框架和编码惯例的发展，静态分析将表现出稳定的性能，但是基于语言建模的解决方案的质量很可能会下降。在我们的研究中，这种现象的一个例子是对Dart编程语言的样式指南进行了更改，建议省略可选的new关键字。虽然这个更改已经过去了一段时间，但是我们的模型仍然建议将new作为构造函数调用的一部分。这样做的原因是，大多数训练示例仍然遵循前面的样式建议。虽然我们可以通过对我们的训练语料库应用一个简单的转换来解决这个存在的问题，但是我们不能在我们的模型中预测未来的演进。

## 10 CONCLUSIONS

We introduced a new approach to source code modeling that combines a character-input, token-output LM with an auxiliary network for local repetition detection and static analysis for prediction validation. Constraints on prediction latency, model size, and output validity were motivated from relevant user experience studies [24, 26, 28, 32].

我们介绍了一种新的源代码建模方法，它将字符输入、令牌输出LM与用于局部重复检测的辅助网络和用于预测验证的静态分析相结合。预测延迟、模型大小和输出有效性的约束来自于相关的用户体验研究[24,26,28,32]。

The open vocabulary nature of source code calls for a model that can draw signal from new words at prediction time. And it is critically important that a completion model can suggest newnames as programmers write new functions and libraries. We considered several prior attempts to address the open vocabulary problem in neural source code modeling [7, 20, 21]. Networks that predict partial tokens are incompatible with the latency requirements of real-world code completion, and a token-level pointer network cannot incorporate signal from OOV tokens.

源代码的开放词汇表特性需要一个模型，该模型可以在预测时从新单词中提取信号。在程序员编写新函数和库时，补全模型可以建议使用新名称，这一点非常重要。我们考虑了之前在神经源代码建模中解决开放词汇表问题的几次尝试[7,20,21]。预测部分令牌的网络与实际代码完成的延迟要求不兼容，而且令牌级指针网络不能合并来自OOV令牌的信号。

Based on these considerations, we believe that a hybrid model which learns subtoken representations but predicts whole tokens and can predict new names through repetition detection is wellsuited to this task. The accuracy of such a model on large, diverse corpora of Dart code meets or exceeds all comparable source code modeling results in the literature we reviewed.

基于这些考虑，我们认为一个学习子标记表示但预测整个标记并能通过重复检测预测新名称的混合模型非常适合这项任务。这样一个模型在大型、多样的Dart代码语料库上的准确性达到或超过了我们所查阅的文献中所有可比较的源代码建模结果。

Our approach leverages a powerful combination between static analysis’ ability to enumerate valid keywords and identifiers in scope and the capability from language modeling to place probability distributions over token sequences. In future work, we hope to study the modeling impact of incorporating type and program context information available through static analysis as training signal. Nguyen et al. found these elements significant in their ngram source code model SLAMC [31], and it’s easy to imagine that these improvements could translate to neural source code models.

我们的方法利用了静态分析在范围内枚举有效关键字和标识符的能力，以及从语言建模到在令牌序列上放置概率分布的能力之间的强大组合。在未来的工作中，我们希望研究将静态分析得到的类型和程序上下文信息合并为训练信号对建模的影响。Nguyen等人在他们的ngram源代码模型SLAMC[31]中发现了这些重要的元素，很容易想象这些改进可以转化为神经源代码模型。

A large token vocabulary remains a challenge to training and prediction performance as well as model size. One of the remediations in our work was to limit the size of output vocabulary to 100k tokens, which also limits model accuracy. Although beam search over subword language model output at prediction time is a major drawback, a smaller output vocabulary of word pieces and the corresponding ability to generate identifier names not seen at training time makes such an architecture well worth exploring.

大量的令牌词汇表仍然是对训练和预测性能以及模型大小的挑战。我们的工作中的一个补救措施是将输出词汇表的大小限制为100k，这也限制了模型的准确性。虽然在预测时对子单词语言模型输出进行波束搜索是一个主要的缺点，但是单词块的更小的输出词汇表和相应的生成标识符名称的能力(在训练时没有看到)使得这种体系结构非常值得探索。
