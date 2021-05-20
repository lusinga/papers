# A Deep Journey into Super-resolution: A Survey

Deep convolutional networks–based super-resolution is a fast-growing field with numerous practical applications. In this exposition, we extensively compare more than 30 state-of-the-art super-resolution Convolutional Neural Networks (CNNs) over three classical and three recently introduced challenging datasets to benchmark single image super-resolution. We introduce a taxonomy for deep learning–based super-resolution networks that groups existing methods into nine categories including linear, residual, multi-branch, recursive, progressive, attention-based, and adversarial designs. We also provide comparisons between the models in terms of network complexity, memory footprint, model input and output, learning details, the type of network losses, and important architectural differences (e.g., depth, skip-connections, filters). The extensive evaluation performed shows the consistent and rapid growth in the accuracy in the past few years along with a corresponding boost in model complexity and the availability of large-scale datasets. It is also observed that the pioneering methods identified as the benchmarks have been significantly outperformed by the current contenders. Despite the progress in recent years, we identify several shortcomings of existing techniques and provide future research directions towards the solution of these open problems. Datasets and codes for evaluation are publicly available at https://github.com/saeed-anwar/SRsurvey.

基于深度卷积网络的超分辨率是一个快速发展的领域，具有许多实际应用。在本次博览会中，我们广泛比较了30多种最新的超分辨率卷积神经网络（CNN）与三个经典的和最近引入的三个具有挑战性的数据集，以对单个图像的超分辨率进行基准测试。我们为基于深度学习的超分辨率网络引入了一种分类法，该分类法将现有方法分为九类，包括线性，残差，多分支，递归，渐进，基于注意力和对抗性设计。我们还根据网络复杂性，内存占用量，模型输入和输出，学习细节，网络损耗的类型以及重要的架构差异（例如深度，跳过连接，过滤器）在模型之间进行比较。进行的广泛评估表明，在过去几年中，准确性一直持续且快速增长，同时模型复杂性和大规模数据集的可用性也相应提高。还可以观察到，被确定为基准的开拓性方法在当前竞争者中的表现明显优于其他方法。尽管近年来取得了进步，但我们发现了现有技术的一些缺点，并为解决这些未解决的问题提供了未来的研究方向。评估的数据集和代码可在https://github.com/saeed-anwar/SRsurvey上公开获得。

## 1 INTRODUCTION

Image Super-Resolution (SR) has received increasing attention from the research community in recent years. Super-resolution aims to convert a given low-resolution image with coarse details to a corresponding high-resolution image with better visual quality and refined details. Image super resolution is also referred to by other names such as image scaling, interpolation, upsampling, zooming, and enlargement. The process of generating a raster image with higher resolution can be performed using a single image or multiple images. Due to practical considerations, this exposition mainly focuses on Single Image Super-Resolution (SISR), which has been extensively studied due to its challenging nature. For SR in higher dimensional inputs (such as videos and 3D scans), we refer the reader to recent seminal works [37, 51, 79, 83, 88].

图像超分辨率(SR)近年来越来越受到研究领域的关注。超分辨率是指将给定的具有粗糙细节的低分辨率图像转换为具有更好视觉质量和细化细节的高分辨率图像。图像超分辨率还有其他名称，如图像缩放、插值、上采样、缩放和放大。生成具有较高分辨率的栅格图像的过程可以使用单个图像或多个图像来执行。由于实际的考虑，本文主要讨论了单图像超分辨率(SISR)，由于其具有挑战性而得到了广泛的研究。对于高维输入中的SR(如视频和3D扫描)，我们建议读者参考最近的重要作品[37,51,79,83,88]。

High-resolution images provide improved reconstructed details of the scenes and constituent objects, which are critical for many devices, such as large computer displays, HD television sets, and hand-held devices (mobile phones, tablets, cameras, etc.). Furthermore, super-resolution has important applications in many other domains, e.g., object detection in scenes [23] (particularly small objects [4]), face recognition in surveillance videos [64], medical imaging [25], improving interpretation of images in remote sensing [53], astronomical images [55], and forensics [80].

高分辨率图像提供了场景和组成物体的改进重建细节，这对许多设备来说是至关重要的，比如大型计算机显示器、高清电视机和手持设备(手机、平板电脑、相机等)。此外，超分辨率在许多其他领域有重要的应用，如场景[23]中的目标检测(特别是小目标[4])，监控视频中的人脸识别[64]，医学成像[25]，提高遥感[53]、天文图像[55]和法医[80]。

