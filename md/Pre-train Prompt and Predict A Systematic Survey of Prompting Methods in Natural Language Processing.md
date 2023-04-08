# Pre-train, Prompt, and Predict: A Systematic Survey of Prompting Methods in Natural Language Processing

- PENGFEI LIU and WEIZHE YUAN, Carnegie Mellon University, USA
- JINLAN FU, National University of Singapore, Singapore
- ZHENGBAOJIANG, HIROAKIHAYASHI, andGRAHAMNEUBIG, Carnegie Mellon University,
USA

This article surveys and organizes research works in a new paradigm in natural language processing, which we dub “prompt-based learning.” Unlike traditional supervised learning, which trains a model to take in an input x and predict an output y as P (y|x), prompt-based learning is based on language models that model the probability of text directly. To use these models to perform prediction tasks, the original input x is modified using a template into a textual string prompt x' that has some unfilled slots, and then the language model is used to probabilistically fill the unfilled information to obtain a final string xˆ, from which the final output y can be derived. This framework is powerful and attractive for a number of reasons: It allows the language model to be pre-trained on massive amounts of raw text, and by defining a new prompting function the model is able to perform few-shot or even zero-shot learning, adapting to new scenarios with few or no labeled data. In this article, we introduce the basics of this promising paradigm, describe a unified set of mathematical notations that can cover a wide variety of existing work, and organize existing work along several dimensions, e.g., the choice of pre-trained language models, prompts, and tuning strategies. To make the field more accessible to interested beginners, we not only make a systematic review of existing works and a highly structured typology of prompt-based concepts but also release other resources, e.g., a website NLPedia–Pretrain including constantly updated survey and paperlist.

这篇文章回顾和组织了自然语言处理中一种新的范式的研究工作，我们称之为“基于提示的学习”。与传统的监督学习不同，它训练一个模型来接受一个输入x并预测一个输出y作为P(y|x)，基于提示的学习是基于直接建模文本概率的语言模型。为了使用这些模型来执行预测任务，原始输入x被使用一个模板修改为一个文本字符串提示x’，其中有一些未填充的槽位，然后语言模型被用来概率地填充未填充的信息，以获得一个最终字符串x^，从中可以推导出最终输出y。这种框架有很多强大和吸引人的原因：它允许语言模型在大量的原始文本上进行预训练，并通过定义一个新的提示函数，使模型能够进行少样本甚至零样本学习，适应新场景而无需或几乎不需要标注数据。在本文中，我们介绍了这种有前途的范式的基础知识，描述了一套统一的数学符号，可以涵盖各种现有的工作，并沿着几个维度组织了现有的工作，例如预训练语言模型、提示和调优策略的选择。为了使感兴趣的初学者更容易接触这个领域，我们不仅系统地回顾了现有的工作和一个高度结构化的基于提示的概念分类，还发布了其他资源，例如一个包含持续更新的综述和论文列表的网站NLPedia–Pretrain。

## 1 TWO SEA CHANGES IN NATURAL LANGUAGE PROCESSING

Fully supervised learning, where a task-specificmodel is trained solely on a dataset of input–output examples for the target task, has long played a central role in many machine learning tasks [60], and natural language processing (NLP) was no exception. Because such manually annotated datasets are ever-insufficient for learning high-quality models, early NLP models relied heavily on feature engineering (Table 1(a); e.g., Guyon et al. [39], Lafferty et al. [63], Och et al. [92], Zhang and Nivre [150]), where NLP researchers or engineers used their domain knowledge to define and extract salient features from raw data and providemodels with the appropriate inductive bias to learn from this limited data. With the advent of neural network models for NLP, salient features were learned jointly with the training of the model itself [6, 16], and hence focus shifted to architecture engineering, where inductive bias was rather provided through the design of a suitable network architecture conducive to learning such features (Table 1(b); e.g., Bahdanau et al. [4], Chung et al. [15], Hochreiter and Schmidhuber [44], Kalchbrenner et al. [54], Kim [57], Vaswani et al. [137]).

- Sotiris B. Kotsiantis, I. Zaharakis, P. Pintelas, et al. 2007. Supervised machine learning: A review of classification techniques. Emerg. Artif. Intell. Appl. Comput. Eng. 160, 1 (2007), 3–24. 监督机器学习：分类技术综述

