# A Survey of Natural Language Generation

- CHENHE DONG, Sun Yat-Sen University, China
- YINGHUI LI, Tsinghua University, China
- HAIFAN GONG, Sun Yat-Sen University, China
- MIAOXIN CHEN and JUNXIN LI, Tsinghua University, China
- YING SHEN, Sun Yat-Sen University, China
- MIN YANG, Chinese Academy of Science, China

This article offers a comprehensive review of the research on Natural Language Generation (NLG) over the past two decades, especially in relation to data-to-text generation and text-to-text generation deep learning methods, as well as new applications of NLG technology. This survey aims to (a) give the latest synthesis of deep learning research on the NLG core tasks, as well as the architectures adopted in the field; (b) detail meticulously and comprehensively various NLG tasks and datasets, and draw attention to the challenges in NLG evaluation, focusing on different evaluation methods and their relationships; (c) highlight some future emphasis and relatively recent research issues that arise due to the increasing synergy between NLG and other artificial intelligence areas, such as computer vision, text, and computational creativity.

这篇文章对过去二十年的自然语言生成（NLG）研究进行了全面的回顾，特别是关于数据到文本生成和文本到文本生成的深度学习方法，以及NLG技术的新应用。这项调查旨在（a）给出深度学习在NLG核心任务上的最新综合，以及该领域采用的架构；（b）详细而全面地介绍各种NLG任务和数据集，并引起对NLG评估中的挑战的关注，重点介绍不同的评估方法及其关系；（c）强调一些未来的重点和相对较新的研究问题，这些问题是由于NLG与其他人工智能领域（如计算机视觉、文本和计算创造力）之间日益增强的协同作用而产生的。

## 1 INTRODUCTION

This article surveys the current state-of-the-art in Natural Language Generation (NLG), defined as the task of generating text from underlying non-linguistic representation of information [102]. NLG has been receiving more and more attention from researchers because of its extremely challenging and promising application prospects.

Ehud Reiter and Robert Dale. 1997. Building applied natural language generation systems. Nat. Lang. Eng. 3, 1 (1997).

- 自然语言生成（NLG）是从非语言信息表示生成文本的任务，有很多实际应用。
- 构建NLG系统需要进行需求分析，确定目标用户、文本类型和评估方法。
- NLG系统需要完成以下基本任务：内容确定、篇章规划、句子聚合、词汇化、指称表达生成和语言实现。
- 这些任务可以用不同的算法和表示方法来实现，有些是基于规则的，有些是基于统计的，有些是基于模板的。
- NLG系统需要各种类型的知识，包括语言知识、领域知识和用户知识，这些知识可以通过语料库分析或其他方法来获取。
- 本文重点介绍了一些已经成熟的技术，可以用来构建简单但实用的NLG系统，并提供了一些更复杂场景下可能用到的技术的参考文献。

这篇文章概述了自然语言生成（NLG）的当前最新技术，定义为从底层非语言信息表示生成文本的任务[102]。NLG因其极具挑战性和有前途的应用前景而受到越来越多研究人员的关注。

### 1.1 What Is Natural Language Generation?

Natural Language Generation (NLG) is the process of producing a natural language text to meet specified communicative goals. The texts that are generated may range from a single phrase given in answer to a question, through multi-sentence remarks and questions within a dialog, to full-page explanations.

自然语言生成（NLG）是指根据指定的交际目标产生自然语言文本的过程。生成的文本可能从回答问题的单个短语，到对话中的多句话和问题，再到整页的解释。

In contrast with the organization of the Natural Language Understanding (NLU) process— which can follow the traditional stages of a linguistic analysis: morphology, syntax, semantics, pragmatics/discourse—the generation process has a fundamentally different character. Generation proceeds involve the content planning, determination, and realization from content to form, from intentions and perspectives to linearly arrayed words and syntactic markers. Coupled with its application, the situation, and the discourse, they provide the basis for making choices among the alternative wordings and constructions that the language provides, which is the primary effort in constructing a text deliberately [78]. With its opposite flow of information, one might assume that a generation process could be organized like an understanding process but with the stages in opposite order.

