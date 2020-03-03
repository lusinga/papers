# An Empirical Study of Fault Localization Families and Their Combinations

## Abstract

The performance of fault localization techniques is critical to their adoption in practice. This paper reports on an empirical study of a wide range of fault localization techniques on real-world faults. Different from previous studies, this paper (1) considers a wide range of techniques from different families, (2) combines different techniques, and (3) considers the execution time of different techniques. Our results reveal that combined technique significantly outperforms any individual technique (200% increase in defects localized in Top 1), suggesting that combination may be a desirable way to apply fault localization techniques and future techniques should also be evaluated in the combined setting. Our implementation is publicly available for evaluating and combining fault localization techniques.

故障定位技术的性能是故障定位技术在实际应用中的关键。本文对实际故障的多种故障定位技术进行了实证研究。与以往的研究不同，本文(1)考虑了来自不同家族的广泛的技术，(2)结合了不同的技术，(3)考虑了不同技术的执行时间。我们的结果显示，组合技术的性能显著优于任何单独的技术(前1项中的缺陷增加了200%)，这表明组合可能是应用故障定位技术的理想方法，并且未来的技术也应该在组合设置中进行评估。我们的实现公开可用来评估和组合故障定位技术。

## 1 INTRODUCTION

THE goal of fault localization is to identify the defective program elements related to software failures. Manual fault localization is a notoriously time-consuming and tedious task that depends on the programmer’s experience and analysis. Automated fault localization uses static and run-time information about the program (test coverage, program dependency, test output, execution results of mutated programs, etc.) to identify program elements that may be the root cause of the failure. This paper considers seven families of fault localization techniques, which take as input seven different types of information:

故障定位的目标是识别与软件故障相关的有缺陷的程序元素。手动故障定位是一项非常耗时和乏味的任务，它依赖于程序员的经验和分析。自动故障定位使用有关程序的静态和运行时信息(测试覆盖率、程序依赖性、测试输出、突变程序的执行结果等)来识别可能是故障的根本原因的程序元素。本文考虑了七种故障定位技术，将七种不同类型的信息作为输入:

- Spectrum-based fault localization (SBFL) [1], [2], [3]: utilizing test coverage information 
- Mutation-based fault localization (MBFL) [4], [5]: utilizing test results collected from mutating the program  
- (Dynamic) program slicing [6], [7]: utilizing the dynamic program dependency  
- Stack trace analysis [8], [9]: utilizing error messages 
- Predicate switching [10]: utilizing test results from mutating the results of conditional expressions  
- Information retrieval-based fault localization (IRbased FL) [11]: utilizing bug report information  
- History-based fault localization [12], [13]: utilizing the development history
- 基于频谱的故障定位(SBFL)[1]，[2]，[3]:利用测试覆盖信息
- 基于突变的故障定位(MBFL)[4]，[5]:利用从程序突变中收集的测试结果
- (动态)程序切片[6]，[7]:利用动态程序依赖
- 堆栈跟踪分析[8]，[9]:利用错误消息
- 谓词切换[10]:利用测试结果突变的结果的条件表达式
- 基于信息检索的故障定位(IRbased FL)[11]:利用错误报告信息
- 基于历史的故障定位[12]，[13]:利用开发历史

Some techniques compute a suspiciousness score for each program element and can generate a ranked list of elements, such as spectrum-based fault localization. Other techniques only mark a set of elements as suspicious, such as dynamic program slicing.

一些技术为每个程序元素计算一个可疑性评分，并可以生成一个元素的排序列表，例如基于频谱的故障定位。其他技术只将一组元素标记为可疑的，比如动态程序切片。

The performance of fault localization is critical to its adoption in practice. Fault localization techniques are helpful only when the root causes are ranked at a high absolute position [14], [15]; the position should be within the top 5 [16]. A number of empirical studies [17], [18], [19], [20] have evaluated the performance of SBFL and MBFL. However, no empirical study has evaluated the performance of other techniques on real-world defects, as far as we know.

故障定位的性能是故障定位在实际应用中的关键。故障定位技术只有在根本原因处于[14]、[15]较高的绝对位置时才有帮助;位置应该在前5[16]以内。[17]、[18]、[19]、[20]等多项实证研究对SBFL和MBFL的性能进行了评价。然而，据我们所知，还没有实证研究评估其他技术在真实缺陷上的性能。

This paper reports on an empirical study of fault localization techniques. Our study aims to include a wide range of fault localization techniques from different families, including SBFL, MBFL, program slicing, predicate switching, stack trace analysis, information retrieve-based fault localization, and history-based fault localization. Following the insight from existing work [17] that the performance of fault localization techniques may differ between real faults and artificial faults, our study is based on 357 real-world faults from the Defects4J dataset [21].

本文对故障定位技术进行了实证研究。我们的研究旨在包括来自不同家族的广泛的故障定位技术，包括SBFL、MBFL、程序切片、谓词切换、堆栈跟踪分析、基于信息检索的故障定位和基于历史的故障定位。根据现有工作[17]的观点，故障定位技术的性能可能在真实故障和人工故障之间有所不同，我们的研究基于来自于Defects4J数据集[21]的357个真实故障。

In contrast to previous studies, our study explores mainly two novelty aspects. First, since techniques in different families use different information sources, it is interesting to know how much these techniques are correlated to each other. We measured the correlation between different pairs of techniques and explored the possibility of combining these techniques using learning to rank model [22]. In contrast, previous work usually considers techniques in one or a few families, e.g., combining different formulae in SBFL [23] or combining SBFL and history-based techniques [25], and our work is the first to explore the combinations of a wide range of techniques that rely on different information sources.

与以往的研究相比，我们的研究主要探索了两个新颖性方面。首先，由于不同家庭中的技术使用不同的信息源，所以了解这些技术之间的相互关系是很有趣的。我们测量了不同技术对之间的相关性，并探索了利用学习对模型[22]进行排序来结合这些技术的可能性。相比之下，以前的工作通常只考虑一个或几个系列的技术，例如，组合SBFL[23]中的不同公式或组合SBFL和基于历史的技术[25]，而我们的工作是第一个探索依赖于不同信息源的各种技术的组合。

The second novelty is that we measured the time cost of different fault localization techniques. Existing studies have shown that efficiency and scalability are both critical to the adoption of fault localization techniques [16]. Thus, a good fault localization approach must balance between localization performance and cost. We have considered different usage scenarios to find the best balance in practice.

与以往的研究相比，我们的研究主要探索了两个新颖性方面。首先，由于不同家庭中的技术使用不同的信息源，所以了解这些技术之间的相互关系是很有趣的。我们测量了不同技术对之间的相关性，并探索了利用学习对模型[22]进行排序来结合这些技术的可能性。相比之下，以前的工作通常只考虑一个或几个系列的技术，例如，组合SBFL[23]中的不同公式或组合SBFL和基于历史的技术[25]，而我们的工作是第一个探索依赖于不同信息源的各种技术的组合。
