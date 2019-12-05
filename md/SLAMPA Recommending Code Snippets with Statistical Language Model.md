# SLAMPA: Recommending Code Snippets with Statistical Language Model

## Abstract

Programming is typically a difficult and repetitive task. Programmers will encounter endless problems during programming, and they often need to write similar code over and over again. Over the years, many tools have been proposed to support programming. However, to the best of our knowledge, these approaches require high-quality queries or programming contexts, which are often difficult to be built or even unavailable.

编程通常是一项困难和重复的任务。程序员在编程过程中会遇到无穷无尽的问题，他们经常需要一遍又一遍地编写类似的代码。多年来，已经提出了许多工具来支持编程。然而，据我们所知，这些方法需要高质量的查询或编程上下文，这通常很难构建，甚至不可用。

To address this challenge, we propose SLAMPA, a novel tool which takes advantage of statistical language model and clone detection techniques to recommend code snippets during programming. Given a piece of incomplete code, SLAMPA first infers its intention using a neural language model. Then it retrieves code snippets from codebase with the support of an efficient clone detection technology Hybrid-CD we proposed. Finally, it recommends the most similar code snippets to programmers.

为了解决这个挑战，我们提出了 SLAMPA，这是一个新的工具，它利用统计语言模型和克隆检测技术在编程过程中推荐代码片段。给定一段不完整的代码，SLAMPA 首先使用神经语言模型推断其意图。然后，在我们提出的一种高效的克隆检测技术 Hybrid-CD 的支持下，从代码库中检索代码片段。最后，它向程序员推荐最相似的代码片段。

Our evaluation results demonstrate that Hybrid-CD precisely detects similar code snippets and it outperforms previous techniques. Our results also show that the snippets recommended by SLAMPA catch the intention of programmers and SLAMPA is capable of finding potential code reuse opportunities during programming.

我们的评估结果表明，Hybrid-CD 可以精确地检测到类似的代码片段，并且优于以前的技术。我们的结果还表明，SLAMPA 推荐的代码片段抓住了程序员的意图，SLAMPA 能够在编程过程中找到潜在的代码重用机会。

## I. INTRODUCTION

In software development activities, programmers may encounter endless problems and they may search for source code examples for help. Source code examples are critical for understanding concepts, applying fixes, improving performance, and extending software functionalities [1][2]. With the development of code repositories such as Github, SourceForge, millions of high-quality code snippets become available.

在软件开发活动中，程序员可能会遇到无穷无尽的问题，他们可能会搜索源代码示例以寻求帮助。源代码示例对于理解概念、应用修复、提高性能和扩展软件功能 [1] [2] 至关重要。随着 Github 、 SourceForge 等代码库的发展，数百万高质量的代码片段变得可用。

Unfortunately, finding appropriate code examples to cope with the problems encountered during programming is still a serious challenge for programmers. Although some existing works have investigated using free-form query (i.e., a query written in natural language, or a list of keywords) to search relevant code snippets [3][4], it can be difficult to construct proper queries, especially for those novices. For example, giving a incomplete code shown in Figure 1, it is hard to accurately depict this piece of code using natural language. Some publicly available code search engines take as input the entire code snippet [5][6], but these code search engines are only able to identify identical code fragments[7]. Furthermore, since it is impossible for programmers to be aware of all the relevant code snippets in code repositories, programmers may miss the potential code reuse
opportunities of the incomplete code and reinvent wheels during programming [8].

不幸的是，找到合适的代码示例来处理编程过程中遇到的问题对程序员来说仍然是一个严峻的挑战。尽管一些现有的作品已经使用自由格式查询 (即用自然语言编写的查询或关键词列表) 来搜索相关的代码片段 [3] [4], 构建正确的查询可能很困难，尤其是对于那些新手。例如，给出图 1 所示的不完整代码，很难用自然语言准确描述这段代码。一些公开的代码搜索引擎将整个代码片段 [5] [6] 作为输入，但是这些代码搜索引擎只能识别相同的代码片段 [7]。此外，由于程序员不可能知道代码库中的所有相关代码片段, 程序员可能会错过不完整代码的潜在代码重用机会，并在编程过程中重新发明轮子 [8]。