Super-resolution is a classical problem that is still considered a challenging and open research problem in computer vision due to several reasons. First, SR is an ill-posed inverse problem, i.e., an under-determined case. Instead of a single unique solution, there exist multiple solutions for the same low-resolution image. To constrain the solution-space, reliable prior information is typically required. Second, the complexity of the problem increases as the upscaling factor increases. At higher factors, the recovery of missing scene details becomes even more complex, and consequently it often leads to reproduction of wrong information. Furthermore, assessment of the quality of output is not straightforward, i.e., quantitative metrics (e.g., PSNR, SSIM) only loosely correlate to human perception.

超分辨率问题是计算机视觉领域的一个经典问题，由于多种原因，目前仍被认为是一个具有挑战性和开放性的研究问题。首先，SR是一个不适定逆问题，即欠定情况。对于同一幅低分辨率图像，存在多个解，而不是一个唯一解。为了约束解空间，通常需要可靠的先验信息。第二，问题的复杂性随着升级因子的增加而增加。在较高的因素下，缺失场景细节的恢复变得更加复杂，因而往往导致错误信息的再现。此外，对输出质量的评估不是简单的，即定量度量(例如，PSNR, SSIM)只与人类的感知松散相关。

Super-resolution methods can be broadly divided into two main categories: traditional and deep learning methods. Classical algorithms have been around for decades now, but are out-performed by their deep learning–based counterparts. Therefore, most recent algorithms rely on data-driven deep learning models to reconstruct the required details for accurate super-resolution. Deep learning is a branch ofmachine learning that aims to automatically learn the relationship between input and output directly from the data. Alongside SR, deep learning algorithms have shown promising results on other sub-fields in artificial intelligence [40], such as object classification [28] and detection [71], natural language processing [11, 45], image processing [3, 50], and audio signal processing [12]. Due to these reasons, in this survey, we mainly focus on deep learning algorithms for SR and only provide a brief background on traditional approaches (Section 2).

超分辨率方法大致可分为两大类:传统方法和深度学习方法。经典算法已经存在了几十年，但是它们的性能被基于深度学习的同类算法所超越。因此，大多数最新的算法都依赖于数据驱动的深度学习模型来重建所需的细节，以获得精确的超分辨率。深度学习是机器学习的一个分支，旨在直接从数据中自动学习输入和输出之间的关系。除了SR，深度学习算法在人工智能[40]的其他子领域也显示出了很好的结果，如对象分类[28]和检测[71]、自然语言处理[11,45]、图像处理[3,50]和音频信号处理[12]。由于这些原因，在本研究中，我们主要关注SR的深度学习算法，仅提供传统方法的简要背景(第2节)。

Our Contributions: In this exposition, our focus is on deep neural networks for single (natural) image super-resolution. Our contribution is five-fold: (1)We provide a thorough review of the recent techniques for image super-resolution. (2) We introduce a new taxonomy of the SR algorithms based on their structural differences. (3) A comprehensive analysis is performed based on the number of parameters, algorithm settings, training details, and important architectural innovations that leads to significant performance improvements. (4) We provide a systematic evaluation of algorithms on six publicly available datasets for SISR. (5)We discuss the challenges and provide insights into the possible future directions.

我们的贡献:在这篇论述中，我们的重点是用于单一(自然)图像超分辨率的深度神经网络。我们的贡献是五方面的:(1)我们提供了一个全面的回顾最近的图像超分辨率技术。(2)针对SR算法的结构差异，提出了一种新的分类方法。(3)根据参数的数量、算法设置、训练细节和导致显著性能改进的重要架构创新进行综合分析。(4)在6个公开数据集上对SISR算法进行了系统评价。(5)我们讨论了面临的挑战，并对未来可能的方向提供了见解。

## 2 BACKGROUND

## 3 SINGLE IMAGE SUPER-RESOLUTION

The SISR problem has been extensively studied in the literature using a variety of deep learning– based techniques. We categorize existing methods into nine groups according to the most distinctive features in their model designs. The overall taxonomy used in this literature is shown in Figure 1. Among these, we begin discussion with the earliest and simplest network designs that are called the linear networks.

SISR问题在文献中已经被广泛研究，使用了多种基于深度学习的技术。我们将现有的方法根据其模型设计中最显著的特点分为九组。本文献中使用的总体分类如图1所示。其中，我们开始讨论最早和最简单的网络设计，称为线性网络。

