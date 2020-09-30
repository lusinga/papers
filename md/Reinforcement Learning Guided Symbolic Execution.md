# Reinforcement Learning Guided Symbolic Execution

## Abstract

Symbolic execution is an indispensable technique for software testing and program analysis. Path-explosion is one of the key challenges in symbolic execution. To relieve the challenge, this paper leverages the Q-learning algorithm to guide symbolic execution. Our guided symbolic execution technique focuses on generating a test input for triggering a particular statement in the program. In our approach, we first obtain the dominators with respect to a particular statement with static analysis. Such dominators are the statements that have to be visited before reaching the particular statement. Then we start the symbolic execution with the branch choice controlled by the policy in Qlearning. Only when symbolic execution encounters a dominator, it returns a positive reward to Q-learning. Otherwise, it will return a negative reward. And we update the Q-table in Qlearning accordingly. Our initial evaluation results indicate that in average more than 90% of exploration paths and instructions are reduced for reaching the target statement compared with the default search strategy in KLEE, which shows the promise of this work.

符号执行是软件测试和程序分析中不可缺少的技术。路径爆炸是符号执行的关键挑战之一。为了缓解这一挑战，本文利用Q-learning算法来指导符号执行。我们的指示符号执行技术主要关注于为触发程序中的特定语句生成测试输入。在我们的方法中，我们首先通过静态分析获得关于特定语句的支配者。这些支配符是必须在到达特定语句之前访问的语句。然后我们用Q-learning策略控制的分支选择开始符号执行。只有当象征性执行遇到主宰者时，它才会对Q-learning给予正面的奖励。否则，它将返回一个负的奖励。我们在Q-learning中相应地更新Q-table。我们的初始评估结果表明，与KLEE中默认的搜索策略相比，为了达到目标语句，平均减少了90%以上的搜索路径和指令，这显示了这项工作的前景。

## I. INTRODUCTION

Symbolic execution was first proposed by King [1] in 1976, which is a widely used technique for software testing and program analysis [2]. Path-explosion is one of the key challenges in symbolic execution [2] as the process to explore all the paths, which is usually exponential to the number of the static branches, is extremely time-consuming [2], [3].

符号执行最早是由国王[1]在1976年提出的，是[2]软件测试和程序分析中广泛使用的一种技术。路径爆炸是符号执行[2]的关键挑战之一，因为探索所有路径的过程(通常是静态分支的指数级)非常耗时。

In this paper, we propose a reinforcement learning guided symbolic execution framework which focuses on the problem of generating a test input for triggering a particular statement in the program. This problem arises in many debugging scenarios. For example, a developer may receive a bug report of an error at a particular line in one program (e.g. an assertion failure that resulted in an error message at that line), lacking a test case correspondingly [4]. In our approach, we combine the Q-learning algorithm [5], one of the algorithms of reinforcement learning, with KLEE [6], a symbolic execution tool, to efficiently generate test input for a statement. We summarize the contributions of this paper as follows:

在本文中，我们提出了一个强化学习引导的符号执行框架，它关注于生成一个测试输入来触发程序中的特定语句的问题。在许多调试场景中都会出现这个问题。例如，开发人员可能会收到一个程序中特定行错误的错误报告(例如，断言失败导致该行出现错误消息)，缺少相应的测试用例[4]。在我们的方法中，我们结合Q-learning算法[5]，其中一个算法的强化学习，与KLEE[6]，一个符号执行工具，有效地生成一个语句的测试输入。我们总结本文的贡献如下:

1) We propose a novel guided symbolic execution algorithm which leverages Q-learning to guide the symbolic execution to a particular program statement.
2) We have realized our algorithm with KLEE and a lightweight Q-learning framework.
3) We evaluate the realization using software verification competition benchmark programs [7]. The initial evaluation results show that more than 90% of exploration paths and instructions are reduced with our algorithm , which shows the promise of this work.

1)我们提出了一种新的引导符号执行算法，它利用Q-learning来引导符号执行到特定的程序语句。
2)我们使用KLEE和轻量级Q-learning框架实现了我们的算法。
3)利用[7]软件对竞争基准程序进行评估验证。初步评估结果表明，该算法减少了90%以上的探测路径和指令，显示了这项工作的前景。

We discuss our detailed methodology in Section II, the evaluation in Section III, and we conclude this work in Section IV.

## II. METHODOLOGY

### III. EVALUATION