与自然语言理解（NLU）过程的组织方式相反——它可以遵循传统的语言分析阶段：形态学、句法学、语义学、语用学/话语——生成过程具有根本不同的特征。生成过程涉及从内容到形式，从意图和观点到线性排列的词和句法标记的内容规划、确定和实现。结合其应用、情境和话语，它们为在语言提供的备选措辞和结构中做出选择提供了基础，这是故意构建文本的主要努力[78]。由于其信息流的相反，人们可能会认为，生成过程可以像理解过程一样组织，但是阶段顺序相反。

Both data-to-text generation and text-to-text generation are instances of NLG. Generating text from image is an application of data-to-text generation. A further text-to-text generation complication is dividing NLG tasks into three categories, i.e., text abbreviation, text expansion, text rewriting and reasoning. The text abbreviation task is formulated to condense information from long texts to short ones, typically including research on text summarization [8, 9, 20, 24, 53, 96, 116], question generation [5, 26, 44, 46, 66, 112, 122, 133, 134, 153, 158], and distractor generation [30, 61, 76, 89, 98, 103, 117, 118]. The text expansion tasks, such as short text expansion [6, 106, 113, 124] and topic-to-essay generation [27, 97, 135, 146, 152], generate complete sentences or even texts from some meaningful words by considering and adding elements such as conjunctions and prepositions to transform the input words into linguistically correct outputs. The goal of text rewriting and reasoning task is to rewrite the text into another style or applying reasoning methods to create responses. There are two sub-tasks: text style transfer [13, 28, 43, 63, 72, 80, 85, 95, 143, 157] and dialogue generation [4, 45, 57, 60, 74, 136, 145, 159]. The task of visual-based text generation targets at generate the explanation or summarization of the given image or video, involving the study of image caption [1, 71, 104, 129, 148, 149], video caption [22, 49, 52, 88, 114, 121, 128, 131, 140, 142, 160, 161], and visual storytelling [40, 55, 147].

- 4. Siqi Bao, Huang He, Fan Wang, Hua Wu, and Haifeng Wang. 2020. PLATO: Pre-trained dialogue generation model with discrete latent variable. In Proceedings of the 58th Annual Meeting of the Association for Computational Linguistics.

- 作者提出了一个新的对话生成预训练框架，支持各种类型的对话，包括闲聊、知识引导的对话和对话式问答。
- 作者采用了灵活的注意力机制，充分利用双向的上下文和单向的语言生成特征。
- 作者引入了离散的潜在变量，来处理回应生成中固有的一对多映射问题。
- 作者设计了两个相互关联的任务：回应生成和潜在行为识别，并在一个共享的网络中同时进行。
- 作者在三个公开的数据集上进行了全面的实验，验证了所提出的框架的有效性和优越性。

- 45. Bin Jiang, Jingxu Yang, Chao Yang, Wanyue Zhou, Liang Pang, and Xiaokang Zhou. 2020. Knowledge augmented

- 作者提出了一个新的对话生成模型，利用背景知识来提高回应的质量和多样性。
- 作者设计了一个分歧事实选择器（Divergent Facts Selector，DFS），用于在对话中选择与当前话题相关的知识事实。
- 作者使用了一个知识感知解码器（Knowledge Aware Decoder，KAD），用于结合上下文和知识事实生成回应。
- 作者在一个新的知识引导的对话数据集Wizard-of-Wikipedia上进行了实验，证明了模型的有效性和优势。

- 57. Xu Li and Jinghua Zhu. 2020. Attention and graph matching network for retrieval-based dialogue system with domain knowledge. In Proceedings of the International Joint Conference on Neural Networks.

- 作者提出了一个新的检索式对话系统模型，利用领域知识来提高回应的匹配度和质量。
- 作者设计了一个注意力和图匹配网络（Attention and Graph Matching Network，AGMN），用于从上下文和回应中提取双重表示，并利用图注意力机制将领域知识融合到词表示中。
- 作者在一个基于Linux手册的领域知识对话数据集上进行了实验，证明了模型的有效性和优势。

