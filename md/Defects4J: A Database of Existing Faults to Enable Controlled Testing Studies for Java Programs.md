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

Defects4J 通过为真实世界的程序提供孤立的真实错误的填充数据库，实现了软件测试研究的再现性。Defects4J 的初始版本为 5 个大型开源程序提供了 357 个真正的 bug。我们预计数据库将增长，因为 Defects4J 被设计成可扩展的。Defects4J 建立在项目的版本控制系统之上 | 一旦在 Defects4J 中发现了一个程序，新的 bug，例如，从新报告的 bug fi 中获得,只需很少或不费力就可以添加到数据库中。

