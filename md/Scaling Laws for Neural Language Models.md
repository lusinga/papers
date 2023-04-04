# Scaling Laws for Neural Language Models

## Abstract

We study empirical scaling laws for language model performance on the cross-entropy loss. The loss scales as a power-law with model size, dataset size, and the amount of compute used for training, with some trends spanning more than seven orders of magnitude. Other architectural details such as network width or depth have minimal effects within a wide range. Simple equations govern the dependence of overfitting on model/dataset size and the dependence of training speed on model size. These relationships allow us to determine the optimal allocation of a fixed compute budget. Larger models are significantly more sampleefficient, such that optimally compute-efficient training involves training very large models on a relatively modest amount of data and stopping significantly before convergence.

我们研究了语言模型在交叉熵损失上的表现的实证规模定律。损失与模型大小、数据集大小和用于训练的计算量呈幂律关系，其中某些趋势跨越了七个数量级以上。其他架构细节，如网络宽度或深度，在很大范围内影响较小。简单的方程描述了模型/数据集大小对过拟合的依赖关系以及模型大小对训练速度的影响。这些关系使我们能够确定固定计算预算的最佳分配。较大的模型在样本效率方面表现更好，因此在计算效率最优的训练中，应在相对较少的数据上训练非常大的模型，并在达到收敛之前明显停止。

