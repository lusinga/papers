# Defects4J: A Database of Existing Faults to Enable Controlled Testing Studies for Java Programs

## ABSTRACT

Empirical studies in software testing research may not be comparable, reproducible, or characteristic of practice. One reason is that real bugs are too infrequently used in software testing research. Extracting and reproducing real bugs is challenging and as a result hand-seeded faults or mutants are commonly used as a substitute.

软件测试研究中的实证研究可能不具有可比性、可复制性或实践特征。其中一个原因是真正的 bug 在软件测试研究中使用太少。提取和复制真正的错误是具有挑战性的，因此手工播种的错误或突变体通常被用作替代品。

This paper presents Defects4J, a database and extensible framework providing real bugs to enable reproducible studies in software testing research. The initial version of Defects4J contains 357 real bugs from 5 real-world open source pro- grams. Each real bug is accompanied by a comprehensive test suite that can expose (demonstrate) that bug. Defects4J is extensible and builds on top of each program's version con-control system. Once a program is configured in Defects4J, new bugs can be added to the database with little or no effort.

本文介绍了 Defects4J，一个数据库和可扩展框架，它提供了真正的 bug，以实现软件测试研究中的可重复研究。Defects4J 的初始版本包含来自 5 个真实世界开源程序的 357 个真实错误。每个真正的 bug 都伴随着一个全面的测试套件，可以暴露 (演示) 该 bug。Defects4J 是可扩展的，构建在每个程序的版本控制系统之上。一旦一个程序在 Defects4J 中被混淆，新的 bug 可以很少或不费力地添加到数据库中。

Defects4J features a framework to easily access faulty and fixed program versions and corresponding test suites. This framework also provides a high-level interface to common tasks in software testing research, making it easy to conduct and reproduce empirical studies. Defects4J is publicly available at http://defects4j.org.

Defects4J 具有一个框架，可以轻松访问错误和固定的程序版本以及相应的测试套件。该框架还为软件测试研究中的常见任务提供了一个高级接口，使其易于进行和再现实证研究。Defects4J 公开在 http://defects4j.org 。

## 1. INTRODUCTION

Reproducibility of empirical studies in software testing research is challenging due to the lack of widely accepted and easy-to-use databases of real bugs. A large number of previously found and fixed bugs are documented in bug tracking systems of open source projects. Yet, extracting, reproducing, and isolating those real bugs requires considerable effort, and hence real bugs are rarely used in software testing research. Existing databases or repositories that provide faulty program versions [2, 4] oer only a very limited quantity of real bugs and as a consequence mutants or hand seeded faults are commonly used as a substitute. However, mutants and hand seeded faults differ from inadvertently introduced bugs, and thus might not be suitable for evaluating testing techniques [6].

由于缺乏广泛接受且易于使用的真实 bug 数据库，软件测试研究中实证研究的再现性具有挑战性。在开源项目的 bug 跟踪系统中记录了大量以前发现和修复的 bug。然而，提取、复制和隔离那些真正的错误需要相当大的努力，因此真正的错误很少用于软件测试研究。提供错误程序版本 [2,4] 的现有数据库或存储库只有非常有限数量的真实错误，因此突变体或手工种子错误通常被用作替代品。然而，突变体和手工种子错误不同于无意中引入的错误，因此可能不适合评估测试技术 [6]。

This paper presents Defects4J, a database and framework providing 357 real bugs to support software testing research. The acronym Defects4J reflects its purpose: A database of existing faults to enable controlled testing studies for Java programs. In summary, Defects4J makes the following contributions:

本文介绍了 Defects4J，一个提供 357 个实际 bug 的数据库和框架，以支持软件测试研究。首字母缩略词 Defects4J 反映了它的目的: 一个现有故障的数据库，以实现对 Java 程序的受控测试研究。总之，Defects4J 做出了以下贡献:

