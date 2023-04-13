# LLaMA: Open and Efficient Foundation Language Models

Hugo Touvron, Thibaut Lavril, Gautier Izacard, Xavier Martinet
Marie-Anne Lachaux, Timothee Lacroix, Baptiste Rozière, Naman Goyal
Eric Hambro, Faisal Azhar, Aurelien Rodriguez, Armand Joulin
Edouard Grave, Guillaume Lample

## Abstract

We introduce LLaMA, a collection of foundation language models ranging from 7B to 65B parameters. We train our models on trillions of tokens, and show that it is possible to train state-of-the-art models using publicly available datasets exclusively, without resorting to proprietary and inaccessible datasets. In particular, LLaMA-13B outperforms GPT-3 (175B) on most benchmarks, and LLaMA- 65B is competitive with the best models, Chinchilla-70B and PaLM-540B. We release all our models to the research community.

我们推出了LLaMA，这是一组基础语言模型，参数范围从70亿到650亿。我们在数万亿的tokens上训练我们的模型，并展示了只使用公共可用的数据集就可以训练出最先进的模型，无需求助于专有且难以获取的数据集。特别地，LLaMA-13B在大多数基准测试上胜过GPT-3（1750亿），而LLaMA-65B与最佳模型Chinchilla-70B和PaLM-540B相媲美。我们将所有模型发布给研究界。

## Introduction

Large Languages Models (LLMs) trained on massive corpora of texts have shown their ability to perform new tasks from textual instructions or from a few examples (Brown et al., 2020). These few-shot properties first appeared when scaling models to a sufficient size (Kaplan et al., 2020), resulting in a line of work that focuses on further scaling these models (Chowdhery et al., 2022; Rae et al., 2021). These efforts are based on the assumption that more parameters will lead to better performance. However, recent work from Hoffmann et al. (2022) shows that, for a given compute budget, the best performances are not achieved by the largest models, but by smaller models trained on more data.

- Tom B. Brown, Benjamin Mann, Nick Ryder, Melanie Subbiah, Jared Kaplan, Prafulla Dhariwal, Arvind Neelakantan, Pranav Shyam, Girish Sastry, Amanda Askell, Sandhini Agarwal, Ariel Herbert-Voss, Gretchen Krueger, Tom Henighan, Rewon Child, Aditya Ramesh, Daniel M. Ziegler, Jeffrey Wu, Clemens Winter, Christopher Hesse, Mark Chen, Eric Sigler, Mateusz Litwin, Scott Gray, Benjamin Chess, Jack Clark, Christopher Berner, Sam Mc- Candlish, Alec Radford, Ilya Sutskever, and Dario Amodei. 2020. Language models are few-shot learners.

- Jared Kaplan, Sam McCandlish, Tom Henighan, Tom B Brown, Benjamin Chess, Rewon Child, Scott Gray, Alec Radford, Jeffrey Wu, and Dario Amodei. Scaling laws for neural language models. arXiv preprint arXiv:2001.08361.
我们研究了语言模型在交叉熵损失上的性能的经验性缩放规律。损失随着模型大小、数据集大小和用于训练的计算量呈幂律缩放，其中一些趋势跨越了七个数量级以上。其他架构细节，如网络宽度或深度，在一个很宽的范围内只有最小的影响。一些简单的方程描述了过拟合与模型/数据集大小的依赖关系，以及训练速度与模型大小的依赖关系。这些关系使我们能够确定固定计算预算的最优分配。更大的模型显著地更节省样本，因此最优地计算效率训练涉及在相对适度的数据量上训练非常大的模型，并在收敛之前显著地停止。

- Aakanksha Chowdhery, Sharan Narang, Jacob Devlin, Maarten Bosma, Gaurav Mishra, Adam Roberts, Paul Barham, Hyung Won Chung, Charles Sutton, Sebastian Gehrmann, Parker Schuh, Kensen Shi, Sasha Tsvyashchenko, Joshua Maynez, Abhishek Rao, Parker Barnes, Yi Tay, Noam Shazeer, Vinodkumar Prabhakaran, Emily Reif, Nan Du, Ben Hutchinson, Reiner Pope, James Bradbury, Jacob Austin, Michael Isard, Guy Gur-Ari, Pengcheng Yin, Toju Duke, Anselm Levskaya, Sanjay Ghemawat, Sunipa Dev, Henryk Michalewski, Xavier Garcia, Vedant Misra, Kevin Robinson, Liam Fedus, Denny Zhou, Daphne Ippolito, David Luan, Hyeontaek Lim, Barret Zoph, Alexander Spiridonov, Ryan Sepassi, David Dohan, Shivani Agrawal, Mark Omernick, Andrew M. Dai, Thanumalayan Sankaranarayana Pillai, Marie Pellat, Aitor Lewkowycz, Erica Moreira, Rewon Child, Oleksandr Polozov, Katherine Lee, Zongwei Zhou, Xuezhi Wang, Brennan Saeta, Mark Diaz, Orhan Firat, Michele Catasta, Jason Wei, Kathy Meier-Hellstern, Douglas Eck, Jeff Dean, Slav Petrov, and Noah Fiedel. 2022. Palm: Scaling language modeling with pathways.

