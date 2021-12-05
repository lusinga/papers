# A Survey on Adversarial Recommender Systems: From Attack/Defense Strategies to Generative Adversarial Networks

Latent-factor models (LFM) based on collaborative filtering (CF), such as matrix factorization (MF) and deep CF methods, are widely used in modern recommender systems (RS) due to their excellent performance and recommendation accuracy. However, success has been accompanied with a major new arising challenge: Many applications of machine learning (ML) are adversarial in nature [146]. In recent years, it has been shown that thesemethods are vulnerable to adversarial examples, i.e., subtle but non-random perturbations designed to force recommendation models to produce erroneous outputs.

基于协作过滤（CF）的潜在因子模型（LFM），例如矩阵分解（MF）和深层CF方法，由于其出色的性能和推荐准确性而被广泛用于现代推荐系统（RS）。随之而来的是一个新的重大挑战：机器学习（ML）的许多应用本质上都是对抗性的[146]。设计用于强制推荐模型产生错误的输出。

The goal of this survey is two-fold: (i) to present recent advances on adversarial machine learning (AML) for the security of RS (i.e., attacking and defense recommendation models) and (ii) to show another successful application of AML in generative adversarial networks (GANs) for generative applications, thanks to their ability for learning (high-dimensional) data distributions. In this survey, we provide an exhaustive literature reviewof 76 articles published in major RS andML journals and conferences. This reviewserves as a reference for the RS community working on the security of RS or on generative models using GANs to improve their quality.

这项调查的目的有两个：（i）展示针对RS安全性的对抗性机器学习（AML）的最新进展（即攻击和防御推荐模型），以及（ii）展示AML在以下领域的另一成功应用由于其具有学习（高维）数据分布的能力，因此用于生成应用的生成对抗网络（GAN）在本次调查中，我们对RS和ML主要期刊和会议上发表的76篇文章进行了详尽的文献综述。 RS社区致力于RS的安全性或使用GAN改进生成质量的生成模型。

## 1 INTRODUCTION

In the age of data deluge, where users face a new form of information explosion, recommender systems (RS) have emerged as a paradigm of information push to lessen decision anxieties and consumer confusion by over-choice. RS enhance users’ decision-making process and support sales by personalizing item recommendations for each user and helping them discover novel products. RS are a pervasive part of user experience online today and serve as the primary choice for many consumer-oriented companies such as Amazon, Netflix, and Google (e.g., YouTube [182]). Among different recommendation techniques, collaborative filtering (CF) methods have been the mainstream of recommendation research both in academia and industry due to their superb recommendation quality. CF builds on the fundamental assumption that users who have expressed similar interests in the past will maintain similar choices in future [59] and infers target user preference over unseen items by leveraging behavioral data of other users and exploiting similarities in their behavioral patterns.

在数据泛滥的时代，用户面临着一种新形式的信息爆炸，推荐系统 (RS) 已成为一种信息推送范式，以减少因过度选择而导致的决策焦虑和消费者困惑。 RS 通过为每个用户提供个性化的商品推荐并帮助他们发现新产品来增强用户的决策过程并支持销售。 RS 是当今在线用户体验的普遍组成部分，并且是许多面向消费者的公司的主要选择，例如亚马逊、Netflix 和谷歌（例如 YouTube [182]）。在不同的推荐技术中，协同过滤（CF）方法因其卓越的推荐质量而成为学术界和工业界推荐研究的主流。 CF 建立在一个基本假设之上，即过去表达过相似兴趣的用户将在未来保持相似的选择 [59]，并通过利用其他用户的行为数据并利用他们行为模式的相似性来推断目标用户对看不见的项目的偏好。

## 2 ADVERSARIAL MACHINE LEARNING FOR SECURITY OF RS

Poisoning attack. Data poisoning attacks are realized by injecting false data points into the training data with the goal to corrupt/degrade the model (e.g., the classifier). Poisoning attacks have been explored in the literature for a variety of tasks [146], such as (i) attacks on binary classification for tasks such as label flipping or against kernelized SVM [163], (ii) attacks on unsupervised learning such as clustering and anomaly detection [18], and (iii) attacks on matrix completion task in RS [47, 89]. As an example, in the pioneering work by Biggio et al. [17], the authors propose a poisoning attack based on properties of the SVM optimal solution that could significantly degrade the classification test accuracy.

中毒攻击。 数据中毒攻击是通过将虚假数据点注入训练数据来实现的，目的是破坏/降级模型（例如分类器）。 文献中已经针对各种任务 [146] 探讨了中毒攻击，例如 (i) 对标签翻转等任务的二元分类攻击或针对核化 SVM [163] 的攻击，(ii) 对无监督学习（例如聚类）的攻击 和异常检测 [18]，以及 (iii) 对 RS [47, 89] 中矩阵完成任务的攻击。 例如，在 Biggio 等人的开创性工作中。 [17]，作者提出了一种基于 SVM 最优解的特性的中毒攻击，这可能会显着降低分类测试的准确性。

