# Attention is all you need

## Abstract

The dominant sequence transduction models are based on complex recurrent or convolutional neural networks that include an encoder and a decoder. The best performing models also connect the encoder and decoder through an attention mechanism. We propose a new simple network architecture, the Transformer, based solely on attention mechanisms, dispensing with recurrence and convolutions entirely. Experiments on two machine translation tasks show these models to be superior in quality while being more parallelizable and requiring significantly less time to train. Our model achieves 28.4 BLEU on the WMT 2014 English-to-German translation task, improving over the existing best results, including ensembles, by over 2 BLEU. On the WMT 2014 English-to-French translation task, our model establishes a new single-model state-of-the-art BLEU score of 41.8 after training for 3.5 days on eight GPUs, a small fraction of the training costs of the best models from the literature. We show that the Transformer generalizes well to other tasks by applying it successfully to English constituency parsing both with large and limited training data.

显性序列转换模型基于复杂的递归或卷积神经网络，包括编码器和解码器。性能最佳的模型还通过注意机制连接编码器和解码器。我们提出了一种新的简单的网络结构，转换器，它完全基于注意机制，完全免除了递归和卷积。在两个机器翻译任务上的实验表明，这些模型在质量上更优，同时更可并行化，所需的训练时间明显更少。我们的模型在2014年WMT英德翻译任务中达到28.4 BLEU，比现有的最佳结果(包括集成部分)提高了2个BLEU以上。在2014年的WMT英法翻译任务中，我们的模型在8个gpu上进行了3.5天的培训后，建立了一个新的单模型——最先进的BLEU评分41.8，这只是文献中最佳模型培训成本的一小部分。我们证明了转换器可以很好地将其推广到其他任务，并成功地将其应用到具有大量和有限训练数据的英语选民分析中。

## 1 Introduction

Recurrent neural networks, long short-term memory [13] and gated recurrent [7] neural networks in particular, have been firmly established as state of the art approaches in sequence modeling and transduction problems such as language modeling and machine translation [35, 2, 5]. Numerous efforts have since continued to push the boundaries of recurrent language models and encoder-decoder architectures [38, 24, 15].

循环神经网络，特别是长短期记忆 [13] 和门控循环 [7] 神经网络，已成为序列建模和转导问题（如语言建模和机器翻译）的最先进方法 [35, 2] , 5]。 此后，许多努力继续推动循环语言模型和编码器-解码器架构的界限 [38, 24, 15]。

[13] Sepp Hochreiter and Jürgen Schmidhuber. Long short-term memory. Neural computation, 9(8):1735–1780, 1997.

    [7] Junyoung Chung, Çaglar Gülçehre, Kyunghyun Cho, and Yoshua Bengio. Empirical evaluation of gated recurrent neural networks on sequence modeling. CoRR, abs/1412.3555, 2014.

    [35] Ilya Sutskever, Oriol Vinyals, and Quoc VV Le. Sequence to sequence learning with neural networks. In Advances in Neural Information Processing Systems, pages 3104–3112, 2014.
    [2] Dzmitry Bahdanau, Kyunghyun Cho, and Yoshua Bengio. Neural machine translation by jointly learning to align and translate. CoRR, abs/1409.0473, 2014.
    [5] Kyunghyun Cho, Bart van Merrienboer, Caglar Gulcehre, Fethi Bougares, Holger Schwenk, and Yoshua Bengio. Learning phrase representations using rnn encoder-decoder for statistical machine translation. CoRR, abs/1406.1078, 2014.

    [38] Yonghui Wu, Mike Schuster, Zhifeng Chen, Quoc V Le, Mohammad Norouzi, Wolfgang Macherey, Maxim Krikun, Yuan Cao, Qin Gao, Klaus Macherey, et al. Google’s neural machine translation system: Bridging the gap between human and machine translation. arXiv preprint arXiv:1609.08144, 2016.
    [24] Minh-Thang Luong, Hieu Pham, and Christopher D Manning. Effective approaches to attentionbased neural machine translation. arXiv preprint arXiv:1508.04025, 2015.
    [15] Rafal Jozefowicz, Oriol Vinyals, Mike Schuster, Noam Shazeer, and Yonghui Wu. Exploring the limits of language modeling. arXiv preprint arXiv:1602.02410, 2016.
}

Recurrent models typically factor computation along the symbol positions of the input and output sequences. Aligning the positions to steps in computation time, they generate a sequence of hidden states ht, as a function of the previous hidden state ht-1 and the input for position t. This inherently sequential nature precludes parallelization within training examples, which becomes critical at longer sequence lengths, as memory constraints limit batching across examples. Recent work has achieved significant improvements in computational efficiency through factorization tricks [21] and conditional
computation [32], while also improving model performance in case of the latter. The fundamental constraint of sequential computation, however, remains.

