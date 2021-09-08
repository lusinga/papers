# A Feature Interaction Network for Customer Churn Prediction

## ABSTRACT

Customer churn prediction is an active research topic for the data mining community and business managers in this rapidly growing society. The ability to detect churn customers precisely is something that every company would wish to achieve. With the great success of DNNs, several churn prediction models based on DNNs are proposed in recent years. However, traditional DNNs cannot learn high-order feature interactions and deal with one-hot vectors well. In this paper, we proposed a feature interaction network (FIN), which aims to enhance the inherent relations of discrete features and learn high-order feature interactions. This network contains two modules: an entity embedding network and a factorization machine network with several sliding windows. From the experiments, it is observed that our proposed model has a better predictive performance than several state-of-the-art models on 4 public datasets.

在这个快速发展的社会中，客户流失预测是数据挖掘社区和业务管理人员的一个活跃的研究课题。精确检测客户流失的能力是每个公司都希望实现的。随着dnn的成功应用，近年来出现了多种基于dnn的用户流失预测模型。然而，传统的dnn不能很好地学习高阶特征相互作用，不能很好地处理单热点向量。在本文中，我们提出了一个特征交互网络(FIN)，旨在增强离散特征的内在联系和学习高阶特征交互。该网络包含两个模块:实体嵌入网络和带有多个滑动窗口的因子分解机器网络。从实验中可以看出，我们所提出的模型在4个公共数据集上的预测性能优于几个最先进的模型。

## 1. INTRODUCTION

Learning how to construct a churn prediction model plays an indispensable role in customer relationship management recently. The goal of customer churn prediction is to estimate the possibility that the customer will churn in the future based on the historical customer information. According to the reliable survey, lone-term customers have stable spending power and attracting a new customer costs several times more than to prevent a regular customer in stop services [1]. Until now, it has gained increasing attention and great improvement. Executives and managers can use customer churn prediction models to find potential churn users and then formulate corresponding strategies. Many fields e.g., financial service [2-4], social network [5-7], airline company [8], online gaming [9, 10], energy industry [11], and telecommunication industry [12-14] are increasingly focus on maintaining stable relationships with long-term customers. However, constructing a reliable prediction model is challenging. Because historical customer information contains a variety of problems, such as incorrect records, heavily imbalanced classes, and missing values [15]. Hence, data preprocessing is as important as classification algorithms. Recently, the great progress of data mining techniques has greatly promoted the development of customer churn prediction.

如何构建客户流失预测模型，是当前客户关系管理中一个不可缺少的研究课题。客户流失预测的目标是根据客户的历史信息来估计未来客户流失的可能性。据可靠调查，长期客户有稳定的消费能力，吸引新客户的成本是防止老客户停止服务[1]的几倍。到目前为止，它得到了越来越多的关注和很大的改进。高管和管理者可以使用客户流失预测模型来发现潜在的流失用户，并制定相应的策略。金融服务[2-4]、社交网络[5-7]、航空公司[8]、在线游戏[9,10]、能源行业[11]、电信行业[12-14]等领域越来越注重与长期客户保持稳定的关系。然而，构建一个可靠的预测模型是一个挑战。因为历史客户信息包含各种问题，例如不正确的记录、严重不平衡的类和丢失的值[15]。因此，数据预处理与分类算法同样重要。近年来，数据挖掘技术的长足发展，极大地促进了客户流失预测的发展。

On the one hand, some researchers focus on enhancing the prediction performance of customer churn prediction models. It has previously been observed that support vector machine (SVM) is one of the best classification algorithms. Brandusoiu I et al. constructed a customer churn prediction model via SVM with four kernel functions [16]. Although SVM has a great performance, it generates black-box problems which keep researchers from understanding the drivers of customer churn. The study neural network for customer churn has achieved great success because neural networks can study the inherent relation of customer features automatically, and have a strong self-learning, self-organization, and self-adapting capability [17]. Yu R et al. proposed a particle classification optimization-based multi-layer perceptron for customer churn. This algorithm prevents loss function from local optimums to improve predictive performance obviously [13]. Extensive research also has shown that ensemble learning has a great achievement on customer churn prediction. Wang Z et al. designed a practical pipeline with stacking models using logistic regression, random forest, and paralleled XGBoost [15], [18]. The stacking model is ranked the third place on the KKBOX‟s Churn Prediction Challenge of the WSDM Cup 2018, but the outputs from several classification models on the stacking stage are completely unable to understand.

