# SMOKE: Scalable Path-Sensitive Memory Leak Detection for Millions of Lines of Code

## Abstract

Detecting memory leak at industrial scale is still not well addressed, in spite of the tremendous effort from both industry and academia in the past decades. Existing work suffers from an unresolved paradox – a highly precise analysis limits its scalability and an imprecise one seriously hurts its precision or recall. In this work, we present SMOKE, a staged approach to resolve this paradox. In the first stage, instead of using a uniform precise analysis for all paths, we use a scalable but imprecise analysis to compute a succinct set of candidate memory leak paths. In the second stage, we leverage a more precise analysis to verify the feasibility of those candidates. The first stage is scalable, due to the design of a new sparse program representation, the use-flow graph (UFG), that models the problem as a polynomial-time state analysis. The second stage analysis is both precise and efficient, due to the smaller number of candidates and the design of a dedicated constraint solver. Experimental results show that SMOKE can finish checking industrial-sized projects, up to 8MLoC, in forty minutes with an average false positive rate of 24.4%. Besides, SMOKE is significantly faster than the state-of-the-art research techniques as well as the industrial tools, with the speedup ranging from 5.2X to 22.8X. In the twenty-nine mature and extensively checked benchmark projects, SMOKE has discovered thirty previouslyunknown memory leaks which were confirmed by developers, and one even assigned a CVE ID.

尽管在过去的几十年里，工业界和学术界做出了巨大的努力，但是在工业规模上检测内存泄漏的问题仍然没有得到很好的解决。现有的工作存在一个未解决的悖论——高度精确的分析限制了它的可伸缩性，而不精确的分析严重地损害了它的精确性或回忆性。在这项工作中，我们提出了烟雾，一个阶段的方法来解决这个悖论。在第一阶段，我们没有对所有路径使用统一的精确分析，而是使用可伸缩但不精确的分析来计算一组简单的候选内存泄漏路径。在第二阶段，我们利用更精确的分析来验证这些候选者的可行性。第一阶段是可伸缩的，由于设计了一种新的稀疏程序表示，即使用流图(use-flow graph, UFG)，它将问题建模为多项式时间状态分析。第二阶段的分析是精确和有效的，由于较少的候选人和一个专门的约束求解器的设计。实验结果表明，烟气可以在40分钟内完成工业规模项目的检测，检测误报率平均为24.4%，检测误报率最高可达8MLoC。此外，烟雾的速度明显快于最先进的研究技术和工业工具，其速度从5.2倍到22.8倍不等。在29个经过广泛检查的成熟基准测试项目中，SMOKE发现了30个以前未知的内存泄漏，这些泄漏得到了开发人员的确认，其中一个甚至分配了CVE ID。

## I. INTRODUCTION

Despite the tremendous research progress in recent decades [1]–[9], the detection of memory leaks in industrialscale is still pretty much an unsolved problem. In the first half of the year 2018, more than 680 memory leak bugs have been reported in Firefox [10] and Chrome [11]. More than 240 CVE (Common Vulnerabilities and Exposures) entries in 2017 are memory leaks bugs [12]. Apparently, with the explosive growth of the code size and the complexity in modern software [13], a practical memory detector needs to be highly scalable, checking millions of lines of code within minutes , and precise, understanding complex path conditions with less than 30% false positives [14], [15].

