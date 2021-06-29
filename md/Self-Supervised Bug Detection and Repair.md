# Self-Supervised Bug Detection and Repair

Machine learning-based program analyses have recently shown the promise of integrating formal and probabilistic reasoning towards aiding software development. However, in the absence of large annotated corpora, training these analyses is challenging. Towards addressing this, we present BUGLAB, an approach for self-supervised learning of bug detection and repair. BUGLAB co-trains two models: (1) a detector model that learns to detect and repair bugs in code, (2) a selector model that learns to create buggy code for the detector to use as training data. A Python implementation of BUGLAB improves by up to 30% upon baseline methods on a test dataset of 2374 real-life bugs and finds 19 previously unknown bugs in open-source software.

最近，基于机器学习的程序分析显示出整合形式推理和概率推理以帮助软件开发的前景。 然而，在没有大型注释语料库的情况下，训练这些分析是具有挑战性的。 为了解决这个问题，我们提出了 BUGLAB，这是一种自监督学习错误检测和修复的方法。 BUGLAB 共同训练两个模型：(1) 一个学习检测和修复代码错误的检测器模型，(2) 一个选择器模型，它学习为检测器创建错误代码以用作训练数据。 BUGLAB 的 Python 实现在 2374 个真实错误的测试数据集上比基线方法提高了 30%，并在开源软件中发现了 19 个以前未知的错误。

## 1 Introduction

Detecting and repairing bugs in source code requires strong reasoning skills over formal structures — such as data and control flow —and ambiguous information— such as identifier names, coding idioms, and comments. Traditional program analyses are able to detect critical bugs through formal reasoning and combinatorial search, but need to be manually coded by experts. That is a lengthy and costly process, which misses the opportunity to use ambiguous information pervasive within code.

检测和修复源代码中的错误需要对正式结构（例如数据和控制流）和不明确的信息（例如标识符名称、编码习惯用法和注释）具有很强的推理能力。 传统的程序分析能够通过形式推理和组合搜索来检测关键错误，但需要由专家手动编码。 这是一个漫长而昂贵的过程，它错过了在代码中使用普遍存在的模糊信息的机会。

Towards broadening the applicability of such methods, and utilizing ambiguous information, deep learning-based bug detection methods are being investigated [21, 3, 13]. These methods have the potential to further improve the engineering of software we rely on every day. However, many challenges in the area remain open, such as creating robust bug detection and repair methods that cover a wide range of common bugs in the absence of large supervised training corpora. Existing work focuses on randomly inserted bugs [21, 13], Cloze test proxy tasks [3], corpora of small code edits that may contain bugs [9] or build errors [27]. All these approaches rely on datasets of very limited size or ones known not to be representative of the characteristics of bugs found in real code.

为了扩大此类方法的适用性并利用模糊信息，正在研究基于深度学习的错误检测方法 [21, 3, 13]。 这些方法有可能进一步改进我们每天依赖的软件工程。 然而，该领域的许多挑战仍然存在，例如在缺乏大型监督训练语料库的情况下创建强大的错误检测和修复方法，以涵盖广泛的常见错误。 现有工作侧重于随机插入的错误 [21, 13]、完形填空测试代理任务 [3]、可能包含错误 [9] 或构建错误的小代码编辑语料库 [27]。 所有这些方法都依赖于规模非常有限的数据集，或者已知不能代表真实代码中发现的错误特征的数据集。

In this work, we propose BUGLAB, a self-supervised approach that trains robust bug detectors by co-training a bug selector that learns to create hard-to-detect bugs (Sec. 2). For example, for a given code snippet with two well-named variables, a variable misuse bug may be easy to detect and repair, whereas an incorrect comparison operator might be significantly harder to identify. We propose a neural architecture for BUGLAB (Sec. 3) and implement it for Python (Sec. 4). Our implementation considers four broad classes of bugs and shows improved performance over training with randomly inserted bugs on a new, manually curated test set of 2374 real-life bugs (Sec. 5). Furthermore, we tested our trained models on popular open-source Python packages to identify 19 previously unreported bugs, though false positive rates remain impractical. We hope that creating machine learning methods that can detect these bugs early and assist developers will speed up software development and allow engineers to deliver more robust software.

