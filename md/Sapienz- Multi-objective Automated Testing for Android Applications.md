# Sapienz: Multi-objective Automated Testing for Android Applications

## ABSTRACT

We introduce Sapienz, an approach to Android testing that uses multi-objective search-based testing to automatically explore and optimize test sequences, minimizing length, while simultaneously maximizing coverage and fault revelation. Sapienz combines random fuzzing, systematic and search-based exploration, exploiting seeding and multi-level instrumentation. Sapienz significantly outperforms (with large effect size) both the state-of-the-art technique Dyno-droid and the widely-used tool, Android Monkey, in 7/10 experiments for coverage, 7/10 for fault detection and 10/10 for fault-revealing sequence length. When applied to the top 1,000 Google Play apps, Sapienz found 558 unique, previously unknown crashes. So far we have managed to make contact with the developers of 27 crashing apps. Of these, 14 have confirmed that the crashes are caused by real faults. Of those 14, six already have developer-confirmed fixes.

我们介绍了 Sapienz，一种 Android 测试方法，它使用基于多目标搜索的测试来自动探索和优化测试序列，最小化长度，同时最大化覆盖范围和故障揭示。Sapienz 结合了随机模糊、系统和基于搜索的探索、播种和多层次仪器。Sapienz 在 7/10 次覆盖实验中显著优于 (大效果尺寸) 最先进的技术 Dyno-droid 和广泛使用的工具 Android Monkey,7/10 用于故障检测，10/10 用于故障揭示序列长度。当应用于前 1,000 名 Google Play 应用时，Sapienz 发现 558 独特的、以前未知的崩溃。到目前为止，我们已经设法与 27 个崩溃应用程序的开发者取得联系。其中，14 起已经证实撞车是由真正的故障引起的。在这 14 个中，6 个已经有开发者确认的修复。

## 1. INTRODUCTION

There are over 1.8 million apps available from the Google Play marketplace, as of January 2016 [9]. For developed internet markets such as the US, UK and Canada, mobile app usage now dominates traditional desktop software usage [29]. Unfortunately, testing technology has yet to catch up, and software testers are faced with additional problems due to device fragmentation [2], which increases test effort due to the number of devices that must be considered. Ac- cording to a study on mobile app development [45], mobile app testing still relies heavily on manual testing, while the use of automated techniques remains rare [48].

截至 2016年1月，Google Play marketplace 提供了超过 180万个应用程序 [9]。对于美国、英国和加拿大等发达互联网市场，移动应用程序的使用现在主导着传统的桌面软件使用 [29]。不幸的是，测试技术还没有赶上，软件测试人员面临额外的问题，由于设备碎片 [2],由于必须考虑的设备数量，这增加了测试工作量。根据一项关于移动应用开发的研究 [45]，移动应用测试仍然严重依赖手动测试，而自动化技术的使用仍然很少 [48]。

Where test automation does occur, it typically uses Google's Android Monkey tool [36], which is currently integrated with the Android system. Since this tool is so widely available and distributed, it is regarded as the current state- of-practice for automated software testing [53]. Although Monkey automates testing, it does so in a relatively unintelligent manner: generating sequences of events at random in the hope of exploring the app under test and revealing failures. It uses a standard, simple-but-effective, default test oracle [22] that regards any input that reveals a crash to be a fault-revealing test sequence.

在测试自动化发生的地方，它通常使用谷歌的 Android 猴子工具 [36]，该工具目前与 Android 系统集成。由于这个工具是如此广泛的可用和分布，它被认为是自动化软件测试的当前实践状态 [53]。尽管 Monkey 自动测试，但它以一种相对不明智的方式进行测试: 随机生成事件序列，希望探索测试中的应用程序并揭示失败。它使用一个标准的、简单但有效的默认测试 oracle [22]，它将任何显示崩溃的输入视为一个显示错误的测试序列。

Automated testing clearly needs to find such faults, but it is no good if it does so with exceptionally long test sequences. Developers may reject longer sequences as being impractical for debugging and also unlikely to occur in practice; the longer the generated test sequence, the less likely it is to occur in practice. Therefore, a critical goal for auto- mated testing is to find faults with the shortest possible test sequences, thereby making fault revelation more actionable to developers.

自动化测试显然需要找到这样的错误，但是如果它用特别长的测试序列这样做是没有用的。开发人员可能会拒绝较长的序列，因为这对于调试来说不切实际，也不太可能在实践中发生; 生成的测试序列越长，在实践中发生的可能性就越小。因此，自动匹配测试的一个关键目标是用尽可能短的测试序列找出错误，从而使错误揭示对开发人员更具可操作性。

Exploratory testing is "simultaneous learning, test design, and test execution" [11], that can be cost-effective and is widely used by industrial practitioners [21, 43, 46] for test- ing in general. However, it is particularly underdeveloped for mobile app testing [41, 42]. Although there exist several test automation frameworks such as Robotium [10] and Appium [3], they require human-implemented scripts, thereby inhibiting full automation.

探索性测试是 “同时学习、测试设计和测试执行” [11]，具有成本效益，并被工业从业者广泛使用 [21,43,46] 用于一般测试。然而，它在移动应用测试方面尤其不发达 [41,42]。虽然存在一些测试自动化框架，如 Robotium [10] 和 Appium [3]，但它们需要人类实现的脚本，从而抑制了完全自动化。

We introduce Sapienz, the rst approach oering multi- objective automated Android app exploratory testing that seeks to maximise code coverage and fault revelation, while minimising the length of fault-revealing test sequences. Our goal is to produce an entirely automated approach that max- imises fault revelation with short test sequences. The key insight in our approach is that minimising test sequence length and maximising other objectives can be combined in a Pareto-optimal multi-objective search-based approach to Android testing. By using Pareto optimality, we do not sac- rice longer test sequences, when they are the only ones that nd faults, nor where they are necessary to achieve higher code coverage. Nevertheless, through its use of Pareto opti- mality, Sapienz progressively replaces such longer sequences with shorter test sequences when equally good. The paper makes the following primary contributions:

