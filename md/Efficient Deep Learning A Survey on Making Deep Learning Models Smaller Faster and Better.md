# Efficient Deep Learning: A Survey on Making Deep Learning Models Smaller, Faster, and Better

Deep learning has revolutionized the fields of computer vision, natural language understanding, speech recognition, information retrieval, and more. However, with the progressive improvements in deep learning models, their number of parameters, latency, and resources required to train, among others, have all increased significantly. Consequently, it has become important to pay attention to these footprint metrics of a model as well, not just its quality.We present and motivate the problem of efficiency in deep learning, followed by a thorough survey of the five core areas of model efficiency (spanning modeling techniques, infrastructure, and hardware) and the seminal work there. We also present an experiment-based guide along with code for practitioners to optimize their model training and deployment. We believe this is the first comprehensive survey in the efficient deep learning space that covers the landscape of model efficiency from modeling techniques to hardware support. It is our hope that this survey would provide readers with the mental model and the necessary understanding of the field to apply generic efficiency techniques to immediately get significant improvements, and also equip them with ideas for further research and experimentation to achieve additional gains.

深度学习已经革新了计算机视觉、自然语言理解、语音识别、信息检索等领域。然而，随着深度学习模型的不断改进，它们的参数数量、延迟和训练所需的资源等都显著增加了。因此，关注模型的这些指标也变得很重要，而不仅仅是它们的质量。我们介绍并激励了深度学习中的效率问题，然后对模型效率的五个核心领域（涵盖建模技术、基础设施和硬件）及其里面的重要工作进行了详尽的调查。我们还提供了一个基于实验的指南以及代码，供实践者优化他们的模型训练和部署。我们相信这是第一篇全面的高效深度学习调查，涵盖了从建模技术到硬件支持的模型效率的全景。我们希望这篇调查能够为读者提供该领域的思维模型和必要的理解，以便应用通用的效率技术立即获得显著的改进，并为进一步的研究和实验提供思路，以实现额外的收益。

## 1 INTRODUCTION

Deep learning with neural networks has been the dominant methodology of training new machine learning models for the past decade. However, deep learning research has been focused on improving the state of the art, and progressive improvements on benchmarks like image classification [57, 124, 130] and text classification [21, 39, 138] have been correlated with an increase in the network complexity, number of parameters, amount of training resources required to train the network, prediction latency, and so on (Figure 1). For instance, GPT-3 comprises 175 billion parameters and costs millions of dollars to train just one iteration [21]. This excludes the cost of experimentation/ trying combinations of different hyper-parameters, which is also computationally expensive.

