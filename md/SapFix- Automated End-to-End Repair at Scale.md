# SapFix: Automated End-to-End Repair at Scale

## Abstract

We report our experience with SAPFIX: the first deployment of automated end-to-end fault fixing, from test case design through to deployed repairs in production code1. We have used SAPFIX at Facebook to repair 6 production systems, each consisting of tens of millions of lines of code, and which are collectively used by hundreds of millions of people worldwide.

我们报告了 SAPFIX 的经验: 从测试用例设计到生产代码中部署的修复，自动化端到端故障修复的首次部署。我们已经使用 Facebook 上的 SAPFIX 修复了 6 个生产系统，每个系统由数千万行代码组成，这些代码被全球数亿人共同使用。

## INTRODUCTION

Automated program repair seeks to find small changes to software systems that patch known bugs [1], [2]. One widely studied approach uses software testing to guide the repair process, as typified by the GenProg approach to search-based program repair [3].

- [1] C. Le Goues, S. Forrest, and W. Weimer, “Current challenges in automatic software repair,” SQJ, vol. 21, no. 3, pp. 421–443, 2013.
- [2] J. Petke, S. O. Haraldsson, M. Harman, W. B. Langdon, D. R. White, and J. R. Woodward, “Genetic improvement of software: a comprehensive survey,” IEEE Transactions on Evolutionary Computation, 2018, to appear.
- [3] C. Le Goues, T. Nguyen, S. Forrest, and W. Weimer, “GenProg: A generic method for automatic software repair,” IEEE Transactions on Software Engineering, vol. 38, no. 1, pp. 54–72, 2012.

自动化程序修复寻求找到修补已知错误的软件系统的小变化 [1]，[2]。一种广泛研究的方法使用软件测试来指导修复过程，以 GenProg 方法为代表的基于搜索的程序修复 [3]。

Recently, the automated test case design system, Sapienz [4], has been deployed at scale [5], [6]. The deployment of Sapienz allows us to find hundreds of crashes per month, before they even reach our internal human testers. Our software engineers have found fixes for approximately 75% of Sapienz reported crashes [6], indicating a high signal-to-noise ratio [5] for Sapienz bug reports. Nevertheless, developers’ time and expertise could undoubtedly be better spent on more creative programming tasks if we could automate some or all of the comparatively tedious and time-consuming repair process.

- [4] K. Mao, M. Harman, and Y. Jia, “Sapienz: Multi-objective automated testing for Android applications,” in International Symposium on Software Testing and Analysis (ISSTA 2016), 2016, pp. 94–105.
- [5] M. Harman and P. O’Hearn, “From start-ups to scale-ups: Opportunities and open problems for static and dynamic program analysis (keynote paper),” in 18th IEEE International Working Conference on Source Code Analysis and Manipulation (SCAM 2018), Madrid, Spain, September 23rd-24th 2018, pp. 1–23.
- [6] N. Alshahwan, X. Gao, M. Harman, Y. Jia, K. Mao, A. Mols, T. Tei, and I. Zorin, “Deploying search based software engineering with Sapienz at Facebook (keynote paper),” in SSBSE 2018, 2018, pp. 3–45.

最近，自动化测试用例设计系统 Sapienz [4] 已经大规模部署 [5]，[6]。Sapienz 的部署允许我们每月找到数百个崩溃，甚至在他们到达我们的内部人类测试人员之前。我们的软件工程师已经找到了大约 75% 的 Sapienz 报告崩溃的修复 [6]，表明 Sapienz 错误报告的信噪比 [5] 很高。然而，如果我们能够自动化部分或全部相对繁琐且耗时的修复过程，开发人员的时间和专业知识无疑可以更好地用于更具创造性的编程任务。

The deployment of Sapienz automated test design means that automated repair can now also take advantage of automated software test design to automatically re-test candidate patches. Therefore, we have started to deploy automated repair, in a tool called SAPFIX, to tackle some of these crashes. SAPFIX automates the entire repair life cycle end-to-end with the help of Sapienz: from designing the test cases that detect the crash, through to fixing and re-testing, the process is fully automated and deployed into Facebook’s continuous integration and deployment system.

Sapienz 自动化测试设计的部署意味着自动化修复现在还可以利用自动化软件测试设计来自动重新测试候选补丁。因此，我们已经开始在一个名为 SAPFIX 的工具中部署自动修复来解决其中的一些崩溃。SAPFIX 在 Sapienz 的帮助下端到端地自动化整个修复生命周期: 从设计检测崩溃的测试用例，到修复和重新测试,该流程完全自动化，并部署到 Facebook 的持续集成和部署系统中。