一方面，一些研究者关注于提高客户流失预测模型的预测性能。以前已经观察到支持向量机(SVM)是最好的分类算法之一。Brandusoiu I等人利用支持向量机构建了客户流失预测模型，该模型具有四个核函数[16]。尽管SVM有很好的性能，但它会产生黑箱问题，使研究人员无法理解客户流失的驱动因素。由于神经网络能够自动研究客户特征之间的内在关系，具有很强的自学习、自组织和自适应能力[17]，因此客户流失研究神经网络取得了巨大的成功。Yu R等人针对客户流失提出了一种基于粒子分类优化的多层感知器。该算法避免了局部最优的损失函数，显著提高了预测性能。大量的研究也表明集成学习在客户流失预测方面取得了很大的成就。Wang Z等利用logistic回归、随机森林和并行XGBoost[15]、[18]的叠加模型设计了一个实用的管道。堆叠模型在2018年WSDM Cup的KKBOX的Churn Prediction Challenge中排名第三，但多个分类模型在堆叠阶段的输出完全无法理解。

On the other hand, researchers want to study what drives the customer churn and try to define a drivers list. Logistic regression (LR) and decision tree (DT) are very popular data mining technologies because of their excellent predictive performance and robustness. Caigny A D et al. proposed a hybrid classification algorithm based on LR and DT. This algorithm consists of two parts: a segmentation stage and a classification stage. In the segmentation stage customers are divided into several groups based on node partition and in the classification stage diverse LR models are created for all leaves of this tree [19]. Yang C et al. developed a two-step framework for comprehensible user clustering and churn prediction. The model leverages inherent relations among customers‟ activities to divide latent groups and constructs classification models for several groups based on parallel LSTM [20]. The experimental results obviously showed that each user group has typical behavior patterns. Hence, it can help researchers to comprehend user activities better. However, they still divided user groups manually which is not suitable for large-scale data mining tasks.

另一方面，研究人员希望研究是什么导致了客户流失，并试图定义一个驱动因素清单。Logistic回归(LR)和决策树(DT)因其优良的预测性能和鲁棒性而成为非常流行的数据挖掘技术。Caigny A D等人提出了一种基于LR和DT的混合分类算法。该算法由分割阶段和分类阶段两部分组成。在细分阶段，根据节点划分将客户分成若干组;在分类阶段，针对这棵树[19]的所有叶子创建不同的LR模型。Yang C等人开发了可理解用户聚类和用户流失预测的两步框架。该模型利用客户活动之间的内在联系来划分潜在群体，并基于并行LSTM[20]构建多个群体的分类模型。实验结果表明，每个用户群体都具有典型的行为模式。因此，它可以帮助研究者更好地理解用户活动。但它们仍然是手工划分用户组，不适合大规模的数据挖掘任务。

It is well-known that feature engineering is high-cost because researchers need to spend a lot of time extracting useful features and exploring potential high-order feature interactions from initial data [21]. In large-scale predictive systems such as web-scale customer churn prediction, the huge number of customer features make it impossible to accomplish feature engineering manually. Moreover, researchers cannot obtain all meaningful cross features only by manual operation. Hence, learning to construct a prediction model without manual feature engineering is significant. In customer churn prediction, the number of discrete features like “gender”, “interests”, and “address” is always huge. The values of these discrete features cannot be processed directly. A general way to solve this problem is to use one-hot encoding, but it will generate a huge number of sparse data. The sparse data can decrease the information relations between different values of discrete features.

