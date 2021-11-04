# Towards Automating Code Review Activities

## Abstract

Code reviews are popular in both industrial and open source projects. The benefits of code reviews are widely recognized and include better code quality and lower likelihood of introducing bugs. However, since code review is a manual activity it comes at the cost of spending developers’ time on reviewing their teammates’ code.

代码审查在工业和开源项目中都很流行。 代码审查的好处被广泛认可，包括更好的代码质量和更低的引入错误的可能性。 但是，由于代码审查是一项手动活动，因此需要花费开发人员的时间来审查团队成员的代码。

Our goal is to make the first step towards partially automating the code review process, thus, possibly reducing the manual costs associated with it. We focus on both the contributor and the reviewer sides of the process, by training two different Deep Learning architectures. The first one learns code changes performed by developers during real code review activities, thus providing the contributor with a revised version of her code implementing code transformations usually recommended during code review before the code is even submitted for review. The second one automatically provides the reviewer commenting on a submitted code with the revised code implementing her comments expressed in natural language.

我们的目标是迈出部分自动化代码审查过程的第一步，从而可能减少与之相关的人工成本。 我们通过训练两种不同的深度学习架构，同时关注过程的贡献者和审查者双方。 第一个学习开发人员在实际代码审查活动期间执行的代码更改，从而为贡献者提供其代码的修订版本，实现代码转换期间通常建议的代码转换，甚至在代码提交审查之前。 第二个自动向审查者提供对提交的代码的评论，以及实施她以自然语言表达的评论的修订代码。

The empirical evaluation of the two models shows that, on the contributor side, the trained model succeeds in replicating the code transformations applied during code reviews in up to 16% of cases. On the reviewer side, the model can correctly implement a comment provided in natural language in up to 31% of cases. While these results are encouraging, more research is needed to make these models usable by developers.

两种模型的实证评估表明，在贡献者方面，训练模型成功复制了代码审查期间应用的代码转换，高达 16% 的案例。 在审阅者方面，该模型可以在高达 31% 的情况下正确实现以自然语言提供的评论。 虽然这些结果令人鼓舞，但还需要更多的研究来使这些模型可供开发人员使用。

## I. INTRODUCTION

Code Review is the process of analyzing source code written by a teammate to judge whether it is of sufficient quality to be integrated into the main code trunk. Recent studies provided evidence that reviewed code has lower chances of being buggy [1]–[3] and exhibit higher internal quality [3], likely being easier to comprehend and maintain. Given these benefits, code reviews are widely adopted both in industrial and open source projects with the goal of finding defects, improving code quality, and identifying alternative solutions.

Code Review是对队友编写的源代码进行分析，判断其质量是否足够好，可以集成到主代码主干中的过程。 最近的研究提供的证据表明，经过审查的代码出现错误的可能性较低 [1]-[3]，并且表现出更高的内部质量 [3]，可能更容易理解和维护。 鉴于这些好处，代码审查在工业和开源项目中被广泛采用，其目标是发现缺陷、提高代码质量和确定替代解决方案。

The benefits brought by code reviews do not come for free. Indeed, code reviews add additional expenses to the standard development costs due to the allocation of one or more reviewers having the responsibility of verifying the correctness, quality, and soundness of newly developed code. Bosu and Carver report that developers spend, on average, more than six hours per week reviewing code [4]. This is not surprising considering the high number of code changes reviewed in some projects: Rigby and Bird [5] show that industrial projects, such as Microsoft Bing, can undergo thousands of code reviews per month (3k in the case of Bing). Also, as highlighted by Czerwonka et al. [6], the effort spent in code review does not only represent a cost in terms of time, but also pushes developers to switch context from their tasks.

代码审查带来的好处不是免费的。 事实上，由于分配了一名或多名审查员负责验证新开发代码的正确性、质量和健全性，代码审查在标准开发成本上增加了额外费用。 Bosu 和 Carver 报告说，开发人员平均每周花费超过 6 个小时来审查代码 [4]。 考虑到某些项目中审查的大量代码更改，这并不奇怪：Rigby 和 Bird [5] 表明，工业项目，例如 Microsoft Bing，每月可以接受数千次代码审查（在 Bing 的情况下为 3k）。 此外，正如 Czerwonka 等人所强调的那样。 [6]，代码审查所花费的精力不仅代表时间成本，而且还会促使开发人员从他们的任务中切换上下文。

