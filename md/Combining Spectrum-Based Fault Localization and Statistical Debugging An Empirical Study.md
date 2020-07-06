# Combining Spectrum-Based Fault Localization and Statistical Debugging: An Empirical Study

## Abstract

Program debugging is a time-consuming task, and researchers have proposed different kinds of automatic fault localization techniques to mitigate the burden of manual debugging. Among these techniques, two popular families are spectrumbased fault localization (SBFL) and statistical debugging (SD), both localizing faults by collecting statistical information at runtime. Though the ideas are similar, the two families have been developed independently and their combinations have not been systematically explored.

程序调试是一项耗时的任务，研究人员提出了各种故障自动定位技术来减轻人工调试的负担。在这些技术中，两个流行的家族是基于频谱的故障定位(SBFL)和统计调试(SD)，都是通过在运行时收集统计信息来定位故障。虽然思想相似，但这两个家族是独立发展的，他们的结合还没有系统地探索。

In this paper we perform a systematical empirical study on the combination of SBFL and SD. We first build a unified model of the two techniques, and systematically explore four types of variations, different predicates, different risk evaluation formulas, different granularities of data collection, and different methods of combining suspicious scores.

在本文中，我们对SBFL和SD的结合进行了系统的实证研究。我们首先建立了这两种技术的统一模型，系统地探索了四种变异类型、不同的谓词、不同的风险评估公式、不同的数据采集粒度、不同的可疑分数组合方法。

Our study leads to several findings. First, most of the effectiveness of the combined approach contributed by a simple type of predicates: branch conditions. Second, the risk evaluation formulas of SBFL significantly outperform that of SD. Third, finegrained data collection significantly outperforms coarse-grained data collection with a little extra execution overhead. Fourth, a linear combination of SBFL and SD predicates outperforms both individual approaches.

我们的研究得出了几个结论。首先，组合方法的大部分有效性来自一种简单的谓词类型:分支条件。第二，SBFL的风险评估公式明显优于SD的。第三，细粒度数据收集的性能明显优于粗粒度数据收集，只需要一点额外的执行开销。第四，SBFL和SD谓词的线性组合优于这两种单独的方法。

According to our empirical study, we propose a new fault localization approach, PREDFL (Predicate-based Fault Localization), with the best configuration for each dimension under the unified model. Then, we explore its complementarity to existing techniques by integrating PREDFL with a state-of-the-art fault localization framework. The experimental results show that PREDFL can further improve the effectiveness of state-of-theart fault localization techniques. More concretely, integrating PREDFL results in an up to 20.8% improvement with regard to the faults successfully located at Top-1, which reveals that PREDFL complements existing techniques.

根据我们的经验研究，我们提出了一种新的故障定位方法，PREDFL(基于预测的故障定位)，在统一的模型下，每个维度都有最佳的配置。然后，我们将PREDFL与先进的故障定位框架相结合，探讨其与现有技术的互补性。实验结果表明，该方法可以进一步提高现有故障定位技术的有效性。更具体地说，集成PREDFL可以使Top-1的故障得到高达20.8%的改善，这表明PREDFL是对现有技术的补充。

## I. INTRODUCTION

Given the cost of manual debugging, a large number of fault localization approaches have been proposed in the past two decades. Among all these approaches, two families of approaches have received significant attentions: spectrum-based fault localization (SBFL) [1]–[3] and statistical debugging (SD) [4]–[7]. Spectrum-based fault localization collects coverage information over different elements during test execution, and calculates the suspiciousness of each element using a risk evaluation formula. On the other hand, statistical debugging seeds a set of predicates into the programs, collects whether they are covered and whether they are evaluated to true during test executions, and calculates the suspiciousness of each predicate using a risk evaluation formula.