众所周知，特征工程的成本很高，因为研究人员需要花费大量时间从初始数据[21]中提取有用的特征，并探索潜在的高阶特征交互。在大规模的预测系统中，如web规模的客户流失预测，大量的客户特征使得手工完成特征工程是不可能的。此外，仅靠人工操作，研究者无法获得所有有意义的交叉特征。因此，学习构建不需要人工特征工程的预测模型具有重要意义。在客户流失预测中，“性别”、“兴趣”和“地址”等离散特性的数量总是很大。这些离散特征的值不能被直接处理。解决这个问题的一般方法是使用一次性编码，但它会产生大量的稀疏数据。稀疏数据可以减少离散特征不同值之间的信息关系。

In this paper, we proposed a feature interaction network (FIN) for customer churn prediction, which contains two modules. The model starts with an input layer, followed by a multi-layer perceptron with entity embedding [22] and a factorization machine [23] network with sliding windows in parallel. In the first module, entity embedding is used to enhance the inherent relation of these sparse data. In the second module, the factorization machine network is applied to generate the new feature interaction vector and solve the problems that the weights of one-hot vectors cannot be updated. And several sliding windows can generate as much high-order feature interactions as possible. The contributions of this study are summarized as follows:

本文提出了一种用于客户流失预测的特征交互网络(FIN)，该网络包含两个模块。该模型从一个输入层开始，然后是一个多层感知器，其中实体嵌入[22]，分解机[23]网络并行滑动窗口。在第一个模块中，利用实体嵌入增强稀疏数据的内在联系。在第二个模块中，利用分解机网络生成新的特征交互向量，解决单热点向量权值无法更新的问题。几个滑动窗口可以产生尽可能多的高阶特征交互。本研究的贡献总结如下:

- (1) The proposed model enhances the inherent relation of discrete features and solves the problems that weights of one-hot vectors cannot be updated.

- (2)We design a feature interaction network for customer churn prediction that can generate high-order feature interactions automatically without any manual feature engineering.

- (3)We make experiments on four real-world datasets to test the predictive performance of the proposed model and the results reveal that it is better than several state-of-the-art classification models.

- (1) 该模型增强了离散特征的内在联系，解决了单热点向量权值无法更新的问题。

- (2) 我们设计了一个用于客户流失预测的特征交互网络，该网络可以自动生成高阶特征交互，无需任何人工特征工程。

- (3) 在4个真实数据集上进行了实验，测试了所提模型的预测性能，结果表明，所提模型优于现有的几种分类模型。

## 2. PRELIMINARIES

## 3. PROPOSED METHODOLOGY

## 4. EXPERIMENTAL SETUP

## 5. RESULTS AND DISCUSSION

## 6. CONCLUSION AND FUTURE WORK

In this paper, we proposed a feature interaction network model to improve the predictive performance of customer churn prediction model. This model contains two modules: an entity embedding network and a factorization machine network. The transformed feature vector in FIN has a better implicit representation ability. The experimental results showed that FIN outperformed the other state-of-the-art models on 4 public datasets. To sum up, our model offers three main contributions to the existing literature such as:

- (1) It has a great prediction performance than the other customer churn prediction models; 
- (2) It can generate high-order feature interactions automatically without any manual feature engineering; 
- (3) It enhanced the inherent relation of one-hot vectors and solve the situations that weights of one-hot vectors cannot be updated.

As a topic of further research, we are interested in explaining and visualizing the transformed vectors in FIN. Besides, we will explore a way to reduce the computational cost. Finally, we focus on deploying this model to the industry.

为了提高客户流失预测模型的预测性能，本文提出了一种特征交互网络模型。该模型包含实体嵌入网络和因子分解机器网络两个模块。变换后的特征向量具有较好的隐式表示能力。实验结果表明，FIN在4个公共数据集上的性能优于其他先进的模型。综上所述，我们的模型对现有文献有三个主要贡献:

- (1)与其他客户流失预测模型相比，具有较好的预测性能;

- (2)无需人工特征工程，可自动生成高阶特征交互;

- (3)增强了单热点向量的内在联系，解决了单热点向量权值无法更新的情况。

作为进一步的研究课题，我们对FIN中变换向量的解释和可视化感兴趣，并将探索一种降低计算成本的方法。最后，我们将重点讨论如何将该模型部署到行业中。