- PaLM是一个5400亿参数的大型语言模型，使用了密集的解码器-只有Transformer结构，能够在多种自然语言理解和生成任务上实现少样本学习¹²。
- PaLM使用了Pathways系统，一个新的机器学习系统，能够高效地在多个TPU Pod上进行分布式训练。PaLM是第一个利用Pathways系统在6144个TPU v4芯片上进行训练的模型。
- PaLM在数百个语言理解和生成基准测试上达到了最先进的少样本学习结果，其中很多任务上的性能显著优于之前的模型。PaLM还展示了随着模型规模增加而出现的新能力，比如多步推理、多语言任务和源代码生成。
- PaLM对训练数据的记忆程度、偏见和毒性进行了全面的分析，并讨论了大型语言模型相关的伦理考量和潜在的缓解策略。

- Jack W. Rae, Sebastian Borgeaud, Trevor Cai, Katie Millican, Jordan Hoffmann, Francis Song, John Aslanides, Sarah Henderson, Roman Ring, Susannah Young, Eliza Rutherford, Tom Hennigan, Jacob Menick, Albin Cassirer, Richard Powell, George van den Driessche, Lisa Anne Hendricks, Maribeth Rauh, Po-Sen Huang, Amelia Glaese, Johannes Welbl, Sumanth Dathathri, Saffron Huang, Jonathan Uesato, John Mellor, Irina Higgins, Antonia Creswell, Nat McAleese, Amy Wu, Erich Elsen, Siddhant Jayakumar, Elena Buchatskaya, David Budden, Esme Sutherland, Karen Simonyan, Michela Paganini, Laurent Sifre, Lena Martens, Xiang Lorraine Li, Adhiguna Kuncoro, Aida Nematzadeh, Elena Gribovskaya, Domenic Donato, Angeliki Lazaridou, Arthur Mensch, Jean-Baptiste Lespiau, Maria Tsimpoukelli, Nikolai Grigorev, Doug Fritz, Thibault Sottiaux, Mantas Pajarskas, Toby Pohlen, Zhitao Gong, Daniel Toyama, Cyprien de Masson d’Autume, Yujia Li, Tayfun Terzi, Vladimir Mikulik, Igor Babuschkin, Aidan Clark, Diego de Las Casas, Aurelia Guy, Chris Jones, James Bradbury, Matthew Johnson, Blake Hechtman, LauraWeidinger, Iason Gabriel,William Isaac, Ed Lockhart, Simon Osindero, Laura Rimell, Chris Dyer, Oriol Vinyals, Kareem Ayoub, Jeff Stanway, Lorrayne Bennett, Demis Hassabis, Koray Kavukcuoglu, and Geoffrey Irving. 2021. Scaling language models: Methods, analysis & insights from training gopher.

- 这篇论文分析了基于Transformer的语言模型在不同模型规模下的性能，从几千万参数到一个2800亿参数的模型，称为Gopher。
- 这些模型在152个不同的任务上进行了评估，在大多数任务上达到了最先进的性能¹²。模型规模的增加在一些领域（如阅读理解、事实核查和有毒语言的识别）带来了最大的收益，但逻辑和数学推理的收益较小。
- 这篇论文提供了对训练数据集和模型行为的全面分析，涵盖了模型规模与偏见和毒性的交叉点。最后，论文讨论了语言模型在人工智能安全方面的应用，以及减轻下游危害的潜在策略。

- Jordan Hoffmann, Sebastian Borgeaud, Arthur Mensch, Elena Buchatskaya, Trevor Cai, Eliza Rutherford, Diego de Las Casas, Lisa Anne Hendricks, Johannes Welbl, Aidan Clark, Tom Hennigan, Eric Noland, Katie Millican, George van den Driessche, Bogdan Damoc, Aurelia Guy, Simon Osindero, Karen Simonyan, Erich Elsen, Jack W. Rae, Oriol Vinyals, and Laurent Sifre. 2022. Training compute-optimal large language models.