- 60. Rongzhong Lian, Min Xie, Fan Wang, Jinhua Peng, and Hua Wu. 2019. Learning to select knowledge for response generation in dialog systems. In Proceedings of the 28th International Joint Conference on Artificial Intelligence.

- 作者提出了一个新的对话生成模型，利用外部知识来生成更有信息量的回应。
- 作者设计了一个新的知识选择机制，使用先验分布和后验分布来促进知识的选择。
- 先验分布是从对话中仅推断出来的，用于在推理过程中选择合适的知识。
- 后验分布是从对话和回应中共同推断出来的，用于在训练过程中确保合适的知识选择。
- 作者在一个新的知识引导的对话数据集Wizard-of-Wikipedia上进行了实验，证明了模型的有效性和优势。

-74. Liangchen Luo, Jingjing Xu, Junyang Lin, Qi Zeng, and Xu Sun. 2018. An auto-encoder matching model for learning utterance-level semantic dependency in dialogue generation. In Proceedings of the Conference on Empirical Methods in Natural Language Processing.

这篇论文的主要观点是提出了一个自编码匹配模型（AEM），用于学习对话生成中的话语级语义依赖关系。话语级语义依赖关系是指输入和输出之间的整体意义之间的关系。该模型包含两个自编码器和一个映射模块。自编码器学习输入和输出的语义表示，映射模块学习连接话语级表示。实验结果表明，该模型能够生成比基线模型更高的一致性和流畅性的回应。

- 136. Yanmeng Wang, Wenge Rong, Jianfei Zhang, Yuanxin Ouyang, and Zhang Xiong. 2020. Knowledge grounded pretrained model for dialogue response generation. In Proceedings of the International Joint Conference on Neural Networks.

这篇论文的主要观点是提出了一个知识驱动的预训练模型，用于对话回应生成。该模型利用外部文本知识来增强多轮对话的建模和生成，解决了对话数据中稀疏实体的问题。该模型采用BERT作为编码器，将外部知识融入对话历史建模，设计了一个多头注意力机制的解码器，将知识和对话的语义信息融入解码过程，生成信息丰富和合适的对话回应。实验结果表明，该模型在两个回应生成任务上都优于基线模型，无论是在自动评估还是人工评估方面。

- 145. Zhen Xu, Bingquan Liu, Baoxun Wang, Chengjie Sun, Xiaolong Wang, Zhuoran Wang, and Chao Qi. 2017. Neural response generation via GAN with an approximate embedding layer. In Proceedings of the Conference on Empirical Methods in Natural Language Processing.

这篇论文的主要观点是提出了一个基于GAN的神经回应生成模型，用于模拟单轮短文本对话。该模型同时训练一个基于Seq2Seq的生成器和一个判别器，判别器用于衡量人类产生的回应和机器生成的回应之间的差异。此外，该模型引入了一个近似嵌入层，用于解决Seq2Seq生成模型中由于采样输出解码过程导致的不可微分问题。GAN的设置提供了一种有效的方法，避免了传统神经回应生成器中常见的非信息性回应（也称为“安全回应”）。实验结果表明，该模型在多样性指标方面显著优于现有的神经回应生成模型，在相关性评分方面也有轻微提升，无论是在中文语料库还是英文语料库上进行评估。

- 159. Ganbin Zhou, Ping Luo, Rongyu Cao, Fen Lin, Bo Chen, and Qing He. 2017. Mechanism-aware neural machine for dialogue response generation. In Proceedings of the AAAI Conference on Artificial Intelligence.

这篇论文的主要内容是提出了一个机制感知的神经机器，用于对话回应生成。该机器认为对于同一个话语，人类的回应可能在内容语义、说话风格、交流意图等方面有很大的多样性。因此，该机器将不同的回应机制建模为隐含的嵌入，并开发了一个编码器-分流器-解码器的框架，以端到端的方式训练其模块。该机器通过引入一个近似嵌入层，解决了Seq2Seq生成模型中由于采样输出解码过程导致的不可微分问题。实验结果表明，该机器在中文语料库和英文语料库上都优于基线模型，在多样性和相关性方面都有提升。