To handle these challenges, we propose a novel code snippets recommendation tool named SLAMPA (Statistical LAnguage Model based Programming Assistant). The recommended code snippets by SLAMPA can be used for understanding concepts, extending software functionalities, etc. For novices, SLAMPA is useful, since it presents code samples to better understand what to do next. Even for expedience programmers, SLAMPA can identify potential code reuse opportunities and thus increase their productivity.

为了应对这些挑战，我们提出了一个新的代码片段推荐工具 SLAMPA (基于统计语言模型的编程助手)。SLAMPA 推荐的代码片段可以用于理解概念、扩展软件功能等。对于新手来说，SLAMPA 很有用，因为它提供了代码示例来更好地理解下一步要做什么。即使对权宜之计程序员来说，SLAMPA 也可以识别潜在的代码重用机会，从而提高他们的生产率。

SLAMPA works by a combination of statistical language model and clone detection techniques. As Hindle et al. reported that programs, as a natural product of human effort, exhibit a good level of repetition [9]. SLAMPA captures the repetition of programs with a deep neural network based statistical language model, which detects long-range functional features in a context-dependent way. With the language model, SLAMPA can predict the intention of a piece of incompletely written code and further infers the next tokens. SLAMPA then implicitly expands the given incomplete code with the inferred tokens and uses Hybrid-
CD (Hybrid-Clone Detector), an efficient clone detection technology we proposed, to retrieve similar code snippets of such tokens. Finally, the most relevant code snippets are recommended to programmers.

SLAMPA 的工作原理是统计语言模型和克隆检测技术的结合。正如 Hindle 等人报道的那样，作为人类努力的自然产物，程序表现出良好的重复水平 [9]。SLAMPA 利用基于深度神经网络的统计语言模型捕捉程序的重复，该模型以上下文相关的方式检测远程功能特征。使用语言模型，SLAMPA 可以预测一段不完全编写的代码的意图，并进一步推断下一个标记。然后，SLAMPA 用推断的标记隐含地扩展给定的不完整代码，并使用我们提出的一种高效的克隆检测技术 -- 混合 CD (Hybrid-Clone Detector)。检索此类标记的类似代码片段。最后，向程序员推荐最相关的代码片段。

We evaluate the effectiveness of Hybrid-CD and SLAMPA on BigCloneBench [10], a large scale inter-project repository with manually labeled clone pairs. Our evaluation results demonstrate that Hybrid-CD outperforms previous techniques. Hybrid-CD is able to detect almost all the T1-ST3 clones (94% - 100%) and more than half of the MT3 clones (51%). Our experiments also show that SLAMPA
can indeed capture the intention of programmers via the statistical language model and find code reuse opportunities. For 68.2% of the queries, the relevant snippets can be found within the top 10 recommended results.