在这项工作中，我们提出了 BUGLAB，这是一种自我监督的方法，它通过共同训练一个错误选择器来训练强大的错误检测器，该选择器学习创建难以检测的错误（第 2 节）。例如，对于具有两个命名良好的变量的给定代码片段，变量滥用错误可能很容易检测和修复，而错误的比较运算符可能很难识别。我们为 BUGLAB（第 3 节）提出了一种神经架构，并为 Python（第 4 节）实现了它。我们的实现考虑了四大类错误，并在一个新的、手动策划的 2374 个真实错误的测试集上展示了随机插入错误的训练性能（第 5 节）。此外，我们在流行的开源 Python 包上测试了我们的训练模型，以识别 19 个以前未报告的错误，尽管误报率仍然不切实际。我们希望创建能够及早发现这些错误并帮助开发人员的机器学习方法将加快软件开发并允许工程师提供更强大的软件。

Contributions: (a) we present BUGLAB, a self-supervised approach for learning to detect and repair real-life bugs; (b) a manually curated dataset of 2374 real-world bugs in Python; (c) an evaluation of a Python implementation of BUGLAB using graph neural networks and relational transformers covering a wide range of realistic bugs showing 30% improvements over non-self-supervised baselines and simple methods that use masked language modelling pre-training for bug detection.

贡献：（a）我们提出了 BUGLAB，一种自我监督的方法，用于学习检测和修复现实生活中的错误； (b) 手动整理的 Python 中 2374 个真实世界错误的数据集； (c) 使用图神经网络和关系转换器对 BUGLAB 的 Python 实现进行评估，涵盖广泛的现实错误，显示比非自监督基线和使用掩码语言建模预训练进行错误检测的简单方法提高了 30% .

## 2 Self-Supervised Bug Detection

In this section, we first introduce the concept of code rewriting, and then use it to define BUGLAB as a framework for self-supervised learning of bug detection and repair.

在本节中，我们首先介绍代码重写的概念，然后用它来定义BUGLAB作为一个bug检测和修复的自监督学习的框架。

## 3 Neural Models

## 4 A Python Implementation

## 5 Evaluation

## 6 Related Work

Detecting bugs in source code has been researched since the early days of computing. Traditionally, bug detection is tackled as a formal task, where any code that cannot be proved to satisfy some correctness property may contain a bug. This is essential for security- and safety-critical bugs, but not for other — equally common — bugs. In the last decade, software engineering and programming language research have increasingly realized ambiguous information within code (e.g. variable names, comments) contains valuable information and using this information can yield valuable results [2]. The main premise is that patterns in source code, such as patterns in names, control, and data flow can be informative. This information can also be exploited to detect some bugs. For example, Ray et al. [23] noted that even simple language models tend to assign lower probability to buggy code.

从计算的早期就开始研究检测源代码中的错误。 传统上，错误检测被视为一项正式任务，其中任何无法证明满足某些正确性属性的代码都可能包含错误。 这对于安全性和安全性关键错误至关重要，但对于其他同样常见的错误则不然。 在过去十年中，软件工程和编程语言研究越来越意识到代码中的模糊信息（例如变量名称、注释）包含有价值的信息，使用这些信息可以产生有价值的结果 [2]。 主要前提是源代码中的模式，例如名称、控制和数据流中的模式可以提供信息。 也可以利用此信息来检测一些错误。 例如，雷等人。 [23] 指出，即使是简单的语言模型也倾向于为错误代码分配较低的概率。

Multiple static analysis methods have been researched that combine some form of data-oriented bug detection. This ranges from language model-based tools, such as the early work of Wang et al. [31] to specification-mining tools such as the work of Eberhardt et al. [10]. BUGLAB is related to DeepBugs [21] which uses an MLP over a limited window of code tokens and train separate models to detect wrong operators, operands, and argument swappings. BUGLAB opts for a more structured representation of code and a single model. Allamanis et al. [3], Vasic et al. [29], Hellendoorn et al. [13] tackle variable misuse bugs (one of the kinds of bugs included in PYBUGLAB) but either by randomly introducing the bugs in code or using a Cloze-like test. Instead, BUGLAB opts for a self-supervised approach and tackles a broader range of bugs. Dinella et al. [9] learn a supervised sequential model that performs graph transformations that replicate small edits in code (refactoring, introducing functionality, bug fixing, etc.). Their model — Hoppity — could serve as a learnable rewrite operation in BUGLAB in future work. Dynamic analysis methods have also been researched with promising results [30], but collecting representative dynamic traces over a diverse set of programs at scale (e.g. from the top Python packages used in this work) is practically impossible.