- Defects4J implements a new approach for reproducing and isolating real bugs from version control history. In this context, isolation means that the bug x does not include irrelevant changes such as features or refactorings. Reproducibility is ensured through an accompanying test suite that includes at least one test case that exposes the bug | that is, the test case succeeds on the xed but fails on the faulty program version.

Defects4J 实现了一种新的方法，用于从版本控制历史记录中复制和隔离真正的 bug。在这种情况下，隔离意味着 bug x 不包括无关的更改，如特性或重构。通过附带的测试套件确保再现性，该测试套件包括至少一个暴露错误的测试用例 | 也就是说，测试用例在错误的程序版本上成功，但在错误的程序版本上失败。

- Defects4J enables reproducibility in software testing research by providing a populated database of isolated real bugs for real-world programs. The initial version of Defects4J provides 357 real bugs for 5 large open source programs. We expect the database to grow be- cause Defects4J is designed to be extensible. Defects4J builds on top of the projects' version control systems | once a program is configured in Defects4J, new bugs, e.g., obtained from newly reported bug fixes, can be added to the database with little or no effort.

Defects4J 通过为真实世界的程序提供孤立的真实错误的填充数据库，实现了软件测试研究的再现性。Defects4J 的初始版本为 5 个大型开源程序提供了 357 个真正的 bug。我们预计数据库将增长，因为 Defects4J 被设计成可扩展的。Defects4J 建立在项目的版本控制系统之上 | 一旦在 Defects4J 中发现了一个程序，新的 bug，例如，从新报告的 bug fix 中获得,只需很少或不费力就可以添加到数据库中。

- Defects4J provides a database abstraction layer that eases the use of the bug database. This abstraction provides a uniform interface for checking out faulty and fixed program versions. It also provides uniform access to build and execution targets by abstracting the build systems of all programs.

Defects4J 提供了一个数据库抽象层，简化了 bug 数据库的使用。这个抽象提供了一个统一的接口，用于检查错误和固定的程序版本。它还通过抽象所有程序的构建系统，提供对构建和执行目标的统一访问。

- Defects4J features a test execution framework that eases the implementation of tools for experiments in software testing research. This framework provides several components for common tasks in software testing such as test execution, test generation, and code coverage or mutation analysis.

Defects4J 提供了一个数据库抽象层，简化了 bug 数据库的使用。这个抽象提供了一个统一的接口，用于检查错误和固定的程序版本。它还通过抽象所有程序的构建系统，提供对构建和执行目标的统一访问。

## 2. REPRODUCING AND ISOLATING BUGS FROM VERSION CONTROL HISTORY

This section describes the methodology we used to populate Defects4J's bug database. It also sheds light on challenges to collecting and reproducing real bugs from version control history and how we addressed those challenges.

本节描述了我们用来填充 Defects4J 的 bug 数据库的方法。它还揭示了从版本控制历史中收集和复制真实 bug 的挑战，以及我们如何应对这些挑战。

The overall goal is to identify real bugs (i.e., bugs fixed by a developer), and to obtain, for each bug, a faulty (V_bug ) and a fixed (V_x ) source code version that differ by only the bug x. Specifically, each real bug included in Defects4J's bug database fulfills the following requirements:

总体目标是识别真正的 bug (i.e.,由开发人员修复的 bug)，并为每个 bug 获取仅与 bug x 不同的错误 (V_bug) 和固定 (v_x) 源代码版本。具体来说，Defects4J 的 bug 数据库中包含的每个真实 bug 都满足以下要求:

- The bug is related to source code

A developer explicitly labeled the commit of Vx as a bug fixing commit, and the bug x applies to the source code - bug fixes within the build system, configuration files, documentation, or tests are not included.

- The bug is reproducible

V_x is accompanied by at least one test that passes on Vx but fails on V_bug , and the bug is reproducible using the project's build system and an up-to-date JVM.

- The bug is isolated

The bug x (i.e., the diff between V_bug and V_x ) does not include unrelated changes such as features or refactorings.

- 该 bug 与源代码有关