- 这篇论文研究了在给定的计算预算下，训练一个Transformer语言模型的最优模型大小和训练数据量¹。论文发现，当前的大型语言模型都显著地训练不足，这是因为近期的研究重点在于增加模型大小，而保持训练数据量不变。
- 通过在5到5000亿个token上训练超过400个语言模型，从7000万参数到160亿参数，论文发现，对于计算最优的训练，模型大小和训练数据量应该等比例地增加：每当模型大小翻倍时，训练数据量也应该翻倍。
- 论文验证了这一假设，通过训练一个预测的计算最优的模型，叫做Chinchilla，它使用了与Gopher相同的计算预算，但是有700亿参数和4倍多的数据¹。Chinchilla在一系列的下游评估任务上均匀地和显著地超过了Gopher (280B)、GPT-3 (175B)、Jurassic-1 (178B)和Megatron-Turing NLG (530B)。这也意味着Chinchilla在微调和推理时使用了更少的计算，极大地方便了下游使用。作为一个亮点，Chinchilla在MMLU基准上达到了最先进的平均准确率67.5%，比Gopher高出7%以上。

大型语言模型（LLMs）在大规模文本语料库上的训练已经展示出了它们从文本指令或少量示例中执行新任务的能力（Brown等人，2020年）。当将模型扩展到足够大的规模时，这些少量样本特性首次出现（Kaplan等人，2020年），从而形成了一系列关注进一步扩展这些模型的工作（Chowdhery等人，2022年；Rae等人，2021年）。这些努力基于这样一个假设，即更多的参数将带来更好的性能。然而，Hoffmann等人（2022年）的最近研究表明，对于给定的计算预算，最佳性能并非由最大的模型实现，而是由在更多数据上训练的较小模型实现。

The objective of the scaling laws from Hoffmann et al. (2022) is to determine how to best scale the dataset and model sizes for a particular training compute budget. However, this objective disregards the inference budget, which becomes critical when serving a language model at scale. In this context, given a target level of performance, the preferred model is not the fastest to train but the fastest at inference, and although it may be cheaper to train a large model to reach a certain level of performance, a smaller one trained longer will ultimately be cheaper at inference. For instance, although Hoffmann et al. (2022) recommends training a 10B model on 200B tokens, we find that the performance of a 7B model continues to improve even after 1T tokens.

Hoffmann等人（2022）提出的扩展规律的目标是确定如何在特定的训练计算预算下最好地扩展数据集和模型大小。然而，这个目标忽略了推理预算，这在大规模部署语言模型时变得至关重要。在这种情境下，考虑到目标性能水平，首选的模型不是训练速度最快的模型，而是推理速度最快的模型，尽管训练一个大模型以达到一定的性能水平可能更便宜，但一个训练时间更长的小模型在推理过程中最终会更便宜。例如，尽管Hoffmann等人（2022）建议在200B个令牌上训练一个10B的模型，但我们发现7B模型的性能在1T个令牌之后仍然有所提升。

The focus of this work is to train a series of language models that achieve the best possible performance at various inference budgets, by training on more tokens than what is typically used. The resulting models, called LLaMA, ranges from 7B to 65B parameters with competitive performance compared to the best existing LLMs. For instance, LLaMA-13B outperforms GPT-3 on most benchmarks, despite being 10x smaller. We believe that this model will help democratize the access and study of LLMs, since it can be run on a single GPU. At the higher-end of the scale, our 65B-parameter model is also competitive with the best large language models such as Chinchilla or PaLM-540B.

本工作的重点是通过在比通常使用的更多令牌上进行训练，训练一系列在各种推理预算下实现最佳性能的语言模型。由此产生的模型被称为LLaMA，参数量从7B到65B不等，在与现有最佳LLMs相比时具有竞争性能。例如，尽管LLaMA-13B比GPT-3小10倍，但在大多数基准测试中的表现更优。我们认为这个模型将有助于实现对LLMs的普及和研究，因为它可以在单个GPU上运行。在较高端的规模上，我们的65B参数模型也与最佳大型语言模型（如Chinchilla或PaLM-540B）具有竞争力。

Unlike Chinchilla, PaLM, or GPT-3, we only use publicly available data, making our work compatible with open-sourcing, while most existing models rely on data which is either not publicly available or undocumented (e.g. “Books – 2TB” or “Social media conversations”). There exist some exceptions, notably OPT (Zhang et al., 2022), GPT-NeoX (Black et al., 2022), BLOOM (Scao et al., 2022) and GLM (Zeng et al., 2022), but none that are competitive with PaLM-62B or Chinchilla.

- Susan Zhang, Stephen Roller, Naman Goyal, Mikel Artetxe, Moya Chen, Shuohui Chen, Christopher Dewan, Mona Diab, Xian Li, Xi Victoria Lin, et al. 2022. Opt: Open pre-trained transformer language models. arXiv preprint arXiv:2205.01068.

