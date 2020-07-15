# Pythia: AI-assisted Code Completion System

## ABSTRACT

In this paper, we propose a novel end-to-end approach for AIassisted code completion called Pythia. It generates ranked lists of method and API recommendations which can be used by software developers at edit time. The system is currently deployed as part of Intellicode extension in Visual Studio Code IDE. Pythia exploits state-of-the-art large-scale deep learning models trained on code contexts extracted from abstract syntax trees. It is designed to work at a high throughput predicting the best matching code completions on the order of 100 ms.

在本文中，我们提出了一种新的人工智能辅助代码完成的端到端方法，称为Pythia。它生成方法和API推荐的排名列表，供软件开发人员在编辑时使用。Pythia利用了最先进的大规模深度学习模型，这些模型是根据从抽象语法树中提取的代码上下文进行训练的。它被设计用于预测100毫秒左右的最佳匹配代码完成情况的高吞吐量。

We describe the architecture of the system, perform comparisons to frequency-based approach and invocation-based Markov Chain language model, and discuss challenges serving Pythia models on lightweight client devices.

我们描述了系统的架构，比较了基于频率的方法和基于调用的马尔可夫链语言模型，并讨论了在轻量级客户端设备上为Pythia模型服务的挑战。

The offline evaluation results obtained on 2700 Python open source software GitHub repositories show a top-5 accuracy of 92%, surpassing the baseline models by 20% averaged over classes, for both intra and cross-project settings.

2700 Python开放源码软件GitHub库的离线评估结果显示，无论是内部设置还是跨项目设置，最高的5级精度为92%，平均比基线模型高出20%。

