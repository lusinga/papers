# Pythia: AI-assisted Code Completion System

## ABSTRACT

In this paper, we propose a novel end-to-end approach for AIassisted code completion called Pythia. It generates ranked lists of method and API recommendations which can be used by software developers at edit time. The system is currently deployed as part of Intellicode extension in Visual Studio Code IDE. Pythia exploits state-of-the-art large-scale deep learning models trained on code contexts extracted from abstract syntax trees. It is designed to work at a high throughput predicting the best matching code completions on the order of 100 ms.

在本文中，我们提出了一种新的人工智能辅助代码完成的端到端方法，称为Pythia。它生成方法和API推荐的排名列表，供软件开发人员在编辑时使用。Pythia利用了最先进的大规模深度学习模型，这些模型是根据从抽象语法树中提取的代码上下文进行训练的。它被设计用于预测100毫秒左右的最佳匹配代码完成情况的高吞吐量。

We describe the architecture of the system, perform comparisons to frequency-based approach and invocation-based Markov Chain language model, and discuss challenges serving Pythia models on lightweight client devices.

我们描述了系统的架构，比较了基于频率的方法和基于调用的马尔可夫链语言模型，并讨论了在轻量级客户端设备上为Pythia模型服务的挑战。

The offline evaluation results obtained on 2700 Python open source software GitHub repositories show a top-5 accuracy of 92%, surpassing the baseline models by 20% averaged over classes, for both intra and cross-project settings.

2700 Python开放源码软件GitHub库的离线评估结果显示，无论是内部设置还是跨项目设置，最高的5级精度为92%，平均比基线模型高出20%。

## 1 INTRODUCTION

In software development through Integrated Development Environments (IDEs) [13], code completion is one of the most widely used features. Intelligent code completion [14] assists developers by reducing typographic and other common errors, effectively improving developer productivity. These features may include pop-ups when typing, calling methods and APIs on typed classes and objects, querying parameters of functions, variable or function name disambiguation, and program structure completion that use code context to reliably predict following code.

在通过集成开发环境[13]进行的软件开发中，代码完成是使用最广泛的特性之一。智能代码完成[14]通过减少排版和其他常见错误来帮助开发人员，有效地提高开发人员的工作效率。这些特性可能包括在键入时弹出窗口、在有类型的类和对象上调用方法和api、查询函数的参数、变量或函数名消除歧义以及使用代码上下文可靠地预测后续代码的程序结构完成。

Traditional code completion tools in IDEs typically list out all possible attributes or methods that can be invoked when a user types a "." or an "=" trigger character. However, not ranking the suggested results requires users to scroll through a long alphabetically ordered list, which is often even slower than typing the full name of a method directly. Studies show that developers tend to rely heavily on prefix filtering to reduce the number of choices [10].

传统的ide代码完成工具通常列出了当用户键入“。”或“=”触发字符时可能调用的所有属性或方法。然而，如果不对建议的结果进行排序，则需要用户滚动一个按字母顺序排列的长列表，这通常比直接输入方法的全名还要慢。研究表明，开发人员往往严重依赖前缀过滤来减少选择[10]的数量。

In this paper, we introduce Pythia – a neural code completion system, trained on code snippets extracted from a large-scale open source code dataset. The fundamental task of the system is to find the most likely method given a code snippet. In other words, given original code snippetC, the vocabularyV , and the set of all possible methods M ⊂ V , we would like to determine:
m∗ = arдmax(P(m|C)), ∀m ∈ M. (1)

In order to find that method, we construct a model capable of scoring responses and then determining the most likely method.

为了找到这种方法，我们构建了一个能够对反应进行评分的模型，然后确定最可能的方法。

A large number of intelligent code completion systems for both statically and dynamically typed languages have been proposed in the literature [1, 2, 7, 14, 17, 18]. Best Matching Neighbor (BMN) and statistical language models such as n-grams, as well as recurrent neural network (RNN) based approaches leveraging sequential nature of the source code have been particularly effective at creating such systems. The majority of the past approaches did not leverage the long-range sequential nature of the source code or tried to transfer natural language methods without taking advantage of unique opportunities offered by code’s abstract syntax trees (AST).

文献[1,2,7,14,17,18]中已经提出了大量用于静态和动态类型语言的智能代码补全系统。最佳匹配近邻(BMN)和统计语言模型(如n-grams)，以及基于递归神经网络(RNN)的方法，利用源代码的顺序性质，在创建这样的系统方面特别有效。过去的大多数方法都没有利用源代码的长期顺序特性，或者试图在没有利用代码抽象语法树(AST)提供的独特机会的情况下转移自然语言方法。

The nature of the problem of code completion makes long shortterm memory (LSTM) networks [4] a promising candidate. Pythia consumes partial ASTs corresponding to code snippets containing member access expressions and module function invocations as an input for model training, aiming to capture semantics carried by distant nodes.

代码完成问题的本质使得长短期记忆(LSTM)网络[4]成为一个有前途的候选者。Pythia使用与包含成员访问表达式和模块函数调用的代码片段相对应的部分ast作为模型训练的输入，目的是捕获远处节点承载的语义。

## 9 CONCLUSIONS

We have introduced a novel end-to-end approach for AI-assisted code completion called Pythia, generating ranked lists of method and API recommendations that can be used by software developers. We have deployed the system as part of Intellicode extension in Visual Studio Code IDE. Pythia makes use of long short-term memory networks trained on long-range code contexts extracted from abstract syntax trees, capturing semantics carried by distant nodes. Evaluation results on a large dataset of 15.8 million method calls extracted from real-world source code showed that our best model achieves 92% top-5 accuracy, beating simpler baselines.

我们已经介绍了一种新的端到端的ai辅助代码完成方法，称为Pythia，它可以生成软件开发人员可以使用的方法和API推荐列表。我们已经在Visual Studio代码IDE中部署了该系统作为Intellicode扩展的一部分。Pythia利用从抽象语法树中提取的远程代码上下文训练的短期长记忆网络，捕获由遥远节点携带的语义。对从真实源代码中提取的1580万个方法调用的大数据集的评估结果显示，我们最好的模型达到92%的前5名精度，击败了更简单的基线。

We have overcome and documented several practical challenges of training deep neural networks and hyperparameter tuning on high-performance computing clusters, and model deployment on lightweight client devices to predict the best matching code completions at edit time.

我们已经克服并记录了在高性能计算集群上训练深度神经网络和超参数调优，以及在轻量级客户机设备上部署模型以预测在编辑时最佳匹配的代码完成情况的几个实际挑战。

Besides Python, Intellicode extension is providing AI-assisted code completions for a variety of programming languages including C#, Java, C++, and XAML based on our Markov Chain model. In the future, advanced deep learning approaches will be explored in application to programming languages other than Python, and for more sophisticated code completion scenarios.

除了Python, Intellicode extension还基于马尔可夫链模型为各种编程语言提供ai辅助的代码补全，包括c#、Java、c++和XAML。在未来，高级深度学习方法将应用于Python以外的编程语言，以及更复杂的代码完成场景。



