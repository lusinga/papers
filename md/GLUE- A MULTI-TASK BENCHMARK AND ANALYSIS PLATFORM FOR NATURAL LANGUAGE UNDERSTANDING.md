# GLUE: A MULTI-TASK BENCHMARK AND ANALYSIS PLATFORM FOR NATURAL LANGUAGE UNDERSTANDING

## ABSTRACT

For natural language understanding (NLU) technology to be maximally useful, it must be able to process language in a way that is not exclusive to a single task, genre, or dataset. In pursuit of this objective, we introduce the General Language Understanding Evaluation (GLUE) benchmark, a collection of tools for evaluating the performance of models across a diverse set of existing NLU tasks. By including tasks with limited training data, GLUE is designed to favor and encourage models that share general linguistic knowledge across tasks. GLUE also includes a hand-crafted diagnostic test suite that enables detailed linguistic analysis of models. We evaluate baselines based on current methods for transfer and representation learning and find that multi-task training on all tasks performs better than training a separate model per task. However, the low absolute performance
of our best model indicates the need for improved general NLU systems.

为了使自然语言理解(NLU)技术发挥最大的作用，它必须能够以一种不局限于单个任务、类型或数据集的方式处理语言。为了实现这一目标，我们引入了通用语言理解评估(GLUE)基准，这是一组工具，用于评估模型在不同的现有NLU任务集上的性能。通过包含具有有限训练数据的任务，GLUE旨在支持和鼓励在任务之间共享一般语言知识的模型。GLUE还包括一个手工制作的诊断测试套件，可以对模型进行详细的语言分析。我们评估了基于当前转移和表示学习方法的基线，发现针对所有任务的多任务训练比针对每个任务单独训练一个模型效果更好。然而，我们的最佳模型的低绝对性能表明需要改进一般NLU系统。

## 1 INTRODUCTION

The human ability to understand language is general, flexible, and robust. In contrast, most NLU models above the word level are designed for a specific task and struggle with out-of-domain data. If we aspire to develop models with understanding beyond the detection of superficial correspondences between inputs and outputs, then it is critical to develop a more unified model that can learn to execute a range of different linguistic tasks in different domains.

人类理解语言的能力是普遍的、灵活的、强健的。相比之下，单词级以上的大多数NLU模型都是为特定的任务设计的，并且需要处理域外数据。如果我们期望开发的模型能够超越对输入和输出之间的表面对应的检测，那么开发一个能够学习在不同领域执行一系列不同语言任务的更统一的模型是至关重要的。

To facilitate research in this direction, we present the General Language Understanding Evaluation (GLUE) benchmark: a collection of NLU tasks including question answering, sentiment analysis, and textual entailment, and an associated online platform for model evaluation, comparison, and analysis. GLUE does not place any constraints on model architecture beyond the ability to process single-sentence and sentence-pair inputs and to make corresponding predictions. For some GLUE tasks, training data is plentiful, but for others it is limited or fails to match the genre of the test set. GLUE therefore favors models that can learn to represent linguistic knowledge in a way that facilitates sample-efficient learning and effective knowledge-transfer across tasks. None of the datasets in GLUE were created from scratch for the benchmark; we rely on preexisting datasets because they
have been implicitly agreed upon by the NLP community as challenging and interesting. Four of the datasets feature privately-held test data, which will be used to ensure that the benchmark is used fairly.

为了促进这方面的研究，我们提出了通用语言理解评估(GLUE)基准:一组NLU任务，包括问题回答、情感分析和文本蕴涵，以及一个相关的在线平台，用于模型评估、比较和分析。GLUE除了处理单句和句子对输入和做出相应的预测之外，对模型架构没有任何限制。一些胶水任务,训练数据丰富,但对其他人来说,它是有限的或不匹配测试集的类型。胶因此支持模型,可以学习代表语言知识,促进sample-efficient学习和有效的知识转移的任务。GLUE中的数据集没有一个是从头创建的;我们依赖现有的数据集，因为它们已经被NLP社区默认为具有挑战性和趣味性。其中四个数据集以私有的测试数据为特色，这些数据将被用来确保基准被公平地使用。

To understand the types of knowledge learned by models and to encourage linguistic-meaningful solution strategies, GLUE also includes a set of hand-crafted analysis examples for probing trained models. This dataset is designed to highlight common challenges, such as the use of world knowledge and logical operators, that we expect models must handle to robustly solve the tasks.

为了理解模型所学习的知识类型，并鼓励采用有语言意义的解决方案策略，GLUE还包括一组手工制作的分析示例，以探测经过训练的模型。该数据集旨在突出常见的挑战，如使用世界知识和逻辑运算符，我们期望模型必须处理这些挑战才能可靠地解决任务。

## 2 RELATED WORK

## 3 TASKS

### 3.1 SINGLE-SENTENCE TASKS