- [1] The LLVM Foundation, “Clang static analyzer,” https://clanganalyzer.llvm.org/, 2018. 
- [2] Facebook, Inc., “Infer,” 2018. [Online]. Available: http://fbinfer.com/ 
- [3] Y. Xie and A. Aiken, “Context-and path-sensitive memory leak detection,” in ACM SIGSOFT Software Engineering Notes, vol. 30, no. 5. ACM, 2005, pp. 115–125. 
- [4] D. L. Heine and M. S. Lam, “Static detection of leaks in polymorphic containers,” Proceeding of the 28th international conference on Software engineering - ICSE ’06, p. 252, 2006. 
- [5] ——, “A practical flow-sensitive and context-sensitive C and C++ memory leak detector,” ACM SIGPLAN Notices, vol. 38, no. 5, p. 168, 2003. 
- [6] M. Orlovich and R. Rugina, “Memory Leak Analysis by Contradiction,” Proceedings of International Static Analysis Symposium SAS06, 2006. 
- [7] Y. Jung, “Practical Memory Leak Detector Based on Parameterized Procedural Summaries,” in Proceedings of the 7th International Symposium on Memory Management, 2008. 
- [8] S. Cherem, L. Princehouse, and R. Rugina, “Practical memory leak detection using guarded value-flow analysis,” in Proceedings of the 28th ACM SIGPLAN Conference on Programming Language Design and Implementation, ser. PLDI ’07. New York, NY, USA: ACM, 2007, pp. 480–491. [Online]. Available: http://doi.acm.org.lib.ezproxy.ust.hk/10.1145/1250734.1250789
- [9] Y. Sui, D. Ye, and J. Xue, “Detecting memory leaks statically with
full-sparse value-flow analysis,” IEEE Transactions on Software Engineering,
vol. 40, no. 2, pp. 107–122, Feb 2014.

尽管近几十年来[1]-[9]的研究取得了巨大的进展，但工业规模的内存泄漏检测仍然是一个未解决的问题。在2018年上半年，Firefox[10]和Chrome[11]中已经报告了超过680个内存泄漏漏洞。2017年超过240个CVE(常见漏洞和暴露)条目是内存泄漏bug[12]。显然，随着现代软件[13]中代码大小和复杂性的爆炸式增长，一个实用的内存检测器需要具有高度的可伸缩性，在几分钟内检查数百万行代码，并且在[14]、[15]的误报率低于30%的情况下精确地理解复杂的路径条件。

The state-of-the-art approaches suffer from the scalability and precision paradox. One category of the approaches [4]–[9] give up path sensitivity for scalability, inevitably introducing imprecise results. For example, we observed that, SABER [9], a recent path-insensitive memory leak detection technique, incurs a false positive rate of 66.7% in our evaluation. Another category [1]–[3] traverse the control flow graph and use the path-sensitive analysis to achieve high precision. However, they are known to easily suffer from scalability issues, especially for the whole-program analysis. For example, SATURN [3] is reported to have spent more than 23 hours in checking memory leaks for a 5MLoC code base. Our experiment shows that CSA [1] and INFER [2] fail to analyze large projects of over 2MLoC in two hours.

最先进的方法受到可伸缩性和精确性悖论的影响。其中一类方法[4]-[9]放弃了对可伸缩性的路径敏感性，不可避免地引入了不精确的结果。例如，我们观察到，SABER[9]，一种最近的路径不敏感内存泄漏检测技术，在我们的评估中产生了66.7%的假阳性率。另一类[1]-[3]遍历控制流图，采用路径敏感分析，实现高精度。但是，他们很容易遇到可伸缩性问题，尤其是整个程序分析。例如，据报道SATURN[3]在检查5MLoC代码库的内存泄漏方面花费了超过23个小时。我们的实验表明，CSA[1]和INFER[2]无法在两小时内分析超过2MLoC的大型项目。

Our idea to resolve this paradox is based on an observation that, in real programs, only a small proportion of program paths lead to memory leaks. Therefore, instead of using a sledge hammer, i.e., the expensive path-sensitive analysis, for all paths, we use a two-staged analysis by first computing a succinct set of candidate memory leak paths through a novel scalable and path-insensitive method, followed by a more precise and heavy-weight verification of the feasibility of these paths, in order to achieve path-sensitivity.

我们解决这个矛盾的想法是基于这样一个观察:在真实的程序中，只有一小部分程序路径会导致内存泄漏。而不是使用大锤，例如。代价昂贵的路径敏感分析，对于所有路径，我们使用两阶段分析，首先通过一种新的可伸缩的路径不敏感方法计算一组候选内存泄漏路径，然后对这些路径的可行性进行更精确和重量级的验证，以实现路径敏感。
