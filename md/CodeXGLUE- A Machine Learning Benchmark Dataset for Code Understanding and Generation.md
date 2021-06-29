# CodeXGLUE: A Machine Learning Benchmark Dataset for Code Understanding and Generation

## ABSTRACT

Benchmark datasets have a significant impact on accelerating research in programming language tasks. In this paper, we introduce CodeXGLUE, a benchmark dataset to foster machine learning research for program understanding and generation. CodeXGLUE includes a collection of 10 tasks across 14 datasets and a platform for model evaluation and comparison. CodeXGLUE also features three baseline systems, including the BERT-style, GPT-style, and Encoder-Decoder models, to make it easy for researchers to use the platform. The availability of such data and baselines can help the development and validation of new methods that can be applied to various program understanding and generation problems.

基准数据集对加速编程语言任务的研究具有重大影响。 在本文中，我们介绍了 CodeXGLUE，这是一个基准数据集，用于促进机器学习研究以进行程序理解和生成。 CodeXGLUE 包括跨 14 个数据集的 10 个任务的集合以及一个用于模型评估和比较的平台。 CodeXGLUE 还具有三个基线系统，包括 BERT 风格、GPT 风格和编码器-解码器模型，以方便研究人员使用该平台。 此类数据和基线的可用性有助于开发和验证可应用于各种程序理解和生成问题的新方法。

## 1 INTRODUCTION

Evans Data Corporation2 estimated that there were 23.9 million professional developers in 2019 and that the number was expected to reach 28.7 million in 2024. With the population of developers growing at such a rate, code intelligence that leverages artificial intelligence (AI) to help software developers improve the productivity of the development process is becoming increasingly important. It is commonly accepted that benchmarks have a significant impact on the growth of applied AI research. In this paper, we focus on establishing a benchmark dataset for code intelligence.

Evans Data Corporation2 估计，2019 年有 2390 万专业开发人员，预计到 2024 年这一数字将达到 2870 万。随着开发人员数量以如此快的速度增长，利用人工智能 (AI) 帮助软件开发人员的代码智能 提高开发过程的生产力变得越来越重要。 人们普遍认为，基准测试对应用人工智能研究的发展有重大影响。 在本文中，我们专注于建立代码智能的基准数据集。

Automated program understanding and generation could increase the productivity of software developers. In fact, developers who want to find code written by others with the same intent can leverage code search systems [23, 35, 58, 85] to automatically retrieve semantically relevant codes through natural language queries. Similarly, developers who are confused about what to write next can use code completion systems [4, 8, 9, 31, 62, 63, 72, 73] to automatically complete the following tokens based on the edits made to the code. Finally, when developers want to implement the Java code in Python, code-to-code translation systems [11, 41, 46, 54] can help translate their code from one programming language (Python) to another (Java).

自动化程序理解和生成可以提高软件开发人员的生产力。 事实上，想要查找具有相同意图的其他人编写的代码的开发人员可以利用代码搜索系统 [23、35、58、85] 通过自然语言查询自动检索语义相关的代码。 同样，对接下来要写什么感到困惑的开发人员可以使用代码完成系统 [4, 8, 9, 31, 62, 63, 72, 73] 根据对代码所做的编辑自动完成以下标记。 最后，当开发人员想要在 Python 中实现 Java 代码时，代码到代码的翻译系统 [11, 41, 46, 54] 可以帮助将他们的代码从一种编程语言 (Python) 翻译成另一种 (Java)。

In recent years, researchers have increasingly applied statistical models, including neural nets, to code intelligence tasks. Very recently, the application of pretrained models that learn from big programming language data has been inspired by the great success of pretrained models like BERT [16] and GPT [69] in natural language processing (NLP). These models, including CodeBERT [18] and IntelliCode Compose [72], have led to further improvements in code understanding and generation problems, but they lack a benchmark suite that covers a wide range of tasks. The use of ImageNet [15] for computer vision and the use of GLUE [81] for NLP have shown that a diversified benchmark dataset has a significant impact on the growth of applied AI research.

- 

近年来，研究人员越来越多地将统计模型（包括神经网络）应用于智能任务编码。 最近，从大编程语言数据中学习的预训练模型的应用受到了 BERT [16] 和 GPT [69] 等预训练模型在自然语言处理 (NLP) 中的巨大成功的启发。 这些模型，包括 CodeBERT [18] 和 IntelliCode Compose [72]，在代码理解和生成问题方面取得了进一步的改进，但它们缺乏涵盖广泛任务的基准套件。 将 ImageNet [15] 用于计算机视觉和将 GLUE [81] 用于 NLP 表明，多样化的基准数据集对应用 AI 研究的增长具有重大影响。

