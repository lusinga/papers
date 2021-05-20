# A Survey on Adversarial Recommender Systems: From Attack/Defense Strategies to Generative Adversarial Networks

Latent-factor models (LFM) based on collaborative filtering (CF), such as matrix factorization (MF) and deep CF methods, are widely used in modern recommender systems (RS) due to their excellent performance and recommendation accuracy. However, success has been accompanied with a major new arising challenge: Many applications of machine learning (ML) are adversarial in nature [146]. In recent years, it has been shown that thesemethods are vulnerable to adversarial examples, i.e., subtle but non-random perturbations designed to force recommendation models to produce erroneous outputs.

基于协作过滤（CF）的潜在因子模型（LFM），例如矩阵分解（MF）和深层CF方法，由于其出色的性能和推荐准确性而被广泛用于现代推荐系统（RS）。随之而来的是一个新的重大挑战：机器学习（ML）的许多应用本质上都是对抗性的[146]。设计用于强制推荐模型产生错误的输出。

The goal of this survey is two-fold: (i) to present recent advances on adversarial machine learning (AML) for the security of RS (i.e., attacking and defense recommendation models) and (ii) to show another successful application of AML in generative adversarial networks (GANs) for generative applications, thanks to their ability for learning (high-dimensional) data distributions. In this survey, we provide an exhaustive literature reviewof 76 articles published in major RS andML journals and conferences. This reviewserves as a reference for the RS community working on the security of RS or on generative models using GANs to improve their quality.

这项调查的目的有两个：（i）展示针对RS安全性的对抗性机器学习（AML）的最新进展（即攻击和防御推荐模型），以及（ii）展示AML在以下领域的另一成功应用由于其具有学习（高维）数据分布的能力，因此用于生成应用的生成对抗网络（GAN）在本次调查中，我们对RS和ML主要期刊和会议上发表的76篇文章进行了详尽的文献综述。 RS社区致力于RS的安全性或使用GAN改进生成质量的生成模型。

## 1 INTRODUCTION

## 2 ADVERSARIAL MACHINE LEARNING FOR SECURITY OF RS

## 3 ADVERSARIAL LEARNING FOR GAN-BASED RECOMMENDATION

### 3.1 Foundations of Generative Adversarial Networks (GANs)

### 3.2 GAN-based Recommendation Framework

### 3.3 GAN-based Recommendation Models: State of the Art

## 4 SUMMARY AND FUTURE DIRECTIONS

In this article, we have surveyed a wide variety of tasks in which adversarial machine learning (AML) is important to attack/defend a recommendation model as well as improve the generalization performance of the model itself. This broad range of applications can be categorized into two—objective-wise distinct—technologies: (i) AML for improving security (cf. Section 2) and (ii) AML used in generative adversarial networks (GANs) exploited for numerous tasks such as better CF recommendation, context-aware recommendation, cross-domain system, or visually aware fashion item/outfit recommendation (cf. Section 3). The common point of both technologies is the joint min-max optimization used for training models, in which two competing players play a zerosum differential game until they reach an equilibrium. To the best of our knowledge, this is the first work that sums up the advances of AML application in recommendation settings and proposes a clear taxonomy to classify such applications.

在本文中，我们调查了各种各样的任务，其中对抗性机器学习（AML）对于攻击/捍卫推荐模型以及提高模型本身的泛化性能很重要。两种是客观的，截然不同的技术：（i）用于提高安全性的AML（请参阅第2节），以及（ii）用于生成对抗网络（GAN）的AML用于许多任务，例如更好的CF推荐，上下文感知推荐，跨域系统或视觉上看得见的时尚商品/服装推荐（请参阅第3节）两种技术的共同点是用于训练模型的联合最小-最大优化，其中两个竞争的玩家玩零和差分游戏直到他们据我们所知，这是第一篇总结AML在推荐设置中的应用并提出明确分类法以对此类应用进行分类的工作。