Our long-term goal is to reduce the cost of code reviewing by (partially) automating this time-consuming process. Indeed, we believe that several code review activities can be automated, such as, catching bugs, improving adherence to the project’s coding style, and refactoring suboptimal design decisions. The final goal is not to replace developers during code reviews but work with them in tandem by automatically solving (or suggesting) code quality issues that developers would manually catch and fix in their final checks. A complete automation, besides likely not being realistic, would also dismiss one of the benefits of code review: the sharing of knowledge among developers.

我们的长期目标是通过（部分）自动化这个耗时的过程来降低代码审查的成本。 事实上，我们相信一些代码审查活动可以自动化，例如捕捉错误、提高对项目编码风格的遵守以及重构次优设计决策。 最终目标不是在代码审查期间替换开发人员，而是通过自动解决（或建议）开发人员在最终检查中手动捕获和修复的代码质量问题与他们协同工作。 完全自动化除了可能不切实际之外，还会忽略代码审查的好处之一：开发人员之间的知识共享。

In this paper, we make a first step in this direction by using Deep Learning (DL) models to partially automate specific code review tasks. First, from the perspective of the contributor (i.e., the developer submitting the code for review), we train a transformer model [7] to “translate” the code submitted for review into a version implementing code changes that a reviewer is likely to suggest. In other words, we learn code changes recommended by reviewers during review activities and we try to automatically implement them on the code submitted for review. This could give a fast and preliminary feedback to the contributor as soon as she submits the code. This model has been trained on 17,194 code pairs of $C_s \to C_r$ where Cs is the code submitted for review and Cr is the code implementing a specific comment provided by the reviewer.

在本文中，我们通过使用深度学习 (DL) 模型来部分自动化特定的代码审查任务，朝着这个方向迈出了第一步。 首先，从贡献者（即提交代码进行审查的开发人员）的角度来看，我们训练了一个转换器模型 [7] 将提交审查的代码“翻译”为实施审查者可能建议的代码更改的版本 . 换句话说，我们在审查活动中学习审查者推荐的代码更改，并尝试在提交审查的代码上自动实施它们。 这可以在贡献者提交代码后立即向她提供快速和初步的反馈。 该模型已经在 17,194 个 Cs 到 Cr 的代码对上进行了训练，其中 Cs 是提交审查的代码，Cr 是实现审查者提供的特定评论的代码。

Once trained, the model can take as input a previously unseen code and recommend code changes as a reviewer would do. The used architecture is a classic encoder-decoder model with one encoder taking the submitted code as input and one decoder generating the revised source code.

训练完成后，模型可以将以前未见过的代码作为输入，并像审阅者一样推荐代码更改。 使用的架构是经典的编码器-解码器模型，其中一个编码器将提交的代码作为输入，一个解码器生成修改后的源代码。

Second, from the perspective of the reviewer, given the code under review (Cs) we want to provide the ability to automatically generate the code Cr implementing on Cs a specific recommendation expressed in natural language (Rnl) by the reviewer. This would allow (i) the reviewer to automatically attach to her natural language comment a preview of how the code would look like by implementing her recommendation, and (ii) the contributor to have a better understanding of what the reviewer is recommending. For such a task, we adapt the previous architecture to use two encoders and one decoder.

其次，从审查者的角度来看，鉴于审查中的代码 (Cs)，我们希望提供自动生成代码 Cr 的能力，以在 Cs 上实现审查者以自然语言 (Rnl) 表达的特定建议。 这将允许 (i) 审查者通过实施她的建议自动将代码外观的预览附加到她的自然语言评论，以及 (ii) 贡献者更好地理解审查者的建议。 对于这样的任务，我们调整以前的架构以使用两个编码器和一个解码器。

The two encoders take as input Cs and Rnl, respectively,
while the decoder is still in charge of generating Cr. The
model has been trained with 17,194 triplets <Cs, Rnl> -> Cr.

两个编码器分别将 Cs 和 Rnl 作为输入，
而解码器仍然负责生成 Cr。 这
模型已经用 17,194 个三元组 <Cs, Rnl> -> Cr 进行了训练。

## II. USING TRANSFORMERS TO AUTOMATE CODE REVIEW

## III. STUDY DESIGN

## IV. RESULTS DISCUSSION

## V. THREATS TO VALIDITY

## VI. RELATED WORK

We summarize the related literature focusing on DL in SE and studies related to code reviews. While DL has been used to support many different SE tasks, given the goal of our work, we only focus on techniques automating source code changes.

我们总结了关注 SE 中 DL 的相关文献和与代码审查相关的研究。 虽然 DL 已被用于支持许多不同的 SE 任务，但鉴于我们的工作目标，我们只关注自动化源代码更改的技术。

### A. Deep Learning for Automating Code Changes

Several DL-based approaches have been proposed to automatically fix bugs [11], [38], [39].