Evasion attack. Unlike poisoning attacks, evasion attacks do not interfere with training data. They adjust malicious samples during the inference phase. These attacks are also named decision-time attacks, referring to their attempt to evade the decision made by the learned model at test time [146]. For instance, evasion attacks can be used to evade spam [76] aswell as network intrusion [151] detectors. Recently, evasive attacks are conducted by crafting adversarial examples, which are subtle but non-random human-imperceptible perturbations, added to original data to cause the learned model to produce erroneous output. Szegedy et al. [139] were the first to discover that some carefully selected perturbations that are barely perceptible to the human eye, when added to an image, could lead a well-trained DNN to misclassify the adversarial image with high confidence.

躲避攻击。 与中毒攻击不同，逃避攻击不会干扰训练数据。 他们在推理阶段调整恶意样本。 这些攻击也被称为决策时间攻击，指的是他们试图逃避学习模型在测试时做出的决策[146]。 例如，逃避攻击可用于逃避垃圾邮件 [76] 以及网络入侵 [151] 检测器。 最近，通过制作对抗性示例来进行规避攻击，这些对抗性示例是微妙但非随机的人类无法察觉的扰动，添加到原始数据中以导致学习模型产生错误的输出。 塞格迪等人。 [139] 是第一个发现一些人眼几乎察觉不到的精心挑选的扰动，当添加到图像中时，可能会导致训练有素的 DNN 以高置信度错误分类对抗性图像。

## 3 ADVERSARIAL LEARNING FOR GAN-BASED RECOMMENDATION

### 3.1 Foundations of Generative Adversarial Networks (GANs)

### 3.2 GAN-based Recommendation Framework

GANs have been successfully applied in state-of-the-art RS to learning recommendation models. Since the first pioneering GAN-basedwork IRGAN [150] in 2017,we have witnessed rapid adoption of these network architectures in many traditional and novel applications and domains. In this section, we provide a conceptual framework that will show how GANs are employed in RS domain and shed light on particularities and differences of GAN application in RecSys and ML.

GAN已成功应用于最新的RS到学习推荐模型中，自2017年首创基于GAN的工作IRGAN [150]以来，我们见证了这些网络体系结构在许多传统和新颖的应用程序和领域中的迅速采用。在本节中，我们提供一个概念框架，该框架将显示GAN如何在RS域中使用，并阐明GAN在RecSys和ML中的特殊性和差异。

GAN-CF problem formulation and conceptual model. The prominent recommendation models in the literature that successfully apply GAN [148, 150] for the CF task utilize the two-player min-max game with objective function built on top of Equation (13).

GAN-CF问题的提出和概念模型在文献中成功地将GAN [148，150]用于CF任务的著名推荐模型利用了具有方程（13）之上的目标函数的两人最小-最大博弈。

### 3.3 GAN-based Recommendation Models: State of the Art

## 4 SUMMARY AND FUTURE DIRECTIONS

In this article, we have surveyed a wide variety of tasks in which adversarial machine learning (AML) is important to attack/defend a recommendation model as well as improve the generalization performance of the model itself. This broad range of applications can be categorized into two—objective-wise distinct—technologies: (i) AML for improving security (cf. Section 2) and (ii) AML used in generative adversarial networks (GANs) exploited for numerous tasks such as better CF recommendation, context-aware recommendation, cross-domain system, or visually aware fashion item/outfit recommendation (cf. Section 3). The common point of both technologies is the joint min-max optimization used for training models, in which two competing players play a zerosum differential game until they reach an equilibrium. To the best of our knowledge, this is the first work that sums up the advances of AML application in recommendation settings and proposes a clear taxonomy to classify such applications.

在本文中，我们调查了各种各样的任务，其中对抗性机器学习（AML）对于攻击/捍卫推荐模型以及提高模型本身的泛化性能很重要。两种是客观的，截然不同的技术：（i）用于提高安全性的AML（请参阅第2节），以及（ii）用于生成对抗网络（GAN）的AML用于许多任务，例如更好的CF推荐，上下文感知推荐，跨域系统或视觉上看得见的时尚商品/服装推荐（请参阅第3节）两种技术的共同点是用于训练模型的联合最小-最大优化，其中两个竞争的玩家玩零和差分游戏直到他们据我们所知，这是第一篇总结AML在推荐设置中的应用并提出明确分类法以对此类应用进行分类的工作。

