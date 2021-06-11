# Assuring the Machine Learning Lifecycle: Desiderata, Methods, and Challenges

Machine learning has evolved into an enabling technology for a wide range of highly successful applications. The potential for this success to continue and accelerate has placed machine learning (ML) at the top of research, economic, and political agendas. Such unprecedented interest is fuelled by a vision of ML applicability extending to healthcare, transportation, defence, and other domains of great societal importance. Achieving this vision requires the use of ML in safety-critical applications that demand levels of assurance beyond those needed for current ML applications. Our article provides a comprehensive survey of the state of the art in the assurance of ML, i.e., in the generation of evidence that ML is sufficiently safe for its intended use. The survey covers the methods capable of providing such evidence at different stages of the machine learning lifecycle, i.e., of the complex, iterative process that starts with the collection of the data used to train anML component for a system, and ends with the deployment of that component within the system. The article begins with a systematic presentation of the ML lifecycle and its stages. We then define assurance desiderata for each stage, review existing methods that contribute to achieving these desiderata, and identify open challenges that require further research.

机器学习已经发展成为广泛成功应用的使能技术。这一成功继续和加速的潜力已将机器学习 (ML) 置于研究、经济和政治议程的首位。这种前所未有的兴趣是由将 ML 适用性扩展到医疗保健、交通、国防和其他具有重要社会意义的领域的愿景所推动的。实现这一愿景需要在安全关键型应用程序中使用 ML，这些应用程序需要的保证水平超出当前 ML 应用程序所需的水平。我们的文章对 ML 保证方面的最新技术进行了全面调查，即生成证据证明 ML 对其预期用途足够安全。该调查涵盖了能够在机器学习生命周期的不同阶段提供此类证据的方法，即复杂的迭代过程，从收集用于训练系统的机器学习组件的数据开始，到部署系统内的那个组件。本文首先系统地介绍了 ML 生命周期及其阶段。然后，我们为每个阶段定义保证需求，审查有助于实现这些需求的现有方法，并确定需要进一步研究的开放挑战。

## 1 INTRODUCTION

## 2 THE MACHINE LEARNING LIFECYCLE

## 3 RELATED SURVEYS

## 4 DATA MANAGEMENT

## 5 MODEL LEARNING

## 6 MODEL VERIFICATION

## 7 MODEL DEPLOYMENT

## 8 CONCLUSION

Recent advances in machine learning underpin the development of many successful systems. ML technology is increasingly at the core of sophisticated functionality provided by smart devices, household appliances and online services, often unbeknownst to their users. Despite the diversity of these ML applications, they share a common characteristic: none is safety critical. Extending the success of machine learning to safety-critical systems holds great potential for application domains ranging from healthcare and transportation to manufacturing, but requires the assurance of the ML models deployed within such systems. Our article explained that this assurance must cover all stages of the ML lifecycle, defined assurance desiderata for each such stage, surveyed the methods available to achieve these desiderata, and highlighted remaining open challenges.

机器学习的最新进展为许多成功系统的开发奠定了基础。 ML 技术越来越成为智能设备、家用电器和在线服务提供的复杂功能的核心，而用户通常不知道。 尽管这些 ML 应用程序多种多样，但它们有一个共同的特点：没有一个是安全关键的。 将机器学习的成功扩展到安全关键系统对于从医疗保健和运输到制造的应用领域具有巨大潜力，但需要保证在此类系统中部署的 ML 模型。 我们的文章解释说，这种保证必须涵盖 ML 生命周期的所有阶段，为每个这样的阶段定义了保证需求，调查了实现这些需求的可用方法，并强调了剩余的开放挑战。

For the Data Management stage, our survey shows that a wide range of data collection, preprocessing, augmentation and analysis methods can help ensure that ML training and verification datasets are Relevant, Complete, Balanced, and Accurate. Nevertheless, further research is required to devise methods capable of demonstrating that these data are sufficiently secure, fit-for-purpose and, when simulation is used to synthesise data, that simulations are suitably realistic.

对于数据管理阶段，我们的调查表明，广泛的数据收集、预处理、扩充和分析方法可以帮助确保 ML 训练和验证数据集是相关的、完整的、平衡的和准确的。 然而，需要进一步的研究来设计能够证明这些数据足够安全、适合用途的方法，并且当使用模拟来合成数据时，模拟是适当的现实。