- [4] B. Liblit, A. Aiken, A. X. Zheng, and M. I. Jordan, “Bug isolation via remote program sampling,” in PLDI. New York, NY, USA: ACM, 2003, pp. 141–154.
- [5] B. Liblit, M. Naik, A. X. Zheng, A. Aiken, and M. I. Jordan, “Scalable statistical bug isolation,” in PLDI. New York, NY, USA: ACM, 2005, pp. 15–26.
- [6] P. Arumuga Nainar and B. Liblit, “Adaptive bug isolation,” ser. ICSE, New York, NY, USA, 2010, pp. 255–264.
- [7] C. Liu, X. Yan, L. Fei, J. Han, and S. P. Midkiff, “Sober: Statistical model-based bug localization,” ser. ESEC/FSE-13. New York, NY, USA: ACM, 2005, pp. 286–295.

考虑到人工调试的成本，在过去的二十年中提出了大量的故障定位方法。在所有这些方法中，有两类方法受到了很大的关注:基于频谱的故障定位(SBFL)[1] -[3]和统计调试(SD)[4] -[7]。基于频谱的故障定位在测试执行期间收集不同元素的覆盖率信息，并使用风险评估公式计算每个元素的可疑性。另一方面，统计调试将一组谓词播撒到程序中，在测试执行期间收集它们是否被覆盖以及它们是否被评估为真，并使用风险评估公式计算每个谓词的可疑性。

Though being similar in the sense of using runtime coverage information to find the suspicious elements, the two families have been developed mostly independently and researchers in each community explored different aspects of the approaches. In the domain of SBFL, different risk evaluation formulas have been proposed and compared both theoretically and empirically [8], [9]. In the domain of SD, different classes of predicates have been designed and studied [5]–[7], [10]–[14]. Since the basic ideas behind the two families are similar, a question naturally raises: whether is it possible to combine the strength of the two families and how would the combination perform?

- [10] P. Arumuga Nainar, T. Chen, J. Rosin, and B. Liblit, “Statistical debugging using compound boolean predicates,” ser. ISSTA, New York, NY, USA, 2007, pp. 5–15.

- [11] T. M. Chilimbi, B. Liblit, K. Mehra, A. V. Nori, and K. Vaswani, “Holmes: Effective statistical debugging via efficient path profiling,” ser. ICSE, Washington, DC, USA, 2009, pp. 34–44.

- [12] A. X. Zheng, M. I. Jordan, B. Liblit, M. Naik, and A. Aiken, “Statistical debugging: Simultaneous identification of multiple bugs,” ser. ICML, New York, NY, USA, 2006, pp. 1105–1112.

- [13] C. Liu, L. Fei, X. Yan, J. Han, and S. P. Midkiff, “Statistical debugging: A hypothesis testing-based approach,” TSE, vol. 32, no. 10, pp. 831–848, Oct 2006.

- [14] L. Jiang and Z. Su, “Context-aware statistical debugging: From bug predictors to faulty control flow paths,” ser. ASE, New York, NY, USA, 2007, pp. 184–193.

虽然在使用运行时覆盖信息来发现可疑元素的意义上是相似的，但这两个家族主要是独立开发的，每个社区的研究人员都探索了方法的不同方面。在SBFL领域，提出了不同的风险评价公式，并在理论和经验上进行了比较[8]、[9]。在SD领域中，设计并研究了不同类型的谓词[5]-[7]、[10]-[14]。既然这两个家族背后的基本理念是相似的，那么一个问题自然就产生了:这两个家族的力量能否结合起来，结合起来会有什么效果?

To answer this question, we build a unified model that captures the commonalities of the two approaches. In this model, SBFL is treated as a kind of predicate in SD, SBFL risk evaluation formulas are mapped to SD risk evaluation formulas, and the suspicious scores of predicates are mapped back to program elements. In this way, we can combine the two approaches as a unified one and explore different variation points. Based on this model, we perform a systematic study to empirically explore four variation points.

为了回答这个问题，我们构建一个统一的模型来捕获两种方法的共性。该模型将SBFL作为SD中的一种谓词，将SBFL风险评价公式映射到SD风险评价公式中，并将可疑的谓词分数映射回程序元素中。这样，我们可以将两种方法统一起来，探索不同的变异点。在此基础上，对四个变分点进行了系统的实证研究。