### 3.1 Linear Networks

Linear networks have a simple structure consisting of only a single path for signal flow without any skip connections or multiple branches. In such network designs, several convolution layers are stacked on top of each other and the input flows sequentially from initial to later layers. Linear networks differ in the way the upsampling operation is performed, i.e., early upsampling or late upsampling. Note that some linear networks learn to reproduce the residual image, i.e., the difference between the LR and HR images [41, 99, 100]. Since the network architecture is linear in such cases, we categorize them as linear networks. This is as opposed to residual networks that have skip connections in their design (Section 3.2). We elaborate on notable linear network designs in these two sub-categories below.

线性网络结构简单，只有一条信号流路径，不存在跳跃连接或多个分支。在这种网络设计中，几个卷积层相互堆叠，输入流按顺序从初始层到后续层。线性网络执行上采样操作的方式不同，有早上采样和晚上采样。需要注意的是，一些线性网络可以学习复制残差图像，即LR和HR图像之间的差异[41,99,100]。由于这种情况下的网络结构是线性的，所以我们将其归类为线性网络。这与在设计中有跳过连接的剩余网络相反(第3.2节)。下面我们将详细阐述这两个子类别中值得注意的线性网络设计。

### 3.2 Residual Networks

In contrast to linear networks, residual learning uses skip connections in the network design to avoid gradients vanishing and makes it feasible to design very deep networks. Its significance was first demonstrated for the image classification problem [28]. Recently, several networks [1, 54] provided a boost to SR performance using residual learning. In this approach, algorithms learn residue i.e., the high-frequencies between the input and ground-truth. Based on the number of stages used in such networks, we categorize existing residual learning approaches into singlestage [1, 54] and multi-stage networks [18, 36, 60].

与线性网络相比，残差学习在网络设计中使用跳跃连接来避免梯度消失，使设计非常深的网络成为可能。它的意义首先体现在图像分类问题[28]上。最近，一些网络[1,54]利用残差学习提高了SR性能。在这种方法中，算法学习剩余，即输入和ground-truth之间的高频。根据这些网络使用的阶段数，我们将现有的残差学习方法分为单阶段[1,54]和多阶段网络[18,36,60]。

### 3.3 Recursive Networks

As the name indicates, recursive networks [81, 82] either employ recursively connected convolutional layers or recursively linked units. The main motivation behind these designs is to progressively break down the harder SR problem into a set of simpler ones that are easy to solve. The basic architecture is shown in Figure 2, and we provide further details of recursive models in the following sections.

顾名思义，递归网络[81,82]要么采用递归连接的卷积层，要么采用递归连接的单元。这些设计背后的主要动机是将更难的SR问题逐步分解成一组容易解决的更简单的问题。基本架构如图2所示，我们将在下面的部分中提供递归模型的进一步细节。

### 3.4 Progressive Reconstruction Designs

Typically, CNN algorithms predict the output in one step; however, it may not be feasible for large
scaling factors. To deal with large factors, some algorithms [47, 91] predict the output in multiple
steps, i.e., 2× followed by 4× and so on. Here, we introduce such algorithms.

通常，CNN算法一步预测输出;然而，它可能不可行的大尺度因子。为了处理大的因素，一些算法[47,91]采用多步预测输出，即2×接着4×等。这里，我们将介绍这些算法。

### 3.5 Densely Connected Networks

Inspired by the success of the DenseNet [31] architecture for image classification, super-resolution algorithms based on densely connected CNN layers have been proposed to improve performance. The main motivation in such a design is to combine hierarchical cues available along the network depth to achieve high flexibility and richer feature representations. We discuss some popular designs in this category below.

受DenseNet[31]架构成功用于图像分类的启发，提出了基于密集连接CNN层的超分辨率算法来提高性能。这种设计的主要动机是将沿网络深度可获得的层次线索结合起来，以实现高灵活性和更丰富的特征表示。下面我们将讨论一些流行的设计。

### 3.6 Multi-branch Designs

In contrast to single-stream (linear) and skip-connection-based designs, multi-branch networks aim to obtain a diverse set of features at multiple context scales. Such complementary information is then fused to obtain better HR reconstructions. This design also enables a multi-path signal flow, leading to better information exchange in forward-backward steps during training. Multibranch designs are becoming common in several other computer vision tasks as well. We explain multi-branch networks in the section below.