已经提出了几种基于深度学习的方法来自动修复错误 [11]、[38]、[39]。

Gupta et al. propose DeepFix to fix common errors in C programs [38]. DeepFix is an end-to-end solution based on a multi-layered sequence-to-sequence model that iteratively tries to locate and fix errors in a given program. In the reported evaluation, it managed to automatically fix 27% of the testing samples.

古普塔等人。 提出 DeepFix 来修复 C 程序中的常见错误 [38]。 DeepFix 是一种基于多层序列到序列模型的端到端解决方案，该模型迭代地尝试定位和修复给定程序中的错误。 在报告的评估中，它设法自动修复了 27% 的测试样本。

Similarly, Chen et al. developed SequenceR, a model designed to automatically repair bugs spanning a single line in Java code [39]. SequenceR has been trained on a dataset of 35,578 one-line bug fixes and, in its best configuration, it was able to perfectly predict the fix for 20% of the testing samples.

同样，陈等人。 开发了 SequenceR，该模型旨在自动修复 Java 代码中跨越单行的错误 [39]。 SequenceR 已经在包含 35,578 个单行错误修复的数据集上进行了训练，并且在其最佳配置下，它能够完美预测 20% 的测试样本的修复。

Another step in the automatic generation of fixes has been taken by Tufano et al. [11], who evaluated the suitability of an NMT-based approach to automatically generate patches for defective code. Results show that the NMT models can correctly produce candidate patches for the given defective code in 9% and 3% of cases (depending on the length of the method) when a single patch is generated by the model.

Tufano 等人采取了自动生成修复程序的另一个步骤。 [11]，他评估了基于 NMT 的方法自动生成缺陷代码补丁的适用性。 结果表明，当模型生成单个补丁时，NMT 模型可以在 9% 和 3% 的情况下（取决于方法的长度）正确地为给定的缺陷代码生成候选补丁。

To generalize the usability of these tools, Tufano et al. [10] investigated the possibility of using an NMT model to learn how to automatically modify a given Java method as developers would do during a pull request (PR). In other words, they try to learn generic code changes implemented over PRs using an Encoder-Decoder RNN model feeding it with methods before and after the mined PRs. They found that the model can learn a wide variety of meaningful code transformations and, in some cases, reproduce precisely the same changes that are implemented by developers in PRs.

为了概括这些工具的可用性，Tufano 等人。 [10] 研究了使用 NMT 模型学习如何自动修改给定 Java 方法的可能性，就像开发人员在拉取请求 (PR) 期间所做的那样。 换句话说，他们尝试使用 Encoder-Decoder RNN 模型学习在 PR 上实现的通用代码更改，在挖掘 PR 之前和之后为其提供方法。 他们发现该模型可以学习各种有意义的代码转换，并且在某些情况下，可以精确重现开发人员在 PR 中实现的相同更改。

With a similar NMT approach, Watson et al. [12] tried to address one of the main open problems of automated software testing, namely the definition of the oracle. They focused on the generation of meaningful assert statements for a given test method. Their approach, generates syntactically and semantically correct assert statements that are comparable to ones manually written by developers in 31% of cases.

- [12] C. Watson, M. Tufano, K. Moran, G. Bavota, and D. Poshyvanyk, “On learning meaningful assert statements for unit test cases,” in Proceedings of the 42nd International Conference on Software Engineering, ICSE 2020, 2020, p. To Appear.

使用类似的 NMT 方法，Watson 等人。 [12] 试图解决自动化软件测试的主要开放问题之一，即预言机的定义。 他们专注于为给定的测试方法生成有意义的断言语句。 他们的方法生成语法和语义上正确的断言语句，在 31% 的情况下与开发人员手动编写的断言语句相当。

DL models have also been used to support code completion. Karampatsis and Sutton [40] presented an open-vocabulary neural language model to address some of the issues raised a few years before by Hellendoorn and Devanbu [41] on the usage of DL models for modeling code. The proposed model is able to handle new identifier names that have not appeared in its training data. Still with the goal of supporting code completion, Kim et al. [42] first showed how Transformer models outperform the previous sequence-based models, and then focused on how to obtain higher accuracy exposing the Transformer to the syntactic structure of the code. Results show that the proposed model surpasses previous work.

DL 模型也被用于支持代码完成。 Karampatsis 和 Sutton [40] 提出了一个开放词汇神经语言模型，以解决几年前 Hellendorn 和 Devanbu [41] 提出的关于使用 DL 模型进行代码建模的一些问题。 所提出的模型能够处理未出现在其训练数据中的新标识符名称。 仍然以支持代码完成为目标，Kim 等人。 [42]首先展示了 Transformer 模型如何优于以前的基于序列的模型，然后专注于如何获得更高的准确性，将 Transformer 暴露于代码的句法结构。 结果表明，所提出的模型超越了以前的工作。