开发人员明确将 V x 的 commit 标记为 bug 修复 commit，bug x 适用于源代码-构建系统内的 bug 修复，配置文件，文档,或者不包括测试。

- 该 bug 是可复制的

V_x 伴随着至少一个通过 V x 但在 V_bug 上失败的测试，并且使用项目的构建系统和最新的 JVM 可以重现该 bug。

- 该 bug 是孤立的

Bug x (即 V_bug 和 v_x 之间的差异) 不包括不相关的变化，如特性或重构。

Defects4J includes a toolset that supports automation of populating the bug database with real bugs from version control history.

Defects4J 包含一个工具集，该工具集支持使用来自版本控制历史记录的真实 bug 自动填充 bug 数据库。

### 2.1 Identify Real Bugs Fixed by Developers

A fundamental challenge when collecting bugs is deciding what constitutes a bug, and what does not. During our analyses of version control and bug tracking systems, we encountered several types of fixes not related to the source code, or features, which were classified as a "bug fix" by developers. Examples of bugs unrelated to the source code include faulty or incomplete documentation, faulty tests or test input data, and faulty build system configurations.

收集 bug 时的一个基本挑战是决定什么构成 bug，什么不构成 bug。在我们分析版本控制和 bug 跟踪系统的过程中，我们遇到了几种与源代码或功能无关的修复，开发人员将其归类为 “bug 修复”。与源代码无关的 bug 示例包括错误或不完整的文档、错误的测试或测试输入数据以及错误的构建系统配置。

In Defects4J, an automated step identifies candidates for inclusion. A commit is considered a fixed program version Vx if 1) the commit log references a bug id of the bug tracking system or 2) the bug tracking system references a commit id of the version control system. A commit is not relevant if it does not include source code changes.

在 Defects4J 中，一个自动化步骤确定了要包含的候选项。如果 1) 提交日志引用了 bug 跟踪系统的 bug id，或者 2) 错误跟踪系统引用版本控制系统的提交 id。如果提交不包括源代码更改，则它是不相关的。

### 2.2 Reproduce Real Bugs

A committed source code version might fail some of its own tests. Yet, those failing tests do not necessarily expose a bug in the source code. Similarly, the existence of tests that sporadically fail might lead to non-deterministic results.

提交的源代码版本可能无法通过其自身的某些测试。然而，那些失败的测试不一定会暴露源代码中的错误。同样，偶尔失败的测试的存在可能导致非确定性结果。

In Defects4J, an automated step removes all tests that fail on Vx from the test suite before attempting to expose the bug in Vbug - the majority of program versions were accompanied by failing tests. The automated step then executes the test suite on Vbug , and a bug is considered reproducible if at least one test case fails on Vbug due to the fault.

在 Defects4J 中，在尝试在 Vbug 中暴露 bug 之前，自动化步骤从测试套件中删除所有在 V x 上失败的测试-大多数程序版本都伴随着失败的测试。然后，自动化步骤在 Vbug 上执行测试套件，如果由于故障导致 Vbug 上至少有一个测试用例失败，则错误被认为是可复制的。

### 2.3 Isolate Real Bugs

Isolating the bug is crucial to support testing experiments that rely on V_bug and V_x . Consider, for example, the evaluation of a test generation approach. If the difference between V_bug and V_x would include features or refactorings, then a test suite generated for Vx could fail on Vbug simply because Vbug misses features unrelated to the bug.

隔离 bug 对于支持依赖 Vbug 和 Vx 的测试实验至关重要。例如，考虑测试生成方法的评估。如果 Vbug 和 Vx 之间的差异将包括功能或重构，那么为 Vx 生成的测试套件可能会在 Vbug 上失败，因为 Vbug 错过了与 bug 无关的功能。

We manually reviewed the source code diffs of reproducible bugs to verify that they did not include irrelevant changes - if necessary, we isolated the bug fix from the source code diff. Figure 2 visualizes the relationship between Vbug and Vx , and the source code versions Vn-1 and Vn , which represent the source code versions of two consecutive revisions in the project's version control system. The difference between Vbug and Vx is the isolated bug fix, which does not contain unrelated changes.