To address this problem, we introduce CodeXGLUE, a machine learning benchmark dataset for program understanding and generation research that includes 14 datasets3, a collection of 10 diversified programming language understanding and generation tasks, and a platform for model evaluation and comparison. CodeXGLUE supports the following tasks: 
- code-code (clone detection [10, 52, 71, 84, 89, 93, 97], 
- defect detection [47, 57, 61, 82, 83, 99], 
- cloze test [18], 
- code completion [4, 8, 9, 31, 62, 63, 72, 73], 
- code repair [2, 28, 30, 75, 76, 78], and 
- code-to-code translation [11, 41, 46, 54]) 
- text-code (natural language code search [23, 35, 85], 
- text-to-code generation [12, 26, 36, 38, 87, 90, 94, 95]) 
- code-text (code summarization [3, 12, 19, 34, 37, 80, 85–87]) 
- text-text (documentation translation [40])

为了解决这个问题，我们引入了 CodeXGLUE，一个用于程序理解和生成研究的机器学习基准数据集，包括 14 个数据集3、10 个多样化的编程语言理解和生成任务的集合，以及一个模型评估和比较平台。 CodeXGLUE 支持以下任务：
- 代码代码（克隆检测 [10, 52, 71, 84, 89, 93, 97],
- 缺陷检测 [47, 57, 61, 82, 83, 99],
- 完形填空测试 [18]，
- 代码完成 [4, 8, 9, 31, 62, 63, 72, 73],
- 代码修复 [2, 28, 30, 75, 76, 78]，以及
- 代码到代码的翻译 [11, 41, 46, 54])
- 文本代码（自然语言代码搜索 [23, 35, 85]，
- 文本到代码生成 [12, 26, 36, 38, 87, 90, 94, 95])
- 代码文本（代码摘要 [3, 12, 19, 34, 37, 80, 85–87]）
- 文本文本（文档翻译 [40]）

CodeXGLUE includes eight previously proposed datasets — Big- CloneBench [71], POJ-104 [52], Devign [99], PY150 [62], Github Java Corpus [4], Bugs2Fix [75], CONCODE [38], and CodeSearch- Net [35]— but also newly introduced datasets that are highlighted in Table 1. The datasets are chosen or created based on the consideration that the task has clear definition, and the volume of the dataset could support the development and evaluation of data-driven machine learning methods. The datasets created by us include (1) two cloze test test sets that cover 6 programming languages, (2) two line level code completion test sets in Java and Python, respectively, (3) a code-to-code translation dataset between Java and C#, (4) two natural language code search test sets with web queries and normalized function and variable names, respectively, and (5) a documentation translation dataset that covers five natural languages.

CodeXGLUE 包括八个先前提出的数据集——Big-CloneBench [71]、POJ-104 [52]、Devig [99]、PY150 [62]、Github Java Corpus [4]、Bugs2Fix [75]、CONCODE [38] 和 CodeSearch - Net [35]——以及表 1 中突出显示的新引入的数据集。选择或创建数据集的考虑是任务具有明确的定义，并且数据集的容量可以支持数据的开发和评估—— 驱动的机器学习方法。 我们创建的数据集包括（1）两个涵盖 6 种编程语言的完形填空测试集，（2）Java 和 Python 的两个行级代码完成测试集，（3）Java 和 Python 之间的代码到代码转换数据集。 C#，(4) 两个自然语言代码搜索测试集，分别带有 Web 查询和规范化的函数和变量名称，以及 (5) 一个涵盖五种自然语言的文档翻译数据集。

To make it easy for participants, we provide three baseline models to help perform the tasks, including a BERT-style pretrained model (in this case, CodeBERT) to supports code understanding problems, a GPT-style pretrained model, which we call CodeGPT, to help solve completion and generation problems, and an Encoder- Decoder framework that tackles sequence-to-sequence generation problems.

为了方便参与者，我们提供了三个基线模型来帮助执行任务，包括支持代码理解问题的 BERT 样式的预训练模型（在本例中为 CodeBERT）、GPT 样式的预训练模型，我们称之为 CodeGPT， 帮助解决完成和生成问题，以及解决序列到序列生成问题的编码器-解码器框架。

