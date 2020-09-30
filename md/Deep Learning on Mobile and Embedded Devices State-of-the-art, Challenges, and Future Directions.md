# Deep Learning on Mobile and Embedded Devices: State-of-the-art, Challenges, and Future Directions

Recent years have witnessed an exponential increase in the use of mobile and embedded devices. With the great success of deep learning in many fields, there is an emerging trend to deploy deep learning on mobile and embedded devices to better meet the requirement of real-time applications and user privacy protection. However, the limited resources of mobile and embedded devices make it challenging to fulfill the intensive computation and storage demand of deep learning models. In this survey,we conduct a comprehensive review on the related issues for deep learning on mobile and embedded devices.We start with a brief introduction of deep learning and discuss major challenges of implementing deep learning models on mobile and embedded devices. We then conduct an in-depth survey on important compression and acceleration techniques that help adapt deep learning models to mobile and embedded devices, which we specifically classify as pruning, quantization, model distillation, network design strategies, and low-rank factorization. We elaborate on the hardware-based solutions, including mobile GPU, FPGA, and ASIC, and describe software frameworks for mobile deep learning models, especially the development of frameworks based on OpenCL and RenderScript. After that, we present the application of mobile deep learning in a variety of areas, such as navigation, health, speech recognition, and information security. Finally, we discuss some future directions for deep learning on mobile and embedded devices to inspire further research in this area.

近年来，移动和嵌入式设备的使用呈指数级增长。随着深度学习在许多领域的巨大成功，将深度学习部署到移动和嵌入式设备上，以更好地满足实时应用和用户隐私保护的需求成为一种新兴趋势。然而，移动和嵌入式设备有限的资源使得深度学习模型的密集计算和存储需求难以满足。在这次调查中，我们对在移动和嵌入式设备上进行深度学习的相关问题进行了全面的回顾。我们首先简要介绍了深度学习，并讨论了在移动设备和嵌入式设备上实现深度学习模型的主要挑战。然后，我们对帮助深度学习模型适应移动和嵌入式设备的重要压缩和加速技术进行了深入调查，我们具体地将其分类为修剪、量化、模型蒸馏、网络设计策略和低秩因数分解。我们详细阐述了基于硬件的解决方案，包括移动GPU、FPGA、ASIC，并描述了移动深度学习模型的软件框架，特别是基于OpenCL和RenderScript的框架的开发。之后，我们介绍了移动深度学习在各个领域的应用，如导航、健康、语音识别和信息安全。最后，我们讨论了在移动设备和嵌入式设备上进行深度学习的一些未来方向，以激发在这一领域的进一步研究。

## 1 INTRODUCTION

## 2 BACKGROUND

### 2.1 Deep Learning

### 2.2 Challenges

## 3 NETWORK COMPRESSION AND ACCELERATION TECHNIQUE

## 4 HARDWARE

### 4.1 Hardware Solutions

### 4.2 Mobile GPU

### 4.3 FPGA

## 5 SOFTWARE FRAMEWORKS

## 6 APPLICATIONS

## 7 DISCUSSION: PRIVACY ISSUES FOR MOBILE DEEP LEARNING

## 8 FUTURE DIRECTION

## 9 CONCLUSION

In this article, we provided a comprehensive survey of the state-of-the-art research on deep learning on mobile and embedded devices. We introduced relevant background information and discussed the challenges of implementing deep learning models on mobile and embedded devices. We presented a comprehensive survey of existing research that addressed the problem related to mobile deep learning from the aspects of compression & acceleration techniques, hardwares, and software frameworks. Current applications of deep learning on mobile deviceswere also described. From our investigation, we highlighted future directions that are critical to the success of mobile deep learning.With the rapid progress in deep learning and mobile/embedded devices, we believe that mobile deep learning will bring profound transformations in the near future.

在这篇文章中，我们提供了一个关于移动和嵌入式设备上的深度学习的最新研究的全面调查。我们介绍了相关的背景信息，并讨论了在移动和嵌入式设备上实现深度学习模型的挑战。我们从压缩和加速技术、硬件和软件框架等方面，对解决移动深度学习相关问题的现有研究进行了全面调查。本文还描述了当前深度学习在移动设备上的应用。从我们的调查中，我们强调了移动深度学习成功的关键未来方向。随着深度学习和移动/嵌入式设备的快速发展，我们相信在不久的将来，移动深度学习将带来深刻的变革。