与单流(线性)和基于跨连接的设计相比，多分支网络的目标是在多个上下文尺度上获得一组不同的特征。然后融合这些互补信息以获得更好的人力资源重构。该设计还可以实现多路径信号流，从而在训练过程中实现更好的前后步信息交换。多分支设计在其他几个计算机视觉任务中也变得普遍。我们将在下面的章节中解释多分支网络。

### 3.7 Attention-based Networks

The previously discussed network designs consider all spatial locations and channels to have a uniform importance for the super-resolution. In several cases, it helps to selectively attend to only a few features at a given layer. Attention-based models [10, 103] allow this flexibility and consider that not all the features are essential for super-resolution but have varying importance. Coupled with deep networks, recent attention-based models have shown significant improvements for SR. Following are the examples of CNN algorithms using attention mechanisms.

前面讨论的网络设计考虑所有空间位置和通道对超分辨率具有统一的重要性。在某些情况下，它有助于有选择地只关注给定层的少数特性。基于注意力的模型[10,103]允许这种灵活性，并考虑到并非所有特征都是超分辨率的必要条件，但其重要性各不相同。再加上深度网络，最近基于注意力的模型显示了对sr的显著改进。以下是CNN算法使用注意机制的例子。

### 3.8 Multiple-degradation Handling Networks

The super-resolution networks discussed so far (e.g., References [15, 41]) consider bicubic degradations. However, in reality, this may not be a feasible assumption, as multiple degradations can simultaneously occur. To deal with such real-world scenarios, the following methods are proposed.

迄今为止讨论的超分辨率网络(如文献[15,41])考虑了双三次退化。然而，在现实中，这可能不是一个可行的假设，因为多重降解可能同时发生。为了处理这样的现实场景，我们提出以下方法。

### 3.9 GAN Models

Generative Adversarial Networks (GAN) [24, 69] employ a game-theoretic approach where two components of the model—namely, a generator and discriminator—try to fool the later. The generator creates SR images that a discriminator cannot distinguish as a real HR image or an artificially super-resolved output. In this manner, HR images with better perceptual quality are generated. The corresponding PSNR values are generally degraded, which highlights the problem that prevalent quantitative measures in SR literature do not encapsulate perceptual soundness of generated HR outputs. The super-resolution methods [49, 73] based on the GAN framework are explained next.

生成式对抗网络(GAN)[24,69]采用了一种博弈论方法，其中模型的两个组成部分——即生成器和鉴别器——试图欺骗后者。生成器创建的SR图像，鉴别器不能区分为真实的HR图像或人工超分辨输出。通过这种方法，可以生成较好的感知质量的HR图像。相应的PSNR值通常是退化的，这突出了在SR文献中流行的定量测度没有封装生成的人力资源输出的知觉稳健性的问题。下面介绍基于GAN框架的超分辨率方法[49,73]。

## 4 EXPERIMENTAL EVALUATION

## 5 SUPER-RESOLUTION COMPETITIONS

## 6 FUTURE DIRECTIONS/OPEN PROBLEMS

Although deep networks have shown exceptional performance on the super-resolution task, there remain several open research questions.We outline some of these future research directions below.

尽管深层网络在超分辨率任务上表现出卓越的性能，但仍然存在一些开放的研究问题。

Incorporation of Priors: Current deep networks for SR are data-driven models that are learned in an end-to-end fashion. While this approach has shown excellent results in general, it proves to be sub-optimal when a particular class of degradation occurs for which a large amount of training data is non-existent (e.g., in medical imaging). In such cases, if the information about the sensor, imaged object/scene, and acquisition conditions is known, useful priors can be designed to obtain high-resolution images. Recent works focusing on this direction have proposed both deep network [87] and sparse coding [17]-based priors for better super-resolution.

融合先验:目前SR的深度网络是数据驱动的模型，可以端到端学习。虽然这种方法在一般情况下显示了良好的结果，但当出现不存在大量训练数据的特定类别的退化时(例如，在医学成像中)，它被证明是次优的。在这种情况下，如果已知传感器、成像目标/场景的信息和获取条件，就可以设计有用的先验来获得高分辨率图像。最近针对这一方向的工作提出了深度网络[87]和基于[17]的稀疏编码先验，以获得更好的超分辨率。