数据到文本生成和文本到文本生成都是NLG的实例。从图像生成文本是数据到文本生成的一种应用。一个进一步的文本到文本生成的复杂问题是将NLG任务分为三类，即文本缩写、文本扩展、文本重写和推理。文本缩写任务的目的是将信息从长文本压缩到短文本，典型的包括对文本摘要[8, 9, 20, 24, 53, 96, 116]、问题生成[5, 26, 44, 46, 66, 112, 122, 133, 134, 153, 158]和干扰项生成[30, 61, 76, 89, 98, 103, 117, 118]的研究。文本扩展任务，如短文本扩展[6, 106, 113, 124]和主题到文章生成[27, 97, 135, 146, 152]，通过考虑和添加诸如连词和介词等元素，将输入的词转换为语法正确的输出，从一些有意义的词生成完整的句子甚至文本。文本重写和推理任务的目标是将文本重写为另一种风格或应用推理方法来创建回应。有两个子任务：文本风格转换[13, 28, 43, 63, 72, 80, 85, 95, 143, 157]和对话生成[4, 45, 57, 60, 74, 136, 145, 159]。基于视觉的文本生成任务的目标是生成给定图像或视频的解释或摘要，涉及到图像字幕[1、71、104、129、148、149]、视频字幕[22、49、52、88、114、121、128、131、140、142、160、161]和视觉故事[40、55、147]的研究。

### 1.2 Why a Survey on Natural Language Generation?

## 2 GENERAL METHODS OF NLG

### 2.8 Pre-trained Model

The pre-trained models can be divided into two categories: non-contextual and contextual. The non-contextual pre-trainedmodels (e.g.,Word2vec [79], GloVe [91]) can learn high quality of word and phrase representations and are widely used to initialize the embeddings and improve the model performance for generation tasks. However, since the non-contextual embeddings are static, it is unable to handle context-dependent words and out-of-vocabulary words.

预训练模型可以分为两类：非上下文相关的和上下文相关的。非上下文相关的预训练模型（例如，Word2vec [79]，GloVe [91]）可以学习高质量的词和短语表示，并广泛用于初始化嵌入和提高生成任务的模型性能。然而，由于非上下文相关的嵌入是静态的，它无法处理依赖于上下文的词和词汇表外的词。

To overcome these problems, contextual pre-trained models are proposed, which can dynamically change the word embeddings when the word appears in different sentences. Traditional contextual models mainly focus on the tasks of natural language understanding (NLU), which are based on the architectures of LSTM (e.g., ELMo [93]) or Transformer encoder (e.g., BERT [18]).

为了克服这些问题，提出了上下文相关的预训练模型，它们可以在词出现在不同的句子中时动态地改变词的嵌入。传统的上下文相关模型主要关注自然语言理解（NLU）的任务，它们基于LSTM（例如，ELMo [93]）或Transformer编码器（例如，BERT [18]）的架构。

To tackle the more challenging tasks of natural language generation (NLG), the architecture of Transformer decoder is widely adopted, and the pre-training objective is further modified to adapt to the NLG task. For example, GPT [7] uses the standard language modeling as the pretraining objective. T5 [99] designs a unified text-to-text framework for both NLU and NLG. During pre-training, each corrupted token span in the input sequence is replaced with a sentinel token, and the output sequence is composed of the dropped-out spans. MASS [116] predicts the sentence fragment with input of masked sequence during pre-training. UniLM [23] propose a unified pretraining framework for both NLU and NLG tasks, which is based on a shared Transformer model with three different types of self-attention masks to switch among different tasks, including unidirectional, bidirectional, and sequence-to-sequence. BART [53] designs a reconstruction objective to restore corrupted documents, where five types of transformation strategies are proposed, including token masking, token deletion, text infilling, sentence permutation, and document rotation. PLATO [4] proposes a pre-training framework targeting the dialogue generation tasks with two reciprocal pre-training tasks, i.e., response generation and latent act recognition. The latent discrete variables are also introduced to solve the one-to-many mapping problem. ERNIE-GEN [141] is a multi-flow Seq2Seq pre-training model, which pays attention to the exposure bias problem of pre-training models on downstream NLG tasks such as question generation, and aims to make the NLG models generate more human-like and fluent texts. OFA [132] presents a unified multimodal pre-training framework to unify various vision and language tasks, such as NLU, NLG, and image classification. Transformer is applied as the backbone architecture, and the sparse coding and a unified vocabulary are utilized to represent the images and linguistic words.