已经研究了多种结合某种形式的面向数据的错误检测的静态分析方法。这包括基于语言模型的工具，例如 Wang 等人的早期工作。 [31] 到规范挖掘工具，例如 Eberhardt 等人的工作。 [10]。 BUGLAB 与 DeepBugs [21] 相关，它在有限的代码标记窗口上使用 MLP 并训练单独的模型来检测错误的运算符、操作数和参数交换。 BUGLAB 选择更结构化的代码表示和单一模型。阿拉马尼斯等人。 [3]，瓦西克等。 [29]，Hellendoorn 等。 [13] 解决可变滥用错误（PYBUGLAB 中包含的一种错误），但可以通过在代码中随机引入错误或使用类似完形填空的测试来解决。相反，BUGLAB 选择了一种自我监督的方法并解决了更广泛的错误。迪内拉等人。 [9] 学习一个监督序列模型，该模型执行图形转换，复制代码中的小编辑（重构、引入功能、错误修复等）。他们的模型——Hoppity——可以在未来的工作中作为 BUGLAB 中可学习的重写操作。动态分析方法的研究也取得了可喜的成果 [30]，但实际上不可能在各种程序集上（例如，从本工作中使用的顶级 Python 包）收集具有代表性的动态轨迹。

BUGLAB is related to ideas around self-supervised learning recently explored in deep learning, computer vision, and NLP. In our case, we aim to train a bug detection model without using training data from real-life bugs. BUGLAB resembles ELECTRA [6], with the important difference that the rewrites to the input code go beyond single token replacement that need to respect strict constraints of programming languages (syntax, variable scopes) and the model is directly used for bug detection, rather than for pre-training. The main BUGLAB objective Eq. 1 also resembles GANs [12] with the exception that the objective is non-differentiable (introducing a bug alters the discrete data representation), the selector is a structured probabilistic code rewriting model, and that we are mainly interested in the bug detector (analogous to the discriminator) rather than the selector.

BUGLAB 与最近在深度学习、计算机视觉和 NLP 中探索的自监督学习相关。 在我们的例子中，我们的目标是在不使用来自真实错误的训练数据的情况下训练错误检测模型。 BUGLAB 类似于 ELECTRA [6]，重要的区别在于对输入代码的重写超越了需要尊重编程语言（语法、变量范围）的严格约束的单个标记替换，并且该模型直接用于错误检测，而不是 用于预训练。 主要的 BUGLAB 目标方程。 1 也类似于 GAN [12]，除了目标是不可微的（引入错误会改变离散数据表示），选择器是结构化的概率代码重写模型，我们主要对错误检测器（类似 到鉴别器）而不是选择器。

## 7 Discussion and Conclusions

Learned program analysis offer the promise to improve the way we develop software. They also offer a great opportunity to study machine learning models that combine formal and probabilistic reasoning. Towards achieving these we presented BUGLAB, a self-supervised approach for learning program analyses, that improves upon baseline methods and detects bugs in real-life code. We also identified previously unknown limitations of bug-detecting machine learning methods, informing future research. This, importantly, includes the relative large gap of performance of existing methods on corpora of randomly inserted bugs— commonly used in prior work— and real-life bugs.

学习程序分析有望改进我们开发软件的方式。 它们还提供了研究结合形式推理和概率推理的机器学习模型的绝佳机会。 为了实现这些，我们提出了 BUGLAB，一种用于学习程序分析的自我监督方法，它改进了基线方法并检测了现实生活中的代码中的错误。 我们还发现了之前未知的错误检测机器学习方法的局限性，为未来的研究提供了参考。 重要的是，这包括现有方法在随机插入的错误语料库上的性能相对较大的差距 - 通常在以前的工作中使用 - 和现实生活中的错误。