Objective Functions and Metrics: Existing SR approaches predominantly use pixel-level error measures, e.g., 1 and 2 distances or a combination of both [85]. Since these measures only encapsulate local pixel-level information, the resulting images do not always provide perceptually sound results. As an example, it has been shown that images with high PSNR and SSIM values give overly smooth images with low perceptual quality [7]. To counter this issue, several perceptual loss measures have been proposed in the literature. The conventional perceptual metrics were fixed, e.g., SSIM [90], multi-scale SSIM [92], while more recent ones are learned to model human perception of images, e.g., LPIPS [102] and PieAPP [67]. Each of these measures has its own failure cases. As a result, there is no universal perceptual metric that optimally works in all conditions and perfectly quantifies the image quality. Therefore, the development of new objective functions is an open research problem. To encourage the development in this area, a dedicated challenge and workshop have been organized for perceptually sound image super-resolution approaches (PIRM [7]).

目标函数和度量:现有的SR方法主要使用像素级误差度量，例如1和2距离或两者的结合[85]。由于这些措施只封装局部像素级信息，结果图像并不总是提供感知良好的结果。例如，高PSNR和SSIM值的图像给出了低感知质量[7]的过度平滑图像。为了解决这个问题，文献中提出了几个知觉丧失的措施。传统的感知度量是固定的，如SSIM[90]，多尺度SSIM[92]，而最近的度量是学习来建模人类对图像的感知，如LPIPS[102]和PieAPP[67]。每一种措施都有其失败的案例。因此，没有一个通用的感知度量能在所有条件下最优地工作，并完美地量化图像质量。因此，开发新的目标函数是一个开放性的研究问题。为了鼓励这一领域的发展，已经组织了一个专门的挑战和研讨会，研究感知声音图像超分辨率方法(PIRM[7])。

Need for Unified Solutions: Two or more degradations often happen simultaneously in reallife situations. An important consideration in such cases is how to jointly recover images with higher resolution, low noise, and enhanced details. Current models developed for SR are generally restricted to only one case and suffer in the presence of other degradations. Furthermore, problemspecific models differ in their architectures, loss functions, and training details. It is a challenge to design unified models that perform well for several low-level vision tasks, simultaneously [101].

对统一解决方案的需求:在现实生活中，经常会同时发生两种或两种以上的降级。在这种情况下，一个重要的考虑是如何联合恢复高分辨率、低噪声和增强的细节图像。目前为SR开发的模型通常仅限于一种情况，并在存在其他退化的情况下受到影响。此外，特定于问题的模型在架构、损失函数和训练细节上也有所不同。设计统一的模型来同时处理多个低层次的视觉任务是一个挑战[101]。

Unsupervised Image SR: Models discussed in this survey generally consider LR-HR image pairs to learn a super-resolution mapping function. One interesting direction is to explore how SR can be performed for cases where corresponding HR images are not available. One solution to this problem is Zero-shot SR [77], which learns the SR model on a further downsampled version of a given image. However, when an input image is already of poor resolution, this solution cannot work. The unsupervised image SR aims to solve this problem by learning a function from unpaired LR-HR image sets [96]. Such a capability is very useful for real-life settings, since it is not trivial to obtain matched HR images in several cases.

无监督图像SR:本研究中讨论的模型一般考虑LR-HR图像对来学习超分辨率映射函数。一个有趣的方向是探索如何在没有相应HR图像的情况下执行SR。这个问题的一个解决方案是零拍SR[77]，它在一个给定图像的进一步降采样版本上学习SR模型。然而，当输入图像的分辨率已经很低时，这种解决方案不能工作。无监督图像SR旨在通过从未配对的LR-HR图像集学习函数来解决这个问题[96]。这种功能对于现实生活设置非常有用，因为在一些情况下获取匹配的HR图像并不简单。

Higher SR rates: Current SR models generally do not tackle extreme super-resolution, which
can be useful for cases such as super-resolving faces in crowd scenes. Very few works target SR
rates higher than 8× (e.g., 16× and 32×) [47]. In such extreme upsampling conditions, it becomes
challenging to preserve accurate local details in the image. Further, an open question is how to
preserve high perceptual quality in these super-resolved images.

更高的SR率:当前的SR模型通常不能解决极端的超分辨率问题，这对于人群场景中的超分辨率人脸非常有用。很少作品的SR率高于8×[47](例如，16×和32×)。在如此极端的上采样条件下，在图像中保持精确的局部细节变得具有挑战性。此外，一个有待解决的问题是如何在这些超分辨率图像中保持高感知质量。

