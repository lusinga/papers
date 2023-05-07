# LORA: LOW-RANK ADAPTATION OF LARGE LANGUAGE MODELS

Edward Hu
Yelong Shen
Phillip Wallis 
Zeyuan Allen-Zhu
Yuanzhi Li
Shean Wang 
Lu Wang 
Weizhu Chen

Microsoft Corporation

## ABSTRACT

An important paradigm of natural language processing consists of large-scale pretraining on general domain data and adaptation to particular tasks or domains. As we pre-train larger models, full fine-tuning, which retrains all model parameters, becomes less feasible. Using GPT-3 175B as an example – deploying independent instances of fine-tuned models, each with 175B parameters, is prohibitively expensive. We propose Low-Rank Adaptation, or LoRA, which freezes the pretrained model weights and injects trainable rank decomposition matrices into each layer of the Transformer architecture, greatly reducing the number of trainable parameters for downstream tasks. Compared to GPT-3 175B fine-tuned with Adam, LoRA can reduce the number of trainable parameters by 10,000 times and the GPU memory requirement by 3 times. LoRA performs on-par or better than finetuning in model quality on RoBERTa, DeBERTa, GPT-2, and GPT-3, despite having fewer trainable parameters, a higher training throughput, and, unlike adapters, no additional inference latency. We also provide an empirical investigation into rank-deficiency in language model adaptation, which sheds light on the efficacy of LoRA.We release a package that facilitates the integration of LoRA with PyTorch models and provide our implementations and model checkpoints for RoBERTa, DeBERTa, and GPT-2 at https://github.com/microsoft/LoRA.

自然语言处理的一个重要范式包括在通用领域数据上进行大规模预训练，并适应特定任务或领域。随着我们预训练更大的模型，对所有模型参数进行完全微调变得不太可行。以 GPT-3 175B 为例——部署独立的微调模型实例，每个实例拥有1750亿参数，成本过高。我们提出了一种低秩适应（LoRA）方法，该方法冻结预训练模型权重，并将可训练的秩分解矩阵注入到 Transformer 架构的每一层，大大减少了下游任务的可训练参数数量。与使用 Adam 微调的 GPT-3 175B 相比，LoRA 可将可训练参数数量减少10000倍，GPU内存需求减少3倍。尽管 LoRA 在可训练参数数量更少、训练吞吐量更高且与适配器不同的情况下没有额外的推理延迟，但在 RoBERTa、DeBERTa、GPT-2 和 GPT-3 的模型质量上表现与微调相当或更好。我们还对语言模型适应中的秩缺陷进行了实证研究，这有助于理解 LoRA 的有效性。我们发布了一个便于将 LoRA 与 PyTorch 模型集成的软件包，并在 https://github.com/microsoft/LoRA 上提供了 RoBERTa、DeBERTa 和 GPT-2 的实现和模型检查点。

## 1 INTRODUCTION

Many applications in natural language processing rely on adapting one large-scale, pre-trained language model to multiple downstream applications. Such adaptation is usually done via fine-tuning, which updates all the parameters of the pre-trained model. The major downside of fine-tuning is that the new model contains as many parameters as in the original model. As larger models are trained every few months, this changes from a mere “inconvenience” for GPT-2 (Radford et al., b) or RoBERTa large (Liu et al., 2019) to a critical deployment challenge for GPT-3 (Brown et al., 2020) with 175 billion trainable parameters.

许多自然语言处理应用程序依赖于将一个大规模的预训练语言模型适应于多个下游应用程序。这种适应通常是通过微调实现的，微调会更新预训练模型的所有参数。微调的主要缺点是新模型包含与原始模型相同数量的参数。随着每几个月训练更大的模型，这从仅仅是对于GPT-2 (Radford et al., b) 或RoBERTa large (Liu et al., 2019) 的“不便”变成了对于拥有1750亿可训练参数的GPT-3 (Brown et al., 2020) 的关键部署挑战。

Many sought to mitigate this by adapting only some parameters or learning external modules for new tasks. This way, we only need to store and load a small number of task-specific parameters in addition to the pre-trained model for each task, greatly boosting the operational efficiency when deployed. However, existing techniques often introduce inference latency (Houlsby et al., 2019; Rebuffi et al., 2017) by extending model depth or reduce the model’s usable sequence length (Li & Liang, 2021; Lester et al., 2021; Hambardzumyan et al., 2020; Liu et al., 2021) (Section 3). More importantly, these method often fail to match the fine-tuning baselines, posing a trade-off between efficiency and model quality.