- 7. Tom Brown, Benjamin Mann, Nick Ryder, Melanie Subbiah, Jared D. Kaplan, Prafulla Dhariwal, Arvind Neelakantan, Pranav Shyam, Girish Sastry, Amanda Askell, Sandhini Agarwal, Ariel Herbert-Voss, Gretchen Krueger, Tom Henighan, Rewon Child, Aditya Ramesh, Daniel Ziegler, JeffreyWu, ClemensWinter, Chris Hesse, Mark Chen, Eric Sigler, Mateusz Litwin, Scott Gray, Benjamin Chess, Jack Clark, Christopher Berner, Sam McCandlish, Alec Radford, Ilya Sutskever, and Dario Amodei. 2020. Language models are few-shot learners. In Proceedings of the Conference on Advances in Neural Information Processing Systems. GPT3

- 99. Colin Raffel, Noam Shazeer, Adam Roberts, Katherine Lee, Sharan Narang, Michael Matena, Yanqi Zhou,Wei Li, and Peter J. Liu. 2020. Exploring the limits of transfer learning with a unified text-to-text transformer. J. Mach. Learn. Res. 21, 1 (2020).
这篇论文的主要观点是提出了一个统一的文本到文本的转换器，用于探索NLP中的转移学习技术的极限。该转换器将所有基于文本的语言问题转换为文本到文本的格式，并在数十个语言理解任务上系统地比较了预训练目标、架构、无标签数据集、转移方法和其他因素。通过将探索得到的洞察与规模和新的“巨大清洁爬行语料库”相结合，该转换器在许多涵盖摘要、问答、文本分类等的基准测试上达到了最新的结果。为了促进NLP转移学习的未来工作，论文发布了数据集、预训练模型和代码。

- 116. Kaitao Song, Xu Tan, Tao Qin, Jianfeng Lu, and Tie-Yan Liu. 2019. MASS: Masked sequence to sequence pre-training for language generation. In Proceedings of the 36th International Conference on Machine Learning.
这篇论文的主要观点是提出了一种用于语言生成的掩码序列到序列预训练方法（MASS）。MASS采用编码器-解码器框架，给定句子的剩余部分来重建一个随机掩码的句子片段：其编码器将一个带有随机掩码片段（几个连续的词）的句子作为输入，其解码器试图预测这个掩码片段。这样，MASS可以联合训练编码器和解码器，以发展表示提取和语言建模的能力。通过进一步在多种零/低资源语言生成任务上进行微调，包括神经机器翻译、文本摘要和对话回应生成（3个任务和共8个数据集），MASS比没有预训练或使用其他预训练方法的基线模型都有显著的提升。特别地，在无监督的英法翻译上，我们达到了最新的准确率（37.5的BLEU分数），甚至超过了早期基于注意力的有监督模型。

