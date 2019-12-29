# Exploring the Limits of Transfer Learning with a Unified Text-to-Text Transformer

## Abstract

Transfer learning, where a model is first pre-trained on a data-rich task before being fine-tuned on a downstream task, has emerged as a powerful technique in natural language processing (NLP). The effectiveness of transfer learning has given rise to a diversity of approaches, methodology, and practice. In this paper, we explore the landscape of transfer learning techniques for NLP by introducing a unified framework that converts every language problem into a text-to-text format. Our systematic study compares pre-training objectives, architectures, unlabeled datasets, transfer approaches, and other factors on dozens of language understanding tasks. By combining the insights from our exploration with scale and our new “Colossal Clean Crawled Corpus”, we achieve state-of-the-art results on many benchmarks covering summarization, question answering, text classification, and more. To facilitate future work on transfer learning for NLP, we release our dataset, pre-trained models, and code.

迁移学习，在对下游任务进行微调之前，首先对模型进行数据丰富任务的预训练, 已经成为自然语言处理 (NLP) 中一种强大的技术。迁移学习的有效性导致了方法、方法和实践的多样性。在本文中，我们通过引入一个统一的框架来探索 NLP 迁移学习技术的前景，该框架将每个语言问题转换成文本到文本的格式。我们的系统研究比较了几十个语言理解任务的训练前目标、体系结构、未标记数据集、转移方法和其他因素。通过将我们探索的见解与规模和我们新的 “巨大的干净爬行语料库” 相结合，我们在涵盖总结、问答、文本分类等等。为了促进未来 NLP 迁移学习的工作，我们发布了我们的数据集、预训练模型和代码。

## 1 Introduction

Training a machine learning model to perform natural language processing (NLP) tasks often requires that the model can process text in a way that is amenable to downstream learning. This can be loosely viewed as developing general-purpose knowledge that allows the model to “understand” text. This knowledge can range from low-level (e.g. the spelling or meaning of words) to high-level (e.g. that a tuba is too large to fit in most backpacks). In modern machine learning practice, providing this knowledge is rarely done explicitly; instead, it is often “learned” as part of an auxiliary task. For example, a historically common approach is to use “word vectors” [Mikolov et al., 2013b,a; Pennington et al., 2014] to map word identities to a continuous representation where, ideally, similar words map to similar vectors. These vectors are often learned through an objective that, for example, encourages co-occurring words to be positioned nearby in the continuous space [Mikolov et al., 2013b].

1. Tomas Mikolov, Ilya Sutskever, Kai Chen, Greg S. Corrado, and Jeff Dean. Distributed representations of words and phrases and their compositionality. In Advances in neural information processing systems, 2013b.
2. Jeffrey Pennington, Richard Socher, and Christopher Manning. GloVe: Global vectors for word representation. In Proceedings of the 2014 conference on empirical methods in natural language processing (EMNLP), 2014.