许多人试图通过适应部分参数或学习新任务的外部模块来减轻这种情况。通过这种方式，我们只需要为每个任务存储和加载少量的任务特定参数，除了预训练模型外，大大提高了部署时的操作效率。然而，现有技术通常通过扩展模型深度或减少模型可用序列长度（第3节）引入推断延迟（Houlsby等人，2019；Rebuffi等人，2017），更重要的是，这些方法通常无法与fine-tuning基准相匹配，存在效率和模型质量之间的权衡。

We take inspiration from Li et al. (2018a); Aghajanyan et al. (2020) which show that the learned over-parametrized models in fact reside on a low intrinsic dimension. We hypothesize that the change in weights during model adaptation also has a low “intrinsic rank”, leading to our proposed Low-Rank Adaptation (LoRA) approach. LoRA allows us to train some dense layers in a neural network indirectly by optimizing rank decomposition matrices of the dense layers’ change during adaptation instead, while keeping the pre-trained weights frozen, as shown in Figure 1. Using GPT-3 175B as an example, we show that a very low rank (i.e., r in Figure 1 can be one or two) suffices even when the full rank (i.e., d) is as high as 12,288, making LoRA both storage- and compute-efficient.

我们受到Li等人(2018a)和Aghajanyan等人(2020)的启发，这些人表明，实际上过度参数化的模型存在低内在维度。我们假设模型适应期间权重的变化也具有低“内在秩”，从而引出我们提出的低秩适应（LoRA）方法。LoRA允许我们通过优化密集层的秩分解矩阵来间接训练神经网络中的一些密集层，而保持预训练权重不变，如图1所示。以GPT-3 175B为例，我们展示了即使完整秩（即d）高达12,288，使用很低的秩（即图1中的r可以是1或2）也足够了，这使得LoRA在存储和计算效率方面都非常高。

LoRA possesses several key advantages.

A pre-trained model can be shared and used to build many small LoRA modules for different tasks. We can freeze the shared model and efficiently switch tasks by replacing the matrices A and B in Figure 1, reducing the storage requirement and task-switching overhead significantly.
一种预训练模型可以共享并用于构建许多不同任务的小型LoRA模块。我们可以冻结共享模型并通过替换图1中的矩阵A和B来高效地切换任务，从而显著减少存储需求和任务切换开销。

LoRA makes training more efficient and lowers the hardware barrier to entry by up to 3 times when using adaptive optimizers since we do not need to calculate the gradients or maintain the optimizer states for most parameters. Instead, we only optimize the injected, much smaller low-rank matrices.
LoRA通过注入优化后的秩分解矩阵，将预训练模型参数冻结，减少了下游任务的可训练参数数量，使得训练更加高效，并且在使用适应性优化器时，降低了硬件进入门槛，最多可降低3倍，因为我们不需要计算大多数参数的梯度或维护优化器状态，而是仅优化注入的、远小于原参数量的秩分解矩阵。

Our simple linear design allows us to merge the trainable matrices with the frozen weights when deployed, introducing no inference latency compared to a fully fine-tuned model, by construction.
我们的简单线性设计允许我们在部署时将可训练矩阵与冻结的权重合并，与完全微调模型相比，通过构造不会引入推理延迟。

## 2 PROBLEM STATEMENT

## 3 AREN’T EXISTING SOLUTIONS GOOD ENOUGH?

## 4 OUR METHOD

## 5 EMPIRICAL EXPERIMENTS

## 6 RELATED WORKS

### Transformer Language Models

Transformer (Vaswani et al., 2017) is a sequence-to-sequence architecture that makes heavy use of self-attention. Radford et al. (a) applied it to autoregressive language modeling by using a stack of Transformer decoders. Since then, Transformer-based language models have dominated NLP, achieving the state-of-the-art in many tasks. A new paradigm emerged with BERT (Devlin et al., 2019b) and GPT-2 (Radford et al., b) – both are large Transformer lan-guage models trained on a large amount of text – where fine-tuning on task-specific data after pretraining on general domain data provides a significant performance gain compared to training on task-specific data directly. Training larger Transformers generally results in better performance and remains an active research direction. GPT-3 (Brown et al., 2020) is the largest single Transformer language model trained to-date with 175B parameters.