循环模型通常沿输入和输出序列的符号位置因子计算。 将位置与计算时间中的步骤对齐，它们生成一系列隐藏状态 ht，作为先前隐藏状态 ht-1 和位置 t 输入的函数。 这种固有的顺序性质排除了训练示例中的并行化，这在更长的序列长度时变得至关重要，因为内存限制限制了跨示例的批处理。 最近的工作通过分解技巧 [21] 和条件计算 [32] 显着提高了计算效率，同时在后者的情况下也提高了模型性能。 然而，顺序计算的基本约束仍然存在。

{
    [21] Oleksii Kuchaiev and Boris Ginsburg. Factorization tricks for LSTM networks. arXiv preprint arXiv:1703.10722, 2017.

    [32] Noam Shazeer, Azalia Mirhoseini, Krzysztof Maziarz, Andy Davis, Quoc Le, Geoffrey Hinton, and Jeff Dean. Outrageously large neural networks: The sparsely-gated mixture-of-experts layer. arXiv preprint arXiv:1701.06538, 2017.

}

Attention mechanisms have become an integral part of compelling sequence modeling and transduction models in various tasks, allowing modeling of dependencies without regard to their distance in the input or output sequences [2, 19]. In all but a few cases [27], however, such attention mechanisms are used in conjunction with a recurrent network.

注意机制已成为各种任务中引人注目的序列建模和转导模型的组成部分，允许对依赖项进行建模，而无需考虑它们在输入或输出序列中的距离 [2, 19]。 然而，除了少数情况 [27]，在所有情况下，这种注意力机制都与循环网络结合使用。

{
    [19] Yoon Kim, Carl Denton, Luong Hoang, and Alexander M. Rush. Structured attention networks. In International Conference on Learning Representations, 2017.

    [27] Ankur Parikh, Oscar Täckström, Dipanjan Das, and Jakob Uszkoreit. A decomposable attention model. In Empirical Methods in Natural Language Processing, 2016.
}

In this work we propose the Transformer, a model architecture eschewing recurrence and instead relying entirely on an attention mechanism to draw global dependencies between input and output.
The Transformer allows for significantly more parallelization and can reach a new state of the art in translation quality after being trained for as little as twelve hours on eight P100 GPUs.

在这项工作中，我们提出了Transformer，一种避免递归的模型架构，而是完全依赖于一种注意力机制来绘制输入和输出之间的全局依赖关系。
Transformer允许更多的并行化，并且可以在8个P100 gpu上进行最少12个小时的培训，从而在翻译质量方面达到新的水平。

## 2 Background

The goal of reducing sequential computation also forms the foundation of the Extended Neural GPU [16], ByteNet [18] and ConvS2S [9], all of which use convolutional neural networks as basic building block, computing hidden representations in parallel for all input and output positions. In these models, the number of operations required to relate signals from two arbitrary input or output positions grows in the distance between positions, linearly for ConvS2S and logarithmically for ByteNet. This makes it more difficult to learn dependencies between distant positions [12]. In the Transformer this is reduced to a constant number of operations, albeit at the cost of reduced effective resolution due to averaging attention-weighted positions, an effect we counteract with Multi-Head Attention as described in section 3.2.

减少顺序计算的目标也构成了扩展神经GPU[16]、ByteNet[18]和ConvS2S[9]的基础，它们都使用卷积神经网络作为基本构件，对所有输入和输出位置并行计算隐藏表示。在这些模型中，将两个任意输入或输出位置的信号关联起来所需的操作数量在位置之间的距离中增长，对于ConvS2S是线性增长，对于ByteNet是对数增长。这使得学习远处位置[12]之间的依赖关系变得更加困难。在转换器中，这被简化为一个恒定的操作数，尽管代价是由于平均注意加权位置而降低了有效分辨率，我们用3.2节中描述的多头注意来抵消这种影响。

Self-attention, sometimes called intra-attention is an attention mechanism relating different positions of a single sequence in order to compute a representation of the sequence. Self-attention has been used successfully in a variety of tasks including reading comprehension, abstractive summarization, textual entailment and learning task-independent sentence representations [4, 27, 28, 22].

{
    [4] Jianpeng Cheng, Li Dong, and Mirella Lapata. Long short-term memory-networks for machine reading. arXiv preprint arXiv:1601.06733, 2016.
    [27] Ankur Parikh, Oscar Täckström, Dipanjan Das, and Jakob Uszkoreit. A decomposable attention model. In Empirical Methods in Natural Language Processing, 2016.
    [28] Romain Paulus, Caiming Xiong, and Richard Socher. A deep reinforced model for abstractive summarization. arXiv preprint arXiv:1705.04304, 2017.
    [22] Zhouhan Lin, Minwei Feng, Cicero Nogueira dos Santos, Mo Yu, Bing Xiang, Bowen Zhou, and Yoshua Bengio. A structured self-attentive sentence embedding. arXiv preprint arXiv:1703.03130, 2017.
}

