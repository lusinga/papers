# The TLA+ Toolbox

We discuss the workflows supported by the TLA+ Toolbox to write and verify specifications. We focus on features that are useful in industry because its users are primarily engineers. Two features are novel in the scope of formal IDEs: CloudTLC connects the Toolbox with cloud computing to scale up model checking. A Profiler helps to debug inefficient expressions and to pinpoint the source of state space explosion. For those who wish to contribute to the Toolbox or learn from its flaws, we present its technical architecture.

## 1 Introduction

The TLA+ Toolbox combines a set of (command-line) tools into an integrated development environment (IDE). Its primary goal it to make TLA+ more widely used. To encourage the use of the Toolbox, the Toolbox is made freely available. It is developed by an open-source community. Section 2 describes the main Toolbox workflows by showing how the Simple spec is model-checked and mechanically verified inside the Toolbox.2 Section 3 shifts the discussion to the architecture and test infrastructure of the Toolbox. We conclude with outlining future engineering and research-related work.

TLA + 工具箱将一组 (命令行) 工具组合到集成开发环境 (IDE) 中。它的主要目标是使 TLA + 得到更广泛的应用。为了鼓励使用工具箱，工具箱是免费提供的。它是由开源社区开发的。第 2 节通过展示如何在工具箱内对简单规范进行模型检查和机械验证，描述了主要工具箱工作流。第 3 节将讨论转移到工具箱的架构和测试基础设施上。我们总结了未来的工程和研究相关的工作。

### 1.1 Background

TLA+ is a high-level, math-based, formal specification language used at companies such as Amazon and Microsoft to design, specify, and document systems [16]. A system is specified by formulas expressed in the Temporal Logic of Actions [6, 8], a variant of Pnueli’s original linear-time temporal logic [18]. Data structures are represented with Zermelo-Fränkel set theory with choice. TLA+ is an untyped language and thus simpler and more expressive than programming languages [13]. Generating code is not in the scope of TLA+. It is implementation language agnostic and meant to find bugs above the code level. Further material about TLA+ is available [8, 9, 10, 12], but is not needed for what follows.

