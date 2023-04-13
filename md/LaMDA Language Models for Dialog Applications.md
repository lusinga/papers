# LaMDA: Language Models for Dialog Applications

We present LaMDA: Language Models for Dialog Applications. LaMDA is a family of Transformerbased neural language models specialized for dialog, which have up to 137B parameters and are pre-trained on 1.56T words of public dialog data and web text. While model scaling alone can improve quality, it shows less improvements on safety and factual grounding. We demonstrate that fine-tuning with annotated data and enabling the model to consult external knowledge sources can lead to significant improvements towards the two key challenges of safety and factual grounding. The first challenge, safety, involves ensuring that the model’s responses are consistent with a set of human values, such as preventing harmful suggestions and unfair bias. We quantify safety using a metric based on an illustrative set of human values, and we find that filtering candidate responses using a LaMDA classifier fine-tuned with a small amount of crowdworker-annotated data offers a promising approach to improving model safety. The second challenge, factual grounding, involves enabling the model to consult external knowledge sources, such as an information retrieval system, a language translator, and a calculator. We quantify factuality using a groundedness metric, and we find that our approach enables the model to generate responses grounded in known sources, rather than responses that merely sound plausible. Finally, we explore the use of LaMDA in the domains of education and content recommendations, and analyze their helpfulness and role consistency.

我们介绍了LaMDA：用于对话应用的语言模型。LaMDA是一系列专为对话而设计的基于Transformer的神经语言模型，参数数量多达1370亿，通过在1.56万亿字的公共对话数据和网络文本上进行预训练。虽然单纯的模型扩展可以提高质量，但在安全性和事实依据方面的改进较小。我们证明，通过使用带有注释的数据进行微调，并使模型能够查询外部知识源，可以显著改善安全性和事实依据这两个关键挑战。第一个挑战是安全性，确保模型的响应与一系列人类价值观一致，例如防止有害建议和不公平偏见。我们使用基于一组具有代表性的人类价值观的指标来量化安全性，并发现使用经过少量众包工人注释数据微调的LaMDA分类器过滤候选响应是提高模型安全性的一种有前景的方法。第二个挑战是事实依据，涉及使模型能够查询外部知识源，如信息检索系统、语言翻译器和计算器。我们使用一个基于事实依据的指标来量化事实性，并发现我们的方法使模型能够生成基于已知来源的响应，而不仅仅是听起来合理的响应。最后，我们探讨了在教育和内容推荐领域应用LaMDA的可能性，并分析了它们的有效性和角色一致性。

## 1 Introduction

Language model pre-training is an increasingly promising research approach in NLP [1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12]. As pre-training uses unlabeled text, it can be combined with scaling model and dataset sizes to achieve better performance or new capabilities [13]. For example, GPT-3 [12], a 175B parameter model trained on a large corpus of unlabeled text, shows an impressive ability in few-shot learning thanks to scaling.

语言模型预训练在自然语言处理（NLP）领域越来越具有前景的研究方法[1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12]。由于预训练使用的是无标签文本，它可以与扩展模型和数据集大小相结合，以实现更好的性能或新的功能[13]。例如，GPT-3[12]是一个在大量无标签文本上训练的具有1750亿参数的模型，得益于扩展，它在少样本学习中表现出令人印象深刻的能力。

Dialog models [14, 15, 16], one of the most interesting applications of large language models, successfully take advantage of Transformers’ ability to represent long-term dependencies in text [17, 18]. Similar to general language models [13], Adiwardana et al. [17] show that dialog models are also well suited to model scaling. There is a strong correlation between model size and dialog quality.

对话模型[14, 15, 16]是大型语言模型最有趣的应用之一，成功利用了Transformer在表示文本中长期依赖关系方面的能力[17, 18]。与通用语言模型[13]类似，Adiwardana等人[17]表明，对话模型也非常适合模型扩展。模型大小与对话质量之间存在很强的相关性。