- 23. Li Dong, Nan Yang, Wenhui Wang, Furu Wei, Xiaodong Liu, Yu Wang, Jianfeng Gao, Ming Zhou, and Hsiao-Wuen Hon. 2019. Unified language model pre-training for natural language understanding and generation. In Proceedings of the Conference on Advances in Neural Information Processing Systems. 13042–13054.
这篇论文的主要观点是提出了一个新的统一预训练语言模型(UniLM)，它可以用于自然语言理解和生成任务的微调。UniLM使用三种类型的语言建模任务进行预训练：单向、双向和序列到序列预测。统一建模是通过使用共享的Transformer网络和利用特定的自注意力掩码来控制预测依赖的上下文实现的。UniLM在GLUE基准测试、SQuAD 2.0和CoQA问答任务上与BERT相比有优势。此外，UniLM在五个自然语言生成数据集上取得了新的最佳结果，包括将CNN/DailyMail抽象摘要ROUGE-L提高到40.51（绝对提升2.04），将Gigaword抽象摘要ROUGE-L提高到35.75（绝对提升0.86），将CoQA生成式问答F1分数提高到82.5（绝对提升37.1），将SQuAD问题生成BLEU-4提高到22.12（绝对提升3.75），以及将DSTC7基于文档的对话回复生成NIST-4提高到2.67（人类表现为2.65）。

- 53. Mike Lewis, Yinhan Liu, Naman Goyal, Marjan Ghazvininejad, Abdelrahman Mohamed, Omer Levy, Veselin Stoyanov, and Luke Zettlemoyer. 2020. BART: Denoising sequence-to-sequence pre-training for natural language generation, translation, and comprehension. In Proceedings of the 58th Annual Meeting of the Association for Computational Linguistics.
这篇论文的主要观点是提出了一个用于预训练序列到序列模型的去噪自编码器BART，它可以用于自然语言生成、翻译和理解任务的微调。BART的预训练过程包括两个步骤：（1）使用任意的去噪函数对文本进行损坏，例如随机打乱句子顺序或用掩码符号替换文本片段；（2）学习一个模型来重建原始文本。BART使用了一个标准的基于Transformer的神经机器翻译架构，它可以看作是泛化了BERT（由于双向编码器）、GPT（由于左到右解码器）和其他更多最近的预训练方案。BART在GLUE和SQuAD等理解任务上达到了与RoBERTa相当的性能，在一系列抽象的对话、问答和摘要任务上取得了新的最先进的结果，其中ROUGE分数提高了3.5个点。BART还在机器翻译任务上提供了1.1个BLEU的提升，仅使用目标语言预训练。论文还复现了其他预训练方案在BART框架内的效果，以更好地衡量哪些因素最影响最终任务性能。


为了应对更具挑战性的自然语言生成（NLG）任务，Transformer解码器的架构被广泛采用，并且预训练目标被进一步修改以适应NLG任务。例如，GPT [7]使用标准的语言模型作为预训练目标。T5 [99]设计了一个统一的文本到文本的框架，用于NLU和NLG。在预训练过程中，输入序列中的每个损坏的词跨度都被替换为一个哨兵词，输出序列由丢弃的跨度组成。MASS [116]在预训练过程中，用掩码序列作为输入来预测句子片段。UniLM [23]提出了一个统一的预训练框架，用于NLU和NLG任务，它基于一个共享的Transformer模型，使用三种不同类型的自注意力掩码来切换不同的任务，包括单向、双向和序列到序列。BART [53]设计了一个重建目标来恢复损坏的文档，在其中提出了五种变换策略，包括词掩码、词删除、文本填充、句子置换和文档旋转。PLATO [4]提出了一个针对对话生成任务的预训练框架，它有两个相互关联的预训练任务，即回应生成和潜在行为识别。潜在离散变量也被引入来解决一对多映射问题。ERNIE-GEN [141]是一个多流Seq2Seq预训练模型，它关注预训练模型在下游NLG任务（如问题生成）上的暴露偏差问题，并旨在使NLG模型生成更像人类和流畅的文本。OFA [132]提出了一个统一的多模态预训练框架，用于统一各种视觉和语言任务，如NLU、NLG和图像分类。Transformer被用作骨干架构，稀疏编码和统一词汇被用来表示图像和语言词。

## 3 TEXT ABBREVIATION

## 4 TEXT EXPANSION

## 5 TEXT REWRITING AND REASONING

## 6 FROM IMAGE TO TEXT GENERATION