Transformer（Vaswani等人，2017）是一种序列到序列的架构，广泛使用自注意力机制。Radford等人（a）使用一堆Transformer解码器将其应用于自回归语言建模。从那以后，基于Transformer的语言模型已经在自然语言处理领域占据了主导地位，在许多任务上取得了最先进的结果。随着BERT（Devlin等人，2019b）和GPT-2（Radford等人，b）的出现，一个新的范例出现了——两者都是在大量文本上训练的大型Transformer语言模型，通过在预训练通用领域数据上进行微调后在任务特定数据上进行微调，相比直接在任务特定数据上训练可以提供显著的性能提升。训练更大的Transformer通常会导致更好的性能，并且仍然是一个活跃的研究方向。GPT-3（Brown等人，2020）是迄今为止训练参数最多的单个Transformer语言模型，拥有1750亿个参数。

### Prompt Engineering and Fine-Tuning

While GPT-3 175B can adapt its behavior with just a few additional training examples, the result depends heavily on the input prompt (Brown et al., 2020). This necessitates an empirical art of composing and formatting the prompt to maximize a model’s performance on a desired task, which is known as prompt engineering or prompt hacking. Fine-tuning retrains a model pre-trained on general domains to a specific task Devlin et al. (2019b); Radford et al. (a). Variants of it include learning just a subset of the parameters Devlin et al. (2019b); Collobert & Weston (2008), yet practitioners often retrain all of them to maximize the downstream performance. However, the enormity of GPT-3 175B makes it challenging to perform fine-tuning in the usual way due to the large checkpoint it produces and the high hardware barrier to entry since it has the same memory footprint as pre-training.

GPT-3 175B 可以通过只使用一些额外的训练示例来调整其行为，但结果严重依赖于输入提示（Brown et al.，2020）。这需要一种经验性的方法来构成和格式化提示，以最大化模型在所需任务上的性能，这被称为提示工程或提示操作。微调重新训练一个在一般领域上预训练的模型以适用于特定任务（Devlin et al.，2019b; Radford et al. (a)）。它的变种包括仅学习一部分参数（Devlin et al.，2019b; Collobert & Weston (2008)），但实践者通常会重新训练所有参数以最大化下游性能。然而，GPT-3 175B 的巨大规模使得以通常的方式进行微调变得具有挑战性，由于它生成的检查点很大，而且由于具有与预训练相同的内存占用量，所以硬件门槛很高。

### Parameter-Efficient Adaptation

Many have proposed inserting adapter layers between existing layers in a neural network (Houlsby et al., 2019; Rebuffi et al., 2017; Lin et al., 2020). Our method uses a similar bottleneck structure to impose a low-rank constraint on the weight updates. The key functional difference is that our learned weights can be merged with the main weights during inference, thus not introducing any latency, which is not the case for the adapter layers (Section 3). A comtenporary extension of adapter is COMPACTER (Mahabadi et al., 2021), which essentially parametrizes the adapter layers using Kronecker products with some predetermined weight sharing scheme. Similarly, combining LoRA with other tensor product-based methods could potentially improve its parameter efficiency, which we leave to future work. More recently, many proposed optimizing the input word embeddings in lieu of fine-tuning, akin to a continuous and differentiable generalization of prompt engineering (Li & Liang, 2021; Lester et al., 2021; Hambardzumyan et al., 2020; Liu et al., 2021). We include comparisons with Li & Liang (2021) in our experiment section. However, this line of works can only scale up by using more special tokens in the prompt, which take up available sequence length for task tokens when positional embeddings are learned.

许多人已经提出在神经网络中现有层之间插入适配器层（Houlsby等，2019年；Rebuffi等，2017年；Lin等，2020年）。我们的方法使用类似的瓶颈结构来对权重更新施加低秩约束。关键的功能差异在于我们学到的权重可以在推断过程中与主要权重合并，因此不会引入任何延迟，而适配器层则不是这种情况（第3节）。适配器的一个现代扩展是COMPACTER（Mahabadi等，2021年），其基本上使用克罗内克积与某些预定的权重共享方案来参数化适配器层。类似地，将LoRA与其他基于张量积的方法相结合可能会提高其参数效率，这留待将来研究。最近，许多人提出了优化输入单词嵌入代替微调的方法，类似于prompt engineering的连续和可微分的推广（Li＆Liang，2021年；Lester等，2021年；Hambardzumyan等，2020年；Liu等，2021年）。我们在实验部分包括与Li＆Liang（2021年）的比较。然而，这一系列工作只能通过在提示中使用更多的特殊标记来扩展，这些标记占用可用序列长度用于任务标记学习位置嵌入。

### Low-Rank Structures in Deep Learning