我们介绍 Sapienz，第一种提供多目标自动化 Android 应用探索性测试的方法，旨在最大限度地提高代码覆盖率和故障揭示，同时最大限度地缩短故障揭示测试序列的长度。我们的目标是产生一种完全自动化的方法，最大限度地利用短测试序列揭示故障。我们方法中的关键见解是，最小化测试序列长度和最大化其他目标可以结合到基于 Pareto 最优多目标搜索的 Android 测试方法中。通过使用帕累托最优，我们不牺牲更长的测试序列，当它们是唯一发现错误的测试序列时，也不牺牲它们在需要实现更高代码覆盖率的地方。尽管如此，通过使用帕累托最优，Sapienz 在同样好的情况下，逐渐用较短的测试序列替换这种较长的序列。该文件做出了以下主要贡献:

1) The Sapienz approach: the paper introduces the first Pareto multi-objective approach to Android testing, combining techniques used for traditional automated testing, adapt- ing and extending them for Android testing. The approach combines random fuzzing, systematic and search-based exploration, string seeding and multi-level instrumentation, all of which have been extended to cater for, not only traditional white box coverage (which we term skeletal coverage'), but also Android user interface coverage (which we term skin coverage').

1) Sapienz 方法: 本文介绍了 Android 测试的第一个 Pareto 多目标方法，结合了用于传统自动化测试的技术，适应并扩展了它们用于 Android 测试。该方法结合了随机模糊、系统和基于搜索的探索、字符串播种和多级仪器，所有这些都被扩展到迎合,不仅是传统的白盒覆盖 (我们称之为骨骼覆盖)，还有 Android 用户界面覆盖 (我们称之为皮肤覆盖)。

2) Experimental results: we present the results of two systematic experimental studies on open-source real-world Android apps. The first uses the 68 apps from an Android benchmark suite [28], while the second uses a controlled random sample of 10 apps from the entire F-Droid suite, for which Sapienz always outperforms both Dynodroid and Monkey, statistically significantly and with large effect size in 24 out of 30 cases.

2) 实验结果: 我们展示了两个关于开源真实世界 Android 应用程序的系统实验研究结果。第一个使用来自 Android 基准套件 [28] 的 68 个应用程序，而第二个使用来自整个 F-Droid 套件的 10 个应用程序的受控随机样本,其中 Sapienz 总是优于 Dynodroid 和 Monkey，在统计学上显著，并且在 30 个病例中有 24 个病例具有大的效应大小。

3) The tool, Sapienz: a practical Android testing tool Sapienz, which we make publicly available.

3) 工具，Sapienz: 一个实用的 Android 测试工具 Sapienz，我们公开提供。

4) Demonstration of usefulness: an empirical study of the practical usefulness of the technique on the top 1,000 Google play apps. Sapienz found 558 unique crashes. The crashing behavior has been verified on real Android devices (as well as Android emulators). At the time of writing, we have started reporting these to the developers, and 14 have been confirmed to be genuine, previously undetected, faults, 6 of which have already been confirmed as fixed by their developers. Since these are the most popular apps in cur- rent use, they will likely have been thoroughly tested, not merely by their developers, but also by their many (hundreds of thousands of) users. These results demonstrate that Sapienz is a practical tool for Android developers as well as for researchers. This paper is the first Android app testing work to report a large-scale evaluation on popular Google Play apps with developer-confirmed real-world faults.

有用性示范: 前 1,000 名 Google play 应用程序上该技术的实际有用性的实证研究。萨皮恩兹发现了 558 起独特的撞车事故。崩溃行为已经在真正的 Android 设备 (以及 Android 仿真器) 上得到验证。在撰写本文时，我们已经开始向开发人员报告这些，并且已经确认 14 个是真实的，以前未被发现的故障,其中 6 个已经被他们的开发者确认为固定。由于这些是当前使用中最受欢迎的应用程序，它们很可能已经过彻底的测试，不仅是由他们的开发人员，还有他们的许多 (数十万) 用户。这些结果表明，Sapienz 是 Android 开发人员以及研究人员的实用工具。本文是第一款 Android 应用程序测试工作汇报大规模评估应由它对谷歌播放应用程序的开发人员确认实际故障.

## 2. RELATEDWORK AND MOTIVATION

Table 1 presents a brief survey of the characteristics of existing Android testing techniques and tools, which we briefly describe below.

表 1 简要介绍了现有 Android 测试技术和工具的特性，我们将在下面简要介绍。

The most closely related work employs search-based methods. Mahmood et al. introduced EvoDroid [53], the first search-based framework for Android testing. EvoDroid extracts the interface model (based on static analysis of manifest and XML conguration les) and a call graph model (based on code analysis by using MoDisco [8]). It uses these models to guide the computational search process. Unfortunately, its implementation is no longer publicly available.

[53] R. Mahmood, N. Mirzaei, and S. Malek. EvoDroid: Segmented evolutionary testing of Android apps. In Proc. of ESEC/FSE'14, pages 599-609, 2014.
[8] Modisco. http://www.eclipse.org/modisco.

最密切相关的工作采用基于搜索的方法。Mahmood 等人介绍了 EvoDroid [53]，这是第一个基于搜索的 Android 测试框架。EvoDroid 提取接口模型 (基于对 manifest 和 XML 配置的静态分析) 和调用图模型 (基于使用 MoDisco 进行的代码分析 [8])。它使用这些模型来指导计算搜索过程。不幸的是，它的实现不再公开。