- CoLA 
The Corpus of Linguistic Acceptability (Warstadt et al., 2018) consists of English acceptability judgments drawn from books and journal articles on linguistic theory. Each example is a sequence of words annotated with whether it is a grammatical English sentence. Following the authors, we use Matthews correlation coefficient (Matthews, 1975) as the evaluation metric, which evaluates performance on unbalanced binary classification and ranges from -1 to 1, with 0 being the performance of uninformed guessing. We use the standard test set, for which we obtained private labels from the authors. We report a single performance number on the combination of the in- and out-of-domain sections of the test set.

语言学可接受性语料库(Warstadt et al.， 2018)由语言学理论书籍和期刊文章中的英语可接受性判断组成。每一个例子都是一个单词序列，并标注它是否是一个合乎语法的英语句子。根据作者的观点，我们使用Matthews相关系数(Matthews, 1975)作为评价指标，该指标对非平衡二分类的性能进行评价，范围为-1到1,0为不知情猜测的性能。我们使用标准的测试集，我们从作者那里获得了私有标签。我们在测试集的域内部分和域外部分的组合上报告单个性能数字。

- SST-2 
The Stanford Sentiment Treebank (Socher et al., 2013) consists of sentences from movie reviews and human annotations of their sentiment. The task is to predict the sentiment of a given sentence. We use the two-way (positive/negative) class split, and use only sentence-level labels.

SST-2斯坦福情感树库(Socher et al.， 2013)由电影评论和人类对其情感的注释组成。任务是预测给定句子的情绪。我们使用双向(正/负)类分割，并且只使用句子级标签。

### 3.2 SIMILARITY AND PARAPHRASE TASKS

- MRPC 
The Microsoft Research Paraphrase Corpus (Dolan & Brockett, 2005) is a corpus of sentence pairs automatically extracted from online news sources, with human annotations for whether the sentences in the pair are semantically equivalent. Because the classes are imbalanced (68% positive), we follow common practice and report both accuracy and F1 score.

Microsoft Research的释义语料库(Dolan &布罗基特，2005)是一个自动从在线新闻源提取的句子对语料库，其中包含人工注释，用于判断句子在语义上是否相同。因为这些班是不平衡的(68%阳性)，我们按照惯例报告准确性和F1分数。

- QQP 
The Quora Question Pairs2 dataset is a collection of question pairs from the community question-answering website Quora. The task is to determine whether a pair of questions are semantically equivalent. As in MRPC, the class distribution in QQP is unbalanced (63% negative), so we report both accuracy and F1 score. We use the standard test set, for which we obtained private labels from the authors. We observe that the test set has a different label distribution than the training set.

Quora Question Pairs2数据集是从社区问答网站Quora收集的问题对。任务是确定一对问题在语义上是否等价。与MRPC一样，QQP中的类分布是不平衡的(63%为负)，因此我们报告了准确性和F1得分。我们使用标准的测试集，我们从作者那里获得了私有标签。我们观察到，测试集的标签分布与训练集不同。

- STS-B 
The Semantic Textual Similarity Benchmark (Cer et al., 2017) is a collection of sentence pairs drawn from news headlines, video and image captions, and natural language inference data.

语义文本相似基准(Cer et al.， 2017)是从新闻标题、视频和图像字幕以及自然语言推理数据中提取的句子对集合。

Each pair is human-annotated with a similarity score from 1 to 5; the task is to predict these scores. Follow common practice, we evaluate using Pearson and Spearman correlation coefficients.

每一对都有人类注释，相似度评分从1到5;任务是预测这些分数。按照惯例，我们使用Pearson和Spearman相关系数进行评估。

### 3.3 INFERENCE TASKS

- MNLI 
The Multi-Genre Natural Language Inference Corpus (Williams et al., 2018) is a crowdsourced collection of sentence pairs with textual entailment annotations. Given a premise sentence and a hypothesis sentence, the task is to predict whether the premise entails the hypothesis (entailment), contradicts the hypothesis (contradiction), or neither (neutral). The premise sentences are
gathered from ten different sources, including transcribed speech, fiction, and government reports. We use the standard test set, for which we obtained private labels from the authors, and evaluate on both the matched (in-domain) and mismatched (cross-domain) sections. We also use and recommend the SNLI corpus (Bowman et al., 2015) as 550k examples of auxiliary training data.

多体裁自然语言推理语料库(Williams et al.， 2018)是一个包含文本隐含注释的句子对众包集合。给定一个前提句和一个假设句，任务是预测前提是否包含假设(暗含)，是否与假设相矛盾(矛盾)，或者两者都不(中立)。前提句从十个不同的来源收集，包括转录的演讲，小说和政府报告。我们使用标准测试集(我们从作者那里获得了私有标签)，并对匹配(域内)和不匹配(跨域)部分进行评估。我们也使用并推荐SNLI语料库(Bowman et al.， 2015)作为550k辅助训练数据的例子。

