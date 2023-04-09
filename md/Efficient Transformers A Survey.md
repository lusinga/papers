# Efficient Transformers: A Survey

- YI TAY, Google Research, USA
- MOSTAFA DEHGHANI, Google Research, Amsterdam
- DARA BAHRI and DONALD METZLER, Google Research, USA

Transformer model architectures have garnered immense interest lately due to their effectiveness across a range of domains like language, vision, and reinforcement learning. In the field of natural language processing for example, Transformers have become an indispensable staple in themodern deep learning stack. Recently, a dizzying number of “X-former” models have been proposed—Reformer, Linformer, Performer, Longformer, to name a few—which improve upon the original Transformer architecture, many of which make improvements around computational and memory efficiency.With the aim of helping the avid researcher navigate this flurry, this article characterizes a large and thoughtful selection of recent efficiency-flavored “X-former” models, providing an organized and comprehensive overview of existing work and models across multiple domains.

变换器模型架构由于在语言、视觉和强化学习等多个领域的有效性而最近引起了极大的兴趣。例如，在自然语言处理领域，变换器已经成为现代深度学习堆栈中不可或缺的重要组成部分。最近，出现了令人眼花缭乱的“X-former”模型，如Reformer、Linformer、Performer、Longformer等，它们对原始变换器架构进行了改进，其中许多模型在计算和内存效率方面进行了改进。为了帮助热心的研究者探索这些模型，本文对近期提出的一些高效的“X-former”模型进行了大量而深思熟虑的选择，提供了一个有组织和全面的概述，涵盖了多个领域的现有工作和模型。

## 1 INTRODUCTION

Transformers [82] are a formidable force in the modern deep learning stack. Transformers are pervasive and have made tremendous impact in many fields such as language understanding [6, 12, 20, 60], image processing [7, 55] and information retrieval [77]. As such, it is only natural that a wealth of research has been dedicated to making fundamental improvements to the model over the past few years [2, 18, 68]. This immense interest has also spurred research into more efficient variants of the model [5, 11, 14, 15, 36, 37, 59, 62, 70, 74, 84, 87].

变换器[82]是现代深度学习堆栈中的一股强大力量。变换器无处不在，对语言理解[6, 12, 20, 60]、图像处理[7, 55]和信息检索[77]等多个领域产生了巨大的影响。因此，过去几年来，人们投入了大量的研究来对模型进行根本性的改进[2, 18, 68]。这种强烈的兴趣也促进了对模型更高效变体的研究[5, 11, 14, 15, 36, 37, 59, 62, 70, 74, 84, 87]。

There has been such a surge of Transformer model variants proposed recently that researchers and practitioners alike may find it challenging to keep pace with the rate of innovation. As of this writing and this manuscript’s first draft (circa August 2020), there have been nearly a dozen new efficiency-focused models proposed in just the past 6 months. Thus, a survey of the existing literature is both beneficial for the community and quite timely.

最近，有如此多的变换器模型变体被提出，以至于研究者和实践者都可能难以跟上创新的速度。截至本文撰写和本文初稿（约2020年8月），仅在过去的6个月里，就有近十种新的以效率为重点的模型被提出。因此，对现有文献的调查对于社区来说既有益处又很及时。

The self-attention mechanism is a key defining characteristic of Transformer models. The mechanism can be viewed as a graph-like inductive bias that connects all tokens in a sequence with a relevance-based pooling operation. A well-known concern with self-attention is the quadratic time and memory complexity, which can hinder model scalability in many settings. There has been an overwhelming influx of model variants proposed recently that address this problem. We hereinafter name this class of models “efficient Transformers”.

自注意力机制是变换器模型的一个关键特征。这种机制可以被视为一种图形化的归纳偏置，它通过一个基于相关性的池化操作将序列中的所有标记连接起来。自注意力的一个众所周知的问题是它的二次时间和内存复杂度，这可能会妨碍模型在许多场景下的可扩展性。最近，有大量的模型变体被提出来解决这个问题。我们以下将这类模型称为“高效变换器”。

The efficiency of a model can be interpreted in a variety of ways. It might refer to the memory footprint of the model, which is of importance when the memory of accelerators on which the model is running is limited. Efficiency might also refer to computational costs, e.g., the number of FLOPs, both during training and inference. In particular, for on-device applications, models often must operate within a highly constrained computational budget. Throughout this survey, we refer to the efficiency of Transformers both in terms of memory and computation. We are especially interested in how such models perform when they are applied to large inputs.

模型的效率可以有多种解释方式。它可能指的是模型的内存占用，这在模型运行的加速器的内存有限时很重要。效率也可能指的是计算成本，例如训练和推理过程中的FLOPs数。特别是对于在设备上的应用，模型通常必须在一个高度受限的计算预算内运行。在本文中，我们从内存和计算两方面来讨论变换器的效率。我们特别感兴趣的是这些模型在应用于大规模输入时的表现。

Efficient self-attention models are crucial in applications that model long sequences. For example, documents, images, and videos are all often composed of a relatively large number of pixels or tokens. Efficiency in processing long sequences is therefore paramount for widespread adoption of Transformers.

高效的自注意力模型在处理长序列的应用中至关重要。例如，文档、图像和视频都通常由相对较多的像素或标记组成。因此，处理长序列的效率对于变换器的广泛应用至关重要。

This survey sets out to provide a comprehensive overview of the recent advances made in this class of models. We are primarily interested in modeling advances and architectural innovations that improve the general efficiency of Transformers, including but not limited to tackling the quadratic complexity issue of the self-attention mechanism or reducing the computation costs by means such as pooling and/or sparsity. We also briefly discuss general improvements and other efficiency improvements such as parameter sharing.

这篇调查旨在全面概述这类模型近期取得的进展。我们主要关注的是提高Transformer的通用效率的建模进步和架构创新，包括但不限于解决自注意力机制的二次复杂度问题或者通过池化和/或稀疏等手段降低计算成本。我们也简要讨论了一般的改进和其他效率提升，如参数共享等。

We propose a taxonomy of efficient Transformer models, characterizing them by their technical innovation and primary use case. Specifically, we review Transformer models that have applications in both language and vision domains, attempting to consolidate the literature across the spectrum.We also provide a detailed walk-through of many of these models and draw connections between them.

我们提出了一个高效Transformer模型的分类法，根据它们的技术创新和主要用例来刻画它们。具体来说，我们回顾了在语言和视觉领域都有应用的Transformer模型，试图整合不同领域的文献。我们也提供了许多这些模型的详细介绍，并在它们之间建立联系。

## 2 BACKGROUND ON TRANSFORMERS

This section provides an overview of the well-established Transformer architecture [82]. Transformers are multi-layered architectures formed by stacking Transformer blocks on top of one another.

本节概述了已经成熟的Transformer架构[82]。Transformer是由多个Transformer块叠加在一起形成的多层架构。