![](https://xulun-mooc.oss-cn-beijing.aliyuncs.com/model_growth.png)

神经网络的深度学习已经成为过去十年训练新机器学习模型的主导方法。然而，深度学习研究一直专注于改进最先进的技术，而图像分类 和文本分类 等基准测试上的渐进式改进与网络复杂度、参数数量、训练网络所需的训练资源、预测延迟等的增加有关（图1）。例如，GPT-3包含了1750亿个参数，仅训练一次就需要花费数百万美元。这还不包括实验/尝试不同超参数组合的成本，这也是计算代价昂贵的。

Although thesemodels perform well on the tasks they are trained on, they might not necessarily be efficient enough for direct deployment in the real world. A deep learning practitioner might face the following challenges when training or deploying a model:

虽然这些模型在它们训练的任务上表现良好，但它们可能不一定足够高效，可以直接部署在现实世界中。一个深度学习从业者在训练或部署一个模型时可能会面临以下挑战：

- Sustainable server-side scaling: Training and deploying large deep learning models is costly. Training could be a one-time cost (or could be free if one is using a pre-trained model); however, deploying and letting inference run over a long period of time could still turn out to be expensive in terms of consumption of server-side RAM, CPU, and so on. There is also a very real concern around the carbon footprint of datacenters even for organizations like Google, Facebook, and Amazon, which spend several billion dollars each per year in capital expenditure on their datacenters.
可持续的服务器端扩展：训练和部署大型深度学习模型成本高昂。训练可能是一次性成本（或者如果使用预训练模型则可能是免费的）；然而，在长时间内部署和运行推理仍可能会消耗服务器端RAM、CPU等方面的资源，从而变得昂贵。即使对于像谷歌、Facebook和亚马逊这样每年在数据中心的资本支出上花费数十亿美元的组织，也存在着非常真实的数据中心碳足迹的担忧。

- Enabling on-device deployment: Certain deep learning applications need to run in real time on IoT and smart devices (where the model inference happens directly on the device), for a multitude of reasons (privacy, connectivity, responsiveness). Thus, it becomes imperative to optimize the models for the target devices.
实现设备端部署：某些深度学习应用需要在物联网和智能设备上实时运行（模型推理直接在设备上进行），原因有很多（隐私、连接性、响应性）。因此，针对目标设备优化模型变得非常重要。

- Privacy and data sensitivity: Being able to use as little data as possible for training is critical when the user data might be sensitive. Hence, efficiently training models with a fraction of the data means less data collection required.
隐私和数据敏感性：在用户数据可能敏感的情况下，能够尽可能少地使用数据进行训练至关重要。因此，高效地使用少量数据进行训练意味着需要收集的数据量更少。

- New applications: Certain new applications offer new constraints (around model quality or footprint) that existing off-the-shelf models might not be able to address.
新应用：某些新应用提供了新的限制条件（围绕模型质量或足迹），现有的现成模型可能无法解决。

- Explosion of models: Although a singular model might work well, training and/or deploying multiple models on the same infrastructure (colocation) for different applications might end up exhausting the available resources.
模型爆炸：尽管单一模型可能表现良好，但在相同基础设施（共同定位）上训练和/或部署多个模型以应对不同应用，可能会耗尽可用资源。

The common theme around the preceding challenges is efficiency, which is about asking if the given model achieves a competitive performance while keeping the training and inference costs fixed, or vice versa. Our goal with this survey article is to provide a detailed treatment on this subject. Specifically, the contributions are as follows:
前述挑战的共同主题是效率，主要涉及一个问题：在保持固定的训练和推理成本的前提下，给定模型是否能取得具有竞争力的性能，反之亦然。本综述文章的目标是对这一主题进行详细论述。具体来说，本文的贡献如下：

- A mental model (Section 2) for thinking about efficiency in deep learning. This includes metrics, comparison between models, and five focus areas. 
- An in-depth survey of the five focus areas (Section 3) covering algorithms, tools, and infrastructure relevant to efficient deep learning. 
- A practitioner’s guide to efficiency (Section 4) that provides insights into multiple possible approaches toward achieving the right tradeoffs between model quality and footprint using techniques covered in Section 3.
- Insights from experiments performed using the recommendations in Section 4 with deployment and benchmarking on multiple devices. We also released an easy to reproduce IPython notebook for the preceding experiments.

- 一个用于思考深度学习效率的心智模型（第2节）。其中包括度量标准、模型间的比较以及五个关注领域。
- 对五个关注领域进行深入调查（第3节），涵盖与高效深度学习相关的算法、工具和基础设施。
- 一份实践者的效率指南（第4节），提供了在第3节中介绍的技术方面，如何在模型质量和占用空间之间取得合适权衡的多种可能方法的洞察。
- 根据第4节的建议进行的实验结果，以及在多种设备上进行部署和基准测试的见解。我们还发布了一个易于复现的 IPython 笔记本，用于记录前述实验。

确实存在其他综述文章，涵盖了高效深度学习领域的部分内容，如特定的压缩技术（如量化和蒸馏[26, 94]）、架构搜索[3]或硬件设计[129]。然而，根据我们的发现，这是第一篇提供高效深度学习领域深入洞察的综述文章，内容包括效率指标及其权衡、优化技术、支持的工具和基础设施，以及实验支持的针对读者和实践者的实用建议。

## 2 A MENTAL MODEL

### 2.1 Efficiency Metrics

In the previous section, we motivated the problem of efficient deep learning. Although efficiency is fuzzily defined, we can somewhat formalize it using two groups of intertwined metrics around model quality and model footprint (refer to the efficiency metrics taxonomy in Figure 2):
在前一节中，我们阐述了高效深度学习的问题。虽然效率的定义模糊不清，但我们可以使用与模型质量和模型占用空间相关的两组交织指标在一定程度上予以规范化（参见图2中的效率指标分类）。

Quality metrics: These are metrics that we typically care about first, such as the model’s accuracy, precision, and recall.

质量指标：这些通常是我们首先关注的指标，例如模型的准确率、精度和召回率。

Footprint metrics: These are cost indicators of training and deploying a model. These may be costs that we typically associate with a deep learning model such as size and latency. It can also include costs such as number of labels required to achieve the desired performance, which in many cases are expensive to obtain.We can further subdivide these costs into costs that apply only during training, or during inference, or both (see Figure 2).

占用空间指标：这些是训练和部署模型的成本指标。这些成本可能是我们通常与深度学习模型相关联的成本，如大小和延迟。它还可以包括成本，例如为实现所需性能而需要的标签数量，这在许多情况下是昂贵的。我们可以进一步将这些成本细分为仅适用于训练、推理或两者都适用的成本（参见图2）。

![](https://xulun-mooc.oss-cn-beijing.aliyuncs.com/efficiency_metrics_1.png)

Regardless of what one might be optimizing for, we want to achieve Pareto-optimality. This implies that any model we choose is the best for the tradeoffs we care about. As an example in Figure 3, the green dots represent Pareto-optimal models, where none of the other models (red dots) get better accuracy with the same inference latency, or the other way around. Together, the Pareto-optimal models (green dots) form our Pareto-frontier (denoted via the dashed lines in Figure 3). The models in the Pareto-frontier are by definition more efficient than the other models, since they perform the best for their given tradeoff. Hence, when we seek efficiency, we should be thinking about discovering and improving on the Pareto-frontier. In Section 4, we also provide suggestions on how to traverse the Pareto-frontier by exchanging some model quality for better model footprint, or vice versa.

![](https://xulun-mooc.oss-cn-beijing.aliyuncs.com/pareto-opt.png)

无论人们优化什么，我们都希望实现帕累托最优。这意味着我们选择的任何模型都是最适合我们关心的权衡的最佳模型。例如，在图3中，绿点代表帕累托最优模型，其中没有其他模型（红点）在相同的推理延迟下获得更高的准确性，或者反之亦然。帕累托最优模型（绿点）共同形成我们的帕累托前沿（在图3中用虚线表示）。帕累托前沿中的模型从定义上来说比其他模型更有效率，因为它们在给定权衡条件下表现最佳。因此，当我们寻求效率时，应该考虑发现和改进帕累托前沿。在第4节中，我们还提供了如何通过在一定程度上牺牲模型质量以换取更好的模型占用空间或者反之的建议，以遍历帕累托前沿。


### 2.2 Five Focus Areas

In this section, we present a survey of algorithms, techniques, tools, and infrastructure that work together to allow users to train and deploy Pareto-optimal models with respect to model quality and its footprint. We propose to structure them in five major areas, with the first four focused on modeling, and the final one around infrastructure, hardware, and tools (Figure 4):

在本节中，我们介绍一些算法、技术、工具和基础设施，它们共同协作使用户能够训练和部署关于模型质量和占用空间方面的帕累托最优模型。我们建议将它们分为五个主要领域，前四个领域侧重于建模，最后一个领域侧重于基础设施、硬件和工具（如图4所示）：

- Compression techniques: These are general techniques and algorithms that look at optimizing the model’s architecture, typically by compressing its layers. A classical example is quantization [67], which tries to compress the weight matrices of a layer, by reducing its precision (e.g., from 32-bit floating point values to 8-bit unsigned integers), with minimal loss in quality.
压缩技术：这些是一般性的技术和算法，通常通过压缩模型的层来优化其架构。一个经典的例子是量化[67]，它试图通过降低精度（例如，从32位浮点值降至8位无符号整数），以最小化质量损失的方式压缩一个层的权重矩阵。

- Learning techniques: These are algorithms that focus on training the model differently (to make fewer prediction errors, require less data, converge faster, etc.). The improved quality can then be exchanged for a smaller footprint/a more efficient model by trimming the number of parameters if needed. An example of a learning technique is distillation [60], which allows improving the accuracy of a smaller model by learning to mimic a larger model.
学习技术：这些是专注于以不同方式训练模型的算法（使其预测错误更少、需要更少的数据、更快地收敛等）。如果需要，可以通过减少参数数量来换取改进的质量/更有效率的模型。学习技术的一个例子是蒸馏[60]，它允许通过学习模仿更大的模型来提高较小模型的准确性。

- Automation: These are tools for improving the coremetrics of the given model using automation. An example is Hyper-Parameter Optimization (HPO) [50], where optimizing the hyper-parameters helps increase the accuracy, which could then be exchanged for a model with fewer parameters. Similarly, architecture search [150] falls in this category too, where the architecture itself is tuned and the search helps find a model that optimizes both the loss/accuracy, and some other metric such as model latency or model size.
自动化：这些是使用自动化工具改善给定模型的核心指标的工具。一个例子是超参数优化（HPO）[50]，其中优化超参数有助于提高准确性，这可能会换取参数更少的模型。同样，架构搜索[150]也属于这一类，其中调整架构本身，搜索有助于找到优化损失/准确性和某些其他指标（如模型延迟或模型大小）的模型。

- Efficient architectures: These are fundamental blocks that were designed from scratch (convolutional layers, attention, etc.) and are a significant leap over the baseline methods used before them (Fully Connected (FC) layers and Recurrent Neural Networks (RNNs), respectively). As an example, convolutional layers introduced parameter sharing for use in image classification, which avoids having to learn separate weights for each input pixel, and also makes them robust to overfitting. Similarly, attention layers [17] solved the problem of information bottleneck in Sequence-to-Sequence (Seq2Seq) models. These architectures can be used directly for efficiency gains.
高效架构：这些是从头设计的基本模块（如卷积层、注意力等），相对于之前使用的基准方法（如全连接层和循环神经网络（RNN））是一个重大飞跃。例如，卷积层引入了参数共享用于图像分类，避免了为每个输入像素学习单独的权重，并使其对过度拟合具有鲁棒性。类似地，注意力层[17]解决了序列到序列（Seq2Seq）模型中的信息瓶颈问题。这些架构可以直接用于获得效率提升。

- Infrastructure: Finally, we also need a foundation of infrastructure and tools that help us build and leverage efficient models. This includes the model training framework, such as TensorFlow [1] or PyTorch [105] (along with the tools required specifically for deploying efficient models like TensorFlow Lite (TFLite), PyTorch Mobile, etc.). We depend on the infrastructure and tooling to leverage gains from efficient models. For example, to get both size and latency improvements with quantized models, we need the inference platform to support common neural network layers in quantized mode.
基础设施：最后，我们还需要基础设施和工具的基础，帮助我们构建和利用高效模型。这包括模型训练框架，如TensorFlow [1]或PyTorch [105]（以及专门用于部署高效模型的工具，如TensorFlow Lite（TFLite）、PyTorch Mobile等）。我们依赖于基础设施和工具来利用高效模型的优势。例如，要获得量化模型的大小和延迟改进，我们需要推理平台支持量化模式下的常见神经网络层。

We survey each of these areas in depth in the following section.

## 3 LANDSCAPE OF EFFICIENT DEEP LEARNING

### 3.1 Compression Techniques

Compression techniques, asmentioned earlier, are usually generic techniques for achieving amore efficient representation of one or more layers in a neural network, with a possible quality tradeoff. They help improve footprint metrics, such as model size, inference latency, and training time required for convergence, in exchange for as little quality loss as possible. In some cases, if the model is over-parameterized, these techniques can improve model generalization.

如前所述，压缩技术通常是一些泛化技术，用于实现神经网络中一个或多个层的更有效表示，可能会有质量方面的权衡。它们有助于改善占用空间指标，例如模型大小、推理延迟和所需的收敛训练时间，以尽可能小的质量损失为代价。在某些情况下，如果模型过度参数化，这些技术可以改善模型的泛化能力。

#### 3.1.1 Pruning

Given a neural network f(X,W), where X is the input andW is the set of parameters (or weights), pruning is a technique for coming up with a minimal subset W' such that the rest of the parameters of W are pruned (or set to 0), while ensuring that the quality of the model remains above the desired threshold (Figure 5). After pruning, we can say the network has been made sparse, where the sparsity can be quantified as the ratio of the number of parameters that were pruned to the number of parameters in the original network $(s = (1− \frac{|W'|}{|W|} ))$. The higher the sparsity, the lesser the number of non-zero parameters in the pruned networks.

