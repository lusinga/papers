# Efficient Deep Learning: A Survey on Making Deep Learning Models Smaller, Faster, and Better

Deep learning has revolutionized the fields of computer vision, natural language understanding, speech recognition, information retrieval, and more. However, with the progressive improvements in deep learning models, their number of parameters, latency, and resources required to train, among others, have all increased significantly. Consequently, it has become important to pay attention to these footprint metrics of a model as well, not just its quality.We present and motivate the problem of efficiency in deep learning, followed by a thorough survey of the five core areas of model efficiency (spanning modeling techniques, infrastructure, and hardware) and the seminal work there. We also present an experiment-based guide along with code for practitioners to optimize their model training and deployment. We believe this is the first comprehensive survey in the efficient deep learning space that covers the landscape of model efficiency from modeling techniques to hardware support. It is our hope that this survey would provide readers with the mental model and the necessary understanding of the field to apply generic efficiency techniques to immediately get significant improvements, and also equip them with ideas for further research and experimentation to achieve additional gains.

深度学习已经革新了计算机视觉、自然语言理解、语音识别、信息检索等领域。然而，随着深度学习模型的不断改进，它们的参数数量、延迟和训练所需的资源等都显著增加了。因此，关注模型的这些指标也变得很重要，而不仅仅是它们的质量。我们介绍并激励了深度学习中的效率问题，然后对模型效率的五个核心领域（涵盖建模技术、基础设施和硬件）及其里面的重要工作进行了详尽的调查。我们还提供了一个基于实验的指南以及代码，供实践者优化他们的模型训练和部署。我们相信这是第一篇全面的高效深度学习调查，涵盖了从建模技术到硬件支持的模型效率的全景。我们希望这篇调查能够为读者提供该领域的思维模型和必要的理解，以便应用通用的效率技术立即获得显著的改进，并为进一步的研究和实验提供思路，以实现额外的收益。

## 1 INTRODUCTION

## 2 A MENTAL MODEL

## 3 LANDSCAPE OF EFFICIENT DEEP LEARNING

## 4 A PRACTITIONER’S GUIDE TO EFFICIENCY

## 5 FUTURE TRENDS

## 6 CONCLUSION

In this article, we started with demonstrating the rapid growth in deep learning models, and motivating the fact that someone training and deploying models today has to make either implicit or explicit decisions about efficiency. However, the landscape of model efficiency is vast.

在这篇文章中，我们从展示深度学习模型的快速增长开始，并激励了这样一个事实，即今天训练和部署模型的人必须对效率做出隐式或显式的决策。然而，模型效率的领域是非常广阔的。

To help with this, we laid out a mental model for the readers to wrap their heads around the multiple focus areas of model efficiency and optimization. The surveys of the core model optimization techniques give the reader an opportunity to understand the state of the art, apply these techniques in the modeling process, and/or use them as a starting point for exploration. The infrastructure section also lays out the software libraries and hardware that make training and inference of efficient models possible.

为了帮助这一点，我们为读者提供了一个思维模型，让他们能够理解模型效率和优化的多个关注领域。对核心模型优化技术的调查给了读者一个机会，了解最新的技术，将这些技术应用于建模过程中，或者将它们作为探索的起点。基础设施部分也介绍了使训练和推理高效模型成为可能的软件库和硬件。

