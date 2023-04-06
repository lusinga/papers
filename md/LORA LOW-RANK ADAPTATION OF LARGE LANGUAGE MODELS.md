# LORA: LOW-RANK ADAPTATION OF LARGE LANGUAGE MODELS

## ABSTRACT

An important paradigm of natural language processing consists of large-scale pretraining on general domain data and adaptation to particular tasks or domains. As we pre-train larger models, full fine-tuning, which retrains all model parameters, becomes less feasible. Using GPT-3 175B as an example – deploying independent instances of fine-tuned models, each with 175B parameters, is prohibitively expensive. We propose Low-Rank Adaptation, or LoRA, which freezes the pretrained model weights and injects trainable rank decomposition matrices into each layer of the Transformer architecture, greatly reducing the number of trainable parameters for downstream tasks. Compared to GPT-3 175B fine-tuned with Adam, LoRA can reduce the number of trainable parameters by 10,000 times and the GPU memory requirement by 3 times. LoRA performs on-par or better than finetuning in model quality on RoBERTa, DeBERTa, GPT-2, and GPT-3, despite having fewer trainable parameters, a higher training throughput, and, unlike adapters, no additional inference latency. We also provide an empirical investigation into rank-deficiency in language model adaptation, which sheds light on the efficacy of LoRA.We release a package that facilitates the integration of LoRA with PyTorch models and provide our implementations and model checkpoints for RoBERTa, DeBERTa, and GPT-2 at https://github.com/microsoft/LoRA.

自然语言处理的一个重要范式包括在通用领域数据上进行大规模预训练，并适应特定任务或领域。随着我们预训练更大的模型，对所有模型参数进行完全微调变得不太可行。以 GPT-3 175B 为例——部署独立的微调模型实例，每个实例拥有1750亿参数，成本过高。我们提出了一种低秩适应（LoRA）方法，该方法冻结预训练模型权重，并将可训练的秩分解矩阵注入到 Transformer 架构的每一层，大大减少了下游任务的可训练参数数量。与使用 Adam 微调的 GPT-3 175B 相比，LoRA 可将可训练参数数量减少10000倍，GPU内存需求减少3倍。尽管 LoRA 在可训练参数数量更少、训练吞吐量更高且与适配器不同的情况下没有额外的推理延迟，但在 RoBERTa、DeBERTa、GPT-2 和 GPT-3 的模型质量上表现与微调相当或更好。我们还对语言模型适应中的秩缺陷进行了实证研究，这有助于理解 LoRA 的有效性。我们发布了一个便于将 LoRA 与 PyTorch 模型集成的软件包，并在 https://github.com/microsoft/LoRA 上提供了 RoBERTa、DeBERTa 和 GPT-2 的实现和模型检查点。

## 1 INTRODUCTION

## 2 PROBLEM STATEMENT

## 3 AREN’T EXISTING SOLUTIONS GOOD ENOUGH?

## 4 OUR METHOD

## 5 EMPIRICAL EXPERIMENTS

## 6 RELATED WORKS

## 7 UNDERSTANDING THE LOW-RANK UPDATES

## 8 CONCLUSION AND FUTURE WORK

Fine-tuning enormous language models is prohibitively expensive in terms of the hardware required and the storage/switching cost for hosting independent instances for different tasks. We propose LoRA, an efficient adaptation strategy that neither introduces inference latency nor reduces input sequence length while retaining high model quality. Importantly, it allows for quick task-switching when deployed as a service by sharing the vast majority of the model parameters. While we focused on Transformer language models, the proposed principles are generally applicable to any neural networks with dense layers.

微调庞大的语言模型在所需硬件和存储/切换成本方面非常昂贵，因为需要为不同任务托管独立实例。我们提出了 LoRA，一种高效的适应策略，既不引入推理延迟，也不减少输入序列长度，同时保持高模型质量。更重要的是，在作为服务部署时，它通过共享绝大多数模型参数实现了快速任务切换。虽然我们关注的是 Transformer 语言模型，但所提出的原则通常适用于任何具有密集层的神经网络。

There are many directions for future works. 1) LoRA can be combined with other efficient adaptation methods, potentially providing orthogonal improvement. 2) The mechanism behind fine-tuning or LoRA is far from clear – how are features learned during pre-training transformed to do well on downstream tasks? We believe that LoRA makes it more tractable to answer this than full fine-tuning. 3) We mostly depend on heuristics to select the weight matrices to apply LoRA to. Are there more principled ways to do it? 4) Finally, the rank-deficiency of $\Delta W$ suggests that W could be rank-deficient as well, which can also be a source of inspiration for future works.

未来的研究方向有很多。1) LoRA 可以与其他高效适应方法相结合，可能提供正交改进。2) 微调或 LoRA 背后的机制尚不清楚——在预训练过程中学到的特征如何转化为在下游任务中表现良好？我们相信，与完全微调相比，LoRA 使得回答这个问题更加容易。3) 我们主要依赖启发式方法来选择应用 LoRA 的权重矩阵。是否有更有原则的方法来实现它？4) 最后，$\Delta W$ 的秩缺陷表明 W 也可能是秩缺陷的，这也可以为未来的研究提供灵感。
