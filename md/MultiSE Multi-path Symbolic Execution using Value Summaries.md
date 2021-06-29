# MultiSE: Multi-path Symbolic Execution using Value Summaries

## ABSTRACT

Dynamic symbolic execution (DSE) has been proposed to effectively generate test inputs for real-world programs. Unfortunately, DSE techniques do not scale well for large realistic programs, because often the number of feasible execution paths of a program increases exponentially with the increase in the length of an execution path.

已经提出动态符号执行 (DSE) 来有效地为现实世界的程序生成测试输入。 不幸的是，DSE 技术对于大型现实程序不能很好地扩展，因为程序的可行执行路径的数量通常随着执行路径长度的增加呈指数增长。

In this paper, we propose MultiSE, a new technique for merging states incrementally during symbolic execution, without using auxiliary variables. The key idea of MultiSE is based on an alternative representation of the state, where we map each variable, including the program counter, to a set of guarded symbolic expressions called a value summary. MultiSE has several advantages over conventional DSE and conventional state merging techniques: value summaries en- able sharing of symbolic expressions and path constraints along multiple paths and thus avoid redundant execution. MultiSE does not introduce auxiliary symbolic variables, which enables it to 1) make progress even when merging values not supported by the constraint solver, 2) avoid expensive constraint solver calls when resolving function calls and jumps, and 3) carry out most operations concretely. Moreover, MultiSE updates value summaries incrementally at every assignment instruction, which makes it unnecessary to identify the join points and to keep track of variables to merge at join points.

在本文中，我们提出了 MultiSE，这是一种在符号执行期间增量合并状态的新技术，无需使用辅助变量。 MultiSE 的关键思想是基于状态的另一种表示，我们将每个变量（包括程序计数器）映射到一组称为值摘要的受保护符号表达式。与传统的 DSE 和传统的状态合并技术相比，MultiSE 有几个优点：值摘要能够沿多条路径共享符号表达式和路径约束，从而避免冗余执行。 MultiSE 不引入辅助符号变量，这使其能够 1) 即使在合并约束求解器不支持的值时也能取得进展，2) 在解析函数调用和跳转时避免昂贵的约束求解器调用，以及 3) 具体执行大多数操作。此外，MultiSE 在每个赋值指令处增量更新值汇总，这使得无需识别连接点和跟踪要在连接点合并的变量。

We have implemented MultiSE for JavaScript programs in a publicly available open-source tool. Our evaluation of MultiSE on several programs shows that 1) value summaries are an effective technique to take advantage of the sharing of value along multiple execution path, that 2) MultiSE can run significantly faster than traditional dynamic symbolic execution and, 3) MultiSE saves a substantial number of state merges compared to conventional state-merging techniques.

我们已经在一个公开可用的开源工具中为 JavaScript 程序实现了 MultiSE。 我们在多个程序上对 MultiSE 的评估表明：1) 值摘要是一种利用沿多个执行路径共享值的有效技术，2) MultiSE 的运行速度可以比传统的动态符号执行快得多，3) MultiSE 节省了 与传统的状态合并技术相比，大量的状态合并。

## 1. INTRODUCTION

Symbolic execution is a technique for automatically generating a symbolic model from a program. It has been used successfully as a key component in a variety of applications, including generating high-coverage tests for C [20, 44, 11, 10, 16], C++ [33], C# [48], Java [3, 36, 2, 29, 42], PHP [4], JavaScript [41, 43], x86-binaries [22, 47, 5].

- [41] P. Saxena, D. Akhawe, S. Hanna, F. Mao, S. McCamant, and D. Song. A symbolic execution framework for javascript. In Proceedings of the 2010 IEEE Symposium on Security and Privacy, SP '10, pages 513{528. IEEE, 2010.
- [43] K. Sen, S. Kalasapur, T. Brutch, and S. Gibbs. Jalangi: A selective record-replay and dynamic analysis framework for javascript. In ESEC/FSE'13, August 2013. To appear.

## 2. OVERVIEW

## 3. ALGORITHM

## 4. IMPLEMENTATION

## 5. EVALUATION

## 6. RELATED WORK