## 7 NLG EVALUATION METRICS

## 8 PROBLEMS AND CHALLENGES

In this section, we primarily point out four problems and challenges that deserve to be tackled and investigated further, including the evaluation method, external knowledge engagement, controllable generation, and multimodal scenarios.

在本节中，我们主要指出了四个值得进一步解决和研究的问题和挑战，包括评估方法、外部知识的引入、可控制的生成和多模态场景。

Evaluation Method. Evaluation method is still an important and open research area for the field of NLG. As pointed by Reference [17], traditional untrained evaluation metrics do not always correlate well with human judgements, while recent machine-learned metrics need a large amount of human annotations and not always have good transferability. Hence, there still exists a significant amount of challenges and improvement room in this area.

评估方法。评估方法仍然是自然语言生成领域的一个重要且开放的研究领域。正如参考文献[17]所指出的，传统的无监督评估指标并不总是与人类判断相关，而最近的机器学习评估指标需要大量的人类注释，并且并不总是具有良好的可迁移性。因此，在这一领域仍然存在着相当多的挑战和改进空间。

External Knowledge Engagement. Considering the limited information lying in the original texts and the difficulty of generating satisfying sentences [151], it is crucial to incorporate external knowledge to enhance the performance. Therefore, how to obtain useful and correlative knowledge and how to effectively incorporate the knowledge still deserve to be investigated.

外部知识的引入。考虑到原始文本中有限的信息和生成令人满意的句子的难度[151]，引入外部知识来提高性能是至关重要的。因此，如何获取有用和相关的知识，以及如何有效地结合知识仍然值得研究。

Controllable Generation. Another challenging problem is how to generate controllable natural language as we would like it to be. Although a great body of work has been done in this area to study how to perform various kinds of controlled text generation, there is still a lack of uniform paradigms and standards about it. More importantly, how to measure the controllability of the generated text remains an open question, for different controlled contents.

可控制的生成。另一个具有挑战性的问题是如何生成我们想要的可控制的自然语言。虽然在这一领域已经做了很多工作，研究如何进行各种类型的受控文本生成，但仍然缺乏关于它的统一的范式和标准。更重要的是，如何衡量生成文本的可控性仍然是一个开放的问题，因为不同的受控内容。

Multimodal Scenarios. Recently, research on various applications in multimodal scenarios have gradually attractedmore and more attention from NLP researchers.Howto apply natural language generation methods in multimodal scenarios has been a worthy problem and promising direction. It is reasonable to believe that the utilization of rich multimodal information into natural language generation tasks will surely further advance the progress and development in this direction.

多模态场景。最近，关于多模态场景中各种应用的研究逐渐引起了自然语言处理研究者的更多关注。如何将自然语言生成方法应用于多模态场景是一个有价值的问题和有前途的方向。有理由相信，将丰富的多模态信息利用到自然语言生成任务中，一定会进一步推动这一方向的进展和发展。

## 9 CONCLUSIONS

Over the past few years, natural language generation tasks and methods have become important and indispensable in natural language processing. This progress owes to advances in various deep learning-based methods. This article describes deep learning research on natural language generation with a historical perspective, emphasizing the special character of the problems to be solved. It begins by contrasting generation with language understanding, establishing basic concepts about the tasks, datasets, and the deep learning methods through it. A section of evaluation metrics from the output of generation systems follows, showing what kinds of performance are possible and where the difficulties are. Finally, some open problems are suggested to indicate the major challenges and future research directions of natural language generation.

在过去的几年里，自然语言生成的任务和方法在自然语言处理中变得重要且不可或缺。这一进步归功于各种基于深度学习的方法的发展。本文从历史的角度介绍了自然语言生成的深度学习研究，强调了需要解决的问题的特殊性质。它首先将生成与语言理解进行对比，通过它建立了关于任务、数据集和深度学习方法的基本概念。接下来是一节关于生成系统输出的评估指标，展示了可能的性能和困难所在。最后，提出了一些开放性问题，指出了自然语言生成的主要挑战和未来的研究方向。