Also Svyatkovskiy et al. [43] used Transformer models to support code completion. They introduced a multi-layer generative transformer model for code named GPT-C. GPTC is the core of IntelliCode Compose, a general-purpose code completion framework able to generate syntactically correct code sequences of arbitrary token types and in multiple programming languages.

- [43] A. Svyatkovskiy, S. K. Deng, S. Fu, and N. Sundaresan, “Intellicode compose: Code generation using transformer,” arXiv preprint arXiv:2005.08025, 2020.

还有 Svyatkovskiy 等人。 [43] 使用 Transformer 模型来支持代码完成。 他们为名为 GPT-C 的代码引入了一个多层生成式转换器模型。 GPTC 是 IntelliCode Compose 的核心，IntelliCode Compose 是一个通用代码完成框架，能够生成任意标记类型和多种编程语言的语法正确的代码序列。

Alon et al. [44] used DL to target code completion in a language-agnostic fashion. They presented a new approach based on LSTMs and Transformers that generates the target AST node-by-node, reaching state-of-the-art performance with an exact match accuracy for the top prediction of 18.04%.

- [44] U. Alon, R. Sadaka, O. Levy, and E. Yahav, “Structural language models of code,” arXiv, pp. arXiv–1910, 2019.

阿隆等人。 [44] 使用 DL 以与语言无关的方式来完成代码。 他们提出了一种基于 LSTM 和 Transformers 的新方法，可以逐节点生成目标 AST，达到最先进的性能，最高预测的精确匹配准确率为 18.04%。

Another research problem in which DL has been applied is the migration of software across programming languages [45], [46], e.g., through statistical machine translation.

- [45] A. T. Nguyen, H. A. Nguyen, T. T. Nguyen, and T. N. Nguyen, “Statistical learning approach for mining API usage mappings for code migration,” in Proceedings of the 29th ACM/IEEE International Conference on Automated Software Engineering, ser. ASE ’14. New York, NY, USA: ACM, 2014, pp. 457–468.
- [46] A. T. Nguyen, T. T. Nguyen, and T. N. Nguyen, “Lexical statistical machine translation for language migration,” in Proceedings of the 2013 9th Joint Meeting on Foundations of Software Engineering, ser. ESEC/FSE 2013. New York, NY, USA: ACM, 2013, pp. 651–654.

应用 DL 的另一个研究问题是跨编程语言的软件迁移 [45]、[46]，例如，通过统计机器翻译。

Looking at our work in the context of the discussed literature, this is, to the best of our knowledge, the first attempt in automating code review activities from both the perspective of the contributor and of the reviewer. Form the “technical” point of view, the closest work to ours is the one by Tufano et al. [10] about learning code changes implemented in PRs. Indeed, their model architecture is similar to the one we use in the 1-encoder scenario (we use Transformers) and we also inherit from them the abstraction procedure. However, we target a different problem that required (i) the collection of a new dataset, with many filtering strategies put in place to avoid noise during the training process; (ii) the definition of a novel architecture exploiting two encoders to process the reviewer comment and the submitted code as input.

- [10] M. Tufano, J. Pantiuchina, C. Watson, G. Bavota, and D. Poshyvanyk, “On learning meaningful code changes via neural machine translation,” in Proceedings of the 41st International Conference on Software Engineering, ICSE 2019, Montreal, QC, Canada, May 25-31, 2019, 2019, pp. 25–36.

在所讨论的文献的背景下查看我们的工作，据我们所知，这是第一次尝试从贡献者和审查者的角度自动化代码审查活动。 从“技术”的角度来看，与我们最接近的工作是 Tufano 等人的工作。 [10] 关于学习 PR 中实现的代码更改。 事实上，他们的模型架构类似于我们在 1-encoder 场景中使用的模型架构（我们使用 Transformers），我们也从他们那里继承了抽象过程。 然而，我们针对一个不同的问题，它需要 (i) 收集新的数据集，并采用了许多过滤策略来避免训练过程中的噪音； (ii) 利用两个编码器来处理评论者评论和提交的代码作为输入的新型架构的定义。

### B. Code Review

Several studies have focused on code reviews, investigating its impact on the code quality [1]–[3], [47]. Kemerer and Paulk [47], McIntosh et al. [1], and Bavota and Russo [3] agree in reporting the lower likelihood of introducing bugs in reviewed code as compared to non-reviewed code. Also, Morales et al. [2] confirm the higher code quality ensured by code review.