监督分类是所谓的智能系统最常执行的任务之一。因此，基于人工智能（基于逻辑的技术、基于感知器的技术）和统计学（贝叶斯网络、基于实例的技术）开发了大量的技术。
监督学习的目标是建立一个简洁的模型，描述类标签在预测特征方面的分布。然后，使用生成的分类器来为测试实例分配类标签，其中预测特征的值是已知的，但类标签的值是未知的。本文描述了各种监督机器学习分类技术。
当然，一篇文章不能完整地回顾所有监督机器学习分类算法（也称为归纳分类算法），但我们希望引用的参考文献能够涵盖主要的理论问题，指导研究者进行有趣的研究方向，并提出可能尚未探索的偏置组合。

- Isabelle Guyon, Jason Weston, Stephen Barnhill, and Vladimir Vapnik. 2002. Gene selection for cancer classification using support vector machines. Mach. Learn. 46, 1 (2002), 389–422. 基于支持向量机的癌症分类基因选择

DNA微阵列现在允许科学家同时筛选数千个基因，并确定这些基因在正常或癌变组织中是活跃的、过度活跃的还是沉默的。由于这些新的微阵列设备产生了令人困惑的大量原始数据，需要开发新的分析方法来分辨出癌症组织是否具有与正常组织或其他类型的癌症组织不同的基因表达特征。
在本文中，我们解决了从广泛的基因表达数据中选择一小部分基因的问题，这些数据是在DNA微阵列上记录的。利用来自癌症和正常患者的可用训练样本，我们构建了一个适合遗传诊断和药物发现的分类器。以前解决这个问题的尝试是使用相关技术选择基因。我们提出了一种新的基因选择方法，利用基于递归特征消除（RFE）的支持向量机方法。我们实验地证明，我们的技术选择的基因具有更好的分类性能，并且与癌症生物学相关。与基线方法相比，我们的方法自动消除了基因冗余，并产生了更好和更紧凑的基因子集。在白血病患者中，我们的方法发现了2个基因，可以得到零留一误差，而基线方法需要64个基因才能得到最好的结果（一个留一误差）。在结肠癌数据库中，使用只有4个基因，我们的方法是98%准确的，而基线方法只有86%准确。

- J. Lafferty, A. McCallum, and Fernando Pereira. 2001. Conditional random fields: Probabilisticmodels for segmenting and labeling sequence data. In Proceedings of the International Conference on Machine Learning (ICML’01). 条件随机场：用于分割和标注序列数据的概率模型

我们提出了条件随机场，一种用于构建概率模型来分割和标注序列数据的框架。条件随机场相比于隐马尔可夫模型和随机文法在这样的任务上有几个优势，包括能够放松这些模型中所做的强独立性假设。条件随机场也避免了最大熵马尔可夫模型（MEMMs）和其他基于有向图模型的判别马尔可夫模型的一个根本限制，即它们可能对具有少数后继状态的状态有偏好。
我们提出了条件随机场的迭代参数估计算法，并将所得模型的性能与HMMs和MEMMs在合成和自然语言数据上进行了比较。

- Franz Josef Och, Daniel Gildea, Sanjeev Khudanpur, Anoop Sarkar, Kenji Yamada, Alex Fraser, Shankar Kumar, Libin Shen, David Smith, Katherine Eng, Viren Jain, Zhen Jin, and Dragomir Radev. 2004. A Smorgasbord of features for statistical machine translation. In Proceedings of the Human Language Technology Conference of the North American Chapter of the Association for Computational Linguistics (HLT-NAACL’04). Association for Computational Linguistics, 161–168. 统计机器翻译的特征大杂烩

我们描述了一种统计机器翻译系统，它使用了大量的特征来捕捉不同层次的翻译知识。这些特征包括基于单词、短语和句子的特征，以及基于语法、词汇和语义的特征。我们使用最小错误率训练方法来自动优化这些特征的权重。
我们在多个数据集上评估了我们的系统，包括汉英、阿英和法英的翻译任务。我们的实验结果表明，使用多种特征可以显著提高翻译质量，而且不同类型的特征对不同的任务有不同的影响。我们还展示了如何利用人工评价来分析不同特征的作用。

- Yue Zhang and Joakim Nivre. 2011. Transition-based dependency parsing with rich non-local features. In Proceedings of the 49th Annual Meeting of the Association for Computational Linguistics: Human Language Technologies. Association for Computational Linguistics, 188–193. https://www.aclweb.org/anthology/P11-2033 基于转移的依存句法分析：丰富的非局部特征。