## 2 TASKS OVERVIEW

In this section, we provide a definition for each task.

- Clone detection [52, 71]. The task is to measure the semantic similarity between codes. This includes two subtasks: binary classification between a pair of codes and code retrieval, where the goal is to find semantically similar codes.
- Defect detection [99]. The objective is to identify whether a body of source code contains defects that may be used to attack software systems, such as resource leaks, use-after-free vulnerabilities, and DoS attack.
- Cloze test [18]. This aims to predict the masked token of a code and includes two subtasks. The first one is to measure the accuracy of predicting the masked token from the whole vocabulary. The other is to test the semantic reasoning ability by distinguishing between “max” and “min”. 
- Code completion [4, 62]. It aims to predict following tokens based on a code context. Its subtasks are token-level completion and line-level completion. The former checks whether the next one token has been predicted correctly, while the latter tests the goodness of the generated line.
- Code translation [54]. It involves translating a code from one programming language to a different one.
- Code search [35]. It measures the semantic relatedness between texts and codes and is composed of two subtasks. The first one is to find the most relevant code in a collection of codes according to a natural language query. The second subtask entails the analysis of a query-code pair to predict whether the code answers the query or not.
- Code repair [75]. Its goal is to refine the code by fixing the bugs automatically. 
- Text-to-code generation [38]. This aims to generate a code via a natural language description.
- Code summarization [37]. The objective is to generate the natural language comment for a code.
- Documentation translation [40]. It aims to translate code documentation from one natural language to different one.

- 克隆检测 [52, 71]。任务是测量代码之间的语义相似度。这包括两个子任务：一对代码之间的二进制分类和代码检索，其目标是找到语义相似的代码。
- 缺陷检测 [99]。目标是确定源代码主体是否包含可用于攻击软件系统的缺陷，例如资源泄漏、释放后使用漏洞和 DoS 攻击。
- 完形填空测试 [18]。这旨在预测代码的掩码标记并包括两个子任务。第一个是衡量从整个词汇表中预测掩码标记的准确性。另一种是通过区分“max”和“min”来测试语义推理能力。
- 代码完成 [4, 62]。它旨在根据代码上下文预测后续标记。它的子任务是令牌级完成和行级完成。前者检查下一个标记是否被正确预测，而后者测试生成的行的优劣。
- 代码翻译 [54]。它涉及将代码从一种编程语言翻译成另一种编程语言。
- 代码搜索 [35]。它衡量文本和代码之间的语义相关性，由两个子任务组成。第一个是根据自然语言查询在代码集合中找到最相关的代码。第二个子任务需要分析查询代码对，以预测代码是否回答了查询。
- 代码修复 [75]。它的目标是通过自动修复错误来优化代码。
- 文本到代码生成 [38]。这旨在通过自然语言描述生成代码。
- 代码总结 [37]。目标是为代码生成自然语言注释。
- 文档翻译 [40]。它旨在将代码文档从一种自然语言翻译成另一种自然语言。

## 3 DATASETS

In this section, we describe the datasets included in CodeXGLUE. Datasets are chosen or created based on the criterion that the volume of the dataset could support the development and evaluation of data-driven machine learning methods.

### 3.1 Clone detection

Clone detection includes two subtasks. The first subtask is to predict whether two given codes have the same semantics. We use the BigCloneBench [71] dataset for the subtask. The second subtask aims to retrieve semantically similar codes given a code as the query and we use the dataset POJ-104 [52] to perform it.

克隆检测包括两个子任务。 第一个子任务是预测两个给定的代码是否具有相同的语义。 我们使用 BigCloneBench [71] 数据集作为子任务。 第二个子任务旨在检索给定代码作为查询的语义相似代码，我们使用数据集 POJ-104 [52] 来执行它。

- BigCloneBench is a widely used large code clone benchmark that contains over 6,000,000 true clone pairs and 260,000 false clone pairs from 10 different functionalities. The dataset provided by Wang et al. [84] is filtered by discarding code fragments without any tagged true or false clone pairs, leaving it with 9,134 Java code fragments. Finally, the dataset includes 901,028/415,416/415,416 examples for training, validation and testing, respectively.