我们总结了关注 SE 中 DL 的相关文献和与代码审查相关的研究。 虽然 DL 已被用于支持许多不同的 SE 任务，但鉴于我们的工作目标，我们只关注自动化源代码更改的技术。

Other authors studied how the code review process is carried out in industry and in open source communities [4]– [6], [48]–[53]. Due to space constraints, we do not discuss these works in details but we only summarize some of the findings relevant for our work. Identifying defects has been confirmed in different studies [48], [50], [51], [53] as the main reason for performing code review, thus highlighting the importance of automating this activity. These studies also provided evidence of the substantial time invested in code review activities [4]–[6], one of the motivations for working on code review automation.

其他作者研究了如何在行业和开源社区中进行代码审查过程 [4]-[6]、[48]-[53]。 由于篇幅限制，我们不详细讨论这些工作，但我们只总结了一些与我们的工作相关的发现。 识别缺陷已在不同的研究中得到证实 [48]、[50]、[51]、[53] 作为执行代码审查的主要原因，从而突出了自动化此活动的重要性。 这些研究还提供了在代码审查活动 [4]-[6] 上投入大量时间的证据，这是进行代码审查自动化的动机之一。

Researchers also analyzed the factors influencing the acceptance of the code changes submitted for review [54]–[57]. Finally, a recent work proposed a tool, named ClusterChanges, to help developers during the code review process [58]. This is the work more related to ours. However, while the overall goal (i.e., helping developers during code review) ClusterChanges automatically decomposes changesets submitted for a review into cohesive, smaller changes, while our goal tries to automate specific code review steps.

- [54] P. Weißgerber, D. Neu, and S. Diehl, “Small patches get in!” in Proceedings of the 2008 International Working Conference on Mining Software Repositories, ser. MSR ’08, 2008, pp. 67–76.
- [55] O. Baysal, O. Kononenko, R. Holmes, and M. Godfrey, “The influence of non-technical factors on code review,” in Reverse Engineering (WCRE), 2013 20th Working Conference on, 2013, pp. 122–131.
- [56] A. Bosu and J. C. Carver, “Impact of developer reputation on code review outcomes in OSS projects: an empirical investigation,” in 2014 ACM-IEEE International Symposium on Empirical Software Engineering and Measurement, ESEM ’14, Torino, Italy, September 18-19, 2014, 2014, p. 33.
- [57] ——, “Characteristics of the vulnerable code changes identified through peer code review,” in 36th International Conference on Software Engineering, ICSE ’14, Companion Proceedings, Hyderabad, India, May 31
June 07, 2014, 2014, pp. 736–738.
- [58] M. Barnett, C. Bird, J. a. Brunet, and S. K. Lahiri, “Helping developers help themselves: Automatic decomposition of code review changesets,” in Proceedings of the 37th International Conference on Software Engineering Volume 1, ser. ICSE ’15, 2015, pp. 134–144.

研究人员还分析了影响接受提交审查的代码更改的因素[54]-[57]。 最后，最近的一项工作提出了一个名为 ClusterChanges 的工具，以在代码审查过程中帮助开发人员 [58]。 这是与我们更相关的工作。 然而，虽然总体目标（即在代码审查期间帮助开发人员）ClusterChanges 自动将提交审查的变更集分解为内聚的、较小的变更，而我们的目标是尝试自动化特定的代码审查步骤。

## VII. CONCLUSIONS

We experimented with DL techniques in the context of automating two code review activities: (i) recommending to the contributor code changes to implement as reviewers would do before submitting the code for review; and (ii) providing the reviewer with the code implementing a comment she has on a submitted code.

我们在自动化两个代码审查活动的上下文中试验了 DL 技术：(i) 向贡献者推荐代码更改以在提交代码以供审查之前执行审查者所做的更改； (ii) 向审查者提供代码，以实现她对提交的代码的评论。

This required the definition of two different transformer based architectures.

The achieved results were promising, with the models able to generate meaningful recommendations in up to 16% (first scenario) and 31% (second scenario) of cases. Still, vast improvements are needed to make such models suitable to be used by developers.

取得的结果很有希望，模型能够在高达 16%（第一种情况）和 31%（第二种情况）的案例中生成有意义的建议。 尽管如此，仍需要大量改进才能使此类模型适合开发人员使用。

In future, we plan to explore different DL architectures and increase the amount of data available for training our models. The latter could be the key for learning a larger variety of code changes.

未来，我们计划探索不同的 DL 架构并增加可用于训练模型的数据量。 后者可能是学习更多代码更改的关键。