我们提出了一种基于转移的依存句法分析器，它使用了丰富的非局部特征来捕捉句子中的长距离依赖关系。这些特征包括基于词性标记、词汇、语义角色和句法结构的特征，以及基于动态规划的特征选择方法。
我们在多个数据集上评估了我们的分析器，包括英语、中文和瑞典语的依存树库。我们的实验结果表明，我们的分析器在准确率和效率方面都优于以前的基于转移的分析器，并且与最先进的基于图的分析器相当或更好。我们还展示了如何利用非局部特征来改善基于转移的分析器在处理非投射依存结构方面的性能。

- Yoshua Bengio, Aaron Courville, and Pascal Vincent. 2013. Representation learning: A review and new perspectives. IEEE Trans. Pattern Anal. Mach. Intell. 35, 8 (2013), 1798–1828.

这篇论文的主要观点是，机器学习算法的成功通常取决于数据表示，而数据表示能够在不同程度上混淆和隐藏数据背后的不同解释性变化因素。作者认为，虽然可以使用特定领域知识来帮助设计表示，但也可以使用具有通用先验知识的学习方法，而对人工智能的追求也促进了更强大的表示学习算法的设计。这篇论文回顾了无监督特征学习和深度学习领域的最新进展，涵盖了概率模型、自编码器、流形学习和深度网络的进步。这也引出了一些长期未解决的问题，关于学习好的表示、计算表示（即推理）以及表示学习、密度估计和流形学习之间的几何联系的合适目标。

- Ronan Collobert, J. Weston, L. Bottou, Michael Karlen, K. Kavukcuoglu, and P. Kuksa. 2011. Natural language processing (almost) from scratch. J. Mach. Learn. Res. 12 (2011), 2493–2537.

这篇论文的主要观点是，提出了一种统一的神经网络架构和学习算法，可以应用于各种自然语言处理任务，包括：词性标注、分块、命名实体识别和语义角色标注。这种通用性是通过尽量避免任务特定的工程化而实现的，因此忽略了很多先验知识。与其利用为每个任务精心优化的人工输入特征，我们的系统基于大量的主要是无标签的训练数据来学习内部表示。这项工作然后被用作构建一个性能良好、计算需求最小的免费标注系统的基础。

完全监督学习，即仅在目标任务的输入-输出示例数据集上训练一个任务特定的模型，长期以来在许多机器学习任务中发挥着核心作用[60]，自然语言处理（NLP）也不例外。由于这种手动标注的数据集对于学习高质量的模型来说永远不足，早期的NLP模型严重依赖于特征工程（表1(a)；例如，Guyon等[39]，Lafferty等[63]，Och等[92]，Zhang和Nivre[150]），其中NLP研究人员或工程师利用他们的领域知识从原始数据中定义和提取显著特征，并为模型提供适当的归纳偏置，以便从这些有限的数据中学习。随着神经网络模型在NLP中的出现，显著特征与模型本身的训练一起进行学习[6, 16]，因此焦点转移到了架构工程，其中归纳偏置是通过设计一个有利于学习这些特征的合适网络架构来提供的（表1(b)；例如，Bahdanau等4，Chung等[15]，Hochreiter和Schmidhuber[44]，Kalchbrenner等[54]，Kim[57]，Vaswani等[137]）。



## 2 A FORMAL DESCRIPTION OF PROMPTING

## 10 CONCLUSION

In this article, we have summarized and analyzed several paradigms in the development of statistical natural language processing techniques and have argued that prompt-based learning is a promising new paradigm that may represent another major change in the way we look at NLP. First, we hope this survey will help researchers more effectively and comprehensively understand the paradigm of prompt-based learning and grasp its core challenges so that more scientifically meaningful advances can be made in this field. In addition, looking all the way back to the summary of the four paradigms of NLP research presented in Section 1, we hope to highlight the commonalities and differences between them, making research on any of these paradigms more full-fledged and potentially providing a catalyst to inspire work toward the next paradigm shift as well.

在本文中，我们总结和分析了统计自然语言处理技术发展中的几种范式，并认为基于提示的学习是一种有前途的新范式，可能代表了我们看待NLP方式的又一次重大变化。首先，我们希望这篇综述能够帮助研究者更有效和全面地理解基于提示的学习范式，并把握其核心挑战，从而在这个领域取得更有科学意义的进展。此外，回顾第1节中对NLP研究的四种范式的总结，我们希望突出它们之间的共性和差异，使对任何这些范式的研究更加全面，并有可能激发向下一个范式转变的工作。