我们手动审查了可复制 bug 的源代码差异，以验证它们不包括不相关的更改 - 如果有必要，我们将 bug 修复与源代码差异隔离开来。图 2 可视化了 Vbug 和 Vx 之间的关系，以及 Vn-1 和 Vn 的源代码版本，它们代表了项目版本控制系统中两个连续修订的源代码版本。Vbug 和 Vx 的区别是孤立的 bug fix，它不包含不相关的变化。

## 3. DATABASE OF REAL BUGS

### 3.1 Artifacts

### 3.2 Implementation Details

## 4. DATABASE ABSTRACTION LAYER

### 4.1 Provided API

### 4.2 Implementation Details

## 5. TEST EXECUTION FRAMEWORK

### 5.1 Provided API

### 5.2 Implementation Details

## 6. RELATEDWORK

The software-artifact infrastructure repository (SIR) [2] can be considered the first attempt to provide a database of real bugs to enable reproducibility in software testing re- search. SIR currently provides 81 subjects written in Java, C, C++, and C#, but most of the faults are hand-seeded or obtained from mutation. The number of real bugs for Java subjects is 35 and the median size of those subjects is 120 LOC, ranging between 24 and 8,570. Besides, none of the program versions that provide real bugs is accompanied by any tests, and SIR does not provide a uniform build system interface. In contrast to SIR, Defects4J provides 357 real bugs for 5 large real-world programs ranging between 22,000 and 96,000 LOC. Moreover, all 5 programs feature comprehensive test suites and each bug is reproducible with an exposing test case.

软件-工件基础设施存储库 (SIR) [2] 可以被认为是第一次尝试提供真实错误的数据库，以实现软件测试再搜索的再现性。SIR 目前提供了用 Java，C，C + + 和 C # 编写的 81 个主题，但大多数错误都是手工种子或从突变中获得的。Java 受试者的真实 bug 数量为 35，这些受试者的中值大小为 120 LOC，范围在 24 到 8,570 之间。此外，没有一个提供真正 bug 的程序版本伴随着任何测试，SIR 也没有提供统一的构建系统接口。与 SIR 相比，Defects4J 为 357 和 22,000 LOC 之间的 5 个大型现实世界程序提供了 96,000 个真实 bug。此外，所有 5 个程序都具有全面的测试套件，每个 bug 都可以通过公开的测试用例重现。

The iBugs project [1] is the closest related work for a database of real bugs for Java programs. The authors created iBugs to provide a benchmark for fault localization techniques. It contains 223 bugs with an exposing test case. Bugs are also extracted from version control history but not isolated. Moreover, the implementation for populating the iBugs database is not publicly available, and the faulty versions can only be built with an outdated version of the JVM. Compared to iBugs, Defects4J has a broader scope of application and the following three advantages. First, for 5 programs, which differ in size and operation purpose, Defects4J provides 357 bugs, all accompanied by comprehensive test suites of which at least one test case exposes the bug. Second, Defects4J accounts for the fact that developers do not always minimize their commits - all bugs in Defects4J are isolated (i.e., they do not include unrelated changes such as features or refactorings). Third, Defects4J provides a comprehensive test execution framework with several built-in components to support common tasks in software testing research.

IBugs 项目 [1] 是 Java 程序的真实 bug 数据库的最接近的相关工作。作者创建了 iBugs 来提供故障定位技术的基准。它包含 223 个带有公开测试用例的 bug。Bug 也从版本控制历史中提取，但不是孤立的。此外，用于填充 iBugs 数据库的实现不公开，并且只能使用过期的 JVM 版本构建错误版本。与 iBugs 相比，Defects4J 具有更广泛的应用范围和以下三个优点。首先，对于大小和操作目的不同的 5 个程序，Defects4J 提供了 357 个 bug，所有 bug 都附带了全面的测试套件，其中至少有一个测试用例暴露了 bug。第二，Defects4J 解释了这样一个事实，即开发人员并不总是最小化他们的提交 -- Defects4J 中的所有 bug 都是孤立的 (i.e.,它们不包括不相关的变化，如特征或重构)。第三，Defects4J 提供了一个具有几个内置组件的全面测试执行框架，以支持软件测试研究中的常见任务。