- BigCloneBench 是一个广泛使用的大型代码克隆基准测试，包含来自 10 个不同功能的超过 6,000,000 个真实克隆对和 260,000 个虚假克隆对。 Wang等人提供的数据集。 [84] 通过丢弃没有任何标记的真假克隆对的代码片段进行过滤，留下 9,134 个 Java 代码片段。 最后，数据集包括 901,028/415,416/415,416 个分别用于训练、验证和测试的示例。

- POJ-104 dataset [52] comes from a pedagogical programming open judge (OJ) system that automatically judges the validity of submitted source code for specific problems by running the code. We use the POJ-104 dataset, which consists of 104 problems and includes 500 student-written C/C++ programs for each problem. Different from that of the BigCloneBench dataset, the task of POJ- 104 aims to retrieve other programs that solve the same problem given a program. We group the datasets in three subsets based on the number of problems they are required to solve (64/16/24) for training, validation, and testing.

- POJ-104 数据集 [52] 来自一个教学编程开放判断（OJ）系统，该系统通过运行代码自动判断提交的源代码对特定问题的有效性。 我们使用 POJ-104 数据集，该数据集包含 104 个问题，并为每个问题包含 500 个学生编写的 C/C++ 程序。 与 BigCloneBench 数据集不同，POJ-104 的任务旨在检索解决给定程序相同问题的其他程序。 我们根据训练、验证和测试所需解决的问题数量 (64/16/24) 将数据集分为三个子集。

### 3.2 Defect detection

### 3.3 Cloze test

### 3.4 Code completion

### 3.5 Code translation

### 3.6 Code search

### 3.7 Code repair

### 3.8 Text-to-code generation

### 3.9 Code summarization

### 3.10 Documentation translation


## 4 BASELINE SYSTEMS

### 4.1 CodeBERT

### 4.2 CodeGPT

### 4.3 Encoder-Decoder



## 5 EXPERIMENT

## 6 RELATED WORK

Benchmark datasets have been playing a central role in the growth of applied AI research. For example, the LibriSpeech [55] and the SQuAD [60] datasets drive the development of data-driven models for automatic speech recognition and reading comprehension of text, respectively. With the growing demand for testing models’ generalization ability on a wide range of applications, researchers have created or assembled datasets that cover many tasks. Representative samples of these datasets include ImageNet [15] for computer vision, GLUE [81] for natural language understanding, XTREME [33] and XGLUE [48] for cross-lingual natural language processing. To the best of our knowledge, CodeXGLUE is the first diversified benchmark dataset that can be applied to various code intelligence problems.

基准数据集在应用 AI 研究的发展中一直发挥着核心作用。 例如，LibriSpeech [55] 和 SQuAD [60] 数据集分别推动了用于自动语音识别和文本阅读理解的数据驱动模型的开发。 随着对在广泛应用中测试模型泛化能力的需求不断增长，研究人员已经创建或组装了涵盖许多任务的数据集。 这些数据集的代表性样本包括用于计算机视觉的 ImageNet [15]、用于自然语言理解的 GLUE [81]、用于跨语言自然语言处理的 XTREME [33] 和 XGLUE [48]。 据我们所知，CodeXGLUE 是第一个可应用于各种代码智能问题的多元化基准数据集。

Many tasks related to machine learning for software engineering [1] have sufficient amount of data to support the development of data-driven methods, but are not covered by CodeXGLUE. We plan to extend to these tasks in the future. For example, the idiom mining task [5, 36] is to extract code idioms, which are syntactic fragments that recur across software projects and serve a single semantic purpose [5]. Bug localization [27, 61, 76] is to point the error location when a program fails tests. The test case generation task [22, 74] is to generate unit test cases automatically. The program synthesis [20, 45, 53, 64, 68, 79, 98] extends the text-to-code generation task aims to generate programs from a specification [24], such as pseudocode, natural language description, and input/output examples.