- 这篇论文提出了开放预训练Transformer (OPT) 语言模型，一系列从125M到175B参数的解码器-只有Transformer模型，目标是完全和负责任地与感兴趣的研究者分享。
- 这些模型在大量的下游任务上进行了评估，表现出与GPT-3类似的性能。OPT-175B使用了与GPT-3相同的计算预算，但是使用了更高效的训练方法和最新的硬件，使得它的碳足迹只有GPT-3的1/7。
- 这篇论文也公开了模型创建的日志和代码，以及训练数据集的详细信息。论文还讨论了大型语言模型相关的伦理、社会和安全问题，并提出了一些减轻潜在危害的建议。

- Sid Black, Stella Biderman, Eric Hallahan, Quentin Anthony, Leo Gao, Laurence Golding, Horace He, Connor Leahy, Kyle McDonell, Jason Phang, et al. 2022. Gpt-neox-20b: An open-source autoregressive language model. arXiv preprint arXiv:2204.06745.

- 这篇论文介绍了GPT-NeoX-20B，一个200亿参数的自回归语言模型，它是在Pile数据集上使用GPT-NeoX库训练的，其权重将通过一个宽松的许可证免费和公开地提供给公众。
- GPT-NeoX-20B的架构有意地与GPT-3相似，几乎与GPT-J-6B相同。它的训练数据集包含了多种英语文本，反映了这个模型的通用性。
- 这篇论文描述了GPT-NeoX-20B的架构和训练，并评估了它在一系列语言理解、数学和知识相关任务上的性能。论文发现，GPT-NeoX-20B是一个强大的少样本推理器，在五样本评估时比同等大小的GPT-3和FairSeq模型有更大的性能提升。

- Teven Le Scao, Angela Fan, Christopher Akiki, Ellie Pavlick, Suzana Ili´c, Daniel Hesslow, Roman Castagné, Alexandra Sasha Luccioni, François Yvon, Matthias Gallé, et al. 2022. Bloom: A 176bparameter open-access multilingual language model. arXiv preprint arXiv:2211.05100.

- 这篇论文介绍了BLOOM，一个1760亿参数的开放访问多语言语言模型，它是由数百名研究者设计和构建的，旨在通过一个负责任的AI许可证与公众分享。
- BLOOM是一个解码器-只有Transformer语言模型，它是在ROOTS语料库上训练的，这是一个包含了46种自然语言和13种编程语言（共59种）的数据集。BLOOM在多种语言理解和生成任务上表现出了竞争力的性能，经过多任务提示微调后性能更强。
- 这篇论文还公开了模型和代码，以及训练数据集的详细信息。论文还估计了BLOOM的碳足迹，并讨论了大型语言模型相关的伦理、社会和安全问题，并提出了一些减轻潜在危害的建议。

- Aohan Zeng, Xiao Liu, Zhengxiao Du, Zihan Wang, Hanyu Lai, Ming Ding, Zhuoyi Yang, Yifan Xu, Wendi Zheng, Xiao Xia, Weng Lam Tam, Zixuan Ma, Yufei Xue, Jidong Zhai, Wenguang Chen, Peng Zhang, Yuxiao Dong, and Jie Tang. 2022. Glm- 130b: An open bilingual pre-trained model.

- 这篇论文介绍了GLM-130B，一个开放的双语（英语和汉语）预训练的语言模型，具有1300亿个参数，使用General Language Model (GLM)算法进行预训练。它的设计目标是支持在单个A100 (40G * 8)或V100 (32G * 8)服务器上使用1300亿参数进行推理任务。
- GLM-130B模型在一系列流行的英语基准测试上表现出了与GPT-3 175B类似的性能，而在OPT-175B和BLOOM-176B中没有观察到性能优势。它也始终显著优于最大的中文模型ERNIE TITAN 3.0 260B——在相关基准测试中。
- 这篇论文还利用了GLM-130B的一个独特的缩放性质，达到了INT4量化，几乎没有性能损失，使它成为第一个实现量化的1000亿规模模型。更重要的是，这个性质使它能够在4×RTX 3090 (24G)或8×RTX 2080 Ti (11G) GPU上有效地进行推理，这是使用1000亿规模模型所需的最便宜的GPU。论文公开了模型和代码，以及训练日志、相关工具包和经验教训。