Inspired by these successes, we train LaMDA, a family of Transformer-based neural language models designed for dialog. These models’ sizes range from 2B to 137B parameters, and they are pre-trained on a dataset of 1.56T words from public dialog data and other public web documents (Section 3). LaMDA makes use of a single model to perform multiple tasks: it generates potential responses, which are then filtered for safety, grounded on an external knowledge source, and re-ranked to find the highest-quality response.

受到这些成功的启发，我们训练了LaMDA，一系列为对话设计的基于Transformer的神经语言模型。这些模型的大小从20亿到1370亿参数不等，它们在一个包含1.56万亿单词的公共对话数据和其他公共网络文档数据集上进行预训练（第3节）。LaMDA利用单个模型执行多个任务：生成潜在的响应，然后过滤以确保安全性，基于外部知识源进行事实依据，并重新排名以找到最高质量的响应。

We study the benefits of model scaling with LaMDA on our three key metrics: quality, safety, and groundedness (Section 4). We observe that: (a) model scaling alone improves quality, but its improvements on safety and groundedness are far behind human performance, and (b) combining scaling and fine-tuning improves LaMDA significantly on all metrics, and although the model’s performance remains below human levels in safety and groundedness, the quality gap to measured crowdworker levels can be narrowed (labeled ‘Human’ in Figure 1).