The Siemens benchmark suite [4] is another set of faulty programs. It consists of 7 C programs, whose sizes vary between 141 and 512 LOC. However, the authors obtained faulty program versions by manually seeding faults, which they described as being very similar to simple mutations.

西门子基准套件 [4] 是另一组有缺陷的程序。它由 7 个 C 程序组成，其大小在 141 和 512 LOC 之间变化。然而，作者通过手动播种故障获得了错误的程序版本，他们描述为非常类似于简单的突变。

## 7. CONCLUSIONS AND FUTURE WORK

This paper presents Defects4J, a database and extensible framework to enable controlled testing studies for Java programs. The initial version of Defects4J contains 357 real bugs for 5 large open source programs, and its comprehensive framework allows an easy integration of those bugs in various software testing studies. The most important feature of Defects4J, in our view, is extensibility. Since Defects4J builds on top of the projects' version control and build systems, new bugs can be added with little or no effort.

本文介绍了 Defects4J，一个数据库和可扩展框架，以实现 Java 程序的受控测试研究。Defects4J 的初始版本包含 5 个大型开源程序的 357 个真实 bug，其全面的框架允许在各种软件测试研究中轻松集成这些 bug。在我们看来，Defects4J 最重要的特性是可扩展性。由于 Defects4J 构建在项目的版本控制和构建系统之上，所以只需很少或不费力就可以添加新的 bug。

Adding a new program to Defects4J requires a one-time manual effort for providing a program-specific wrapper build file but reproducing bugs is an automated step. The inclusion of a new bug into Defects4J's database for an already existing program is automated if the following requirements are fulfilled:

向 Defects4J 添加新程序需要一次性的手动工作来提供程序特定的包装器构建文件，但是复制 bug 是一个自动化的步骤。如果满足以下要求，则可以自动将一个新的 bug 包含到 Defects4J 的数据库中，用于现有程序:

- The build system configuration does not change. 
- The bug fix does not include features and refactorings.
- The fixed program version is accompanied by at least one test case that exposes the bug in the faulty version.
- 生成系统配置不变。
- Bug 修复不包括功能和重构。
- 固定程序版本伴随着至少一个暴露错误版本中 bug 的测试用例。

We assume that the build system configuration of a program does not frequently change. This assumption is supported by our experience with the 5 programs included in the initial version of Defects4J - the build system configuration changed at most 4 times over a development period of 12 years.

我们假设程序的构建系统配置不会经常改变。这一假设得到了我们在 Defects4J 初始版本中包含的 5 个程序的经验的支持 - 在 12 年的开发期间，构建系统配置最多改变了 4 次。

Committing minimized bug fixes together with a regression test that exposes the bug is already considered best practice for some of the included programs. Yet, Defects4J provides utilities to ease the process of bug isolation for the cases where a developer commits a bug fix along with unrelated changes.

对于一些包含的程序，将最小化的错误修复与暴露错误的回归测试一起提交已经被认为是最佳实践。然而，Defects4J 提供了实用程序来缓解开发人员提交 bug 修复以及不相关更改的情况下的 bug 隔离过程。

Adding further programs to enable an increase of the number of bugs in Defects4J is part of our future work. Besides increasing the number of programs and bugs in Defects4J, improving the provided bug metadata by adding a classification[8] is another area for future work. Defects4J is publicly available on its website: http://defects4j.org

添加进一步的程序以增加 Defects4J 中的错误数量是我们未来工作的一部分。除了增加 Defects4J 中的程序和 bug 数量之外，通过添加分类来改进提供的 bug 元数据是未来工作的另一个领域。Defects4J 在其网站上公开发布: http://defects4j.org