我们评估了 Hybrid-CD 和 SLAMPA 在 BigCloneBench [10 上的有效性，这是一个具有手动标记克隆对的大型项目间存储库。我们的评估结果表明，混合 CD 优于以前的技术。Hybrid-CD 能够检测几乎所有的 T1-ST3 克隆 (94%-100%) 和超过一半的 MT3 克隆 (51%)。我们的实验还表明，SLAMPA 确实可以通过统计语言模型捕捉程序员的意图，并找到代码重用的机会。对于 68.2% 的查询，相关的片段可以在前 10 个推荐结果中找到。

In summary, the contributions of this paper lie in:
- We propose SLAMPA, a novel code recommendation tool that makes it possible to automatically and accurately recommend code snippets during programming. To the best of our knowledge, we are the first to propose a combination of language model and code clone detection to recommend code snippets.
- We propose Hybrid-CD, a novel clone detection technology leveraging deep learning. Hybrid-CD combines automatically extracted high-level features (by a deep neural network) and handcrafted low-level features. Hybrid-CD can effectively detect clones in real-world repositories.
- We implement a prototype of SLAMPA and evaluate it on real-world repositories. Our results show that SLAMPA is of potential use in software development.

总之，本文的贡献在于:
-我们提出了 SLAMPA，这是一个新的代码推荐工具，可以在编程过程中自动准确地推荐代码片段。据我们所知，我们是第一个提出语言模型和代码克隆检测的组合来推荐代码片段的。
-我们提出了 Hybrid-CD，一种利用深度学习的新型克隆检测技术。Hybrid-CD 将自动提取的高级特征 (通过深度神经网络) 和手工制作的低级特征相结合。混合光盘可以有效地检测现实世界库中的克隆。
-我们实现了 SLAMPA 的原型，并在现实世界的存储库中进行评估。我们的结果表明 SLAMPA 在软件开发中有潜在的用途。

## II. BACKGROUND

Our approach combines recent advanced techniques from programming language processing and deep learning. We will introduce the background of these techniques in this section.

我们的方法结合了编程语言处理和深度学习的最新高级技术。我们将在本节介绍这些技术的背景。

## V. RELATED WORK

Currently, there are two strategies for code recommendations: recommendation by generation and recommendation by search. In this section, we will present related works about code generation and code search.

目前，代码推荐有两种策略: 按生成推荐和按搜索推荐。在本节中，我们将介绍有关代码生成和代码搜索的相关工作。

### A. Code Generation

In recent years, researchers try to adopt deep learning techniques for code generation. Balog et al. proposed Deep-Coder [27] to generate code. DeepCoder leverages Domain Specific Language (DSL) and program synthesis techniques to search and combine DSL into simple programs. Gu et al. [28] apply deep learning techniques for API learning and proposed DeepAPI. DeepAPI semantically generates API usage sequences for a given natural language query using RNNs.

近年来，研究人员试图采用深度学习技术来生成代码。Balog 等人提出了深编码器 [27] 来生成代码。DeepCoder 利用领域特定语言 (DSL) 和程序合成技术来搜索并将 DSL 组合成简单的程序。Gu 等人 [28] 将深度学习技术应用于 API 学习，并提出了 DeepAPI。DeepAPI 在语义上使用 RNNs 为给定的自然语言查询生成 API 使用序列。

A potential problem of these approaches is that the automatically generated code may not meet the requirements, or even impossible to be compiled.

这些方法的一个潜在问题是，自动生成的代码可能不满足需求，甚至无法编译。

SLAMPA evades this problem via the combination of intention analyzing and similar snippets retrieving. SLAMPA implicitly generates tokens according to the given incomplete code and finally recommends compilable code snippets from high-quality code repository to programmers.

SLAMPA通过结合意图分析和类似的片段检索来避免这个问题。SLAMPA根据给定的不完整代码隐式地生成标记，并最终向程序员推荐来自高质量代码存储库的可编译代码片段。

### B. Code Search

In code search, there are many approaches proposed. Much of the existing works focus on free-form query searching. CodeHow proposed by Lv et al. [3] and DeepCS proposed by Gu et al. [4] aim to find code snippets relevant to a user query written in natural language. DeepCS [4] incorporates an extended Boolean model and explores API documents to identify relationships between query terms and APIs. DeepCS measures the similarity between code snippets and user queries through joint embedding and deep
learning.

在代码搜索中，提出了许多方法。现有的工作大多集中在自由形式的查询搜索。Lv等人提出的CodeHow .[3]和Gu等人提出的DeepCS .[4]的目的是寻找与用自然语言编写的用户查询相关的代码片段。DeepCS[4]整合了一个扩展的布尔模型，并探索API文档来识别查询术语和API之间的关系。DeepCS通过联合嵌入和深度学习来度量代码段和用户查询之间的相似性。

Some publicly available code search engines [5][6] identify key words in the given code to find snippets. Unfortunately, these code search engines appear to be able to identify only identical code fragments [7].

[5. codesearch.com]

一些公开可用的代码搜索引擎[5][6]识别给定代码中的关键字以查找代码片段。不幸的是，这些代码搜索引擎似乎只能识别相同的代码片段[7]。

There are a few other researches attempting to tackle the code-to-code searching problem. FaCoY proposed by Kim et al. [7] takes a completed code fragment of some functionality as input, and finds semantically similar code fragments to the given fragment. It is used to find alternative implementations of some functionalities.

还有一些其他的研究试图解决代码到代码的搜索问题。由Kim等人提出的FaCoY .[7]采用一些功能的一个完整的代码片段作为输入，并找到语义上类似的代码片段的给定片段。它用于查找某些功能的替代实现。

SLAMPA differs from these existing code search techniques in the following aspects:
(1) SLAMPA takes a incomplete code as input, instead of free-form query, keywords or completed code snippet. 
(2) SLAMPA focuses on mining the intention under the given incomplete code for code recommendation.
(3) SLAMPA recommends code snippets during the process of programming, neither before programming (free-form query) nor after programming (completed code).

SLAMPA与这些现有的代码搜索技术在以下方面有所不同:
(1) SLAMPA采用不完整的代码作为输入，而不是自由形式的查询、关键字或完整的代码片段。
(2) SLAMPA侧重于在给定的不完整代码下挖掘代码推荐的意图。
(3) SLAMPA建议在编程过程中使用代码片段，无论是在编程之前(自由形式的查询)还是在编程之后(完成的代码)。

### C. Code Completion

Recently, researchers have investigated possible applications of statistical language model to code completion. Hindle et al. [9] applied a N-gram model to complete code, and Nguyen et al. [29] extended N-gram model by taking semantic information of source code into consideration. Nowadays, neural network techniques such as RNNs have achieved the state-of-the-art results in code completion tasks. Liu et al. [30] proposed a code completion model based on LSTM network and Li et al. [31] extended it with a pointer mixture network.

最近，研究人员调查了统计语言模型在代码完成中的可能应用。Hindle等人[9]采用N-gram模型来完成代码，Nguyen等人[29]考虑了源代码的语义信息，扩展了N-gram模型。现在，像RNNs这样的神经网络技术已经在代码完成任务方面取得了最先进的成果。Liu等人提出了一种基于LSTM网络的代码补全模型，Li等人利用指针混合网络对其进行了扩展。

Most of the existing works focus on next-token suggestion and come in the form of code completion plugins integrated with IDEs. These approaches cannot find completed code snippets.

大多数现有的工作集中于下一个标记建议，并以与ide集成的代码完成插件的形式出现。这些方法无法找到完整的代码片段。

SLAMPA adopts a simple neural language model and slightly adjusts it to repeatly generate tokens. It leverages the language model and the Hybrid-CD we proposed to retrieve compilable code snippets from high-quality code repository and recommend them to programmers. It does not try to automatically complete the entire program.

SLAMPA采用了一个简单的神经语言模型，并对其进行了轻微的调整以重复地生成标记。它利用了我们提出的语言模型和混合cd，从高质量的代码库中检索可编译的代码片段，并将它们推荐给程序员。它不会尝试自动完成整个程序。

It is apparent that we do not try to propose a new approach for building language model or code completion in this paper. Actually, we are looking forward to the development of code completion techniques. As we shown in Section IV that SLAMPA can be further enhanced by introducing more powerful language models.

很明显，我们在本文中并没有试图提出一种新的方法来构建语言模型或代码完成。实际上，我们期待着代码补全技术的发展。如第四节所示，SLAMPA可以通过引入更强大的语言模型来进一步增强。

## VI. CONCLUSION AND FUTURE WORK

In this paper, we present SLAMPA, a novel snippets code recommending tool with a statistical language model and a hybrid code clone technique named Hybrid-CD. Given a piece of incomplete code, SLAMPA can infer its intentions, retrieve relevant code from codebase with the support of Hybrid-CD, and recommend the most similar code snippets to programmers.

本文提出了一种新的代码片段推荐工具SLAMPA，该工具使用统计语言模型和混合代码克隆技术hybrid - cd。对于一段不完整的代码，SLAMPA可以推断它的意图，在Hybrid-CD的支持下从代码库中检索相关的代码，并向程序员推荐最相似的代码片段。

Our evaluation results show: 
(1) Hybrid-CD outperforms the existing works and is effective to retrieve similar code snippets; 
(2) Leveraging a deep neural language model, SLAMPA is able to find the potential reuse opportunities and recommends appropriate code snippets to programmers during programming; 
(3) The performance of SLAMPA is related to the capability of language model.

我们的评估结果显示:
(1) Hybrid-CD优于现有的工作，可以有效地检索相似的代码片段;
(2)利用深度神经语言模型，SLAMPA能够发现潜在的重用机会，并在编程过程中向程序员推荐适当的代码片段;
(3) SLAMPA的性能与语言模型的能力有关。

As for our future work, we will further enhance SLAMPA by introducing the latest language models (e.g., Pointer Sentinel-LSTM [34]). Furthermore, we will incorporate some domain specific knowledge (e.g., return type, related document and comments, etc. ) into SLAMPA, which are not in the scope of consideration yet in this work.

至于我们未来的工作，我们将通过引入最新的语言模型 (例如，指针 Sentinel-LSTM [34) 来进一步增强 SLAMPA。此外，我们将纳入一些特定领域的知识 (例如，返回类型、相关文档和评论等)。) 到 SLAMPA，这些都不在这项工作的考虑范围之内。