训练一个机器学习模型来执行自然语言处理(NLP)任务通常需要该模型能够以一种适合下游学习的方式来处理文本。这可以粗略地看作是开发通用知识，允许模型“理解”文本。这些知识可以是低级的(如单词的拼写或含义)，也可以是高级的(如大号太大，大多数背包装不下)。在现代机器学习实践中，提供这些知识很少是明确的;相反，它经常作为辅助任务的一部分被“学习”。例如，历史上常用的方法是使用“词向量”[Mikolov et al.， 2013b,a;将单词标识映射到一个连续的表示，在理想情况下，相似的单词映射到相似的向量。这些向量通常是通过一个目标来学习的，例如，鼓励同时出现的单词被放置在连续空间附近[Mikolov et al.， 2013b]。

Recently, it has become increasingly common to pre-train the entire model on a data-rich task. Ideally, this pre-training causes the model to develop general-purpose abilities and knowledge that can then be “transferred” to downstream tasks. In applications of transfer learning to computer vision [Oquab et al., 2014; Jia et al., 2014; Huh et al., 2016; Yosinski et al., 2014], pre-training is typically done via supervised learning on a large labeled dataset like ImageNet [Russakovsky et al., 2015; Deng et al., 2009]. In contrast, modern techniques for transfer learning in NLP often pre-train using unsupervised learning on unlabeled data. This approach has recently been used to obtain state-of-the-art results in many of the most common NLP benchmarks [Devlin et al., 2018; Yang et al., 2019; Dong et al., 2019; Liu et al., 2019c; Lan et al., 2019]. Beyond its empirical strength, unsupervised pre-training for NLP is particularly attractive because unlabeled text data is available en masse thanks to the Internet – for example, the Common Crawl project2 produces about 20TB of text data extracted from web pages each month. This is a natural fit for neural networks, which have been shown to exhibit remarkable scalability, i.e. it is often possible to achieve better performance simply by training a larger model on a larger dataset [Hestness et al., 2017; Shazeer et al., 2017; Jozefowicz et al., 2016; Mahajan et al., 2018; Radford et al., 2019; Shazeer et al., 2018, 2017; Huang et al., 2018b].

1. Maxime Oquab, Leon Bottou, Ivan Laptev, and Josef Sivic. Learning and transferring mid-level image representations using convolutional neural networks. In Proceedings of the IEEE conference on computer vision and pattern recognition, 2014.
2. Yangqing Jia, Evan Shelhamer, Jeff Donahue, Sergey Karayev, Jonathan Long, Ross Girshick, Sergio Guadarrama, and Trevor Darrell. Caffe: Convolutional architecture for fast feature embedding. In Proceedings of the 22nd ACM international conference on Multimedia, 2014.
3. Minyoung Huh, Pulkit Agrawal, and Alexei A. Efros. What makes ImageNet good for transfer learning? arXiv preprint arXiv:1608.08614, 2016.
4. Jason Yosinski, Jeff Clune, Yoshua Bengio, and Hod Lipson. How transferable are features in deep neural networks? In Advances in neural information processing systems, 2014.
5. Olga Russakovsky, Jia Deng, Hao Su, Jonathan Krause, Sanjeev Satheesh, Sean Ma, Zhiheng Huang, Andrej Karpathy, Aditya Khosla, Michael Bernstein, et al. ImageNet large scale visual recognition challenge. International journal of computer vision, 2015.
6. Jia Deng, Wei Dong, Richard Socher, Li-Jia Li, Kai Li, and Li Fei-Fei. ImageNet: A large-scale hierarchical image database. In 2009 IEEE conference on computer vision and pattern recognition, 2009.
7. (BERT) Jacob Devlin, Ming-Wei Chang, Kenton Lee, and Kristina Toutanova. BERT: Pre-training of deep bidirectional transformers for language understanding. arXiv preprint arXiv:1810.04805, 2018.
8. (XLNet) Zhilin Yang, Zihang Dai, Yiming Yang, Jaime Carbonell, Ruslan Salakhutdinov, and Quoc V. Le. XLNet: Generalized autoregressive pretraining for language understanding. arXiv preprint arXiv:1906.08237, 2019.
9. Li Dong, Nan Yang, Wenhui Wang, Furu Wei, Xiaodong Liu, Yu Wang, Jianfeng Gao, Ming Zhou, and Hsiao-Wuen Hon. Unified language model pre-training for natural language understanding and generation. arXiv preprint arXiv:1905.03197, 2019.
10. Yinhan Liu, Myle Ott, Naman Goyal, Jingfei Du, Mandar Joshi, Danqi Chen, Omer Levy, Mike Lewis, Luke Zettlemoyer, and Veselin Stoyanov. RoBERTa: A robustly optimized BERT pretraining approach. arXiv preprint arXiv:1907.11692, 2019c.
11. Zhenzhong Lan, Mingda Chen, Sebastian Goodman, Kevin Gimpel, Piyush Sharma, and Radu Soricut. ALBERT: A lite BERT for self-supervised learning of language representations. arXiv preprint arXiv:1909.11942, 2019.
12. Joel Hestness, Sharan Narang, Newsha Ardalani, Gregory Diamos, Heewoo Jun, Hassan Kianinejad, Md. Mostofa Ali Patwary, Yang Yang, and Yanqi Zhou. Deep learning scaling is predictable, empirically. arXiv preprint arXiv:1712.00409, 2017.
13. Noam Shazeer, Azalia Mirhoseini, Krzysztof Maziarz, Andy Davis, Quoc Le, Geoffrey Hinton, and Jeff Dean. Outrageously large neural networks: The sparsely-gated mixture-of-experts layer. arXiv preprint arXiv:1701.06538, 2017.
14. Rafal Jozefowicz, Oriol Vinyals, Mike Schuster, Noam Shazeer, and Yonghui Wu. Exploring the limits of language modeling. arXiv preprint arXiv:1602.02410, 2016.
15. Dhruv Mahajan, Ross Girshick, Vignesh Ramanathan, Kaiming He, Manohar Paluri, Yixuan Li, Ashwin Bharambe, and Laurens van der Maaten. Exploring the limits of weakly supervised pretraining. In Proceedings of the European Conference on Computer Vision (ECCV), 2018.
16. Alec Radford, Jeffrey Wu, Rewon Child, David Luan, Dario Amodei, and Ilya Sutskever. Language models are unsupervised multitask learners, 2019.
17. Noam Shazeer, Youlong Cheng, Niki Parmar, Dustin Tran, Ashish Vaswani, Penporn Koanantakool, Peter Hawkins, HyoukJoong Lee, Mingsheng Hong, Cliff Young, Ryan Sepassi, and Blake Hechtman. Mesh-tensorflow: Deep learning for supercomputers. In Advances in Neural Information Processing Systems, 2018.
18. Noam Shazeer, Azalia Mirhoseini, Krzysztof Maziarz, Andy Davis, Quoc Le, Geoffrey Hinton, and Jeff Dean. Outrageously large neural networks: The sparsely-gated mixture-of-experts layer. arXiv preprint arXiv:1701.06538, 2017.
19. anping Huang, Yonglong Cheng, Dehao Chen, HyoukJoong Lee, Jiquan Ngiam, Quoc V Le, and Zhifeng Chen. GPipe: Efficient training of giant neural networks using pipeline parallelism. arXiv preprint arXiv:1811.06965, 2018b.

最近，在一个数据丰富的任务上对整个模型进行预培训变得越来越普遍。理想情况下，这种预训练会使模型发展出通用的能力和知识，然后可以“转移”到下游的任务中。转移学习在计算机视觉中的应用[Oquab等，2014;Jia等，2014;Huh等人，2016;Yosinski等人，2014]，通常是通过在ImageNet等大型标记数据集上的监督学习来完成预训练[Russakovsky等人，2015;邓等人，2009]。与此相反，在NLP中，现代的转移学习技术通常是对未标记数据进行无监督学习的预训练。这种方法最近被用于在许多最常见的NLP基准测试中获得最先进的结果[Devlin et al.， 2018;杨等人，2019年;董等人，2019年;刘等，2019c;Lan等人，2019年]。除了它的经验优势之外，无监督的NLP前培训特别有吸引力，因为未标记的文本数据可以通过互联网获得——例如，常见的爬网项目t2每个月从web页面提取20TB的文本数据。这对于神经网络来说是一种自然的适合，因为神经网络已经被证明具有显著的可扩展性，也就是说，通常可以通过在更大的数据集上训练更大的模型来获得更好的性能[Hestness et al.， 2017;Shazeer等人，2017;Jozefowicz等，2016;马哈詹等人，2018;Radford等人，2019年;Shazeer等人，2018年，2017年;黄等，2018b]。

This synergy has resulted in a great deal of recent work developing transfer learning methodology for NLP, which has produced a wide landscape of pre-training objectives [Howard and Ruder, 2018; Devlin et al., 2018; Yang et al., 2019; Dong et al., 2019], unlabeled datasets [Yang et al., 2019; Liu et al., 2019c; Zellers et al., 2019], benchmarks [Wang et al., 2019b, 2018; Conneau and Kiela, 2018], fine-tuning methods [Howard and Ruder, 2018; Houlsby et al., 2019; Peters et al., 2019], and more. The rapid rate of progress and diversity of techniques in this burgeoning field can make it difficult to compare different algorithms, tease apart the effects of new contributions, and understand the space of existing methods for transfer learning. Motivated by a need for more rigorous understanding,
we present a unified approach to transfer learning that allows us to systematically study different approaches and push the current limits of the field.

1. Jeremy Howard and Sebastian Ruder. Universal language model fine-tuning for text classification. arXiv preprint arXiv:1801.06146, 2018.
2. Jacob Devlin, Ming-Wei Chang, Kenton Lee, and Kristina Toutanova. BERT: Pre-training of deep bidirectional transformers for language understanding. arXiv preprint arXiv:1810.04805, 2018.
3. Zhilin Yang, Zihang Dai, Yiming Yang, Jaime Carbonell, Ruslan Salakhutdinov, and Quoc V. Le. XLNet: Generalized autoregressive pretraining for language understanding. arXiv preprint arXiv:1906.08237, 2019.
4. Li Dong, Nan Yang, Wenhui Wang, Furu Wei, Xiaodong Liu, Yu Wang, Jianfeng Gao, Ming Zhou, and Hsiao-Wuen Hon. Unified language model pre-training for natural language understanding and generation. arXiv preprint arXiv:1905.03197, 2019.
5. Yinhan Liu, Myle Ott, Naman Goyal, Jingfei Du, Mandar Joshi, Danqi Chen, Omer Levy, Mike Lewis, Luke Zettlemoyer, and Veselin Stoyanov. RoBERTa: A robustly optimized BERT pretraining approach. arXiv preprint arXiv:1907.11692, 2019c
6. Rowan Zellers, Ari Holtzman, Hannah Rashkin, Yonatan Bisk, Ali Farhadi, Franziska Roesner, and Yejin Choi. Defending against neural fake news. arXiv preprint arXiv:1905.12616, 2019.
以下三种为评价指标数据集：
7. Alex Wang, Yada Pruksachatkun, Nikita Nangia, Amanpreet Singh, Julian Michael, Felix Hill, Omer Levy, and Samuel R. Bowman. SuperGLUE: A stickier benchmark for general-purpose language understanding systems. arXiv preprint arXiv:1905.00537, 2019b.
8. Alex Wang, Amapreet Singh, Julian Michael, Felix Hill, Omer Levy, and Samuel R. Bowman. GLUE: A multi-task benchmark and analysis platform for natural language understanding. arXiv preprint arXiv:1804.07461, 2018.
9.  Alexis Conneau and Douwe Kiela. SentEval: An evaluation toolkit for universal sentence representations. arXiv preprint arXiv:1803.05449, 2018.
微调方法：
10. Jeremy Howard and Sebastian Ruder. Universal language model fine-tuning for text classification. arXiv preprint arXiv:1801.06146, 2018.
11. Neil Houlsby, Andrei Giurgiu, Stanislaw Jastrzebski, Bruna Morrone, Quentin De Laroussilhe, Andrea Gesmundo, Mona Attariyan, and Sylvain Gelly. Parameter-efficient transfer learning for NLP. arXiv preprint arXiv:1902.00751, 2019.
12. Matthew Peters, Sebastian Ruder, and Noah A. Smith. To tune or not to tune? adapting pretrained representations to diverse tasks. arXiv preprint arXiv:1903.05987, 2019.

这种协同作用导致了最近大量的工作，为NLP开发转移学习方法，产生了广泛的培训前目标景观[Howard和Ruder, 2018;Devlin et al.， 2018;杨等人，2019年;Dong等人，2019年]，未标记数据集[Yang等人，2019年;刘等，2019c;Zellers等人，2019年]，benchmark [Wang等人，2019b, 2018年;Conneau和Kiela, 2018]，微调方法[Howard和Ruder, 2018;Houlsby等人，2019年;彼得斯等人，2019年]，以及更多。在这个新兴领域中，快速的发展速度和技术的多样性使得比较不同的算法、梳理新贡献的效果和理解现有的转移学习方法的空间变得困难。出于对更严格理解的需要，我们提出了一个统一的方法来转移学习，使我们能够系统地研究不同的方法，并推动该领域目前的限制。

The basic idea underlying our work is to treat every NLP problem as a “text-to-text” problem, i.e. taking text as input and producing new text as output. Similar approaches were used as part of the Natural Language Decathlon [McCann et al., 2018] and to test the zero-shot learning capabilities of language models [Radford et al., 2019]. Crucially, our text-to-text framework allows us to directly apply the same model, objective, training procedure, and decoding process to every task we consider. We leverage this flexibility by evaluating performance on a wide variety of English-based NLP problems, including question answering, document summarization, and sentiment classification, to name a few. With this unified approach, we can compare the effectiveness of different transfer learning objectives, unlabeled datasets, and other factors, while exploring the limits of transfer learning for NLP by scaling up models and datasets beyond what has previously been considered. By combining our insights and scale, we obtain state-of-the-art results in many of the tasks we consider.

1. Bryan McCann, Nitish Shirish Keskar, Caiming Xiong, and Richard Socher. The natural language decathlon: Multitask learning as question answering. arXiv preprint arXiv:1806.08730, 2018.

我们工作的基本思想是把每一个NLP问题当作一个“文本到文本”的问题，即以文本为输入，以生成新文本为输出。类似的方法被用作自然语言十项全能(Natural Language Decathlon, McCann et al.， 2018)的一部分，并被用来测试语言模型的零射击学习能力(zero-shot learning capability, Radford et al.， 2019)。至关重要的是，我们的文本到文本的框架允许我们直接将相同的模型、目标、训练过程和解码过程应用于我们考虑的每一项任务。我们利用这种灵活性来评估各种基于英语的NLP问题的性能，包括问题回答、文档摘要和情绪分类等。通过这种统一的方法，我们可以比较不同的迁移学习目标、未标记的数据集和其他因素的有效性，同时通过扩展模型和数据集来探索NLP迁移学习的极限。通过结合我们的洞察力和规模，我们在许多我们考虑的任务中获得了最先进的结果。

We emphasize that our main goal is not to propose new methods but instead to provide a comprehensive perspective on where the field stands. As such, the bulk of our work comprises of a survey, exploration, and empirical comparison of existing techniques. Our approach of casting every problem as a text-to-text task constitutes an additional major contribution. This unifying framework differs from current practice and boasts both simplicity and strong performance. Finally, we also push the field forward by training larger models than have been previously considered (up to 11 billion parameters). In order to perform experiments at this scale, we introduce the “Colossal Clean Crawled Corpus” (C4), a dataset consisting of hundreds of gigabytes of clean English text scraped from the web. Recognizing that the main utility of transfer learning is the possibility of leveraging pre-trained models in data-scarce settings, we also contribute our code, datasets, and pre-trained models.

我们强调，我们的主要目标不是提出新的方法，而是就该领域的立场提供一个全面的观点。因此，我们的大部分工作包括对现有技术的调查、探索和经验比较。我们把每一个问题都当作一个文本到文本的任务的方法构成了另一个重要贡献。这个统一的框架不同于当前的实践，它具有简单性和强大的性能。最后，我们还通过训练比以前考虑的更大的模型(多达110亿个参数)来推动该领域的发展。为了在这个规模上进行实验，我们引入了“巨大的干净抓取语料库”(C4)，这是一个由从网上抓取的几百gb的干净英文文本组成的数据集。认识到转移学习的主要用途是在数据缺乏的情况下利用预训练模型的可能性，我们也提供了我们的代码、数据集和预训练模型。

The remainder of the paper is structured as follows: In the following section, we discuss our base model and its implementation, our procedure for formulating every problem as a text-to-text task, and the suite of tasks we consider. In Section 3, we present a large set of experiments that explore the field of transfer learning for NLP. At the end of the section (Section 3.7), we combine insights from our systematic study to obtain state-of-the-art results on a wide variety of benchmarks. Finally, we provide a summary of our results and wrap up with a look towards the future in Section 4.

本文的其余部分的结构如下:在下一节中，我们将讨论基本模型及其实现，将每个问题形成为文本到文本任务的过程，以及我们所考虑的任务集。在第3节中，我们提出了一组大型的实验来探索NLP的转移学习领域。在本节的最后(第3.7节)，我们结合我们的系统研究的见解，在各种基准上获得最新的结果。最后，我们对结果进行总结，并在第4节中展望未来。

## 2 Setup

Before presenting the results from our large-scale empirical study, we review the necessary background topics required to understand our results, including the Transformer model architecture and the downstream tasks we evaluate on. We also introduce our approach for treating every problem as a text-to-text task and describe our “Colossal Clean Crawled Corpus” (C4), the Common Crawl-based dataset we created as a source of unlabeled text data. We refer to our model and framework as the “Text-to-Text Transfer Transformer” (T5).

在展示我们大规模实证研究的结果之前，我们回顾了理解我们的结果所需的必要的背景主题，包括Transformer模型架构和我们评估的下游任务。我们还介绍了将每个问题作为文本到文本任务处理的方法，并描述了我们的“巨大的、干净的爬行语料库”(C4)，这是我们创建的通用的基于爬虫的数据集，作为未标记文本数据的来源。我们将我们的模型和框架称为“文本到文本传输转换器”(T5)。

### 2.1 Model

Early results on transfer learning for NLP leveraged recurrent neural networks [Peters et al., 2018; Howard and Ruder, 2018], but it has recently become more common to use models based on the “Transformer” architecture [Vaswani et al., 2017]. The Transformer was initially shown to be effective for machine translation, but it has subsequently been used in a wide variety of NLP settings [Radford et al., 2018; Devlin et al., 2018; McCann et al., 2018; Yu et al., 2018]. Due to its improved performance and increasing ubiquity, all of the models we study are based on the Transformer architecture. Apart from the details mentioned below and the variants we explore in Section 3.2, we do not deviate significantly from this architecture as originally proposed. Instead of providing a comprehensive definition of this model, we refer the interested reader to the original paper [Vaswani et al., 2017] or follow-up tutorials for a more detailed introduction.

1. Matthew E. Peters, Mark Neumann, Mohit Iyyer, Matt Gardner, Christopher Clark, Kenton Lee, and Luke Zettlemoyer. Deep contextualized word representations. arXiv preprint arXiv:1802.05365,2018.
2. Jeremy Howard and Sebastian Ruder. Universal language model fine-tuning for text classification. arXiv preprint arXiv:1801.06146, 2018.

NLP杠杆递归神经网络转移学习的早期结果[Peters et al.， 2018;但是最近使用基于“Transformer”架构的模型变得更加普遍[Vaswani et al.， 2017]。该变压器最初被证明是有效的机器翻译，但它后来被用于各种NLP设置[Radford et al.， 2018;Devlin et al.， 2018;麦肯等，2018;Yu等人，2018]。由于其改进的性能和增加的普遍性，我们研究的所有模型都基于Transformer架构。除了下面提到的细节和我们在3.2节中探讨的变体之外，我们并没有明显偏离最初提出的体系结构。我们没有提供该模型的全面定义，而是建议感兴趣的读者参考原始论文[Vaswani et al.， 2017]或后续教程以获得更详细的介绍。

The primary building block of the Transformer is self-attention [Cheng et al., 2016]. Self-attention is a variant of attention [Graves, 2013; Bahdanau et al., 2015] that processes a sequence by replacing each element by a weighted average of the rest of the sequence. The original Transformer consisted of an encoder-decoder architecture and was intended for sequence-to-sequence [Sutskever et al., 2014; Kalchbrenner et al., 2014] tasks. It has recently also become common to use models consisting of a single Transformer layer stack, with varying forms of self-attention used to produce architectures appropriate for language modeling [Radford et al., 2018; Al-Rfou et al., 2019] or classification and span prediction tasks [(BERT)Devlin et al., 2018; (XLNet)Yang et al., 2019]. We empirically explore these architectural variants in Section 3.2.

1. Jianpeng Cheng, Li Dong, and Mirella Lapata. Long short-term memory-networks for machine reading. arXiv preprint arXiv:1601.06733, 2016.
2. Alex Graves. Generating sequences with recurrent neural networks. arXiv preprint arXiv:1308.0850,2013.
3. Dzmitry Bahdanau, Kyunghyun Cho, and Yoshua Bengio. Neural machine translation by jointly learning to align and translate. In Third International Conference on Learning Representations, 2015.
4. Ilya Sutskever, Oriol Vinyals, and Quoc V. Le. Sequence to sequence learning with neural networks. In Advances in neural information processing systems, 2014.
5. Nal Kalchbrenner, Edward Grefenstette, and Phil Blunsom. A convolutional neural network for modelling sentences. In Proceedings of the 52nd Annual Meeting of the Association for Computational Linguistics, 2014.
6. Alec Radford, Karthik Narasimhan, Tim Salimans, and Ilya Sutskever. Improving language understanding by generative pre-training, 2018.
7. Rami Al-Rfou, Dokook Choe, Noah Constant, Mandy Guo, and Llion Jones. Character-level language modeling with deeper self-attention. In Proceedings of the AAAI Conference on Artificial Intelligence, 2019.

变压器的主要构件是self-attention [Cheng et al.， 2016]。自我关注是注意力的一种变体[Graves, 2013;它通过用序列其余部分的加权平均值替换每个元素来处理序列。最初的Transformer由一个编码器-解码器架构组成，用于从序列到序列[Sutskever et al.， 2014;Kalchbrenner等人，2014]任务。最近，使用由单个Transformer层堆栈组成的模型也变得很普遍，使用不同形式的自我关注来生成适合于语言建模的体系结构[Radford et al.， 2018;或分类和跨度预测任务[Devlin et al.， 2018;Yang等人，2019年]。我们在3.2节中对这些架构变体进行了实证研究。

Overall, our encoder-decoder Transformer implementation closely follows its originally-proposed form [Vaswani et al., 2017]. First, an input sequence of tokens is mapped to a sequence of embeddings, which is then passed into the encoder. The encoder consists of a stack of “blocks”, each of which comprises two subcomponents: a self-attention layer followed by a small feed-forward network. Layer normalization [Ba et al., 2016] is applied to the input of each subcomponent and a residual skip connection [He et al., 2016] adds each subcomponent’s input to its output. Dropout [Srivastava et al., 2014] is applied within the feed-forward network, on the skip connection, on the attention weights, and at the input and output of the entire stack. The decoder is similar in structure to the encoder except that it includes a standard attention mechanism after each self-attention layer that attends to the output of the encoder. The self-attention mechanism in the decoder also uses a form of autoregressive or causal self-attention, which only allows the model to attend to past outputs. The output of the final decoder block is fed into a dense layer with a softmax output, whose weights are shared with the input embedding matrix. All attention mechanisms in the Transformer are split up into independent “heads” whose outputs are concatenated before being further processed.

1. Jimmy Lei Ba, Jamie Ryan Kiros, and Geoffrey E. Hinton. Layer normalization. arXiv preprint arXiv:1607.06450, 2016.
2. Kaiming He, Xiangyu Zhang, Shaoqing Ren, and Jian Sun. Deep residual learning for image recognition. In Proceedings of the IEEE conference on computer vision and pattern recognition,2016.
3. Nitish Srivastava, Geoffrey Hinton, Alex Krizhevsky, Ilya Sutskever, and Ruslan Salakhutdinov. Dropout: a simple way to prevent neural networks from overfitting. The Journal of Machine Learning Research, 2014.

总的来说，我们的编码器-解码器转换器的实现非常接近于其最初提出的形式[Vaswani et al.， 2017]。首先，将标记的输入序列映射到嵌入序列，然后将其传递到编码器。编码器由一组“块”组成，每个“块”由两个子组件组成:一个自我注意层和一个小的前馈网络。层归一化[Ba et al.， 2016]应用于每个子组件的输入，剩余的跳跃连接[He et al.， 2016]将每个子组件的输入添加到其输出中。Dropout [Srivastava et al.， 2014]应用于前馈网络、跳跃连接、注意权重，以及整个堆栈的输入和输出。解码器在结构上与编码器相似，除了它在每个关注编码器输出的自我关注层之后包括一个标准的注意机制。解码器中的自我注意机制也使用一种形式的自回归或因果自我注意，这只允许模型注意过去的输出。最后的解码器块的输出被送入一个具有softmax输出的密集层，其权值与输入嵌入矩阵共享。转换器中的所有注意机制被分割成独立的“头”，这些“头”的输出在进一步处理之前被连接起来。

Since self-attention is order-independent (i.e. it is an operation on sets), it is common to provide an explicit position signal to the Transformer. While the original Transformer used a sinusoidal position signal or learned position embeddings, it has recently become more common to use relative position embeddings [Shaw et al., 2018; Huang et al., 2018a]. Instead of using a fixed embedding for each position, relative position embeddings produce a different learned embedding according to the offset between the “key” and “query” being compared in the self-attention mechanism. We use a simplified form of position embeddings where each “embedding” is simply a scalar that is added to the corresponding logit used for computing the attention weights. For efficiency, we also share the position embedding parameters across all layers in our model, though within a given layer each attention head uses a different learned position embedding. Typically, a fixed number of embeddings are learned, each corresponding to a range of possible key-query offsets. In this work, we use 32 embeddings for all of our models with ranges that increase in size logarithmically up to an offset of 128 beyond which we assign all relative positions to the same embedding. Note that a given layer is insensitive to relative position beyond 128 tokens, but subsequent layers can build a sensitivity to larger offsets by combining local information from previous layers.

1. Peter Shaw, Jakob Uszkoreit, and Ashish Vaswani. Self-attention with relative position representations. arXiv preprint arXiv:1803.02155, 2018.
2. Cheng-Zhi Anna Huang, Ashish Vaswani, Jakob Uszkoreit, Ian Simon, Curtis Hawthorne, Noam Shazeer, Andrew M. Dai, Matthew D. Hoffman, Monica Dinculescu, and Douglas Eck. Music transformer: Generating music with long-term structure. In Seventh International Conference on Learning Representations, 2018a.

由于自我注意是与顺序无关的(即它是对集的操作)，所以通常向变压器提供显式的位置信号。虽然最初的变压器使用正弦位置信号或学习位置嵌入，但最近使用相对位置嵌入变得更加普遍[Shaw等，2018;黄等，2018a]。相对位置嵌入不是对每个位置使用固定的嵌入，而是根据自我注意机制中正在比较的“键”和“查询”之间的偏移量产生不同的学习嵌入。我们使用一种简化的位置嵌入形式，其中每个“嵌入”只是一个标量，它被添加到相应的logit中，用于计算注意权重。为了提高效率，我们还在我们的模型中共享所有层的位置嵌入参数，尽管在给定的层中，每个注意力头部使用不同的学习位置嵌入。通常，要学习固定数量的嵌入，每个嵌入对应可能的键查询偏移量范围。在这项工作中，我们为所有的模型使用32个嵌入，它们的范围以对数方式增长，最大偏移量为128，超过这个范围，我们将所有相对位置分配给相同的嵌入。请注意，给定的层对超过128个标记的相对位置不敏感，但是后续层可以通过组合来自前一层的本地信息来构建对较大偏移量的敏感性。

As part of our study, we experiment with the scalability of these models, i.e. how their performance changes as they are made to have more parameters or layers. Training large models can be non-trivial since they might not fit on a single machine and require a great deal of computation. As a result, we use a combination of model and data parallelism and train models on “slices” of Cloud TPU Pods.5 TPU pods are are multi-rack ML supercomputers that contain 1,024 TPU v3 chips connected via a high-speed 2D mesh interconnect with supporting CPU host machines. We leverage the Mesh TensorFlow library [Shazeer et al., 2018] for ease of implementation of both model parallelism and data parallelism [Krizhevsky, 2014].

1. Noam Shazeer, Youlong Cheng, Niki Parmar, Dustin Tran, Ashish Vaswani, Penporn Koanantakool, Peter Hawkins, HyoukJoong Lee, Mingsheng Hong, Cliff Young, Ryan Sepassi, and Blake Hechtman. Mesh-tensorflow: Deep learning for supercomputers. In Advances in Neural Information Processing Systems, 2018.
2. Alex Krizhevsky. One weird trick for parallelizing convolutional neural networks. arXiv preprint arXiv:1404.5997, 2014.

作为我们研究的一部分，我们对这些模型的可伸缩性进行了实验，即当它们具有更多的参数或层时，它们的性能如何变化。训练大型模型可能不是件小事，因为它们可能不适用于一台机器，并且需要大量的计算。因此，我们将模型和数据并行性结合起来，并在云TPU泡头的“切片”上建立训练模型。5个TPU泡头是包含1024个TPU v3芯片的多机架超级计算机，它们通过高速2D网格与支持CPU主机互连。我们利用Mesh TensorFlow库[Shazeer et al.， 2018]来简化模型并行化和数据并行化的实现[Krizhevsky, 2014]。

### 2.2 The Colossal Clean Crawled Corpus

Much of the previous work on transfer learning for NLP makes use of large unlabeled datasets for unsupervised learning. In this paper, we are interested in measuring the effect of the quality, characteristics, and size of this unlabeled data. To generate datasets that satisfy our needs, we leverage Common Crawl as a source of text scraped from the web. Common Crawl has previously been used as a source of text data for NLP, for example to train an n-gram language model [Buck et al., 2014], for commonsense reasoning [Trinh and Le, 2018], for mining parallel texts for machine translation [Smith et al., 2013], as a pre-training dataset [Baevski et al., 2019; Zellers et al., 2019; Liu et al., 2019c], and even simply as a giant text corpus for testing optimizers [Anil et al., 2019].

以前的许多关于NLP转移学习的工作都使用了大量的无标记数据集来进行无监督学习。在本文中，我们感兴趣的是测量这些未标记数据的质量、特性和大小的影响。为了生成满足我们需要的数据集，我们利用普通的抓取作为从web上抓取文本的来源。常见的爬行曾被用作NLP文本数据的来源,例如训练语法语言模型(巴克et al ., 2014),为常识推理(陈和勒,2018),对矿业平行文本为机器翻译(史密斯et al ., 2013),作为训练数据集(Baevski et al ., 2019;泽勒斯等人，2019年;Liu等人，2019c]，甚至简单地作为测试优化器的大型文本语料库[Anil等人，2019]。

Common Crawl is a publicly-available web archive that provides “web extracted text” by removing markup and other non-text content from the scraped HTML files. This process produces around 20TB of scraped text data each month. Unfortunately, the majority of the resulting text is not natural language. Instead, it largely comprises gibberish or boiler-plate text like menus, error messages, or duplicate text. Furthermore, a good deal of the scraped text contains content that is unlikely to be helpful for any of the tasks we consider (offensive language, placeholder text, source code, etc.). To address these issues, we used the following heuristics for cleaning up Common Crawl’s web extracted text:

- We only retained lines that ended in a terminal punctuation mark (i.e. a period, exclamation mark, question mark, or end quotation mark).
- We removed any page that contained any word on the “List of Dirty, Naughty, Obscene or Otherwise Bad Words”.
- Many of the scraped pages contained warnings stating that Javascript should be enabled so we removed any line with the word Javascript.
- Some pages had placeholder “lorem ipsum” text; we removed any page where the phrase “lorem ipsum” appeared.
- Some pages inadvertently contained code. Since the curly bracket “{” appears in many programming languages (such as Javascript, widely used on the web) but not in natural text, we removed any pages that contained a curly bracket.
- To deduplicate the dataset, we discarded all but one of any three-sentence span occurring more than once in the dataset.



普通抓取是一个公开可用的web存档，它通过从被抓取的HTML文件中删除标记和其他非文本内容来提供“web提取文本”。这个过程每个月产生大约20TB的文本数据。不幸的是，结果文本的大部分不是自然语言。相反，它主要由乱码或样板文本(如菜单、错误消息或重复文本)组成。此外，很多被删除的文本包含的内容不太可能对我们考虑的任何任务有帮助(攻击性语言、占位符文本、源代码等)。为了解决这些问题，我们使用以下启发式清理普通爬网提取的文本:

- 我们只保留以标点符号结尾的行(即句号、感叹号、问号或引号结尾)。
- 我们删除任何载有“肮脏、淫亵、淫亵或其他不雅字眼”的网页。
- 许多被抓取的页面都包含了警告，声明Javascript应该被启用，所以我们删除了所有带有Javascript这个词的行。
- 一些页面有占位符“lorem ipsum”文本;我们删除了任何出现短语“lorem ipsum”的页面。
- 有些页面无意中包含了代码。由于花括号“{”出现在许多编程语言中(如Javascript，在web上广泛使用)，但不出现在自然文本中，所以我们删除了任何包含花括号的页面。
- 为了删除数据集，我们丢弃了数据集中不止一次出现的任何三句话中的一个。

Additionally, since most of our downstream tasks are focused on English-language text, we used langdetect7 to filter out any pages that were not classified as English with a probability of at least 0.99.

此外，由于我们的大多数下游任务都集中在英语文本上，所以我们使用langdetect7过滤掉了不属于英语的页面，其概率至少为0.99。

To assemble our base dataset, we downloaded the web extracted text from April 2019 and applied the aforementioned filtering. This produces a collection of text that is not only orders of magnitude larger than most datasets used for pre-training (about 750 GB) but also comprises reasonably clean and natural English text. We dub this dataset the “Colossal Clean Crawled Corpus” (or C4 for short) and release it as part of TensorFlow Datasets.8 We consider the impact of using various alternative versions of this dataset in Section 3.4.

为了组装我们的基本数据集，我们下载了从2019年4月开始提取的web文本，并应用了前面提到的过滤。这将生成一个文本集合，该集合不仅比用于预培训的大多数数据集(约750 GB)大几个数量级，而且还包含相当干净和自然的英语文本。我们将该数据集命名为“巨大的抓取数据集”(简称C4)，并将其作为TensorFlow数据集的一部分发布。

### 2.3 Downstream tasks

Our goal in this paper is to measure general language learning abilities. As such, we study downstream performance on a diverse set of benchmarks, including machine translation, question answering, abstractive summarization, and text classification. Specifically, we measure performance on the GLUE and SuperGLUE text classification meta-benchmarks; CNN/Daily Mail abstractive summarization; SQuAD question answering; and WMT English to German, French, and Romanian translation. All data was sourced from TensorFlow Datasets.

我们的目标是衡量一般的语言学习能力。因此，我们研究了不同基准上的下游性能，包括机器翻译、问题回答、抽象摘要和文本分类。具体来说，我们测量了GLUE和SuperGLUE文本分类元基准的性能;CNN/每日邮报摘要;阵容问题回答;以及从英语到德语、法语和罗马尼亚语的翻译。所有数据均来自TensorFlow数据集。

GLUE [Wang et al., 2018] and SuperGLUE [Wang et al., 2019b] each comprise a collection of text classification tasks meant to test general language understanding abilities:

- Sentence acceptability judgment (CoLA [Warstadt et al., 2018])
- Sentiment analysis (SST-2 [Socher et al., 2013])
- Paraphrasing/sentence similarity (MRPC [Dolan and Brockett, 2005], STS-B [Cer et al., 2017], QQP [Iyer et al., 2017])
- Natural language inference (MNLI [Williams et al., 2017], QNLI [Rajpurkar et al., 2016], RTE [Dagan et al., 2005], CB [De Marneff et al., 2019])
- Coreference resolution (WNLI and WSC [Levesque et al., 2012])
- Sentence completion (COPA [Roemmele et al., 2011])
- Word sense disambiguation (WIC [Pilehvar and Camacho-Collados, 2018])
- Question answering (MultiRC [Khashabi et al., 2018], ReCoRD [Zhang et al., 2018], BoolQ [Clark et al., 2019])

GLUE [Wang et al.， 2018]和SuperGLUE [Wang et al.， 2019b]分别包含一系列文本分类任务，用于测试一般的语言理解能力:

- 句子可接受性判断(CoLA [Warstadt et al.， 2018])
- 情绪分析(SST-2 [Socher et al.， 2013])
- 释义/句子相似性(MRPC [Dolan and Brockett, 2005]， STS-B [Cer et al.， 2017]， QQP [Iyer et al.， 2017])
- 自然语言推理(MNLI [Williams et al.， 2017]， QNLI [Rajpurkar et al.， 2016]， RTE [Dagan et al.， 2005]， CB [De Marneff et al.， 2019])
- Coreference resolution (WNLI和WSC [Levesque et al.， 2012])
- 句子完成(COPA [Roemmele et al.， 2011])
- 词义消歧(WIC [Pilehvar和camachoo - collados, 2018])
- 答疑(MultiRC [Khashabi et al.， 2018]， ReCoRD [Zhang et al.， 2018]， q [Clark et al.， 2019])

We use the datasets as distributed by the GLUE and SuperGLUE benchmarks. For simplicity, when fine-tuning we treat all of the tasks in the GLUE benchmark (and similarly for SuperGLUE) as a single task by concatenating all of the constituent datasets. As suggested by Kocijan et al. [2019] we also include the Definite Pronoun Resolution (DPR) dataset [Rahman and Ng, 2012] in the combined SuperGLUE task.

我们使用GLUE和SuperGLUE基准测试发布的数据集。为简单起见，在进行微调时，我们通过连接所有组成数据集，将GLUE基准测试中的所有任务(与超级GLUE类似)视为单个任务。根据Kocijan等人[2019]的建议，我们还将定代词分辨率(DPR)数据集[Rahman和Ng, 2012]包含在联合超级胶水任务中。

The CNN/Daily Mail [Hermann et al., 2015] dataset was introduced as a question-answering task but was adapted for text summarization by Nallapati et al. [2016]; we use the non-anonymized version from See et al. [2017] as an abstractive summarization task. SQuAD [Rajpurkar et al., 2016] is a common question-answering benchmark. In our experiments, the model is fed the question and its context and asked to generate the answer token-by-token. For WMT English to German, we use the same training data as [Vaswani et al., 2017] (i.e. News Commentary v13, Common Crawl, Europarl v7) and newstest2013 as a validation set [Bojar et al., 2014]. For English to French, we use the standard training data from 2015 and newstest2014 as a validation set [Bojar et al., 2015]. For English to Romanian, which is a standard lower-resource machine translation benchmark, we use the train and validation sets from WMT 2016 [Bojar et al., 2016]. Note that we only pre-train on English data, so in order to learn to translate a given model will need to learn to generate text in a new language.

CNN/Daily Mail [Hermann et al.， 2015]将数据集引入为问答任务，但Nallapati等[2016]将其用于文本摘要;我们使用See等人[2017]的非匿名版本作为一个抽象的摘要任务。SQuAD (Rajpurkar et al.， 2016)是一个常见的问答基准。在我们的实验中，将问题及其上下文输入到模型中，并要求模型逐令牌生成答案。对于WMT英语到德语，我们使用与[Vaswani et al.， 2017](即News Commentary v13、Common crawley、Europarl v7)和newstest2013相同的训练数据作为验证集[Bojar et al.， 2014]。对于英语到法语，我们使用2015年和newstest2014年的标准培训数据作为验证集[Bojar et al.， 2015]。对于英语到罗马尼亚语，这是一个标准的低资源机器翻译基准，我们使用2016年WMT的培训和验证集[Bojar et al.， 2016]。请注意，我们只对英语数据进行预培训，因此为了学习翻译给定的模型，需要学习用一种新语言生成文本。

### 2.4 Input and output format

In order to train a single model on the diverse set of tasks described above, we need a consistent input and output format across all tasks. As recently noted by [McCann et al., 2018; Radford et al., 2019], it is possible to formulate most NLP tasks in a “text-to-text” format – that is, a task where the model is fed some text for context or conditioning and is then asked to produce some output text. This framework provides a consistent training objective both for pre-training and fine-tuning. Specifically, the model is trained with a maximum likelihood objective (using “teacher forcing” [Williams and Zipser, 1989]) regardless of the task. To specify which task the model should perform, we add a task-specific (text) prefix to the original input sequence before feeding it to the model. As an example, to ask the model to translate the sentence “That is good.” from English to German, the model would be fed the sequence “translate English to German: That is good.” and would be trained to output “Das ist gut.” For text classification tasks, the model simply predicts a single word corresponding to the target label. For example, on the MNLI benchmark [Williams et al., 2017] the goal is to predict whether a premise implies (“entailment”), contradicts (“contradiction”), or neither (“neutral”) a hypothesis. With our preprocessing, the input sequence becomes “mnli premise: I hate pigeons. hypothesis: My feelings towards pigeons are filled with animosity.” with the corresponding target word “entailment”. Note that an issue arises if our model outputs text on a text classification task that does not correspond to any of the possible labels (for example if the model outputs “hamburger” when the only possible labels for a task were “entailment”, “neutral”, or “contradiction”). In this case, we always count the model’s output as wrong, though we never observed this behavior in any of our trained models. A diagram of our text-to-text framework with a few input/output examples is shown in Figure 1.

为了在上面描述的不同任务集上训练单个模型，我们需要在所有任务之间使用一致的输入和输出格式。[McCann等人，2018;Radford等人，2019年]，可以用“文本到文本”的格式来制定大多数NLP任务——也就是说，在一个任务中，模型被输入一些文本用于上下文或条件设置，然后被要求生成一些输出文本。该框架为培训前和微调提供了一致的培训目标。具体地说，不管任务是什么，模型都用最大可能目标(使用“教师强迫”[Williams和Zipser, 1989])进行训练。为了指定模型应该执行哪个任务，我们在原始输入序列中添加一个特定于任务的(文本)前缀，然后将其提供给模型。例如，让模型翻译句子“That is good”。“从英语到德语，模型将被输入序列”将英语翻译成德语:这很好。并被训练输出“这是直觉”。对于文本分类任务，该模型只是预测一个与目标标签对应的单词。例如，在MNLI基准中[Williams et al.， 2017]，目标是预测一个前提是否意味着(“隐含”)，是否与(“矛盾”)矛盾，或者既不意味着(“中性”)假设。通过我们的预处理，输入序列变成了“mnli前提:我讨厌鸽子”。假设:我对鸽子充满了敌意。与对应的目标词“entailment”。注意，如果我们的模型在一个文本分类任务上输出的文本与任何可能的标签都不对应，就会出现问题(例如，如果模型输出“hamburger”，而该任务惟一可能的标签是“entailment”、“neutral”或“矛盾性”)。在这种情况下，我们总是认为模型的输出是错误的，尽管我们从未在任何经过训练的模型中观察到这种行为。图1显示了带有一些输入/输出示例的文本到文本框架的关系图。

Following this approach allows us to straightforwardly use a text-to-text format for every task except STS-B, which is a regression task where the goal is to predict a similarity score between 1 and 5. We found that most of these scores were annotated in increments of 0.2, so we simply rounded any score to the nearest increment of 0.2 and converted the result to a literal string representation of the number (e.g. the floating-point value 2.57 would be mapped to the string “2.6”). At test time, if the model outputs a string corresponding to a number between 1 and 5, we convert it to a floating-point value; otherwise, we treat the model’s prediction as incorrect. This effectively recasts the STS-B regression problem as a 21-class classification problem.

按照这种方法，我们可以直接对每个任务使用文本到文本的格式，除了STS-B，这是一个回归任务，其目标是预测1到5之间的相似性得分。我们发现,大多数的这些分数0.2注释的增量,所以我们只是绕过任何分数到最近的增量0.2和转换结果的文字字符串表示的数量(如浮点值2.57将映射到字符串“2.6”)。在测试时，如果模型输出一个与1到5之间的数字对应的字符串，我们将它转换为浮点值;否则，我们认为模型的预测是不正确的。这有效地将STS-B回归问题重新定义为21类分类问题。

Separately, we also convert the Winograd tasks (WNLI from GLUE, WSC from SuperGLUE, and the DPR dataset we add to SuperGLUE) into a simpler format that is more amenable to the text-to-text framework. Examples from the Winograd tasks consist of a text passage containing an ambiguous pronoun that could refer to more than one of the noun phrases in the passage. For example, the passage might be “The city councilmen refused the demonstrators a permit because they feared violence.”, which contains the ambiguous pronoun “they” that could refer to “city councilmen” or “demonstrators”. We cast the WNLI, WSC, and DPR tasks as text-to-text problems by highlighting the ambiguous pronoun in the text passage and asking the model to predict the noun that it refers to. The example mentioned above would be transformed to the input “The city councilmen refused the demonstrators a permit because they feared violence.” and the model would be trained to predict the target text “The city councilmen”.

另外，我们还将Winograd任务(WNLI来自GLUE, WSC来自SuperGLUE，以及我们添加到SuperGLUE的DPR数据集)转换为更简单的格式，更适合文本到文本的框架。Winograd任务中的例子包括一篇文章，其中包含一个模糊的代词，它可以指代文章中的一个以上的名词短语。例如，这段话可能是“市议员拒绝向示威者发放许可，因为他们害怕暴力。”，其中含有一个模糊的代词“他们”，可以指“市议员”或“示威者”。我们将WNLI、WSC和DPR任务转换为文本到文本的问题，方法是突出文本段落中的模糊代词，并要求模型预测它所指代的名词。上面提到的例子将被转化为“市议员拒绝向游行者发放许可，因为他们害怕暴力。”模型将被训练来预测目标文本“城市议员”。

For WSC, examples contain the passage, the ambiguous pronoun, a candidate noun, and a True/False label reflecting whether the candidate matches the pronoun (ignoring any articles). We only train on examples with a “True” label since we do not know the correct noun targets for examples with a “False” label. For evaluation, we assign a “True” label if the words in the model’s output are a subset of the words in the candidate noun phrase (or vice versa) and assign a “False” label otherwise. This removes roughly half of the WSC training set, but the DPR dataset adds about 1,000 pronoun resolution examples. Examples from DPR are annotated with the correct referent noun, making it easy to use this dataset in the format listed above.

对于WSC来说，例子包含了段落、歧义代词、候选名词和真/假标签，反映了候选人是否匹配该代词(忽略任何冠词)。我们只训练带有“正确”标签的例子，因为我们不知道正确的名词目标是带有“错误”标签的例子。对于评估，如果模型输出的单词是候选名词短语中的单词的子集(反之亦然)，则分配“True”标签，否则分配“False”标签。这删除了大约一半的WSC训练集，但是DPR数据集增加了大约1000个代词解析示例。来自DPR的示例使用正确的指称名词进行了注释，使得使用上面列出的格式的数据集变得很容易。

The WNLI training and validation sets have a significant overlap with the WSC training set. To avoid leaking validation examples into our training data (a particular issue in the multi-task experiments of Section 3.5.2), we therefore never train on WNLI and never report results on the WNLI validation set. Omitting results on the WNLI validation set is standard practice [Devlin et al., 2018] due to the fact that it is “adversarial” with respect to the training set, i.e. validation examples are all slightly-perturbed versions of training examples with the opposite label. As such, we do not include WNLI in the average GLUE score whenever we report on the validation set (all sections except Section 3.7 where results are presented on the test sets). Converting examples from WNLI to the “referent noun prediction” variant described above is a little more involved; we describe this process in Appendix B.

WNLI训练和验证集重大WSC训练集重叠。为了避免泄漏验证的例子在我们训练数据(一个特定问题的多任务实验部分3.5.2),因此,我们从未火车WNLI和报告结果WNLI WNLI验证验证集。省略结果集是标准做法(Devlin et al ., 2018)因为它是“对抗”对训练集,例如，验证示例都是带有相反标签的稍微有点混乱的训练示例版本。因此，无论何时我们报告验证集时，我们都不将WNLI包括在平均胶合评分中(除了第3.7节中测试集上显示的结果之外的所有部分)。将例子从WNLI转换成上面描述的“指称名词预测”稍微复杂一些;我们在附录B中描述了这个过程。

We provide full examples of preprocessed inputs for every task we studied in Appendix D.

我们为附录D中研究的每个任务提供了预处理输入的完整示例。

## 3 Experiments

Recent advances in transfer learning for NLP have come from a wide variety of developments, such as new pre-training objectives, model architectures, unlabeled datasets, and more. In this section, we carry out an empirical survey of these techniques in hopes of teasing apart their contribution and significance. We then combine the insights gained to attain state-of-the-art in many of the tasks we consider. Since transfer learning for NLP is a rapidly growing area of research, it is not feasible for us to cover every possible technique or idea in our empirical study. For a broader literature review, we recommend a recent survey by Ruder et al. [2019].

NLP转移学习的最新进展来自于各种各样的发展，如新的培训前目标、模型架构、未标记的数据集等。在本节中，我们对这些技术进行了实证调查，希望梳理出它们的贡献和意义。然后，我们将获得的洞察力结合起来，在我们考虑的许多任务中获得最先进的技术。由于NLP的转移学习是一个快速发展的研究领域，我们不可能在实证研究中涵盖所有可能的技术或思想。对于更广泛的文献综述，我们推荐Ruder等人最近的一项调查[2019]。

We systematically study these contributions by taking a reasonable baseline (described in Section 3.1) and altering one aspect of the setup at a time. For example, in Section 3.3 we measure the performance of different unsupervised objectives while keeping the rest of our experimental pipeline fixed. This “coordinate descent” approach might miss second-order effects (for example, some particular unsupervised objective may work best on a model larger than our baseline setting), but performing a combinatorial exploration of all of the factors in our study would be prohibitively expensive. In future work, we expect it could be fruitful to more thoroughly consider combinations of the approaches we study.

我们通过采取合理的基线(在3.1节中描述)并一次改变设置的一个方面来系统地研究这些贡献。例如，在3.3节中，我们测量了不同的非监督目标的性能，同时保持我们实验管道的其余部分不变。这种“坐标下降”方法可能会忽略二阶效应(例如，某些特定的无监督目标可能在比我们的基线设置更大的模型上工作得最好)，但是在我们的研究中对所有因素进行组合探索的代价将会非常昂贵。在未来的工作中，我们希望能够更深入地考虑我们所研究的方法的组合。

Our goal is to compare a variety of different approaches on a diverse set of tasks while keeping as many factors fixed as possible. In order to satisfy this aim, in some cases we do not exactly replicate existing approaches. For example, “encoder-only” models like BERT [Devlin et al., 2018] are designed to produce a single prediction per input token or a single prediction for an entire input sequence. This makes them applicable for classification or span prediction tasks but not for generative tasks like translation or abstractive summarization. As such, none of the model architectures we consider are identical to BERT or consist of an encoder-only structure. Instead, we test approaches that are similar in spirit – for example, we consider an analogous objective to BERT’s “masked language modeling” objective in Section 3.3 and we consider a model architecture that behaves similarly to BERT on text classification tasks in Section 3.2.

我们的目标是在保持尽可能多的固定因素的同时，对不同任务集的各种不同方法进行比较。为了满足这一目标，在某些情况下，我们并不完全复制现有的方法。例如，像BERT [Devlin et al.， 2018]这样的“仅编码”模型被设计为为每个输入令牌生成单个预测，或者为整个输入序列生成单个预测。这使得它们适用于分类或跨度预测任务，但不适用于生成任务，如翻译或抽象摘要。因此，我们所考虑的模型架构中没有一个与BERT相同，也没有一个是由只针对编码器的结构组成的。相反，我们测试在精神上类似的方法——例如，我们考虑一个类似于第3.3节中BERT的“掩蔽语言建模”目标的目标，我们考虑一个与第3.2节中BERT的文本分类任务行为类似的模型架构。

After outlining our baseline experimental setup in the following subsection, we undertake an empirical comparison of model architectures (Section 3.2), unsupervised objectives (Section 3.3), pre-training datasets (Section 3.4), transfer approaches (Section 3.5), and scaling (Section 3.6). At the culmination of this section, we combine insights from our study with scale to obtain state-of-the-art results in many tasks we consider (Section 3.7).

在以下小节中概述了我们的基线实验设置之后，我们对模型架构(3.2节)、无监督目标(3.3节)、训练前数据集(3.4节)、转移方法(3.5节)和缩放(3.6节)进行了实证比较。在本节的最后，我们结合我们的研究与规模的见解，以获得最先进的结果，在许多任务，我们认为(第3.7节)。

### 3.1 Baseline

Our goal for our baseline is to reflect typical, modern practice. We pre-train a standard Transformer (described in Section 2.1) using a simple denoising objective and then separately fine-tune on each of our downstream tasks. We describe the details of this experimental setup in the following subsections.

我们的基线目标是反映典型的、现代的实践。我们使用一个简单的去噪目标预先训练一个标准转换器(在2.1节中描述)，然后分别对我们的每个下游任务进行微调。我们将在下面的小节中详细描述这个实验设置。

#### 3.1.1 Model

For our model, we use a standard encoder-decoder Transformer as proposed by Vaswani et al. [2017]. While many modern approaches to transfer learning for NLP use a Transformer architecture consisting of only a single “stack” (e.g. for language modeling [Radford et al., 2018; Dong et al., 2019] or classification and span prediction [Devlin et al., 2018; Yang et al., 2019]), we found that using a standard encoder-decoder structure achieved good results on both generative and classification tasks. We explore the performance of different model architectures in Section 3.2.

在我们的模型中，我们使用了Vaswani等人[2017]提出的标准编码器-解码器变压器。虽然许多用于NLP转移学习的现代方法使用了一个仅由单个“堆栈”组成的转换器体系结构(例如，用于语言建模[Radford et al.， 2018;或分类和跨度预测[Devlin et al.， 2018;Yang等人，2019])，我们发现使用标准的编码器-解码器结构在生成和分类任务上都取得了良好的结果。我们将在第3.2节中探讨不同模型架构的性能。

Our baseline model is designed so that the encoder and decoder are each similar in size and configuration to a “$BERT_{BASE}$” [Devlin et al., 2018] stack. Specifically, both the encoder and decoder consist of 12 blocks (each block comprising self-attention, optional encoder-decoder attention, and a feed-forward network). The feed-forward networks in each block consist of a dense layer with an output dimensionality of $d_{ff}$ = 3072 followed by a ReLU nonlinearity and another dense layer. The “key” and “value” matrices of all attention mechanisms have an inner dimensionality of dkv = 64 and all attention mechanisms have 12 heads. All other sub-layers and embeddings have a dimensionality of dmodel = 768. In total, this results in a model with about 220 million parameters. This is roughly twice the number of parameters of BERTBASE since our baseline model contains two layer stacks instead of one. For regularization, we use a dropout probability of 0.1 everywhere dropout is applied in the model.

我们的基线模型被设计成编码器和解码器在大小和配置上都类似于“BERTBASE”[Devlin et al.， 2018]堆栈。具体来说，编码器和解码器都由12个块组成(每个块包括自我注意、可选的编码器-解码器注意和前馈网络)。每个块中的前馈网络由一个输出维数为dff = 3072的稠密层、一个ReLU非线性和另一个稠密层组成。所有注意机制的“键”和“值”矩阵的内维数为dkv = 64，所有注意机制都有12个头。所有其他子层和嵌入的维数为dmodel = 768。总的来说，这就产生了一个包含大约2.2亿个参数的模型。这大约是BERTBASE参数数量的两倍，因为我们的基线模型包含两个层堆栈，而不是一个。对于正则化，我们使用0.1的dropout概率在模型中应用。

#### 3.1.2 Training

As described in Section 2.4, all tasks are formulated as text-to-text tasks. As a result, we always train using standard maximum likelihood, i.e. using teacher forcing [Williams and Zipser, 1989] and a cross-entropy loss. For optimization, we use AdaFactor [Shazeer and Stern, 2018]. At test time, we use greedy decoding (i.e. choosing the highest-probability logit at every timestep).

如第2.4节所述，所有任务都被表示为文本到文本的任务。因此，我们总是使用标准的最大似然训练，即使用教师强迫[Williams和Zipser, 1989]和交叉熵损失。为了优化，我们使用AdaFactor [Shazeer和Stern, 2018]。在测试时，我们使用贪心译码(即在每一步选择概率最高的logit)。

We pre-train each model for 219 = 524,288 steps on C4 before fine-tuning. We use a maximum sequence length of 512 and a batch size of 128 sequences. Whenever possible, we “pack” multiple sequences into each entry of the batch10 so that our batches contain roughly 216 = 65,536 tokens. In total, this batch size and number of steps corresponds to pre-training on 235  34B tokens. This is considerably less than BERT [Devlin et al., 2018], which used roughly 137B tokens, or RoBERTa [Liu et al., 2019c], which used roughly 2.2T tokens. Using only 235 tokens results in a reasonable computational budget while still providing a sufficient amount of pre-training for acceptable performance. We consider the effect of pre-training for more steps in Sections 3.6 and 3.7. Note that 235 tokens only covers a fraction of the entire C4 dataset, so we never repeat any data during pre-training.

在进行微调之前，我们对每个型号进行了219 = 524,288步的C4预训练。我们使用的最大序列长度为512，批量大小为128个序列。只要有可能，我们就将多个序列“打包”到batch10的每个条目中，这样我们的批次大约包含216 = 65,536个令牌。In total, 这 批 规模 和 数量 的 步骤 对应 训练 235  34 B tokens.这大大少于BERT [Devlin et al.， 2018]和RoBERTa [Liu et al.， 2019c]，前者使用了大约137B的代币，后者使用了大约2.2T的代币。仅使用235个令牌就可以得到合理的计算预算，同时仍然可以提供足够的预训练以获得可接受的性能。我们将在第3.6和3.7节中讨论更多步骤的预训练的效果。请注意，235个令牌只覆盖了整个C4数据集的一小部分，因此我们在训练前从不重复任何数据。


During pre-training, we use an “inverse square root” learning rate schedule: $\frac{1}{\sqrt{max(n, k)}}$ where n is the current training iteration and k is the number of warm-up steps (set to 104 in all of our experiments). This sets a constant learning rate of 0.01 for the first 104 steps, then exponentially decays the learning rate until pre-training is over. We also experimented with using a triangular learning rate [Howard and Ruder, 2018], which produced slightly better results but requires knowing the total number of training steps ahead of time. Since we will be varying the number of training steps in some of our experiments, we opt for the more generic inverse square root schedule.

在训练前，我们使用了一个“逆平方根”学习进度计划:$\frac{1}{\sqrt{max(n, k)}}$，其中n是当前的训练迭代，k是热身步骤的数量(在我们所有的实验中设置为104)。这设定了前104步的恒定学习速率为0.01，然后指数衰减学习速率，直到训练结束。我们还尝试使用三角形学习率[Howard和Ruder, 2018]，这产生了稍好一点的结果，但需要提前知道训练步骤的总数。由于我们将在一些实验中改变训练步骤的数量，所以我们选择更通用的平方根逆调度。

Our models are fine-tuned for 218 = 262,144 steps on all tasks. This value was chosen as a trade-off between the high-resource tasks (i.e. those with large datasets), which benefit from additional fine-tuning, and low-resource tasks (smaller datasets), which overfit quickly. During fine-tuning, we continue using batches with 128 length-512 sequences (i.e. 216 tokens per batch). We use a constant learning rate of 0.001 when fine-tuning. We save a checkpoint every 5,000 steps and report results on the model checkpoint corresponding to the highest validation performance. For models fine-tuned on multiple tasks, we choose the best checkpoint for each task independently. For all of the experiments except those in Section 3.7, we report results in the validation set to avoid performing model selection on the test set.

我们的模型对所有任务的218 = 262,144步进行了微调。选择这个值是为了权衡高资源任务(即拥有大数据集的任务)和低资源任务(小数据集)之间的关系，前者受益于额外的调优，而后者很快就会过度适应。在微调期间，我们继续使用具有128个长度-512个序列的批(即每个批216个令牌)。我们在微调时使用0.001的恒定学习率。我们每5,000步保存一个检查点，并报告与最高验证性能相对应的模型检查点的结果。对于针对多个任务进行微调的模型，我们分别为每个任务选择最佳检查点。对于除第3.7节之外的所有实验，我们在验证集中报告结果，以避免在测试集上执行模型选择。

#### 3.1.3 vocabulary

We use SentencePiece [Kudo and Richardson, 2018] to encode text as WordPiece tokens [Sennrich et al., 2015; Kudo, 2018]. For all experiments, we use a vocabulary of 32,000 wordpieces. Since we ultimately fine-tune our model on English to German, French, and Romanian translation, we also require that our vocabulary covers these non-English languages. To address this, we classified pages from the Common Crawl scrape used in C4 as German, French, and Romanian. Then, we trained our SentencePiece model on a mixture of 10 parts of English C4 data with 1 part each of data classified as German, French or Romanian. This vocabulary was shared across both the input and output of our model. Note that our vocabulary makes it so that our model can only process a predetermined, fixed set of languages.

1. Taku Kudo and John Richardson. SentencePiece: A simple and language independent subword tokenizer and detokenizer for neural text processing. arXiv preprint arXiv:1808.06226, 2018.
2. Rico Sennrich, Barry Haddow, and Alexandra Birch. Neural machine translation of rare words with subword units. arXiv preprint arXiv:1508.07909, 2015.
3. Taku Kudo. Subword regularization: Improving neural network translation models with multiple subword candidates. arXiv preprint arXiv:1804.10959, 2018.

我们使用SentencePiece [Kudo和Richardson, 2018]将文本编码为单词标记[Sennrich et al.， 2015;荣誉,2018]。在所有的实验中，我们使用了32000个单词。由于我们最终将英语的模型调整为德语、法语和罗马尼亚语的翻译，所以我们还要求词汇表涵盖这些非英语语言。为了解决这个问题，我们将C4中常见的爬行刮痕页面分为德语、法语和罗马尼亚语。然后，我们将10份英语C4数据与1份德语、法语或罗马尼亚语数据的混合物训练成句子模型。这个词汇表在我们的模型的输入和输出之间共享。请注意，我们的词汇表使得我们的模型只能处理预定的、固定的一组语言。

#### 3.1.4 Unsupervised objective

Leveraging unlabeled data to pre-train our model necessitates an objective that does not require labels but (loosely speaking) teaches the model generalizable knowledge that will be useful in downstream tasks. Early work on transfer learning for NLP used a language modeling objective [Peters et al., 2018; Radford et al., 2018; Howard and Ruder, 2018]. However, it has recently been shown that “denoising” objectives [Devlin et al., 2018; Yang et al., 2019; Taylor, 1953] produce better performance, and as a result they have quickly become standard. In a denoising objective, the model is trained to predict missing or otherwise corrupted tokens in the input. Inspired by BERT’s “masked language modeling” objective and the “word dropout” regularization technique [Bowman et al., 2015], we design an objective that randomly samples and then drops out 15% of tokens in the input sequence. All consecutive spans of dropped-out tokens are replaced by a single sentinel token. Each sentinel token is assigned a token ID that is unique to the sequence. The target then corresponds to all of the dropped-out spans of tokens, delimited by the same sentinel tokens used in the input sequence plus a final sentinel token to mark the end of the target sequence. An example of the transformation resulting from applying this objective is shown in Figure 2. We empirically compare this objective to many other variants in Section 3.3.

1. Matthew E. Peters, Mark Neumann, Mohit Iyyer, Matt Gardner, Christopher Clark, Kenton Lee, and Luke Zettlemoyer. Deep contextualized word representations. arXiv preprint arXiv:1802.05365,2018.
2. Alec Radford, Karthik Narasimhan, Tim Salimans, and Ilya Sutskever. Improving language understanding by generative pre-training, 2018.
3. Jeremy Howard and Sebastian Ruder. Universal language model fine-tuning for text classification. arXiv preprint arXiv:1801.06146, 2018.

利用未标记的数据对我们的模型进行预培训，这需要一个不需要标记的目标，但是(粗略地说)教授模型可推广的知识，这些知识将在后续任务中非常有用。早期关于NLP迁移学习的研究使用了语言建模目标[Peters et al.， 2018;Radford等人，2018;霍华德和鲁德，2018年]。然而，最近的研究表明，“去噪”的目标[Devlin et al.， 2018;杨等人，2019年;产生更好的性能，因此他们很快成为标准。在去噪目标中，对模型进行训练，以预测输入中丢失或损坏的标记。受BERT的“蒙面语言建模”目标和“单词丢失”正则化技术的启发[Bowman et al.， 2015]，我们设计了一个目标，随机抽取样本，然后在输入序列中丢失15%的标记。所有连续范围的丢失令牌将被单个标记令牌替换。每个标记令牌都被分配一个令牌ID，该ID对于序列是唯一的。然后，目标对应于所有标记的下拉范围，由输入序列中使用的相同标记标记加上最后的标记标记来分隔。图2显示了应用这个目标所产生的转换的一个例子。我们根据经验将这一目标与3.3节中的许多其他变量进行比较。

#### 3.1.5 Baseline performance

In this section, we present results using the baseline experimental procedure described above to get a sense of what kind of performance to expect on our suite of downstream tasks. Ideally, we would repeat every experiment in our study multiple times to get a confidence interval on our results. Unfortunately, this would be prohibitively expensive due to the large number of experiments we run. As a cheaper alternative, we train our baseline model 10 times from scratch (i.e. with different random initializations and dataset shuffling) and assume that the variance over these runs of the base model also applies to each experimental variant. We don’t expect most of the changes we make to have a dramatic effect on the inter-run variance, so this should provide a reasonable indication of the significance of different changes. Separately, we also measure the performance of training our model for 218 steps (the same number we use for fine-tuning) on all downstream tasks without pre-training. This gives us an idea of how much pre-training benefits our model in the baseline setting.

在本节中，我们将使用上面描述的基线实验过程来展示结果，以了解我们的下游任务套件的性能。理想情况下，我们会在我们的研究中多次重复每个实验，以获得结果的置信区间。不幸的是，由于我们进行了大量的实验，这将是非常昂贵的。作为一种更便宜的选择，我们从零开始训练我们的基线模型10次(即使用不同的随机初始化和数据集变换)，并假设这些基本模型运行的方差也适用于每个实验变量。我们并不期望我们所做的大多数更改会对运行间的差异产生显著的影响，因此这应该能够合理地指示不同更改的重要性。另外，我们还测量了我们的模型在没有预先培训的情况下对所有下游任务进行218步(与我们用于微调的数量相同)训练的性能。这让我们知道在基线设置中，预训练对我们的模型有多大的好处。

When reporting results in the main text, we only report a subset of the scores across all the benchmarks to conserve space and ease interpretation. For GLUE and SuperGLUE, we report the average score across all subtasks (as stipulated by the official benchmarks) under the headings “GLUE” and “SGLUE”. For all translation tasks, we report the BLEU score [Papineni et al., 2002] as provided by SacreBLEU v1.3.0 [Post, 2018] with “exp” smoothing and “intl” tokenization. We refer to scores for WMT English to German, English to French, and English to Romanian as EnDe, EnFr, and EnRo, respectively. For CNN/Daily Mail, we find the performance of models on the ROUGE-1-F, ROUGE-2-F, and ROUGE-L-F metrics [Lin, 2004] to be highly correlated so we report the ROUGE-2-F score alone under the heading “CNNDM”. Similarly, for SQuAD we find the performance of the “exact match” and “F1” scores to be highly correlated so we report the “exact match” score alone. We provide every score achieved on every task for all experiments in Table 15, Appendix E.

当在主要文本中报告结果时，我们只报告所有基准测试分数的一个子集，以节省空间并简化解释。对于GLUE和SuperGLUE，我们在“GLUE”和“SGLUE”标题下报告了所有子任务的平均得分(根据官方基准规定)。对于所有翻译任务，我们报告了BLEU评分[Papineni et al.， 2002]，由SacreBLEU v1.3.0 [Post, 2018]提供的“exp”平滑和“intl”标记化。我们将WMT英语的分数分别引用为德语、法语和罗马尼亚语，分别为EnDe、EnFr和EnRo。对于CNN/Daily Mail，我们发现ROUGE-1-F、ROUGE-2-F和ROUGE-L-F指标模型的性能高度相关[Lin, 2004]，因此我们在“CNNDM”标题下单独报告ROUGE-2-F分数。同样，对于球队，我们发现“精确匹配”和“F1”分数的表现是高度相关的，所以我们只报告“精确匹配”分数。我们在表15附录E中提供了所有实验中每个任务的得分。

Our results tables are all formatted so that each row corresponds to a particular experimental configuration with columns giving the scores for each benchmark. We will include the mean performance of the baseline configuration in most tables. Wherever a baseline configuration appears, we will mark it with a F (as in the first row of Table 1). We also will boldface any score that is within two standard deviations of the maximum (best) in a given experiment.

Our baseline results are shown in Table 1. Overall, our results are comparable to existing models of similar size. For example, BERTBASE achieved an exact match score of 80.8 on SQuAD and an accuracy of 84.4 on MNLI-matched, whereas we achieve 80.88 and 84.24, respectively (see Table 15). Note that we cannot directly compare our baseline to BERTBASE because ours is an encoder-decoder model and was pre-trained for roughly 1⁄4 as many steps. Unsurprisingly, we find that pre-training provides significant gains across almost all benchmarks. The only exception is WMT English to French, which is a large enough dataset that gains from pre-training tend to be marginal. We include this task in our experiments to test the behavior of transfer learning in the high-resource regime.

As for inter-run variance, we find that for most tasks the standard deviation across runs is smaller than 1% of the task’s baseline score. Exceptions to this rule include CoLA, CB, and COPA, which are all low-resource tasks from the GLUE and SuperGLUE benchmarks. For example, on CB our baseline model had an average F1 score of 91.22 with a standard deviation of 3.237 (see Table 15), which may be partly due to the fact that CB’s validation set contains only 56 examples. Note that the GLUE and SuperGLUE scores are computed as the average of scores across the tasks comprising each benchmark. As a result, we caution that the high inter-run variance of CoLA, CB, and COPA can make it harder to compare models using the GLUE and SuperGLUE scores alone.

### 3.2 Architectures

While the Transformer was originally introduced with an encoder-decoder architecture, much modern work on transfer learning for NLP uses alternative architectures. In this section, we review and compare these architectural variants.

### 3.3 Unsupervised objectives

### 3.4 Pre-training dataset

### 3.5 Training strategy

### 3.6 scaling

### 3.7 Putting it all together

## 4. Reflection

### 4.1 Takeaways

### 4.2 Outlook