![](https://xulun-mooc.oss-cn-beijing.aliyuncs.com/pruning1.png)

给定一个神经网络 f(X,W)，其中 X 是输入，W 是参数（或权重）集合，剪枝是一种技术，用于找到一个最小的子集W'，使得W的其余参数被剪枝（或设为0），同时确保模型的质量保持在所需的阈值以上（如图5所示）。剪枝后，我们可以说网络已经变得稀疏，其中稀疏度可以量化为剪枝前后非零参数数量的比例 $(s = (1− \frac{|W'|}{|W|} ))$。稀疏度越高，剪枝后网络中的非零参数数量越少。

Some of the classical works in this area are Optimal Brain Damage (OBD) by LeCun et al. [82] and the Optimal Brain Surgeon paper by Hassibi et al. [56]. These methods usually take a network that has been pre-trained to a reasonable quality and then iteratively prune the parameters that have the lowest saliency score such that the impact on the validation loss is minimized. Once pruning concludes, the network is fine-tuned with the remaining parameters. This process is repeated a number of times until the desired number of original parameters are pruned (Algorithm 1).

![](https://xulun-mooc.oss-cn-beijing.aliyuncs.com/prune_algo1.png)

在这个领域中一些经典的工作包括LeCun等人的“Optimal Brain Damage”(OBD) [82]和Hassibi等人的“Optimal Brain Surgeon”[56]。这些方法通常采用已经训练到合理质量的网络，然后迭代地剪枝具有最低显著性分数的参数，以使对验证损失的影响最小化。一旦剪枝完成，使用剩余参数对网络进行微调。这个过程重复多次，直到原始参数的所需数量被剪掉（算法1）。

OBD approximates the saliency score by using a second derivative of the parameters $(\frac{\partial ^2 L}{\partial w_i^2})$, where L is the loss function and wi is the candidate parameter for removal. The intuition is that the higher this value for a given parameter, the larger the change in the loss function’s gradient if it were to be pruned.

OBD使用参数的二阶导数$( \frac{\partial ^2 L}{\partial w_i^2})$来近似显著性分数，其中L是损失函数，wi是候选移除的参数。其直觉是，对于给定参数，这个值越高，如果它被剪枝，则损失函数的梯度变化越大。

For the purpose of speeding up the computation of the second derivatives, OBD ignores cross interaction between the weights $(\frac{\partial ^2 L}{\partial w_i \partial w_j})$ and hence computes only the diagonal elements of the Hessian matrix. Otherwise, computing the full Hessian matrix is unwieldy for even a reasonable number of weights (with n = $10^4$, the size of the matrix is $10^4 \times 10^4 = 10^8$). In terms of results, LeCun et al. [82] demonstrate that pruning reduced the parameters in a well-trained neural net by 8× (combination of both automatic and manual removal) without a drop in classification accuracy.
为了加速计算二阶导数，OBD忽略了权重之间的交互作用$(\frac{\partial ^2 L}{\partial w_i \partial w_j})$，因此只计算海森矩阵的对角元素。否则，即使对于一个合理数量的权重（例如$n=10^4$），计算完整的海森矩阵也会非常困难（矩阵的大小为$10^4 \times 10^4 = 10^8$）。在结果方面，LeCun等人证明，剪枝可以将经过充分训练的神经网络中的参数数量减少8倍（包括自动和手动移除的组合），而不会降低分类准确度。

Across different pruning strategies, the core algorithm could remain similar, with changes in the following aspects (also refer to Table 1 for various results on the same MobileNet v2 architecture, which might be fair comparisons):
在不同的剪枝策略中，核心算法可能保持相似，但以下方面可能会有所改变（也可以参考表1中对MobileNet v2架构的各种结果，这可能是公平的比较）:

- Saliency: Although some methods [56, 82] use second-order derivatives, other methods rely on simpler magnitude based pruning [53, 54] or momentum based pruning [38], among others, to determine the saliency score.
显著性评分：虽然一些方法 [56，82] 使用二阶导数，但其他方法则依赖于简单的基于大小的剪枝 [53，54] 或基于动量的剪枝 [38] 等方法来确定显著性评分。

- Structured v/s unstructured: The most flexible way of pruning is unstructured (or random) pruning, where all given parameters are treated equally. In structured pruning, parameters are pruned in blocks (pruning row-wise in a weight matrix, or pruning channel-wise in a convolutional filter [5, 84, 89, 98], etc.). The latter allows easier leveraging of inference-time gains in size and latency, since these blocks of pruned parameters can be intelligently skipped for storage and inference. Note that unstructured pruning can also be viewed as structured pruning with block size = 1.
结构化剪枝与非结构化剪枝：非结构化（或随机）剪枝是最灵活的一种方式，其中所有给定的参数被平等对待。在结构化剪枝中，参数按块进行剪枝（在权重矩阵中按行进行剪枝，或者在卷积滤波器中按通道进行剪枝 [5, 84, 89, 98] 等）。后者可以更轻松地利用尺寸和延迟的推理时间增益，因为这些被剪枝参数的块可以被智能地跳过存储和推理。请注意，非结构化剪枝也可以视为块大小为1的结构化剪枝。

- Distribution: The decision about howto distribute the sparsity budget (number of parameters to be pruned) could be made either by pooling in all the parameters from the network and then deciding which parameters to prune, or by smartly selecting how much to prune in each layer individually [42, 58]. Elsen et al. [45] and Google Research [114] have found that some architectures like MobileNetV2 and EfficientNet [131] have thin first layers that do not contribute significantly to the number of parameters and pruning them leads to an accuracy drop without much gain. Hence, intuitively, it would be helpful to allocate sparsity on a per-layer basis.
分布：关于如何分配稀疏度预算（要剪枝的参数数量）的决定可以通过将网络中的所有参数进行汇总，然后决定剪枝哪些参数，或者通过智能地选择每个层要剪枝的数量来实现[42, 58]。Elsen等人 [45] 和Google Research [114] 发现一些架构（如MobileNetV2和EfficientNet [131]）的第一层非常薄，其不会对参数数量产生很大的贡献，剪枝这些层会导致准确度下降而几乎没有收益。因此，直觉上，按层分配稀疏度将是有帮助的。

- Scheduling: Another question is how much to prune, and when? Should we prune an equal number of parameters every round [54, 56, 82], or should we prune at a higher pace in the beginning and gradually decrease [38, 149]?
调度：另一个问题是应该剪掉多少，以及何时剪枝？我们应该每轮剪掉相等数量的参数 [54, 56, 82]，还是应该在开始时以更快的速度剪枝并逐渐减少 [38, 149]？

- Regrowth: Some methods allow regrowing pruned connections [38, 47] to keep the same level of sparsity through constant cycles of prune-redistribute-regrow. Dettmers and Zettlemoyer [38] estimate training time speedups between 2.7times and 5.6times by starting and operating with a sparse model throughout. However, there is a gap in terms of implementation of sparse operations on CPU, Graphics Processing Unit (GPU), and other hardware.
重生：一些方法允许重新生长被剪枝的连接 [38, 47]，通过不断的剪枝-重新分配-重生的循环来保持相同的稀疏度水平。Dettmers和Zettlemoyer [38] 通过始终使用稀疏模型来估计训练时间的加速，速度提升在2.7倍到5.6倍之间。然而，就CPU、GPU和其他硬件上的稀疏操作的实现而言，存在差距。

#### 3.1.2 Quantization

Almost all the weights and activations of a typical network are in 32-bit floating-point values. One of the ideas of reducing the model footprint is to reduce the precision for the weights and activations by quantizing to a lower-precision datatype (often 8-bit fixed-point integers). There are two kinds of gains that we can get from quantization: (1) lower model size and (2) lower inference latency. Often, only the model size is a constraint, and in this case we can employ a technique called weight quantization and get model size improvements [11], where only the model weights are in reduced precision. To get latency improvements, the activations need to be in fixed-point aswell (activation quantization [67, 136]) such that all operations in the quantized graph are happening in fixed-point math as well.

几乎所有典型网络的权重和激活值都是32位浮点值。减小模型的占用空间的想法之一是通过量化为较低精度的数据类型（通常为8位定点整数）来减小权重和激活值的精度。我们可以从量化中获得两种收益：（1）较小的模型尺寸和（2）较低的推理延迟。通常，只有模型大小是一个限制因素，在这种情况下，我们可以采用一种称为权重量化的技术来获得模型大小的改进[11]，其中仅模型权重处于降低的精度中。要获得延迟改进，激活值也需要采用定点表示（激活量化[67, 136]），以便量化图中的所有操作都是在定点数学中进行的。

##### Weight Quantization

A simple scheme for quantizing weights to get model size improvements (similar to Krishnamoorthi [75]) is as follows. Given a 32-bit floating-point weight matrix in a model, we can map the minimum weight value (xmin) in that matrix to 0 and the maximum value (xmax) to 2b − 1 (where b is the number of bits of precision, and b < 32). Then we can linearly extrapolate all values between them to an integer value in [0, 2b − 1] (Figure 6). Thus, we are able to map each floating-point value to a fixed-point value where the latter requires a lesser number of bits than the floating-point representation. This process can also be done for signed b-bit fixedpoint integers, where the output values will be in the range $[–2^{\frac{b}{2}}−1, 2^{\frac{b}{2}}-1]$. One of the reasonable values of b is 8, since this would lead to a 32/8 = 4× reduction in space, and also because of the near-universal support for uint8_t and int8_t datatypes.

权重量化。一种简单的将权重量化以获得模型大小改进的方案（类似于Krishnamoorthi [75]）如下所述。给定模型中的32位浮点权重矩阵，我们可以将该矩阵中的最小权重值（xmin）映射为0，将最大值（xmax）映射为2b−1（其中b是精度位数，且b < 32）。然后，我们可以将它们之间的所有值线性外推到[0, 2b−1]范围内的整数值（图6）。因此，我们能够将每个浮点值映射到一个固定点值，后者所需的位数比浮点表示法要少。这个过程也可以用于有符号的b位定点整数，其中输出值将在$[–2^{\frac{b}{2}}−1, 2^{\frac{b}{2}}-1]$范围内。b的一个合理值是8，因为这将导致空间减小32/8 = 4倍，并且几乎所有平台都支持uint8_t和int8_t数据类型。

During inference,we go in the reverse direction wherewe recover a lossy estimate of the original floating-point value (dequantization) using just xmin and xmax . This estimate is lossy since we lost 32 − b bits of information when did the rounding (another way to look at it is that a range of floating-point values map to the same quantized value).

在推断过程中，我们会反向进行操作，使用xmin和xmax恢复原始浮点值的损失估计（解量化）。这个估计是有损的，因为在量化时我们丢失了32-b位的信息（另一种看待这个问题的方式是，一系列浮点值映射到同一个量化值）。

![](https://xulun-mooc.oss-cn-beijing.aliyuncs.com/quantized_value.png)

Jacob et al. [67] and Krishnamoorthi [75] formalize the quantization scheme with the following two constraints:
- The quantization scheme should be linear (affine transformation) so that the precision bits are linearly distributed.
- 0.0 shouldmap exactly to a fixed-point value $x_{q_0}$ such that dequantizing $x_{q_0}$ gives us 0.0. This is an implementation constraint, since 0 is also used for padding to signify missing elements in tensors, and if dequantizing $x_{q_0}$ leads to a non-zero value, then it might be interpreted incorrectly as a valid element at that index.

Jacob等人[67]和Krishnamoorthi [75]使用以下两个限制条件来规范量化方案：
- 量化方案应该是线性的（仿射变换），以使精度位线性分布。
- 0.0应该恰好映射到一个固定点值$xq0$，以便对$xq0$进行反量化会得到0.0。这是一种实现约束条件，因为0还用于填充以表示张量中缺失的元素，如果将$xq0$反量化得到一个非零值，则可能会错误地将其解释为该索引处的有效元素。

The second constraint described previously requires that 0 be a part of the quantization range, which in turn requires updating xmin and xmax , followed by clamping x to lie in [xmin, xmax ]. Following this, we can quantize x by constructing a piece-wise linear transformation as follows:
$quantize(x) = x_q = round ({\frac{x}{s}})$
先前描述的第二个约束要求0是量化范围的一部分，这又需要更新xmin和xmax，然后将x夹在[xmin，xmax]之间。随后，我们可以通过构建以下分段线性变换来量化x：
$quantize(x) = x_q = round ({\frac{x}{s}})$

s is the floating-point scale value (can be thought of as the inverse of the slope, which can be computed using xmin, xmax and the range of the fixed-point values). z is an integer zero-point value, which is the quantized value that is assigned to x = 0.0. This is the terminology followed in the literature [67, 75] (Algorithm 2).
s是浮点数的缩放因子（可以视为斜率的倒数，可以使用xmin、xmax和定点值的范围来计算）。z是整数零点值，是分配给x = 0.0的量化值。这是文献[67，75]中采用的术语（算法2）。

The dequantization step constructs $\hat x$, which is a lossy estimate of x, since we lose precision when quantizing to a lower number of bits. We can compute it as follows:
$dequantize(x_q) = \hat{x} = s (x_q − z)$
量化反推（dequantization）的过程会构建一个$\hat x$，由于我们在将数值量化成低位数时会丢失精度，所以$\hat x$是一个有损的估计值。具体计算方法如下所示：
$dequantize(x_q) = \hat{x} = s (x_q − z)$

Since s is in floating-point, ˆ x is also a floating-point value (Algorithm 3). Note that the quantization and dequantization steps can be performed for signed integers too by appropriately changing the value xqmin (which is the lowest fixed-point value in b-bits) in Algorithm 2.
由于 s 是浮点数，因此 ˆx 也是浮点数值（算法3）。请注意，通过适当改变算法2中的 xqmin 值（即 b 位中最低的定点值），量化和反量化步骤也可以针对带符号整数执行。

We can utilize Algorithms 2 and 3 for quantizing and dequantizing the model’s weight matrices. Quantizing a pre-trained model’s weights for reducing the size is termed as post-training quantization in the literature [11]. This might be sufficient for the purpose of reducing the model size when there is sufficient representational capacity in the model.
我们可以利用算法2和算法3来对模型的权重矩阵进行量化和反量化。文献中将对预训练模型的权重进行量化以减小其大小的过程称为后训练量化。当模型具有足够的表示能力时，这可能足以减小模型的大小。

There are other works in the literature [65, 83, 110] that demonstrate slightly different variants of quantization. XNOR-Net [110], binarized neural networks [65], and others use b = 1 and thus have weight matrices that just have two possible values 0 or 1, and the quantization function there is simply the sign(x) function (assuming the weights are symmetrically distributed around 0).
文献中还有其他一些工作[65，83，110]展示了略微不同的量化变体。XNOR-Net [110]，二值神经网络[65]和其他一些工作使用 b = 1，因此它们的权重矩阵只有两个可能的值 0 或 1，而在这种情况下的量化函数只是符号函数 sign(x)（假设权重在0附近对称分布）。

The promise with such extreme quantization approaches is the theoretical 32/1 = 32× reduction in model size without much quality loss. Some of the works claim improvements on larger networks like AlexNet [77], VGG [124], and Inception [130], which might already be more amenable to compression. A more informative taskwould be to demonstrate extreme quantization on smaller networks like the MobileNet family [62, 117]. Additionally, binary quantization (and other quantization schemes like ternary [83], bit-shift based networks [110], etc.) promise latency-efficient implementations of standard operations where multiplications and divisions are replaced by cheaper operations like addition and subtraction. These claims need to be verified because even if these lead to theoretical reduction in FLOPs, the implementations still need support from the underlying hardware. A fair comparison would be using standard quantization with b = 8, where the multiplications and divisions also become cheaper, and are supported by the hardware efficiently via SIMD instructions that allow for low-level data parallelism (e.g., on x86 via the SSE instruction set, on ARM via the Neon [90] intrinsics, and even on specialized DSPs like the Qualcomm Hexagon [16]).
这种极端量化方法的优势在于在几乎不损失质量的情况下，理论上可以将模型大小减小32倍。一些研究声称在更大的网络如AlexNet [77]、VGG [124]和Inception [130]上取得了改进，这些网络本身可能已经更易于压缩。一个更具信息性的任务是展示在较小的网络（如MobileNet系列[62, 117]）上进行极端量化。此外，二值量化（以及其他量化方案，如三值[83]、基于位移的网络[110]等）承诺使用比乘法和除法更便宜的操作（如加法和减法）实现低延迟的标准操作。这些声明需要验证，因为即使这些方法导致FLOPs的理论减少，实现仍然需要来自底层硬件的支持。一个公平的比较应该使用b=8的标准量化，这样乘法和除法也变得更便宜，并且通过允许低级别的数据并行性的SIMD指令有效地由硬件支持（例如，在x86上通过SSE指令集，在ARM上通过Neon [90]内部函数，在专用DSP上，例如高通Hexagon [16]）。

##### Activation Quantization

To be able to get latency improvements with quantized networks, the math operations have to be done in fixed-point representations too. This means all intermediate layer inputs and outputs are also in fixed-point, and there is no need to dequantize the weight matrices since they can be used directly along with the inputs.

要通过量化网络获得延迟改进，数学运算也必须使用定点表示。这意味着所有中间层输入和输出也都是定点表示，而且不需要将权重矩阵去量化，因为它们可以直接与输入一起使用。

Vanhoucke et al. [136] demonstrated a 3× inference speedup using a fully fixed-point model on an x86 CPU, when compared to a floating-point model on the same CPU, without sacrificing accuracy. The weights are still quantized similar to post-training quantization; however, all layer inputs (except the first layer) and the activations are fixed-point. In terms of performance, the primary driver for this improvement was the availability of fixed-point SIMD instructions in Intel’s SSE4 instruction set [35], where commonly used building-block operations like the Multiply- Accumulate (MAC) [34] can be parallelized. Since the paper was published, Intel has released two more iterations of these instruction sets [31], which might further improve the speedups.

Vanhoucke等人在一篇论文中展示了一个完全定点模型相较于同一CPU上的浮点模型，使用x86 CPU可以实现3倍的推理速度提升，而不会影响准确性。权重仍然像训练后量化那样被量化；但是，除了第一层之外，所有层的输入和激活都是定点表示的。在性能方面，这种改进的主要驱动力是Intel SSE4指令集中的定点SIMD指令的可用性，其中常用的构建块操作如乘加（MAC）[34]可以并行化。自该论文发表以来，英特尔已经发布了两个更高版本的这些指令集[31]，这可能会进一步提高速度。

The network that Vanhoucke et al. [136] mention was a five-layer feed-forward network that was post-training quantized. However, post-training quantization can lead to quality loss during inference as highlighted in other works [67, 75, 139] as the networks become more complex. These could be because of (1) outlier weights that skew the computation of the quantized values for the entire input range toward the outliers, leading to fewer bits being allocated to the bulk of the range, or (2) different distribution of weights within the weight matrix— for example, within a convolutional layer, the distribution of weights between each filter might be different, but they are quantized the same way. These effects might be more pronounced at low bit widths due to an even worse loss of precision.
Vanhoucke 等人[136]提到的网络是一个经过后训练量化的五层前馈网络。然而，后训练量化可能会导致推理过程中的质量损失，正如其他一些工作[67，75，139]所强调的，因为网络变得更加复杂。这可能是由于以下原因：（1）异常权重使整个输入范围的量化值的计算向异常值倾斜，导致范围的大部分分配的位数较少；（2）权重矩阵内部的权重分布不同——例如，在卷积层中，每个滤波器之间的权重分布可能不同，但它们是以相同的方式量化的。这些效应在低位宽时可能更加明显，因为精度损失更严重。

Jacob et al. [67] propose (and further detailed by Krishnamoorthi [75]) a training regime that is quantization-aware. In this setting, the training happens in floating-point but the forward-pass simulates the quantization behavior during inference. Both weights and activations are passed through a function that simulates this quantization behavior (fake-quantized is the term used by many works [67, 75]) (Figure 7).
Jacob等人（引用文献67）提出了一个量化感知的训练方案，由Krishnamoorthi进一步详细阐述[引用文献75]。在这种情况下，训练是在浮点数下进行的，但正向传递模拟了推理时的量化行为。权重和激活值都通过一个函数进行传递，这个函数模拟了量化行为（许多工作使用“假量化”这个术语[引用文献67，75]）（图7）。

Quantization-Aware Training (QAT) allows the network to adapt to tolerate the noise introduced by the clamping and rounding behavior during inference. Once the network is trained, tools such as the TFLite Model Converter [12] can generate the appropriate fixed-point inference model from a network annotated with the quantization nodes.
量化感知训练（QAT）允许网络适应推理过程中引入的夹紧和舍入行为带来的噪声。一旦网络被训练好了，像TFLite模型转换器[引用文献12]这样的工具就可以从带有量化节点注释的网络中生成适当的定点推理模型。

Results. Table 2 presents a comparison between the baseline floating-point model, post-training quantized, and QAT models [11]. The model with post-training quantization gets close to the baseline, but there is still a significant accuracy difference. The model size is 4× smaller, but the latency is slightly higher due to the need to dequantize the weights during inference. The model with 8- bit QAT gets quite close to the baseline floating-point model while requiring 4× less disk space and being 1.64× faster. To summarize, we recommend evaluating standard 8-bit QAT for your networks, since the resulting model quality is comparable to floating-point models, and there is broad support for faster inference with 8-bit models.
结果。表2展示了基准浮点模型、训练后量化模型和QAT模型[引用文献11]之间的比较。训练后量化的模型接近基准模型，但仍存在显著的准确度差异。模型大小缩小了4倍，但由于推理时需要对权重进行去量化，所以延迟略高。8位QAT模型非常接近基准浮点模型，同时需要4倍少的磁盘空间，并且速度快1.64倍。总之，我们建议对您的网络评估标准的8位QAT，因为所得到的模型质量与浮点模型相当，而且支持使用8位模型进行更快的推理。

#### 3.1.3 Other Compression Techniques

There are other compression techniques like low-rank decomposition [146] and weight sharing via k-means clustering [53], which are also actively being used for model compression [103] and might be suitable for further compressing hotspots in a model.
还有其他的压缩技术，如低秩分解[引用文献146]和通过k-means聚类实现权重共享[引用文献53]，这些技术也被积极用于模型压缩[引用文献103]，并且可能适用于进一步压缩模型中的热点区域。

### 3.2 Learning Techniques

Learning techniques try to train a model differently to obtain better quality metrics (accuracy, F1 score, precision, recall, etc.). The improvement in quality can sometimes be traded off for a smaller footprint by reducing the number of parameters/layers in the model and achieving the same baseline quality with a smaller model (see Section 4). An incentive of paying attention to learning techniques is that they need to be applied only during the training phase, without impacting the inference.
学习技术试图以不同的方式训练模型，以获得更好的质量度量（准确性、F1得分、精度、召回率等）。通过减少模型中的参数/层数量并使用较小的模型达到相同的基准质量，有时可以通过牺牲质量的提高来换取更小的占用空间（参见第4节）。关注学习技术的一个好处是，它们只需要在训练阶段应用，而不会影响推理过程。

#### 3.2.1 Distillation

Ensembles are well known to help with generalization [55, 78]. The intuition is that this enables learning multiple independent hypotheses, which are likely to be better than learning a single hypothesis. Dietterich [40] goes over some of the standard ensembling methods, such as bagging (learning models that are trained on non-overlapping data and then ensembling them), boosting (learning models that are trained to fix the classification errors of other models in the ensemble), and averaging (voting by all the ensemble models). Buciluˇa et al. [22] used large ensembles to label synthetic data that they generated using various schemes. A smaller neural net is then trained to learn not just from the labeled data but also from this weakly labeled synthetic data. They found that single neural nets were able to mimic the performance of larger ensembles, while being 1, 000× smaller and faster. This demonstrated that it is possible to transfer the cumulative knowledge of ensembles to a single small model. However, it might not be sufficient to rely on just the existing labeled data.

集成已被证明有助于泛化[55, 78]。直觉上，这使得学习多个独立的假设成为可能，这些假设很可能比学习单个假设更好。Dietterich [40]介绍了一些标准的集成方法，如bagging（学习在不重叠的数据上训练的模型，然后将它们集成），boosting（学习训练以修正集成中其他模型的分类错误的模型），和平均（由所有集成模型投票）。Buciluˇa等人[22]使用大型集成来标记他们使用各种方案生成的合成数据。然后训练一个较小的神经网络，不仅从标记的数据中学习，还从这些弱标记的合成数据中学习。他们发现，单个神经网络能够模仿大型集成的性能，同时比它们小和快1,000倍。这表明将集成的累积知识转移到单个小模型是可能的。然而，仅仅依赖现有的标记数据可能是不够的。

#### 3.2.2 Data Augmentation

#### 3.2.3 Self-Supervised Learning

### 3.3 Automation

#### 3.3.1 Hyper-Parameter Optimization

### 3.4 Efficient Architectures

#### 3.4.1 Vision

#### 3.4.2 Natural Language Understanding

### 3.5 Infrastructure

#### 3.5.1 TensorFlow Ecosystem

#### 3.5.2 PyTorch Ecosystem

#### 3.5.3 Hardware-Optimized Libraries

#### 3.5.4 Hardware: GPUs and TPUs

## 4 A PRACTITIONER’S GUIDE TO EFFICIENCY

## 5 FUTURE TRENDS

## 6 CONCLUSION

In this article, we started with demonstrating the rapid growth in deep learning models, and motivating the fact that someone training and deploying models today has to make either implicit or explicit decisions about efficiency. However, the landscape of model efficiency is vast.

在这篇文章中，我们从展示深度学习模型的快速增长开始，并激励了这样一个事实，即今天训练和部署模型的人必须对效率做出隐式或显式的决策。然而，模型效率的领域是非常广阔的。

To help with this, we laid out a mental model for the readers to wrap their heads around the multiple focus areas of model efficiency and optimization. The surveys of the core model optimization techniques give the reader an opportunity to understand the state of the art, apply these techniques in the modeling process, and/or use them as a starting point for exploration. The infrastructure section also lays out the software libraries and hardware that make training and inference of efficient models possible.

为了帮助这一点，我们为读者提供了一个思维模型，让他们能够理解模型效率和优化的多个关注领域。对核心模型优化技术的调查给了读者一个机会，了解最新的技术，将这些技术应用于建模过程中，或者将它们作为探索的起点。基础设施部分也介绍了使训练和推理高效模型成为可能的软件库和硬件。

If we were to be given two models, performing equally well on a given task (comparable quality metrics),we might want to choose a model that has lower footprint costs during training, inference, or both (as appropriate for the given use case).1 Deploying a model on devices with constrained (e.g., mobile and embedded devices) or expensive (cloud servers) compute resources requires paying attention to inference efficiency. Similarly, training largemodels with limited or costly training resources would require monitoring training efficiency.

如果我们得到两个在某项任务上表现相同的模型（具有可比的质量指标），我们可能会选择在训练、推理或两者都具有更低占用空间成本的模型（根据给定的用例选择合适的选项）。在设备计算资源受限（例如移动和嵌入式设备）或成本高昂（云服务器）的情况下部署模型需要注意推理效率。同样地，使用有限或昂贵的训练资源训练大型模型需要监控训练效率。

--