- [1] Miltiadis Allamanis, Earl T. Barr, Premkumar Devanbu, and Charles Sutton. 2018. A Survey of Machine Learning for Big Code and Naturalness. ACM Comput. Surv. 51, 4, Article 81 (July 2018), 37 pages. https://doi.org/10.1145/3212695
- [5] Miltiadis Allamanis and Charles Sutton. 2014. Mining idioms from source code. In Proceedings of the 22nd ACM SIGSOFT International Symposium on Foundations of Software Engineering. 472–483.
- [36] Srinivasan Iyer, Alvin Cheung, and Luke Zettlemoyer. 2019. Learning programmatic idioms for scalable semantic parsing. arXiv preprint arXiv:1904.09086 (2019).
- bug预测：
  - [27] Rahul Gupta, Aditya Kanade, and Shirish Shevade. 2019. Neural Attribution for Semantic Bug-Localization in Student Programs. In Advances in Neural Information Processing Systems. 11884–11894.
  - [61] Baishakhi Ray, Vincent Hellendoorn, Saheel Godhane, Zhaopeng Tu, Alberto Bacchelli, and Premkumar Devanbu. 2016. On the" naturalness" of buggy code. In 2016 IEEE/ACM 38th International Conference on Software Engineering (ICSE). IEEE, 428–439.
  - [76] Marko Vasic, Aditya Kanade, Petros Maniatis, David Bieber, and Rishabh Singh. 2019. Neural program repair by jointly learning to localize and repair. arXiv preprint arXiv:1904.01720 (2019).
- 测试生成：
  - [22] Gordon Fraser and Andrea Arcuri. 2011. Evosuite: automatic test suite generation for object-oriented software. In Proceedings of the 19th ACM SIGSOFT symposium and the 13th European conference on Foundations of software engineering. 416–419.
  - [74] Michele Tufano, Dawn Drain, Alexey Svyatkovskiy, Shao Kun Deng, and Neel Sundaresan. 2020. Unit Test Case Generation with Transformers. arXiv preprint arXiv:2009.05617 (2020).
- 程序合成
  - [20] John K. Feser, Swarat Chaudhuri, and Isil Dillig. 2015. Synthesizing Data Structure Transformations from Input-Output Examples. In Proceedings of the 36th ACM SIGPLAN Conference on Programming Language Design and Implementation (Portland, OR, USA) (PLDI ’15). Association for Computing Machinery, New York, NY, USA, 229–239. https://doi.org/10.1145/2737924.2737977
  - [45] Sumith Kulal, Panupong Pasupat, Kartik Chandra, Mina Lee, Oded Padon, Alex Aiken, and Percy S Liang. 2019. Spoc: Search-based pseudocode to code. In Advances in Neural Information Processing Systems. 11906–11917.
  - [53] Arvind Neelakantan, Quoc V Le, and Ilya Sutskever. 2015. Neural programmer: Inducing latent programs with gradient descent. arXiv preprint arXiv:1511.04834 (2015).
  - [64] Scott Reed and Nando De Freitas. 2015. Neural programmer-interpreters. arXiv preprint arXiv:1511.06279 (2015).
  - [68] Rishabh Singh and Sumit Gulwani. 2015. Predicting a correct program in programming by example. In International Conference on Computer Aided Verification. Springer, 398–414.
  - [79] Murali Vijayaraghavan, Chaudhuri Swarat, and Jermaine Chris. 2017. Bayesian Sketch Learning for Program Synthesis. CoRR.—-2017.—-Vol. abs/1703.05698.—- 1703.05698 (2017).


许多与软件工程机器学习相关的任务 [1] 有足够的数据量来支持数据驱动方法的开发，但 CodeXGLUE 没有涵盖。 我们计划在未来扩展到这些任务。 例如，习语挖掘任务 [5, 36] 是提取代码习语，它们是在软件项目中重复出现并服务于单一语义目的的句法片段 [5]。 错误定位 [27, 61, 76] 是在程序测试失败时指出错误位置。 测试用例生成任务 [22, 74] 是自动生成单元测试用例。 程序合成 [20, 45, 53, 64, 68, 79, 98] 扩展了文本到代码生成任务，旨在从规范 [24] 生成程序，例如伪代码、自然语言描述和输入/输出 例子。

## 7 CONCLUSION

With CodeXGLUE, we seek to support the development of models that can be applied to various program understanding and generation problems, with the goal of increasing the productivity of software developers. We encourage researchers to participate in the open challenge to make progress in code intelligence. Moving forward, we are planning to extend CodeXGLUE to more programming languages and downstream tasks while continuing to develop advanced pretrained models by exploring new model structures, introducing new pretraining tasks, using different types of data, and more.

通过 CodeXGLUE，我们寻求支持可应用于各种程序理解和生成问题的模型的开发，目标是提高软件开发人员的生产力。 我们鼓励研究人员参与公开挑战，以在代码智能方面取得进展。 展望未来，我们计划将 CodeXGLUE 扩展到更多的编程语言和下游任务，同时通过探索新的模型结构、引入新的预训练任务、使用不同类型的数据等继续开发高级预训练模型。