与Chinchilla、PaLM或GPT-3不同，我们只使用公开可用的数据，使我们的工作与开源兼容，而大多数现有模型依赖于要么不公开可用、要么无文档（如“图书 - 2TB”或“社交媒体对话”）的数据。的确存在一些例外，如OPT（Zhang等人，2022）、GPT-NeoX（Black等人，2022）、BLOOM（Scao等人，2022）和GLM（Zeng等人，2022），但没有一个能与PaLM-62B或Chinchilla竞争。

In the rest of this paper, we present an overview of the modifications we made to the transformer architecture (Vaswani et al., 2017), as well as our training method. We then report the performance of our models and compare with others LLMs on a set of standard benchmarks. Finally, we expose some of the biases and toxicity encoded in our models, using some of the most recent benchmarks from the responsible AI community.

在本文的其余部分中，我们将介绍我们对 Transformer 架构（Vaswani 等人，2017）所做的修改概述以及我们的训练方法。然后，我们报告我们的模型在一系列标准基准测试中的表现，并与其他大型语言模型进行比较。最后，我们使用负责任 AI 社区中一些最新的基准测试，揭示我们的模型中编码的一些偏见和毒性。

## 2 Approach

Our training approach is similar to the methods described in previous work (Brown et al., 2020; Chowdhery et al., 2022), and is inspired by the Chinchilla scaling laws (Hoffmann et al., 2022). We train large transformers on a large quantity of textual data using a standard optimizer.

我们的训练方法类似于先前工作中描述的方法（Brown等人，2020；Chowdhery等人，2022），并受Chinchilla扩展定律（Hoffmann等人，2022）的启发。我们使用标准优化器在大量文本数据上训练大型Transformer模型。

### 2.1 Pre-training Data

### 2.2 Architecture

Following recent work on large language models, our network is based on the transformer architecture (Vaswani et al., 2017). We leverage various improvements that were subsequently proposed, and used in different models such as PaLM. Here are the main difference with the original architecture, and where we were found the inspiration for this change (in bracket):

参考最近的大型语言模型研究，我们的网络基于Transformer架构（Vaswani等人，2017）。我们利用了随后提出的各种改进，这些改进在不同的模型中得到了应用，例如PaLM。以下是与原始架构的主要区别，以及我们在哪里找到了这种改变的灵感（括号内）：

- Pre-normalization [GPT3]. To improve the training stability, we normalize the input of each transformer sub-layer, instead of normalizing the output. We use the RMSNorm normalizing function, introduced by Zhang and Sennrich (2019).

Biao Zhang and Rico Sennrich. 2019. Root mean square layer normalization. Advances in Neural Information Processing Systems, 32.

- 提出了一种新的层归一化方法，称为根均方层归一化（RMSNorm），它只使用根均方（RMS）来规范神经元的输入，而不需要计算均值和方差¹²。
- RMSNorm具有重缩放不变性和隐式学习率自适应能力，可以帮助稳定深度神经网络的训练和加速模型收敛¹²。
- RMSNorm比层归一化（LayerNorm）更简单和高效，可以减少7%~64%的运行时间¹²。
- RMSNorm在不同的网络结构和任务上与LayerNorm达到了可比较的性能¹²。

- SwiGLU activation function [PaLM]. We replace the ReLU non-linearity by the SwiGLU activation function, introduced by Shazeer (2020) to improve the performance. We use a dimension of 2 34d instead of 4d as in PaLM.

Noam Shazeer. 2020. Glu variants improve transformer. arXiv preprint arXiv:2002.05202.

SwiGLU激活函数[PaLM]。我们用SwiGLU激活函数替换了ReLU非线性函数，这是Shazeer (2020)为了提高性能而引入的。我们使用的维度是 2 34d，而不是PaLM中的4d。

- Rotary Embeddings [GPTNeo]. We remove the absolute positional embeddings, and instead, add rotary positional embeddings (RoPE), introduced by Su et al. (2021), at each layer of the network.

Jianlin Su, Yu Lu, Shengfeng Pan, Ahmed Murtadha, Bo Wen, and Yunfeng Liu. 2021. Roformer: Enhanced transformer with rotary position embedding. arXiv preprint arXiv:2104.09864.

- 提出了一种新的位置嵌入方法，称为旋转位置嵌入（RoPE），它用旋转矩阵编码绝对位置，并在自注意力公式中加入显式的相对位置依赖。
- RoPE具有一些有价值的特性，包括序列长度的灵活性，相对距离增加时的依赖衰减，以及给线性自注意力添加相对位置编码的能力。
- 在不同的长文本分类基准数据集上评估了增强的变换器模型，也称为RoFormer，实验结果表明它一致地优于其替代方案。
- 还提供了理论分析来解释一些实验结果。