Low-rank structure is very common in machine learning. A lot of machine learning problems have certain intrinsic low-rank structure (Li et al., 2016; Cai et al., 2010; Li et al., 2018b; Grasedyck et al., 2013). Moreover, it is known that for many deep learning tasks, especially those with a heavily over-parametrized neural network, the learned neural network will enjoy low-rank properties after training (Oymak et al., 2019). Some prior works even explicitly impose the low-rank constraint when training the original neural network (Sainath et al., 2013; Povey et al., 2018; Zhang et al., 2014; Jaderberg et al., 2014; Zhao et al., 2016; Khodak et al., 2021; Denil et al., 2014); however, to the best of our knowledge, none of these works considers low-rank update to a frozen model for adaptation to downstream tasks. In theory literature, it is known that neural networks outperform other classical learning methods, including the corresponding (finite-width) neural tangent kernels (Allen-Zhu et al., 2019; Li & Liang, 2018) when the underlying concept class has certain low-rank structure (Ghorbani et al., 2020; Allen-Zhu & Li, 2019; Allen-Zhu & Li, 2020a). Another theoretical result in Allen-Zhu & Li (2020b) suggests that low-rank adaptations can be useful for adversarial training. In sum, we believe that our proposed low-rank adaptation update is well-motivated by the literature.

机器学习中很多问题都具有固有的低秩结构（Li et al., 2016; Cai et al., 2010; Li et al., 2018b; Grasedyck et al., 2013），因此低秩结构在机器学习中很常见。此外，已知在许多深度学习任务中，特别是那些具有过度参数化的神经网络，训练后学习的神经网络会具有低秩属性（Oymak et al., 2019）。有些先前的工作甚至在训练原始神经网络时显式地强制执行低秩约束（Sainath et al., 2013; Povey et al., 2018; Zhang et al., 2014; Jaderberg et al., 2014; Zhao et al., 2016; Khodak et al., 2021; Denil et al., 2014）；然而据我们所知，这些工作中没有一个考虑对冻结模型进行低秩更新以适应下游任务。在理论文献中，已知当底层概念类别具有某些低秩结构时，神经网络优于其他经典学习方法，包括相应的（有限宽度的）神经切向核（Allen-Zhu et al., 2019; Li & Liang, 2018）(Ghorbani et al., 2020; Allen-Zhu & Li, 2019; Allen-Zhu & Li, 2020a)。Allen-Zhu & Li (2020b) 的另一个理论结果表明，低秩自适应可以用于对抗训练。综上所述，我们认为我们提出的低秩自适应更新受到了文献的良好启发。

## 7 UNDERSTANDING THE LOW-RANK UPDATES

## 8 CONCLUSION AND FUTURE WORK

Fine-tuning enormous language models is prohibitively expensive in terms of the hardware required and the storage/switching cost for hosting independent instances for different tasks. We propose LoRA, an efficient adaptation strategy that neither introduces inference latency nor reduces input sequence length while retaining high model quality. Importantly, it allows for quick task-switching when deployed as a service by sharing the vast majority of the model parameters. While we focused on Transformer language models, the proposed principles are generally applicable to any neural networks with dense layers.

微调庞大的语言模型在所需硬件和存储/切换成本方面非常昂贵，因为需要为不同任务托管独立实例。我们提出了 LoRA，一种高效的适应策略，既不引入推理延迟，也不减少输入序列长度，同时保持高模型质量。更重要的是，在作为服务部署时，它通过共享绝大多数模型参数实现了快速任务切换。虽然我们关注的是 Transformer 语言模型，但所提出的原则通常适用于任何具有密集层的神经网络。

There are many directions for future works. 1) LoRA can be combined with other efficient adaptation methods, potentially providing orthogonal improvement. 2) The mechanism behind fine-tuning or LoRA is far from clear – how are features learned during pre-training transformed to do well on downstream tasks? We believe that LoRA makes it more tractable to answer this than full fine-tuning. 3) We mostly depend on heuristics to select the weight matrices to apply LoRA to. Are there more principled ways to do it? 4) Finally, the rank-deficiency of $\Delta W$ suggests that W could be rank-deficient as well, which can also be a source of inspiration for future works.

未来的研究方向有很多。1) LoRA 可以与其他高效适应方法相结合，可能提供正交改进。2) 微调或 LoRA 背后的机制尚不清楚——在预训练过程中学到的特征如何转化为在下游任务中表现良好？我们相信，与完全微调相比，LoRA 使得回答这个问题更加容易。3) 我们主要依赖启发式方法来选择应用 LoRA 的权重矩阵。是否有更有原则的方法来实现它？4) 最后，$\Delta W$ 的秩缺陷表明 W 也可能是秩缺陷的，这也可以为未来的研究提供灵感。