The Model Learning stage has been the focus of tremendous research effort, and a vast array of model selection and learning methods are available to support the development of Performant and Robust ML models. In contrast, there is a significant need for additional hyperparameter selection and transfer learning methods, and for research into ensuring that ML models are Reusable and Interpretable, in particular through providing context-relevant explanations of behaviour.

模型学习阶段一直是大量研究工作的重点，大量模型选择和学习方法可用于支持高性能和鲁棒 ML 模型的开发。 相比之下，非常需要额外的超参数选择和迁移学习方法，以及确保 ML 模型可重用和可解释的研究，特别是通过提供与上下文相关的行为解释。

Assurance concerns associated with the Model Verification stage are addressed by numerous test-based verification methods and by a small repertoire of recently introduced formal verification methods. The verification results provided by these methods are often Comprehensive (for the ML model aspects being verified) and, in some cases, Contextually Relevant. However, there are currently insufficient methods capable of encoding the requirements of the model being verified into suitable tests and formally verifiable properties. Furthermore, ensuring that verification results are Comprehensible is still very challenging.

与模型验证阶段相关的保证问题通过许多基于测试的验证方法和最近引入的一小部分形式验证方法来解决。 这些方法提供的验证结果通常是全面的（对于正在验证的 ML 模型方面），并且在某些情况下，与上下文相关。 然而，目前没有足够的方法能够将被验证的模型的要求编码为合适的测试和形式上可验证的属性。 此外，确保验证结果是可理解的仍然非常具有挑战性。

The integration and monitoring activities from the Model Deployment stage are supported by a sizeable set of methods that can help address the Fit-for-Purpose and Tolerable desiderata of deployed ML models. These methods are often inspired by analogous methods for the integration and monitoring of software components developed using traditional engineering approaches. In contrast, ML model updating using data collected during operation has no clear software engineering counterpart. As such, model updating methods are scarce and typically unable to provide the assurance needed to deploy ML models that are Adaptable within safety-critical systems.

模型部署阶段的集成和监控活动得到了大量方法的支持，这些方法可以帮助解决已部署 ML 模型的“适合用途”和“可容忍”需求。 这些方法通常受到用于集成和监控使用传统工程方法开发的软件组件的类似方法的启发。 相比之下，使用在操作期间收集的数据更新 ML 模型没有明确的软件工程对应物。 因此，模型更新方法很少，并且通常无法提供在安全关键系统中部署适应性强的 ML 模型所需的保证。

A further open challenge for assuring the ML lifecycle is the determination of effective tradeoffs between the desiderata over the four stages. In many applications, the desiderata are closely related, either reinforcing each other or placing competing demands on the ML lifecycle activities. As an example, a highly Reusable ML model will likely be easily Adaptable, but might be less Contextually Relevant. Further tradeoffs arise in the assurance domain, e.g., an Explainable model might introduce vulnerabilities exploitable in a cyberattack [77].

确保机器学习生命周期的另一个开放挑战是确定四个阶段的需求之间的有效权衡。 在许多应用程序中，需求是密切相关的，要么相互加强，要么对 ML 生命周期活动提出竞争要求。 例如，高度可重用的 ML 模型可能很容易适应，但可能与上下文相关性较低。 在保证领域中出现了进一步的权衡，例如，可解释模型可能会引入可在网络攻击中利用的漏洞 [77]。

This brief summary shows that considerable research is still needed to address outstanding assurance concerns associated with every stage of theML lifecycle. In general, using ML components within safety-critical systems poses numerous open challenges. At the same time, the research required to address these challenges can build on a promising combination of rigorous methods developed by several decades of sustained advances in machine learning, in software and systems engineering, and in assurance development.

这个简短的总结表明，仍然需要大量的研究来解决与 ML 生命周期每个阶段相关的突出保证问题。 一般来说，在安全关键系统中使用 ML 组件会带来许多开放性挑战。 与此同时，解决这些挑战所需的研究可以建立在机器学习、软件和系统工程以及保证开发方面几十年的持续进步所开发的严谨方法的有希望的组合之上。

