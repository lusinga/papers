# Deep Learning on Mobile and Embedded Devices: State-of-the-art, Challenges, and Future Directions

Recent years have witnessed an exponential increase in the use of mobile and embedded devices. With the great success of deep learning in many fields, there is an emerging trend to deploy deep learning on mobile and embedded devices to better meet the requirement of real-time applications and user privacy protection. However, the limited resources of mobile and embedded devices make it challenging to fulfill the intensive computation and storage demand of deep learning models. In this survey,we conduct a comprehensive review on the related issues for deep learning on mobile and embedded devices.We start with a brief introduction of deep learning and discuss major challenges of implementing deep learning models on mobile and embedded devices. We then conduct an in-depth survey on important compression and acceleration techniques that help adapt deep learning models to mobile and embedded devices, which we specifically classify as pruning, quantization, model distillation, network design strategies, and low-rank factorization. We elaborate on the hardware-based solutions, including mobile GPU, FPGA, and ASIC, and describe software frameworks for mobile deep learning models, especially the development of frameworks based on OpenCL and RenderScript. After that, we present the application of mobile deep learning in a variety of areas, such as navigation, health, speech recognition, and information security. Finally, we discuss some future directions for deep learning on mobile and embedded devices to inspire further research in this area.

近年来，移动和嵌入式设备的使用呈指数增长。随着深度学习在许多领域的巨大成功，出现了一种在移动和嵌入式设备上部署深度学习以更好地满足实时应用程序和用户隐私保护要求的趋势。但是，移动和嵌入式设备的有限资源使其难以满足深度学习模型的密集计算和存储需求。在本次调查中，我们对移动和嵌入式设备上的深度学习的相关问题进行了全面回顾。我们首先简要介绍了深度学习，并讨论了在移动和嵌入式设备上实现深度学习模型的主要挑战。然后，我们对重要的压缩和加速技术进行了深入调查，这些技术有助于使深度学习模型适应移动和嵌入式设备，我们将其具体分类为修剪，量化，模型提炼，网络设计策略和低阶分解。我们详细介绍了基于硬件的解决方案，包括移动GPU，FPGA和ASIC，并描述了用于移动深度学习模型的软件框架，尤其是基于OpenCL和RenderScript的框架的开发。之后，我们介绍了移动深度学习在导航，健康，语音识别和信息安全等各个领域的应用。最后，我们讨论了在移动和嵌入式设备上进行深度学习的一些未来方向，以激发该领域的进一步研究。

## 1 INTRODUCTION

## 2 BACKGROUND

## 3 NETWORK COMPRESSION AND ACCELERATION TECHNIQUE

## 4 HARDWARE

## 5 SOFTWARE FRAMEWORKS

## 6 APPLICATIONS

## 7 DISCUSSION: PRIVACY ISSUES FOR MOBILE DEEP LEARNING

## 8 FUTURE DIRECTION

The emerging trend to deploy deep learning models on mobile and embedded devices has attracted the attention of both academia and industry. However, we are still facing many challenges. In this section, we discuss some promising future directions in this field.

在移动和嵌入式设备上部署深度学习模型的新兴趋势吸引了学术界和行业的关注。 但是，我们仍然面临许多挑战。 在本节中，我们讨论该领域中一些有希望的未来方向。

- Adaptive/automatic compression. 

Current network compression and acceleration techniques involve many hyperparameters, such as the threshold of pruning and the bitwidth of linear quantization, which need to be determined based on empirical experiments and expert knowledge, which poses great pressure on mobile application developers. Additionally, it is critical to retrain the network by fine-tuning, which requires expert knowledge and considerable experiments. It is a promising direction to have adaptive/automatic compression and acceleration methods and tools, not relying on manual design, which can be readily used by developers. To the best of our knowledge, some explorations have begun [66].

当前的网络压缩和加速技术涉及许多超参数，例如修剪的阈值和线性量化的位宽，需要根据经验实验和专家知识来确定，这给移动应用程序开发人员带来了巨大压力。 此外，通过微调来重新训练网络至关重要，这需要专业知识和大量实验。 拥有不依赖于手动设计的自适应/自动压缩和加速方法和工具是一个有希望的方向，开发人员可以轻松地使用它们。 据我们所知，一些探索已经开始[66]。

## 9 CONCLUSION

In this article, we provided a comprehensive survey of the state-of-the-art research on deep learning on mobile and embedded devices. We introduced relevant background information and discussed the challenges of implementing deep learning models on mobile and embedded devices. We presented a comprehensive survey of existing research that addressed the problem related to mobile deep learning from the aspects of compression & acceleration techniques, hardwares, and software frameworks. Current applications of deep learning on mobile deviceswere also described. From our investigation, we highlighted future directions that are critical to the success of mobile deep learning.With the rapid progress in deep learning and mobile/embedded devices, we believe that mobile deep learning will bring profound transformations in the near future.

在本文中，我们提供了有关移动和嵌入式设备上深度学习的最新研究的全面综述。 我们介绍了相关的背景信息，并讨论了在移动和嵌入式设备上实现深度学习模型的挑战。 我们对现有研究进行了全面的调查，从压缩和加速技术，硬件和软件框架等方面解决了与移动深度学习有关的问题。 还描述了深度学习在移动设备上的当前应用。 通过我们的调查，我们强调了对移动深度学习成功至关重要的未来方向。随着深度学习和移动/嵌入式设备的快速发展，我们相信移动深度学习将在不久的将来带来深刻的变革。