The Sapienz deployment at Facebook, with which SapFix integrates, tests Facebook’s apps using automated search over the space of test input sequences [7]. This paper focuses on the deployment of SapFix, which has been used to suggest fixes for six key Android apps in the Facebook App Family, for which the Sapienz test input generation infrastructure has also been deployed. These are Facebook, Messenger, Instagram, FBLite, Workplace and Workchat. These six Android apps collectively consist of tens of millions of lines of code and are used daily by hundreds of millions of users worldwide to support communication, social media and community building activities.

SapFix 与之集成的 Sapienz 在 Facebook 上的部署，在测试输入序列的空间上使用自动搜索测试 Facebook 的应用程序 [7]。本文重点介绍 SapFix 的部署，它已被用于为 Facebook 应用程序系列中的六个关键 Android 应用程序提出修复建议，Sapienz 测试输入生成基础设施也已为此部署。这些是 Facebook，Messenger，Instagram，FBLite，Workplace 和 Workchat。这六款 Android 应用程序总共由数千万行代码组成，每天被全球数亿用户使用，以支持通信、社交媒体和社区建设活动。

In order to deploy such a fully automated end-to-end detect-and-fix process we naturally needed to combine a number of different techniques. Nevertheless the SAPFIX core algorithm is a simple one. Specifically, it combines straightforward approaches to mutation testing [8], [9], search-based software testing [6], [10], [11], and fault localisation [12] as well as existing developer-designed test cases. We also needed to deploy many practical engineering techniques and develop new engineering solutions in order to ensure scalability.

为了部署这样一个完全自动化的端到端检测和修复过程，我们自然需要结合许多不同的技术。尽管如此，SAPFIX 核心算法是一个简单的算法。具体来说，它结合了直接的方法来进行突变测试 [8]，[9]，基于搜索的软件测试 [6]，[10]，[11],和故障定位 [12] 以及现有的开发人员设计的测试用例。我们还需要部署许多实用的工程技术，并开发新的工程解决方案，以确保可扩展性。

SAPFIX combines a mutation-based technique, augmented by patterns inferred from previous human fixes, with a reversion-aslast resort strategy for high-firing crashes (that would otherwise block further testing, if not fixed or removed). This core fixing technology is combined with Sapienz automated test design, Infer’s static analysis and the localisation infrastructure built specifically for Sapienz [6]. SAPFIX is deployed on top of the Facebook FBLearner Machine Learning infrastructure [13] into the Phabricator code review system, which supports the interactions with developers.

SAPFIX 结合了一种基于突变的技术，通过从以前的人类修复中推断出来的模式进行了增强，并采用了一种以复原状的策略来应对高火碰撞 (否则会阻碍进一步的测试,如果没有固定或删除)。这种核心固定技术与 Sapienz 自动化测试设计、 Infer 的静态分析和专门为 Sapienz [6] 构建的本地化基础设施相结合。SAPFIX 部署在 Facebook FBLearner 机器学习基础设施 [13] 之上，进入 Phabricator 代码审查系统，该系统支持与开发人员的交互。

Because of its focus on deployment in a continuous integration environment, SAPFIX makes deliberate choices to sidestep some of the difficulties pointed out in the existing literature on automated program repair (see Related Work section). Since SAPFIX focuses on null-dereference faults revealed by Sapienz test cases as code is submitted for review it can re-use the Sapienz fault localisation step [6]. The focus on null-dereference errors also means that a limited number of fix patterns suffice. Moreover, these particular patterns do not require additional fix ingredients (sometimes known as donor code), and can be applied without expensive exploration.

由于其专注于在持续集成环境中的部署，SAPFIX 做出了深思熟虑的选择，以避开现有文献中指出的关于自动化程序修复的一些困难 (参见相关工作部分)。由于 SAPFIX 专注于由 Sapienz 测试案例显示的空取消引用故障，因为代码提交审查，它可以重复使用 Sapienz 故障定位步骤 [6]。对空引用错误的关注也意味着有限数量的修复模式就足够了。此外，这些特定的模式不需要额外的修复成分 (有时称为施主代码)，并且可以在不需要昂贵的探索的情况下应用。

We report our experience, focusing on the techniques required to deploy repair at scale into continuous integration and deployment. We also report on developers’ reactions and the socio-technical issues raised by automated program repair. We believe that this experience may inform and guide future research in automated repair.