Arbitrary SR rates: In practical scenarios, it is often not known which upsampling factor is the optimal one for a given input. When the downsampling factor is not known for all the images in the dataset, it becomes a significant challenge during training, since it becomes hard for a single model to encapsulate several levels of details. In such cases, it is important to first characterize the level of degradation before training and performing inference through a specified SR model.

任意SR率:在实际情况中，对于给定的输入，通常不知道哪个上采样因子是最优的。当不知道数据集中所有图像的降采样因子时，这在训练过程中成为一个重大挑战，因为单个模型很难封装多个层次的细节。在这种情况下，在通过指定的SR模型进行训练和执行推理之前，首先确定退化级别是很重要的。

Real vs. Artificial Degradation: Existing SR works mostly use a bicubic interpolation to generate LR images. Actual LR images that are encountered in real-world scenarios have a totally different distribution compared to the ones generated synthetically using bicubic interpolation. As a result, SR networks trained on artificially created degradations do not generalize well to actual LR images in practical scenarios. One recent effort towards the solution of this problem first learns a GAN to model the real-world degradation [8]. Another recent effort proposes to enrich features by preserving the original spatial resolution and exchanging multi-scale information along the feature processing path for real image SR [97]. Recently, an extensive challenge was organized for real-image super-resolution in CVPR’19 to promote development on this crucial research problem [8, 65].

真实与人工退化:现有的SR工作大多使用双三次插值来生成LR图像。真实场景中遇到的LR图像与双三次插值合成得到的图像分布完全不同。因此，经过人工退化训练的SR网络在实际场景中不能很好地推广到实际LR图像。最近针对这个问题的一项研究首先学习了一种GAN来模拟真实世界的退化[8]。最近的另一项研究提出，在真实图像SR中，通过保留原始的空间分辨率和沿特征处理路径交换多尺度信息来丰富特征[97]。最近，CVPR’19组织了一项关于真实图像超分辨率的广泛挑战，以促进这一关键研究问题的发展[8,65]。

## 7 CONCLUSIONS

Single-image super-resolution is a challenging research problem with important real-life applications. The phenomenal success of deep learning approaches has resulted in rapid growth in deep convolutional network-based techniques for image super-resolution. A diverse set of approaches has been proposed with exciting innovations in network architectures and learning methodologies. This survey provides a comprehensive analysis of existing deep learning–based methods for superresolution. Through extensive quantitative and qualitative comparisons, we note the following trends in the existing art: (a) GAN-based approaches generally deliver visually pleasing outputs, while the reconstruction error–based methodsmore accurately preserve spatial details in an image; (b) for the case of highmagnification rates (8× or above), the existing models generally deliver suboptimal results; (c) the top-performing methods generally have higher computational complexity and are deeper than their counterparts; (d) residual learning has been a major contributing factor for performance improvement due to its signal decomposition that makes the learning task easier. Overall, we note that the super-resolution performance has been greatly enhanced in recent years, with a corresponding increase in the network complexity. Remarkably, the state-of-the-art approaches still suffer from limitations that restrict their application to vital real-world scenarios (e.g., inadequate metrics, high model complexity, inability to handle real-life degradations). We hope this survey will attract new efforts towards the solution of these crucial problems.

对于重要的现实应用，单图像超分辨率是一个具有挑战性的研究问题。深度学习方法的巨大成功已导致基于深度卷积网络的图像超分辨率技术迅速发展。已经提出了多种方法，并在网络体系结构和学习方法上进行了令人兴奋的创新。这项调查提供了对现有基于深度学习的超分辨率方法的全面分析。通过广泛的定量和定性比较，我们注意到现有技术的以下趋势：（a）基于GAN的方法通常可提供视觉上令人愉悦的输出，而基于重建误差的方法可更准确地保留图像中的空间细节； （b）对于高放大倍率（8倍或更高）的情况，现有模型通常不能达到最佳效果； （c）表现最好的方法通常具有更高的计算复杂度，并且比同类方法更深； （d）残差学习由于其信号分解使学习任务更容易而成为提高性能的主要因素。总体而言，我们注意到，近年来，超分辨率性能已得到极大提高，网络复杂性也相应增加。值得注意的是，最新方法仍然受到限制，将其应用限制在重要的现实场景中（例如，度量标准不足，模型复杂性高，无法处理实际退化）。我们希望这项调查将吸引新的努力来解决这些关键问题。