自我注意，有时也称为内注意，是一种将单个序列的不同位置联系起来以计算序列表示的注意机制。自我注意已经成功地应用于各种任务中，包括阅读理解、抽象总结、文本蕴涵和学习任务无关的句子表征[4,27,28,22]。

End-to-end memory networks are based on a recurrent attention mechanism instead of sequence aligned recurrence and have been shown to perform well on simple-language question answering and language modeling tasks [34].

端到端记忆网络基于递归注意机制，而不是序列对齐递归，在简单语言问题回答和语言建模任务[34]中表现良好。

To the best of our knowledge, however, the Transformer is the first transduction model relying entirely on self-attention to compute representations of its input and output without using sequence aligned RNNs or convolution. In the following sections, we will describe the Transformer, motivate self-attention and discuss its advantages over models such as [17, 18] and [9].

然而，就我们所知，Transformer是第一个完全依赖于自我关注来计算其输入和输出表示的转换模型，而不使用序列对齐的RNNs或卷积。在接下来的部分中，我们将描述Transformer，激发self-attention，并讨论它相对于[17,18]和[9]等模型的优点。

## 3 Model Architecture

Most competitive neural sequence transduction models have an encoder-decoder structure [5, 2, 35]. Here, the encoder maps an input sequence of symbol representations $(x_1,...,x_n)$ to a sequence of continuous representations $z=(z_1,...,z_n)$. Given z, the decoder then generates an output sequence $(y_1,...,y_m)$ of symbols one element at a time. At each step the model is auto-regressive [10], consuming the previously generated symbols as additional input when generating the next.

The Transformer follows this overall architecture using stacked self-attention and point-wise, fully connected layers for both the encoder and decoder, shown in the left and right halves of Figure 1, respectively.

![](https://gw.alicdn.com/imgextra/i2/O1CN01z2lAsX1LYTyaKmXSJ_!!6000000001311-2-tps-946-1086.png)

### 3.1 Encoder and Decoder Stacks

- Encoder: The encoder is composed of a stack of N = 6 identical layers. Each layer has two sub-layers. The first is a multi-head self-attention mechanism, and the second is a simple, positionwise fully connected feed-forward network. We employ a residual connection [11] around each of the two sub-layers, followed by layer normalization [1]. That is, the output of each sub-layer is LayerNorm(x + Sublayer(x)), where Sublayer(x) is the function implemented by the sub-layer itself. To facilitate these residual connections, all sub-layers in the model, as well as the embedding layers, produce outputs of dimension $d_{model} = 512$.

- 编码器：编码器由 N = 6 个相同层的堆栈组成。 每层有两个子层。 第一个是多头自注意力机制，第二个是简单的、位置全连接的前馈网络。 我们在两个子层的每一个周围都使用了一个残差连接 [11]，然后是层归一化 [1]。 即每个子层的输出是LayerNorm(x + Sublayer(x))，其中Sublayer(x)是子层自己实现的函数。 为了促进这些残差连接，模型中的所有子层以及嵌入层产生维度为 $d_{model} = 512$ 的输出。

- Decoder: The decoder is also composed of a stack of N = 6 identical layers. In addition to the two sub-layers in each encoder layer, the decoder inserts a third sub-layer, which performs multi-head attention over the output of the encoder stack. Similar to the encoder, we employ residual connections around each of the sub-layers, followed by layer normalization. We also modify the self-attention sub-layer in the decoder stack to prevent positions from attending to subsequent positions. This masking, combined with fact that the output embeddings are offset by one position, ensures that the predictions for position i can depend only on the known outputs at positions less than i.

- 解码器：解码器也由 N = 6 个相同层的堆栈组成。 除了每个编码器层中的两个子层之外，解码器还插入了第三个子层，该子层对编码器堆栈的输出执行多头注意。 与编码器类似，我们在每个子层周围使用残差连接，然后进行层归一化。 我们还修改了解码器堆栈中的自注意子层，以防止位置关注后续位置。 这种掩蔽与输出嵌入偏移一个位置的事实相结合，确保位置 i 的预测只能依赖于小于 i 位置的已知输出。

### 3.2 Attention

An attention function can be described as mapping a query and a set of key-value pairs to an output, where the query, keys, values, and output are all vectors. The output is computed as a weighted sum of the values, where the weight assigned to each value is computed by a compatibility function of the query with the corresponding key.

注意力函数可以描述为将一个查询和一组键值对映射到一个输出，其中查询、键、值和输出都是向量。 输出计算为值的加权总和，其中分配给每个值的权重由查询与相应键的兼容性函数计算。

![](https://gw.alicdn.com/imgextra/i2/O1CN01zUggNb1x62meJJX12_!!6000000006393-2-tps-1094-608.png)