我们报告了我们的经验，重点是将修复大规模部署到持续集成和部署中所需的技术。我们还报告了开发人员的反应以及自动化程序修复提出的社会技术问题。我们相信，这一经验可能会为未来的自动修复研究提供信息和指导。

The SAPFIX project is a small, but nevertheless distinct advance, along the path to the realisation of the FiFiVerify vision [10] of fully automated and verified code improvement. The primary contributions of the present paper, which reports on this deployment of SAPFIX are:

- The first end-to-end deployment of industrial repair;
- The first combination of automated repair with static and dynamic analysis for crash identification, localisation and re-testing;
- Results from repair applied to 6 multi-million line systems;
- Results and insights from professional developers’ feedback on proposed repairs.

SAPFIX 项目是一个小的，但仍然是明显的进步，沿着实现完全自动化和经过验证的代码改进的 FiFiVerify 愿景 [10] 的道路。主要贡献的报告，在这一部署的 SAPFIX 是:
- 工业维修的首次端到端部署;
- 自动修复与静态和动态分析的第一个组合，用于碰撞识别、定位和重新测试;
- 应用于 6 个数百万线路系统的维修结果;
- 专业开发人员对建议维修的反馈结果和见解。

## RELATED WORK

SAPFIX is grounded in the approach to software engineering known as Search Based Software Engineering (SBSE) [34], [35]; the space of potential fixes to a software systems is considered to be a search space constructed from small modifications to an existing system under test. In the deployment of SAPFIX we do not claim any strong novelty in terms of the core repair algorithm. In fact, SAPFIX does not use any of the repair approaches from the literature on repair and SBSE, since their sophistication might have inhibited scalability. Instead we favoured using more simple approach in which a patch is simply a higher order mutant [9], [36] and we perform a single generation search over this space; essentially little more than random search, with some smart selection. As such, our results may best be thought of as a base line [37], against which to measure the advances we hope to see produced by future research and development.

SAPFIX 以软件工程方法为基础，被称为基于搜索的软件工程 (SBSE) [34]，[35]; 软件系统的潜在修复空间被认为是由对正在测试的现有系统的小修改构建的搜索空间。在 SAPFIX 的部署中，我们不声称核心修复算法有任何强烈的新颖性。事实上，SAPFIX 没有使用任何关于修复和 SBSE 的文献中的修复方法，因为它们的复杂性可能抑制了可扩展性。相反，我们赞成使用更简单的方法，其中一个补丁只是一个高阶突变体 [9]，[36]，我们在这个空间执行单代搜索; 基本上只是随机搜索，有一些聪明的选择。因此，我们的结果最好被认为是一条基线 [37]，以此来衡量我们希望看到的未来研发产生的进步。

## CONCLUSIONS AND FUTURE WORK

The SAPFIX system is now running in continuous integration and deployment. This is the first time that automated end-to-end repair has been deployed into production on industrial software systems in continuous integration and deployment. Much remains to be done. Our repairs aim to tackle the most prevalent (yet arguably also the most simple) bugs, fixable by small patches, comparatively easily checked by the final human gatekeeper. As such, our paper tackles few, if any, of the many interesting and exciting open research problems for automated repair. SapFix patches tend to ameliorate rather than fix root causes, which remains an open problem. Nevertheless, even masking a newly-landed failure can be useful to unblock automated testing of other recent code changes. We share the lessons we learned from the deployment of SAPFIX in this paper seeking to provide additional input to the development of this challenging but important research field from a practical industrial perspective. We hope this contributes to on-going and further research and development on Automated Program Repair, Automated Software Testing and Search Based Software Engineering.

SAPFIX 系统现在正在持续集成和部署中运行。这是第一次将自动化端到端修复以持续集成和部署的方式部署到工业软件系统的生产中。仍有许多工作要做。我们的修复旨在解决最普遍的 (但可以说也是最简单的) 错误，可通过小补丁修复，相对容易由最终的人类看门人检查。因此，我们的论文解决了许多有趣和令人兴奋的自动化修复开放研究问题中的少数 (如果有的话)。SapFix 补丁倾向于改善而不是修复根本原因，这仍然是一个开放的问题。尽管如此，即使屏蔽新登陆的故障也可能有助于解除对其他最近代码更改的自动测试。我们在本文中分享了我们从 SAPFIX 部署中吸取的经验教训，试图从实际工业角度为这个具有挑战性但重要的研究领域的发展提供额外的投入。我们希望这有助于自动化程序修复、自动化软件测试和基于搜索的软件工程的持续和进一步的研究和开发。