![图1](https://oss-lusing.oss-cn-beijing.aliyuncs.com/lamda1.png?Expires=1681407808&OSSAccessKeyId=TMP.3Kdb8Ppqi2RKZEnwRDgcutEzE2VL34d5VrAcAW2pxCCd6Lqvdm2RrxfAkGjXaJcZ5cnNpoqAAdjmcraJnhDGap8kPPsDkm&Signature=6BSbCYfWG%2BHdkCv4Ny3W%2BClfvmo%3D)

我们研究了在LaMDA上使用模型扩展对我们的三个关键指标（质量、安全性和事实依据）的优势（第4节）。我们观察到：（a）单独的模型扩展可以提高质量，但在安全性和事实依据方面的改进远远落后于人类表现；（b）将扩展和微调相结合，可以显著提高LaMDA在所有指标上的表现，尽管模型在安全性和事实依据方面的表现仍低于人类水平，但与测量的众包工人水平之间的质量差距可以缩小（如图1中的“人类”标签所示）。

The first metric, quality, is based on three components: sensibleness, specificity, and interestingness (Section 4). We collect annotated data that describes how sensible, specific, and interesting a response is for a multiturn context. We then use these annotations to fine-tune a discriminator to re-rank candidate responses.

第一个指标——质量，是基于三个组成部分的：合理性、特异性和趣味性（第4节）。我们收集带有注释的数据，描述在多轮对话背景下响应的合理性、特异性和趣味性。然后，我们使用这些注释来微调一个鉴别器，以重新排名候选响应。

The second metric, safety, is introduced to reduce the number of unsafe responses that the model generates. To achieve this, we define an illustrative set of safety objectives that attempt to capture the behavior that the model should exhibit in a dialog (Appendix A.1), and we use a demographically diverse set of crowdworkers to label responses in multiturn dialogs for these objectives (Appendix A.2, A.3). We then use these labels to fine-tune a discriminator to detect and remove unsafe responses (Section 6.1). Our work on safety for LaMDA can be understood as a process for AI value alignment, at a high level.

第二个指标——安全性，旨在减少模型生成的不安全响应数量。为了实现这一目标，我们定义了一组说明性的安全目标，试图捕捉模型在对话中应表现出的行为（附录A.1），并使用一个人口统计特征多样化的众包工人群体为这些目标在多轮对话中的响应进行标记（附录A.2，A.3）。然后，我们使用这些标签来微调一个鉴别器，以便检测和移除不安全的响应（第6.1节）。从高层次来看，我们在LaMDA的安全性方面的工作可以被理解为一种AI价值观一致性的过程。

The third metric, groundedness, is introduced for the model to produce responses that are grounded in known sources wherever they contain verifiable external world information. Due to neural language models such as LaMDA’s capacity to generalize rather than just memorize, they tend to generate responses that may seem plausible, but actually contradict factual statements made in established sources. We use this metric for the model to avoid this tendency. While grounding in known sources does not guarantee factual accuracy, it allows users or external systems to judge the validity of a response based on the reliability of its source and its faithful reproduction. We find that augmenting model outputs with the ability to use external tools, such as an information retrieval system, is a promising approach to achieve this goal. Therefore, we collect data from a setting where crowdworkers can use external tools to research factual claims, and train the model to mimic their behavior.

第三个指标——事实依据，旨在使模型在包含可验证的外部世界信息的地方产生基于已知来源的响应。由于神经语言模型（如LaMDA）具有概括而非仅仅记忆的能力，它们往往会生成看似合理但实际上与已建立来源中的事实陈述相矛盾的响应。我们使用这个指标让模型避免这种倾向。虽然在已知来源中寻求事实依据不能保证事实准确性，但它允许用户或外部系统根据响应来源的可靠性和其忠实再现来判断响应的有效性。我们发现，通过让模型输出具有使用外部工具（如信息检索系统）的能力，是实现这一目标的有前景的方法。因此，我们收集了一些数据，在这些数据中，众包工人可以使用外部工具研究事实主张，并训练模型模仿他们的行为。

Finally, we explore the use of LaMDA in the domains of education and content recommendations to investigate its potential and shortcomings. Similar to the concept of prompts in GPT-3 [12], we precondition LaMDA on a few turns of application-specific dialog to adapt LaMDA to the target applications. We perform experiments to compare the application-specific helpfulness (i.e., useful and correct responses) and role consistency (i.e., agent utterances match agent role) of pre-training-only and fine-tuned LaMDA models subject to application-specific preconditioning. We find that both types of models can adapt to their expected application roles fairly well, but fine-tuned LaMDA models are significantly more helpful.

最后，我们探讨了在教育和内容推荐领域使用LaMDA的可能性和不足。与GPT-3[12]中的提示概念类似，我们在应用特定的对话中对LaMDA进行了一些预处理，以使LaMDA适应目标应用。我们进行了实验，比较了仅预训练和微调后的LaMDA模型在应用特定预处理下的应用特定帮助性（即有用且正确的响应）和角色一致性（即代理话语与代理角色相匹配）。我们发现，这两种类型的模型都可以相当好地适应预期的应用角色，但经过微调的LaMDA模型要更有帮助。

## 2 Related work

![](https://oss-lusing.oss-cn-beijing.aliyuncs.com/lamda2.png?Expires=1681408846&OSSAccessKeyId=TMP.3Kdb8Ppqi2RKZEnwRDgcutEzE2VL34d5VrAcAW2pxCCd6Lqvdm2RrxfAkGjXaJcZ5cnNpoqAAdjmcraJnhDGap8kPPsDkm&Signature=moHHeWwsKd1DK99NLAN5XcY5ggs%3D)

## 3 LaMDA pre-training

LaMDA was pre-trained to predict the next token in a text corpus. Unlike previous dialog models trained on dialog data alone [17, 18], we pre-trained LaMDA on a dataset created from public dialog data and other public web documents. Therefore, LaMDA can be used as a general language model prior to fine-tuning.

- Daniel Adiwardana, Minh-Thang Luong, David R. So, Jamie Hall, Noah Fiedel, Romal Thoppilan, Zi Yang, Apoorv Kulshreshtha, Gaurav Nemade, Yifeng Lu, and Quoc V. Le. Towards a human-like open-domain chatbot. arXiv preprint arXiv:2001.09977, 2020.
我们提出了Meena，一个多轮开放式对话机器人，它是在从公共领域的社交媒体对话中挖掘和过滤的数据上端到端训练的。这个2.6B参数的神经网络只是简单地训练来最小化下一个词的困惑度。我们还提出了一个人类评估指标，叫做合理性和特定性平均（SSA），它捕捉了人类多轮对话的关键元素。我们的实验表明，困惑度和SSA之间有很强的相关性。事实上，最佳困惑度端到端训练的Meena在SSA上得分很高（在多轮评估中为72%），这表明如果我们能够更好地优化困惑度，那么人类水平的SSA（86%）就有可能达到。此外，Meena的完整版本（带有过滤机制和调整过的解码）得分为79% SSA，比我们评估的现有聊天机器人高出23%的绝对SSA。

- Stephen Roller, Emily Dinan, Naman Goyal, Da Ju, Mary Williamson, Yinhan Liu, Jing Xu, Myle Ott, Kurt Shuster, Eric M. Smith, Y-Lan Boureau, and Jason Weston. Recipes for building an open-domain chatbot. arXiv preprint arXiv:2004.13637, 2020.
构建开放式对话机器人是机器学习研究的一个具有挑战性的领域。虽然先前的工作表明，通过增加神经模型的参数数量和训练数据的规模可以得到改进的结果，但我们表明，其他因素对于一个高性能的对话机器人也很重要。良好的对话需要多种技能，一个专业的对话者可以无缝地融合这些技能：提供有吸引力的话题并倾听他们的伙伴，适当地展示知识、同理心和个性，同时保持一致的人格。我们表明，当给定适当的训练数据和生成策略时，大规模模型可以学习这些技能。我们构建了这些配方的变体，分别有90M、2.7B和9.4B个参数的模型，并且公开了我们的模型和代码。人类评估表明，我们最好的模型在多轮对话中优于现有方法，在吸引力和人性化方面的测量。然后，我们通过分析我们模型的失败案例来讨论这项工作的局限性。


LaMDA是预训练在一个文本语料库上来预测下一个词的。与之前仅仅在对话数据上训练的对话模型[17, 18]不同，我们在一个由公开的对话数据和其他公开的网页文档创建的数据集上预训练LaMDA。因此，LaMDA可以在微调之前作为一个通用的语言模型使用。

The pre-training dataset consists of 2.97B documents, 1.12B dialogs, and 13.39B dialog utterances, for a total of 1.56T words (Appendix E). Over 90% of the pre-training dataset is in the English language. We used the SentencePiece library [90] to tokenize the dataset into 2.81T byte pair encoding (BPE) tokens [91], with a vocabulary of 32K tokens. For comparison, the total number of words in the training set for Meena [17] was 40B words, which is nearly 40x smaller.

- Taku Kudo and John Richardson. SentencePiece: A simple and language independent subword tokenizer and detokenizer for neural text processing. In Proceedings of the 2018 Conference on Empirical Methods in Natural Language Processing: System Demonstrations, 2018
本文介绍了SentencePiece，一个用于神经文本处理（包括神经机器翻译）的语言无关的子词分词器和反分词器。它提供了子词单元的开源C++和Python实现。现有的子词分割工具假设输入是预先分词成单词序列的，而SentencePiece可以直接从原始句子中训练子词模型，这使我们能够构建一个纯粹的端到端和语言无关的系统。我们在英日机器翻译上进行了NMT的验证实验，并发现直接从原始句子中训练子词可以达到与现有方法相当的准确率。我们还比较了不同配置下的子词训练和分割的性能。SentencePiece在Apache 2许可下可用。

- Rico Sennrich, Barry Haddow, and Alexandra Birch. Neural machine translation of rare words with subword units. arXiv preprint arXiv:1508.07909, 2015.
神经机器翻译（NMT）模型通常使用固定的词汇表，但翻译是一个开放词汇的问题。先前的工作通过退化到字典来解决词汇表外单词的翻译问题。在本文中，我们介绍了一种更简单和更有效的方法，通过将稀有和未知单词编码为子词单元序列，使NMT模型能够进行开放词汇的翻译。这基于这样一种直觉，即各种词类可以通过比单词更小的单元来翻译，例如名字（通过字符复制或音译）、复合词（通过组合翻译）以及同源词和外来词（通过音韵和形态变换）。我们讨论了不同的分词技术的适用性，包括简单的字符n-gram模型和基于字节对编码压缩算法的分割，并且凭经验证明，子词模型在WMT 15英德和英俄翻译任务上分别比退化到字典的基线提高了1.1和1.3个BLEU。

预训练数据集由29.7亿个文档、11.2亿个对话和133.9亿个对话话语组成，共计1.56T个单词（附录E）。超过90%的预训练数据集为英语。我们使用SentencePiece库 [90]将数据集分词为2.81T个字节对编码（BPE）标记 [91]，词汇量为32K个标记。相比之下，Meena [17]的训练集中单词的总数为400亿个，几乎是本文数据集的40倍小。

The largest LaMDA model has 137B non-embedding parameters, which is ~50x more parameters than Meena [17]. We use a decoder-only Transformer [92] language model as the model architecture for LaMDA. The Transformer has 64 layers, dmodel = 8192, dff = 65536, h = 128, dk = dv = 128, relative attention as described in T5 [11], and gated-GELU activation as described in Raffel et al. [93].

- Colin Raffel, Noam Shazeer, Adam Roberts, Katherine Lee, Sharan Narang, Michael Matena, Yanqi Zhou, Wei Li, and Peter J. Liu. Exploring the limits of transfer learning with a unified text-to-text transformer. Journal of Machine Learning Research, 2020.
迁移学习，即先在一个数据丰富的任务上预训练一个模型，然后在一个下游任务上微调它，已经成为自然语言处理（NLP）中的一种强大技术。迁移学习的有效性催生了多种方法、方法论和实践。在本文中，我们通过引入一个统一的框架，将所有基于文本的语言问题转换为文本到文本的格式，来探索NLP中迁移学习技术的疆界。我们对数十个语言理解任务进行了系统的研究，比较了预训练目标、架构、无标注数据集、迁移方法等因素。通过将我们的探索结果与规模和我们新的“巨大清洁爬行语料库”相结合，我们在许多涵盖摘要、问答、文本分类等方面的基准测试上实现了最先进的结果。为了促进NLP领域迁移学习的未来工作，我们发布了我们的数据集、预训练模型和代码。

- Noam Shazeer. Glu variants improve transformer. arXiv preprint arXiv:2002.05202, 2020.
门控线性单元（GLU）由两个线性投影的分量乘积组成，其中一个先通过一个sigmoid函数。可以使用不同的非线性（甚至线性）函数来替代sigmoid，从而得到GLU的变体。我们在Transformer的前馈子层中测试了这些变体，并发现其中一些可以比通常使用的ReLU或GELU激活函数获得质量的提升。


最大的LaMDA模型具有1370亿个非嵌入参数，比Meena [17]多约50倍。我们使用仅解码器的Transformer [92]语言模型作为LaMDA的模型架构。Transformer具有64层，dmodel = 8192，dff = 65536，h = 128，dk = dv = 128，相对注意力如T5 [11]所述，并且具有Raffel等人描述的门控GELU激活函数[93]。

We pre-trained LaMDA on 1024 TPU-v3 chips for a total of about 57.7 days, and 256K tokens per batch. We used the Lingvo framework [94] for training and achieved 123 TFLOPS/sec with 56.5% FLOPS utilization with the 2D sharding algorithm, as described in GSPMD [95] (see Section 10 for carbon footprint estimates). We also trained smaller 2B-parameter and 8B-parameter models to measure the effects of model scaling on our metrics. Hyperparameter details for the models of different sizes can be found in Table 27, Appendix D.

- Jonathan Shen, Patrick Nguyen, Yonghui Wu, Zhifeng Chen, Mia X Chen, Ye Jia, Anjuli Kannan, Tara Sainath, Yuan Cao, Chung-Cheng Chiu, et al. Lingvo: a modular and scalable framework for sequence-to-sequence modeling. arXiv preprint arXiv:1902.08295, 2019.
Lingvo是一个Tensorflow框架，为协作的深度学习研究提供了一个完整的解决方案，特别关注序列到序列模型。Lingvo模型由灵活且易于扩展的模块化构建块组成，实验配置集中且高度可定制。框架内直接支持分布式训练和量化推理，并包含了大量的实用程序、辅助函数和最新的研究思想的现有实现。在过去的两年中，Lingvo已经在20多篇论文中与数十名研究人员合作使用。本文概述了Lingvo的底层设计，并介绍了框架的各个部分，同时也提供了一些展示框架能力的高级特性的示例。

- Yuanzhong Xu, HyoukJoong Lee, Dehao Chen, Blake A. Hechtman, Yanping Huang, Rahul Joshi, M. Krikun, Dmitry Lepikhin, Andy Ly, Marcello Maggioni, Ruoming Pang, Noam M. Shazeer, Shibo Wang, Tao Wang, Yonghui Wu, and Zhifeng Chen. Gspmd: General and scalable parallelization for ml computation graphs. arXiv preprint arXiv:2105.04663, 2021.
我们提出了GSPMD，一个用于常见机器学习计算的自动、基于编译器的并行化系统。它允许用户像为单个设备编写程序一样编写程序，然后通过一些注释来提示如何分布张量，基于此GSPMD将并行化计算。它的分区表示简单而通用，允许它在各种模型上表达不同或混合的并行范式。GSPMD根据有限的用户注释推断每个操作符的分区，使得将现有的单设备程序扩展起来很方便。它解决了生产使用中的几个技术挑战，使GSPMD能够在多达2048个Cloud TPUv3核心上实现50%到62%的计算利用率，用于高达一万亿参数的模型。




我们在1024个TPU-v3芯片上进行了LaMDA的预训练，每个批次包含256K个标记，共计约57.7天。我们使用Lingvo框架 [94]进行训练，并使用2D分片算法（如GSPMD [95]所述）实现了123 TFLOPS/sec和56.5％的FLOPS利用率（有关碳足迹估计，请参见第10节）。我们还训练了较小的2B参数和8B参数模型，以衡量模型规模对我们的指标的影响。不同大小模型的超参数详细信息可以在附录D的表27中找到。


## 5 LaMDA fine-tuning and evaluation data

## 6 LaMDA fine-tuning

## 7 Results on foundation metrics

## 8 Domain grounding

## 9 Discussion and limitations

## 10 Energy and Carbon Footprint Estimate of LaMDA

## 11 Conclusion

This paper studies the importance of scale, annotated data for model fine-tuning, and the use of information retrieval as a tool in dialog modeling. Our experiments show that scaling alone offers improvements in all metrics, but its improvements on safety and groundedness are far behind human performance. We find that crowd-annotated data is an effective tool for driving significant additional gains. We also find that calling external APIs (such as an information retrieval system) offers a path towards significantly improving groundedness, which we define as the extent to which a generated response contains claims that can be referenced and checked against a known source.

本文研究了规模、用于模型微调的带注释数据以及在对话建模中作为工具使用信息检索的重要性。我们的实验表明，仅仅依靠规模在所有指标上都有改进，但在安全性和事实依据上的改进远远落后于人类的表现。我们发现，众包注释数据是实现显著额外收益的有效工具。我们还发现，调用外部API（如信息检索系统）为显著提高事实依据提供了一条途径，我们将事实依据定义为生成响应中包含的主张能在已知来源中进行引用和检查的程度。

We perform experiments to compare the per-application helpfulness (i.e., useful and correct responses) and role consistency of pre-training-only (PT) and LaMDA models when subject to the same application-specific preconditioning. We pre-condition the models on a small number of turns of application-specific dialogs (similar to the concept of prompts in GPT-3) to quickly adapt LaMDA to these applications. We find that both types of models can adapt to their expected context, with more than four out of five responses staying consistent with their assigned roles. However, LaMDA-based applications are significantly more helpful than PT applications.

我们进行了实验，比较了仅预训练（PT）和LaMDA模型在受到相同应用特定预处理条件时的每个应用的帮助性（即有用且正确的响应）和角色一致性。我们对模型进行预处理，对一小部分应用特定的对话进行处理（类似于GPT-3中的提示概念），以快速使LaMDA适应这些应用。我们发现，这两种类型的模型都可以适应预期的上下文，五分之四以上的响应与分配的角色保持一致。然而，基于LaMDA的应用比PT应用更有帮助。

LaMDA is a step closer to practical and safe open-ended dialog systems, which can in turn unlock a wide range of useful applications. We hope that this work encourages further research in this area.

LaMDA是一个更接近实用和安全的开放式对话系统的步骤，这反过来又可以解锁一系列有用的应用。我们希望这项工作能够鼓励在这一领域进行更多的研究。
