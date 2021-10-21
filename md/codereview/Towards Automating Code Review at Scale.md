# Towards Automating Code Review at Scale

## ABSTRACT

As neural methods are increasingly used to support and automate software development tasks, code review is a natural next target. Yet, training models to imitate developers based on past code reviews is far from straightforward: reviews found in open-source projects vary greatly in quality, phrasing, and depth depending on the reviewer. In addition, changesets are often large, stretching the capacity of current neural models. Recent work reported modest success at predicting review resolutions, but largely side-stepped the above issues by focusing on small inputs where comments were already known to occur. This work examines the vision and challenges of automating code review at realistic scale. We collect hundreds of thousands of changesets across hundreds of projects that routinely conduct code review, many of which change thousands of tokens. We focus on predicting just the locations of comments, which are quite rare. By analyzing model performance and dataset statistics, we show that even this task is already challenging, in no small part because of tremendous variation and (apparent) randomness in code reviews. Our findings give rise to a research agenda for realistically and impactfully automating code review.

随着神经方法越来越多地用于支持和自动化软件开发任务，代码审查自然成为下一个目标。然而，基于过去的代码审查来训练模仿开发人员的模型远非简单：在开源项目中发现的审查在质量、措辞和深度上差异很大，这取决于审查者。此外，变更集通常很大，扩展了当前神经模型的容量。最近的工作报告在预测审查决议方面取得了一定的成功，但通过专注于已知会发生评论的小输入，在很大程度上回避了上述问题。这项工作检查了在现实规模上自动化代码审查的愿景和挑战。我们在数百个定期进行代码审查的项目中收集了数十万个变更集，其中许多变更了数千个令牌。我们专注于预测评论的位置，这是非常罕见的。通过分析模型性能和数据集统计数据，我们表明即使是这项任务也已经具有挑战性，这在很大程度上是由于代码审查中的巨大变化和（明显的）随机性。我们的发现产生了一个研究议程，以现实和有效地自动化代码审查。

## 1 INTRODUCTION

## 2 RELATED WORK

Our work relates to both the study of technical and social mechanisms of code contributions in GitHub, and the fast-growing body of work on machine learning for code. Free open-source software projects have a long history of reviewing open contributions with benefits such as identifying and reducing defects [11] and newcomer socialization [5]. In GitHub’s code contribution mechanism of pull requests, discussions are often opportunities for core members to mentor and convey project norms [4] or for community members to negotiate design decisions and project direction with core members [14]. The recent GitHub feature of reviewers explicitly suggesting code changes also increases the discussion around changes and decreases the turnaround time to implement changes [3].

我们的工作既涉及对 GitHub 中代码贡献的技术和社会机制的研究，也涉及快速增长的代码机器学习工作。 免费开源软件项目在审查开放贡献方面有着悠久的历史，这些贡献具有识别和减少缺陷 [11] 和新人社会化 [5] 等优点。 在 GitHub 的 pull request 的代码贡献机制中，讨论往往是核心成员指导和传达项目规范 [4] 或社区成员与核心成员协商设计决策和项目方向的机会 [14]。 评论者最近在 GitHub 上明确建议更改代码的功能也增加了围绕更改的讨论并减少了实施更改的周转时间 [3]。

Machine learning and language models have been applied to study code review acceptance rates [6, 12], generate review comments [13], and predict specific review changes [15]. The latter uses a deep-learning model to recommend code revisions that a reviewer should suggest to the contributor. They consider two settings, both of which aim to predict the corrected code after review, the first just using the faulty/submitted code, while the second uses both the faulty code and the reviewer comments. Thus the task is complementary to the one we explore, which is predicting where to comment. Our data collection approach is also substantially more comprehensive and may benefit their models in turn.

机器学习和语言模型已被应用于研究代码审查接受率 [6, 12]，生成审查评论 [13]，并预测特定审查变化 [15]。 后者使用深度学习模型来推荐审阅者应该向贡献者建议的代码修订。 他们考虑了两种设置，两者都旨在预测审查后更正的代码，第一个只使用错误/提交的代码，而第二个使用错误代码和审查者评论。 因此，该任务是我们探索的任务的补充，即预测评论的位置。 我们的数据收集方法也更加全面，可能反过来有利于他们的模型。