旋转嵌入[GPTNeo]。我们去掉了绝对位置嵌入，而是在网络的每一层添加了旋转位置嵌入（RoPE），这是Su et al. (2021)提出的。

The details of the hyper-parameters for our different models are given in Table 2.

![](https://oss-lusing.oss-cn-beijing.aliyuncs.com/llama_t2.png?Expires=1681413496&OSSAccessKeyId=TMP.3Kdb8Ppqi2RKZEnwRDgcutEzE2VL34d5VrAcAW2pxCCd6Lqvdm2RrxfAkGjXaJcZ5cnNpoqAAdjmcraJnhDGap8kPPsDkm&Signature=lNd7DtThgFjh0kWt2fRHJWkzJgM%3D)

![](https://oss-lusing.oss-cn-beijing.aliyuncs.com/llama1.png?Expires=1681412741&OSSAccessKeyId=TMP.3Kdb8Ppqi2RKZEnwRDgcutEzE2VL34d5VrAcAW2pxCCd6Lqvdm2RrxfAkGjXaJcZ5cnNpoqAAdjmcraJnhDGap8kPPsDkm&Signature=vsUayqcKmAZxNT05Qk95qaJYN8w%3D)

### 2.3 Optimizer

Our models are trained using the AdamW optimizer (Loshchilov and Hutter, 2017), with the following hyper-parameters: beta1 = 0:9; beta2 = 0:95. We use a cosine learning rate schedule, such that the final learning rate is equal to 10% of the maximal learning rate. We use a weight decay of 0:1 and gradient clipping of 1:0. We use 2; 000 warmup steps, and vary the learning rate and batch size with the size of the model (see Table 2 for details).

Ilya Loshchilov and Frank Hutter. 2017. Decoupled weight decay regularization. arXiv preprint arXiv:1711.05101.

- 提出了一种新的权重衰减正则化方法，称为解耦权重衰减正则化（Decoupled Weight Decay Regularization），它可以恢复权重衰减正则化的原始公式，通过解耦权重衰减和损失函数的优化步骤。
- 证明了L2正则化和权重衰减正则化在标准随机梯度下降（SGD）中是等价的（当按学习率重新缩放时），但在自适应梯度算法（如Adam）中并不等价。
- 提供了实证证据表明，所提出的修改方法（i）可以解耦SGD和Adam的最优权重衰减因子和学习率的选择，（ii）显著提高了Adam的泛化性能，使其能够在图像分类数据集上与SGD动量相竞争（在这些数据集上，Adam通常被后者超越）。

我们的模型使用AdamW优化器(Loshchilov and Hutter, 2017)进行训练，使用以下超参数：beta1 = 0:9; beta2 = 0:95。 我们使用余弦学习率调度，使得最终的学习率等于最大学习率的10%。我们使用0:1的权重衰减和1:0的梯度裁剪。我们使用2; 000个预热步骤，并根据模型的大小调整学习率和批量大小（详见表2）。

### 2.4 Efficient implementation

We make several optimizations to improve the training speed of our models. First, we use an efficient implementation of the causal multi-head attention to reduce memory usage and runtime. This implementation, available in the xformers library,2 is inspired by Rabe and Staats (2021) and uses the backward from Dao et al. (2022). This is achieved by not storing the attention weights and not computing the key/query scores that are masked due to the causal nature of the language modeling task.

我们采用了几种优化方法来提高模型的训练速度。首先，我们使用了一种高效的因果多头注意力的实现，以减少内存使用和运行时间。这种实现在xformers库中可用，2是受到Rabe和Staats (2021)的启发，并使用了Dao et al. (2022)的反向传播。这是通过不存储注意力权重和不计算由于语言建模任务的因果性而被掩盖的键/查询分数来实现的。

To further improve training efficiency, we reduced the amount of activations that are recomputed during the backward pass with checkpointing. More precisely, we save the activations that are expensive to compute, such as the outputs of linear layers. This is achieved by manually implementing the backward function for the transformer layers, instead of relying on the PyTorch autograd. To fully benefit from this optimization, we need to reduce the memory usage of the model by using model and sequence parallelism, as described by Korthikanti et al. (2022). Moreover, we also overlap the computation of activations and the communication between GPUs over the network (due to all_reduce operations) as much as possible.

为了进一步提高训练效率，我们使用了检查点技术，减少了在反向传播期间重新计算的激活数量。更具体地说，我们保存了计算成本高昂的激活，例如线性层的输出。这是通过手动实现变压器层的反向函数来实现的，而不是依赖于PyTorch autograd。为了充分利用这种优化，我们需要使用模型和序列并行性来减少模型的内存使用，正如Korthikanti等人（2022）所描述的那样。此外，我们还尽可能地重叠激活的计算和GPU之间的通信（由于all_reduce操作）。

When training a 65B-parameter model, our code processes around 380 tokens/sec/GPU on 2048 A100 GPU with 80GB of RAM. This means that training over our dataset containing 1.4T tokens takes approximately 21 days.

当训练一个65B参数的模型时，我们的代码在具有80GB RAM的2048个A100 GPU上处理约380个标记/秒/GPU。这意味着在包含1.4T个标记的数据集上训练需要大约21天。

## 3 Main results

## 4 Instruction Finetuning

In this section, we show that briefly finetuning on instructions data rapidly leads to improvements on MMLU. Although the non-finetuned version of LLaMA-65B is already able to follow basic instructions, we observe that a very small amount of finetuning improves the performance on MMLU, and further improves the ability of the model to follow instructions. Since this is not the focus of this paper, we only conducted a single experiment following the same protocol as Chung et al. (2022) to train an instruct model, LLaMA-I.

在本节中，我们展示了在指令数据上进行简短的微调可以快速提高MMLU的性能。虽然没有微调的LLaMA-65B已经能够遵循基本的指令，但我们观察到非常少量的微调可以提高MMLU的性能，并进一步提高模型遵循指令的能力。由于这不是本文的重点，我们只进行了一个实验，遵循Chung et al. (2022)的协议，训练了一个指令模型，LLaMA-I。

## 5 Bias, Toxicity and Misinformation

## 6 Carbon footprint

## 7 Related work

Language models are probability distributions over sequences of words, tokens or characters (Shannon, 1948, 1951). This task, often framed as next token prediction, has long been considered a core problem in natural language processing (Bahl et al., 1983; Brown et al., 1990). Because Turing (1950) proposed to measure machine intelligence by using language through the “imitation game”, language modeling has been proposed as a benchmark to measure progress toward artificial intelligence (Mahoney, 1999).

语言模型是对单词、标记或字符序列的概率分布（Shannon，1948年，1951年）。这个任务通常被定义为下一个标记预测，长期以来一直被认为是自然语言处理领域的核心问题（Bahl 等人，1983年；Brown 等人，1990年）。自从图灵（1950年）通过“模仿游戏”提议使用语言来衡量机器智能以来，语言建模就被提议作为衡量人工智能进步的基准（Mahoney，1999年）。

Architecture. Traditionally, language models were based on n-gram count statistics (Bahl et al., 1983), and various smoothing techniques were proposed to improve the estimation of rare events (Katz, 1987; Kneser and Ney, 1995). In the past two decades, neural networks have been successfully applied to the language modelling task, starting from feed forward models (Bengio et al., 2000), recurrent neural networks (Elman, 1990; Mikolov et al., 2010) and LSTMs (Hochreiter and Schmidhuber, 1997; Graves, 2013). More recently, transformer networks, based on self-attention, have led to important improvements, especially for capturing long range dependencies (Vaswani et al., 2017; Radford et al., 2018; Dai et al., 2019).

架构。传统上，语言模型基于n-gram计数统计（Bahl等人，1983），并提出了各种平滑技术来改进对罕见事件的估计（Katz，1987；Kneser和Ney，1995）。在过去的二十年中，神经网络已经成功应用于语言建模任务，从前馈模型（Bengio等人，2000）、循环神经网络（Elman，1990；Mikolov等人，2010）到LSTMs（Hochreiter和Schmidhuber，1997；Graves，2013）。最近，基于自注意力的Transformer网络取得了重要的进展，特别是对于捕获长距离依赖关系（Vaswani等人，2017；Radford等人，2018；Dai等人，2019）。

Scaling. There is a long history of scaling for language models, for both the model and dataset sizes. Brants et al. (2007) showed the benefits of using language models trained on 2 trillion tokens, resulting in 300 billion n-grams, on the quality of machine translation. While this work relied on a simple smoothing technique, called Stupid Backoff, Heafield et al. (2013) later showed how to scale Kneser-Ney smoothing to Web-scale data. This allowed to train a 5-gram model on 975 billions tokens from CommonCrawl, resulting in a model with 500 billions n-grams (Buck et al., 2014). Chelba et al. (2013) introduced the One Billion Word benchmark, a large scale training dataset to measure the progress of language models.

规模。语言模型的规模，无论是模型还是数据集的大小，都有着悠久的历史。Brants et al. (2007)展示了在机器翻译的质量上使用训练在2万亿token上的语言模型的好处，产生了3000亿个n-gram。虽然这项工作依赖于一种简单的平滑技术，称为Stupid Backoff，Heafield et al. (2013)后来展示了如何将Kneser-Ney平滑扩展到Web规模的数据。这使得能够在CommonCrawl的9750亿token上训练一个5-gram模型，产生了一个有5000亿n-gram的模型（Buck et al., 2014）。Chelba et al. (2013)引入了十亿词基准，一个大规模的训练数据集，用来衡量语言模型的进步。

In the context of neural language models, Jozefowicz et al. (2016) obtained state-of-the-art results on the Billion Word benchmark by scaling LSTMs to 1 billion parameters. Later, scaling transformers lead to improvement on many NLP tasks. Notable models include BERT (Devlin et al., 2018), GPT-2 (Radford et al., 2019), Megatron- LM (Shoeybi et al., 2019), and T5 (Raffel et al., 2020). A significant breakthrough was obtained with GPT-3 (Brown et al., 2020), a model with 175 billion parameters. This lead to a series of Large Language Models, such as Jurassic-1 (Lieber et al., 2021), Megatron-Turing NLG (Smith et al., 2022), Gopher (Rae et al., 2021), Chinchilla (Hoffmann et al., 2022), PaLM (Chowdhery et al., 2022), OPT (Zhang et al., 2022), and GLM (Zeng et al., 2022). Hestness et al. (2017) and Rosenfeld et al. (2019) studied the impact of scaling on the performance of deep learning models, showing the existence of power laws between the model and dataset sizes and the performance of the system. Kaplan et al. (2020) derived power laws specifically for transformer based language models, which were later refined by Hoffmann et al. (2022), by adapting the learning rate schedule when scaling datasets. Finally, Wei et al. (2022) studied the effect of scaling on the abilities of large language models.

在神经语言模型的背景下，Jozefowicz et al. (2016)通过将LSTM扩展到10亿参数，在十亿词基准上获得了最先进的结果。后来，扩展变换器导致了许多NLP任务的改进。值得注意的模型包括BERT (Devlin et al., 2018)，GPT-2 (Radford et al., 2019)，Megatron-LM (Shoeybi et al., 2019)，和T5 (Raffel et al., 2020)。一个重大的突破是GPT-3 (Brown et al., 2020)，一个有1750亿参数的模型。这导致了一系列的大型语言模型，如Jurassic-1 (Lieber et al., 2021)，Megatron-Turing NLG (Smith et al., 2022)，Gopher (Rae et al., 2021)，Chinchilla (Hoffmann et al., 2022)，PaLM (Chowdhery et al., 2022)，OPT (Zhang et al., 2022)，和GLM (Zeng et al., 2022)。Hestness et al. (2017)和Rosenfeld et al. (2019)研究了规模对深度学习模型性能的影响，显示了模型和数据集大小与系统性能之间存在幂律关系。Kaplan et al. (2020)推导出了专门针对基于变换器的语言模型的幂律，后来被Hoffmann et al. (2022)通过调整学习率计划来适应数据集规模而改进。最后，Wei et al. (2022)研究了规模对大型语言模型能力的影响。



## 8 Conclusion

In this paper, we presented a series of language models that are released openly, and competitive with state-of-the-art foundation models. Most notably, LLaMA-13B outperforms GPT-3 while being more than 10x smaller, and LLaMA-65B is competitive with Chinchilla-70B and PaLM-540B. Unlike previous studies, we show that it is possible to achieve state-of-the-art performance by training exclusively on publicly available data, without resorting to proprietary datasets. We hope that releasing these models to the research community will accelerate the development of large language models, and help efforts to improve their robustness and mitigate known issues such as toxicity and bias. Additionally, we observed like Chung et al. (2022) that finetuning these models on instructions lead to promising results, and we plan to further investigate this in future work. Finally, we plan to release larger models trained on larger pretraining corpora in the future, since we have seen a constant improvement in performance as we were scaling.

在本文中，我们展示了一系列公开发布且与最先进的基础模型竞争的语言模型。值得注意的是，LLaMA-13B在性能上超过了GPT-3，同时比GPT-3小10倍以上，而LLaMA-65B则与Chinchilla-70B和PaLM-540B相媲美。与先前的研究不同，我们证明了仅通过在公开可用数据上进行训练，而不依赖专有数据集，就可以实现最先进的性能。我们希望将这些模型发布给研究社区，加速大型语言模型的发展，并帮助提高它们的鲁棒性，减轻已知问题如有害信息和偏见。此外，我们观察到与Chung等人（2022）一样，通过对这些模型进行微调以适应指令可导致很有前景的结果，我们计划在未来的工作中进一步研究这个问题。最后，我们计划在未来发布在更大预训练语料库上训练的更大模型，因为我们在扩展过程中看到了性能的持续提升。