- QNLI 
The Stanford Question Answering Dataset (Rajpurkar et al. 2016) is a question-answering dataset consisting of question-paragraph pairs, where one of the sentences in the paragraph (drawn from Wikipedia) contains the answer to the corresponding question (written by an annotator). We convert the task into sentence pair classification by forming a pair between each question and each
sentence in the corresponding context, and filtering out pairs with low lexical overlap between the question and the context sentence. The task is to determine whether the context sentence contains the answer to the question. This modified version of the original task removes the requirement that the model select the exact answer, but also removes the simplifying assumptions that the answer is always present in the input and that lexical overlap is a reliable cue. This process of recasting existing datasets into NLI is similar to methods introduced in White et al. (2017) and expanded upon in Demszky et al. (2018). We call the converted dataset QNLI (Question-answering NLI).

斯坦福问答数据集(Rajpurkar et al. 2016)是一个由问题-段落对组成的问答数据集，其中段落中的一个句子(来自Wikipedia)包含相应问题的答案(由注释者编写)。我们通过在每个问题和相应上下文中的每个句子之间形成一对，并过滤掉问题和上下文句子之间词汇重叠程度较低的句子，从而将任务转换为句子对分类。任务是确定上下文句子是否包含问题的答案。这个原始任务的修改版本消除了模型选择准确答案的要求，但也消除了简化的假设，即答案总是出现在输入中，词汇重叠是可靠的提示。将现有数据集重新映射到NLI的过程类似于White等(2017)和Demszky等(2018)的方法。我们将转换后的数据集称为QNLI(问答NLI)。

- RTE 
The Recognizing Textual Entailment (RTE) datasets come from a series of annual textual entailment challenges. We combine the data from RTE1 (Dagan et al., 2006), RTE2 (Bar Haim et al., 2006), RTE3 (Giampiccolo et al., 2007), and RTE5 (Bentivogli et al., 2009). Examples are constructed based on news and Wikipedia text. We convert all datasets to a two-class split, where for three-class datasets we collapse neutral and contradiction into not entailment, for consistency.

识别文本蕴涵数据集来自于一系列的年度文本蕴涵挑战。我们合并了来自RTE1 (Dagan等，2006)、RTE2 (Bar Haim等，2006)、RTE3 (Giampiccolo等，2007)和RTE5 (Bentivogli等，2009)的数据。示例是基于新闻和Wikipedia文本构建的。我们将所有的数据集转换为两个类的拆分，而对于三个类的数据集，为了保持一致性，我们将中立的和矛盾的数据集折叠为不包含在内。

- WNLI 
The Winograd Schema Challenge (Levesque et al., 2011) is a reading comprehension task in which a system must read a sentence with a pronoun and select the referent of that pronoun from a list of choices. The examples are manually constructed to foil simple statistical methods: Each one is contingent on contextual information provided by a single word or phrase in the sentence.
To convert the problem into sentence pair classification, we construct sentence pairs by replacing the ambiguous pronoun with each possible referent. The task is to predict if the sentence with the pronoun substituted is entailed by the original sentence. We use a small evaluation set consisting of new examples derived from fiction books5 that was shared privately by the authors of the original corpus. While the included training set is balanced between two classes, the test set is imbalanced between them (65% not entailment). Also, due to a data quirk, the development set is adversarial: hypotheses are sometimes shared between training and development examples, so if a model memorizes the training examples, they will predict the wrong label on corresponding development set example. As with QNLI, each example is evaluated separately, so there is not a systematic correspondence between a model’s score on this task and its score on the unconverted original task. We call converted dataset WNLI (Winograd NLI).

Winograd模式挑战(Levesque et al.， 2011)是一项阅读理解任务，其中系统必须阅读一个带有代词的句子，并从选项列表中选择该代词的指称。这些例子是人工构建的，以衬托简单的统计方法:每个例子都依赖于句子中单个单词或短语提供的上下文信息。为了把这个问题转换成句子对分类，我们用每个可能的指称代替歧义代词来构造句子对。任务是预测代词替换后的句子是否由原句引出。我们使用一个小的评估集，由来自小说的新示例组成，这些示例由原始文集的作者私下共享。所包含的训练集在两个类之间是平衡的，而测试集在两个类之间是不平衡的(65%不包含)。此外，由于数据的特殊性，开发集是对抗性的:假设有时在培训和开发示例之间共享，因此如果一个模型记住了培训示例，那么它们将在相应的开发集示例上预测错误的标签。与QNLI一样，每个示例都是单独评估的，因此模型在此任务上的得分与未转换的原始任务上的得分之间没有系统的对应关系。我们将转换后的数据集称为WNLI (Winograd NLI)。

## 4 DIAGNOSTIC DATASET

## 5 BASELINES

## 6 BENCHMARK RESULTS

## 7 ANALYSIS

## 8 CONCLUSION

