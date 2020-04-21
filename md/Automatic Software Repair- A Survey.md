# Automatic Software Repair: A Survey
2019

## Abstract

Despite their growing complexity and increasing size, modern software applications must satisfy strict release requirements that impose short bug fixing and maintenance cycles, putting significant pressure on developers who are responsible for timely producing high-quality software. To reduce developers workload, repairing and healing techniques have been extensively investigated as solutions for efficiently repairing and maintaining software in the last few years. In particular, repairing solutions have been able to automatically produce useful fixes for several classes of bugs that might be present in software programs. A range of algorithms, techniques, and heuristics have been integrated, experimented, and studied, producing a heterogeneous and articulated research framework where automatic repair techniques are proliferating. This paper organizes the knowledge in the area by surveying a body of 108 papers about automatic software repair techniques, illustrating the algorithms and the approaches, comparing them on representative examples, and discussing the open challenges and the empirical evidence reported so far.

尽管现代软件应用程序的复杂性和规模在不断增长，但它们必须满足严格的发布需求，这些需求要求缩短bug修复和维护周期，这给负责及时生成高质量软件的开发人员带来了巨大的压力。为了减少开发人员的工作量，在过去的几年中，修复和修复技术作为有效地修复和维护软件的解决方案被广泛地研究。特别是，修复解决方案能够自动为软件程序中可能出现的几类bug生成有用的修复。一系列的算法、技术和启发法已经被集成、实验和研究，产生了一个异构的、铰接的研究框架，其中自动修复技术正在激增。本文通过对108篇关于软件自动修复技术的论文的调查，整理了该领域的知识，阐述了算法和方法，并将它们与典型的例子进行了比较，讨论了目前所报道的开放挑战和经验证据。

## 1 INTRODUCTION

DEBUGGING software failures is still a painful, time consuming, and expensive process. For instance, recent studies showed that debugging activities often account for about 50 percent of the overall development cost of software products [1], [2].

1. T. Britton, L. Jeng, G. Carver, and P. Cheak, "Reversible debugging software - quantify the time and cost saved using reversible debuggers," 2013.
2. Undo Software, "Increasing software development productivity with reversible debugging," Undo Software, Tech. Rep. white paper, 2014.

调试软件失败仍然是一个痛苦、耗时和昂贵的过程。例如，最近的研究表明，调试活动通常占软件产品[1]、[2]的总体开发成本的50%左右。

There are many factors contributing to the cost of debugging, but the most impacting one is the extensive manual effort that is still required to identify and remove faults. In particular, the debugging process requires analyzing and understanding failed executions, identifying the causes of the failures, implementing fixes, and validating that the fixed program works correctly, that is, the problem has been fixed without introducing any side effect [3], [4], [5]. Most of these activities are executed manually or with partial tool support.

- 3. M. A. M€ullerburg, "The role of debugging within software engineering environments," ACM SIGSOFT Softw. Eng. Notes, vol. 8, no. 4, pp. 81–90, 1983, doi: 10.1145/1006147.1006165. 
- 4. B. Hailpern and S. Padmanabhan, "Software debugging, testing, and verification," IBM Syst. J., vol. 41, no. 1, pp. 4–12, 2002, doi: 10.1147/sj.411.0004. 
- 5. A. Zeller, Why Programs Fail, Second Edition: A Guide to Systematic Debugging, 2nd ed. Burlington, MA, USA: Morgan Kaufmann, 2009.
[这是一本书]

影响调试成本的因素有很多，但影响最大的是仍然需要大量的手工工作来识别和删除错误。特别地，调试过程需要分析和理解失败的执行，确定失败的原因，实现修复，并验证修复后的程序是否正确工作，也就是说，在没有引入任何副作用[3]、[4]、[5]的情况下修复了问题。大多数这些活动都是手动执行的，或者使用部分工具支持。

So far, the automation of debugging activities essentially concerned with the identification of the statements that are likely to be faulty [6], [7], [8], with the isolation of the specific inputs or application states that may cause failures [9], [10], [11], and with the detection of the anomalous events that may partially explain the reason of a failure[12], [13], [14], [15].

- [6] J. A. Jones and M. J. Harrold, "Empirical evaluation of the Tarantula automatic fault-localization technique," in Proc. Int. Conf. Automated Softw. Eng., 2005, pp. 273–282, doi: 10.1145/1101908.1101949. 
- [7] C. Liu, X. Yan, L. Fei, J. Han, and S. P. Midkiff, "Sober: Statistical model-based bug localization," in Proc. Joint Meet. Eur. Softw. Eng. Conf. Symp. Foundations Softw. Eng., 2005, pp. 286–295, doi: 10.1145/1081706.1081753. 
- [8] R. Abreu, P. Zoeteweij, and A. J. C. van Gemund, "Spectrumbased multiple fault localization," in Proc. Int. Conf. Automated Softw. Eng., 2009, pp. 88–99, doi: 10.1109/ASE.2009.25.

- [9] A. Zeller, “Yesterday, my program worked. Today, it does not. Why?” in Proc. Joint Meet. Eur. Softw. Eng. Conf. Symp. Found. Softw. Eng., 1999, pp. 253–267, doi: 10.1007/3-540-48166-4_16. 
- [10] A. Zeller and R. Hildebrandt, "Simplifying and isolating failureinducing input," IEEE Trans. Softw. Eng., vol. 28, no. 2, pp. 183– 200, Feb. 2002, doi: 10.1109/32.988498. 
- [11] A. Zeller, "Isolating cause-effect chains from computer programs," in Proc. Symp. Foundations Softw. Eng., 2002, pp. 1–10, doi: 10.1145/587051.587053.

- [12] L. Mariani and F. Pastore, "Automated identification of failure causes in system logs," in Proc. Int. Symp. Softw. Rel. Eng., 2008, pp. 117–126, doi: 0.1109/ISSRE.2008.48. 
- [13] A. Babenko, L. Mariani, and F. Pastore, "AVA: Automated interpretation of dynamically detected anomalies," in Proc. Int. Symp. Softw. Testing Anal., 2009, pp. 237–248, doi: 10.1145/ 1572272.1572300. 
- [14] L. Mariani, F. Pastore, and M. Pezze, "Dynamic analysis for diagnosing integration faults," IEEE Trans. Softw. Eng., vol. 37, no. 4, pp. 486–508, Jul./Aug. 2011, doi: 10.1109/TSE.2010.93. 
- [15] D. Zuddas, W. Jin, F. Pastore, L. Mariani, and A. Orso, "MIMIC: locating and understanding bugs by analyzing mimicked executions," in Proc. Int. Conf. Automated Softw. Eng., 2014, pp. 815– 826, 10.1145/2642937.2643014.

到目前为止,有关自动化调试活动本质上与识别的语句可能错误的[6],[7],[8],隔离的特定输入或应用程序状态,可能会导致失败[9],[10],[11],和异常事件的检测可能部分解释失败的原因[12],[13],[14],[15]。

Techniques for the isolation of the likely faulty statements report to testers a list of statements ranked by suspiciousness, determined by considering the number of failing and passing test cases that execute each statement [6], [7], [8]. The intuition is that the most suspicious statements should be executed by several failing tests and few passing test cases.

隔离可能有错误的语句的技术向测试人员报告一个根据可疑程度排序的语句列表，根据执行每个语句[6]、[7]、[8]的失败和通过测试用例的数量来确定。直觉上，最可疑的语句应该由几个失败的测试和几个通过的测试用例来执行。

Techniques that identify specific inputs and specific states that may trigger failures are often based on the well-known Delta Debugging technique [9]. The intuition is that by iteratively refining and reducing the input [10] and the state space [11], it should be possible to identify the smallest input and the smallest portion of the application state that cannot be eliminated to observe the failure. This information is relevant to understand the conditions that may trigger failures.

识别特定输入和可能触发故障的特定状态的技术通常基于众所周知的增量调试技术[9]。直观的理解是，通过迭代地细化和减少输入[10]和状态空间[11]，应该能够识别出最小的输入和应用程序状态中不能被排除以观察故障的最小部分。这些信息与理解可能触发故障的条件有关。

Finally, anomaly detection techniques can detect the operations that are executed by an application during failures but not during correct executions. These operations may explain why and how an application failed. Anomaly detection techniques usually exploit specification mining approaches [16], [17], [18], [19] to automatically generate models that represent the legal behavior of an application, and then use these models to analyze the failed executions and determine the anomalous events [12], [13], [14], [15].

规范挖掘方法
- [16] M. D. Ernst, J. Cockrell, W. G. Griswold, and D. Notkin, “Dynamically discovering likely program invariants to support program evolution,” IEEE Trans. Softw. Eng., vol. 27, no. 2, pp. 99–123, Feb. 2001, doi: 10.1145/302405.302467. 
- [17] D. Lorenzoli, L. Mariani, and M. Pezze, “Automatic generation of software behavioral models,” in Proc. Int. Conf. Softw. Eng., 2008, pp. 501–510, doi: 10.1145/1368088.1368157. 
- [18] M. Gabel and Z. Su, “Testing mined specifications,” in Proc. Int. Symp. Foundations Softw. Eng., 2012, pp. 4:1–4:11, doi: 10.1145/ 2393596.2393598. 
- [19] I. Krka, Y. Brun, and N. Medvidovic, “Automatic mining of specifications from invocation traces and method invariants,” in Proc. Int. Symp. Found. Softw. Eng., 2014, pp. 178–189, doi: 10.1145/ 2635868.2635890.

最后，异常检测技术可以检测应用程序在故障期间执行的操作，但在正确执行期间不能检测这些操作。这些操作可以解释应用程序失败的原因和原因。异常检测技术通常利用规范挖掘方法[16]、[17]、[18]、[19]自动生成代表应用程序合法行为的模型，然后利用这些模型分析执行失败的情况，确定异常事件[12]、[13]、[14]、[15]。

All these techniques provide useful insights about the possible locations of the faults, the inputs and states responsible for the failures, as well as the anomalous operations executed during failures. However, developers must still put a relevant effort on the analysis of the failed executions to exactly identify the faults that must be fixed. In addition, these techniques do not help the developers with the synthesis of an appropriate fix.

所有这些技术都提供了关于故障可能的位置、故障的输入和状态以及故障期间执行的异常操作的有用信息。但是，开发人员仍然必须对失败的执行进行相关的分析，以准确地确定必须修复的错误。此外，这些技术不能帮助开发人员合成适当的修复。

Recently, researchers focused on a new class of approaches, namely program repair techniques [20], [21], [22], [23], [24], [25], [26], [27], [28], [29]. The key idea of these techniques is to try to automatically repair software systems by producing an actual fix that can be validated by the testers before it is finally accepted, or that can be adapted to properly fit the system. The benefit of using these techniques is that the fix both explains the reason of the failure and provides a possible solution to the problem, thus alleviating the effort necessary to identify and correct faults [30], [31].

程序修复技术

- [20] W. Weimer, T. Nguyen, C. Le Goues, and S. Forrest, "Automatically finding patches using genetic programming," in Proc. Int. Conf. Softw. Eng., 2009, pp. 364–374, doi: 10.1109/ ICSE.2009.5070536. 
- [21] H. Nguyen, D. Qi, A. Roychoudhury, and S. Chandra, "Semfix: Program repair via semantic analysis," in Proc. Int. Conf. Softw. Eng., 2013, pp. 772–781, doi:10.1109/ICSE.2013.6606623. 
- [22] D. Kim, J. Nam, J. Song, and S. Kim, "Automatic patch generation learned from human-written patches," in Proc. Int. Conf. Softw. Eng., 2013, pp. 802–811, doi: 10.1109/ICSE.2013.6606626. 
- [23] F. DeMarco, J. Xuan, D. L. Berre, and M. Monperrus, "Automatic repair of buggy if conditions and missing preconditions with SMT," in Proc. Int. Workshop Constraints Softw. Testing Verification Anal., 2014, pp. 30–39, doi: 10.1145/2593735.2593740. 
- [24] S. Sidiroglou-Douskos, E. Lahtinen, F. Long, and M. Rinard, "Automatic error elimination by horizontal code transfer across multiple applications," in Proc. Conf. Program. Language Des. Implementation, 2015, pp. 43–54, doi: 10.1145/2737924.2737988. 
- [25] V. Dallmeier, A. Zeller, and B. Meyer, "Generating fixes from object behavior anomalies," in Proc. Int. Conf. Automated Softw. Eng., 2009, pp. 550–554, doi: 10.1109/ASE.2009.15. 
- [26] S. Marcote and M. Monperrus, "Automatic repair of infinite loops," Tech. Rep. hal-01144026, 2015. 
- [27] T. Ackling, B. Alexander, and I. Grunert, "Evolving patches for software repair," in Proc. Annu. Conf. Genetic Evol. Comput., 2011, pp. 1427–1434, doi: 10.1145/2001576.2001768. 
- [28] F. Long and M. Rinard, "Staged program repair with condition synthesis," in Proc. Joint Meet. Eur. Softw. Eng. Conf. Symp. Foundations Softw. Eng., 2015, pp. 166–178, doi: 10.1145/ 2786805.2786811. 
- [29] Y. Qi, X. Mao, Y. Lei, Z. Dai, and C. Wang, "The strength of random search on automated program repair," in Proc. Int. Conf. Softw. Eng., 2014, pp. 254–265, doi: 10.1145/2568225.2568254.

- [30] Y. Tao, J. Kim, S. Kim, and C. Xu, "Automatically generated patches as debugging aids: a human study," in Proc. Int. Symp. Found. Softw. Eng., 2014, pp. 64–74, doi: 10.1145/2635868.2635873. 
- [31] W. Weimer, "Patches as better bug reports," in Proc. Int. Conf. Generative Program. Component Eng., 2006, pp. 181–190, doi: 10.1145/1173706.1173734.

最近，研究人员关注于一类新的方法，即程序修复技术[20]、[21]、[22]、[23]、[24]、[25]、[26]、[27]、[28]、[29]。这些技术的关键思想是，通过生成一个实际的补丁来自动修复软件系统，这个补丁可以在最终被接受之前由测试人员进行验证，或者可以调整以适当地适合系统。使用这些技术的好处是，修复既解释了失败的原因，又为问题提供了可能的解决方案，从而减轻了识别和纠正错误[30]和[31]所必需的工作。

Since program repair techniques have the potential to dramatically reduce debugging effort, they attracted the interest of many researchers who produced a number of approaches for repairing different classes of faults under different conditions and hypotheses. Important results have been already achieved, but at the same time these results revealed the existence of relevant challenges that have to be faced.

由于程序修复技术有可能极大地减少调试工作，因此吸引了许多研究人员的兴趣，他们提出了许多在不同条件和假设下修复不同类型故障的方法。已经取得了重要的成果，但与此同时，这些成果也揭示了必须面对的相关挑战。

The contributions in the area have been preliminary analyzed by Khalilian et al. [32], who compared three repair techniques, and Monperrus et al. [33], [34] who discussed repair and self-healing techniques focusing on the oracle problem.

- [32] A. Khalilian, A. Baraani-Dastjerdi, and B. Zamani, ‘‘On the evaluation of automatic program repair techniques and tools,’’ in Proc. Iranian Conf. Elect. Eng., 2016, pp. 61–66, doi: 10.1109/ IranianCEE.2016.7585390. 
- [33] M. Monperrus, ‘‘Automatic software repair: A bibliography,’’ Univ. Lille, Lille, France, Tech. Rep. hal-01206501, 2015. 
- [34] M. Monperrus and B. Baudry, ‘‘Two flavors in automated software repair: Rigid repair and plastic repair,’’ in Dagstuhl Seminar n. 13061 ‘‘Fault Prediction, Localization, and Repair’’, 2013.

Khalilian et al.[32]比较了三种修复技术，Monperrus et al.[33]、[34]讨论了修复和自愈技术，重点讨论了oracle问题，并对该领域的贡献进行了初步分析。

This paper contributes to the research on program repair techniques by providing a comprehensive review of the available approaches, critically analyzing the capabilities of repairing techniques, surveying the results achieved so far, and identifying the key open challenges that should be faced by the research community in the future.

这篇论文对程序修复技术的研究做出了贡献，它对现有的方法进行了全面的回顾，批判性地分析了修复技术的能力，调查了到目前为止所取得的成果，并确定了未来研究界应该面临的关键挑战。

The paper is organized as follows. Section 2 describes the procedure we followed to select the papers for this survey and presents statistics about the selected papers. Section 3 introduces software repair solutions contrasting them with software self-healing solutions. Section 4 introduces a running example, consisting of multiple faulty versions of a same program that are used throughout the paper to illustrate the repairing techniques. Section 5 presents the localization techniques that have been exploited as part of the program repair solutions. Section 6 presents the algorithms for generating fixes. Section 7 extends the discussion to techniques recommending fixes that can be turned into an actual fix with small manual effort. Section 8 presents the main empirical findings that have been obtained so far. Section 9 discusses open challenges and future research directions. Section 10 provides final remarks.

论文组织如下。第2节描述了我们为这次调查选择论文所遵循的程序，并给出了关于所选论文的统计数据。第3节将介绍软件修复解决方案，并将它们与软件自修复解决方案进行对比。第4节介绍了一个正在运行的示例，该示例由一个程序的多个错误版本组成，在本文中用于演示修复技术。第5节介绍了作为程序修复解决方案一部分的本地化技术。第6节介绍了生成修复的算法。第7节将讨论扩展到建议修复的技术，这些修复可以通过少量手动操作转换为实际的修复。第8节介绍了迄今为止获得的主要实证结果。第9节讨论了开放的挑战和未来的研究方向。第10节提供最后的说明。

## 2 PAPER SELECTION

We selected the papers for the survey by searching in the ACM Digital Library, the IEEE Explorer Digital Library, and the Google Scholar repository for the following terms commonly used in software repair papers: "program repair", "software repair", "automatic patch generation", "automatic fix generation", "generate and validate", and "semantics driven repair". We performed the paper selection at the beginning of January 2017. For each query, we extracted the first 200 references sorted by relevance. Since a few queries returned less than 200 references, we ended up with a total of 3,262 references to be analyzed. After removing duplicated entries, we obtained a population of 2,573 references.

我们选择的论文调查通过搜索ACM数字图书馆,IEEE Explorer数字图书馆,下列用语和谷歌学术搜索存储库中常用软件修复文件:“程序修复”、“软件修复”,“自动补丁的一代”,“自动修复的一代”,“生成和验证”,“语义驱动的修复”。我们在2017年1月初进行了论文筛选。对于每个查询，我们提取了按相关性排序的前200个引用。由于一些查询返回的引用少于200个，因此我们总共需要分析3262个引用。在删除重复条目之后，我们获得了2,573个引用。

We manually analyzed all these references by checking the abstract, the introduction, and the conclusion sections, and scanning the rest of the paper, to quickly eliminate papers clearly unrelated to the topic of this survey. At the end of this process, we obtained a total of 107 possibly relevant papers. We carefully read these papers to determine their relevance according to the conceptual framework discussed in Section 3. This produced a total of 85 relevant papers. To be as much comprehensive as possible, we included the relevant papers that we missed with our searches but were cited in the papers we selected. This activity produced 23 additional references that have been included in the survey [26], [35], [36], [37], [38], [39], [40], [41], [42], [43], [44], [45], [46], [47], [48], [49], [50], [51], [52], [53], [54], [55], [56] for a total of 108 selected papers. The resulting material has been used to produce this survey.

我们通过检查摘要、引言和结论部分，并扫描论文的其余部分，手动分析所有这些参考文献，以快速排除与本次调查主题明显无关的论文。在这个过程的最后，我们一共获得了107篇可能相关的论文。我们仔细阅读了这些论文，根据第3节讨论的概念框架来确定它们的相关性。共发表相关论文85篇。为了尽可能全面，我们把我们在搜索中漏掉但在我们选择的论文中被引用的相关论文包括在内。这项活动产生了23个额外的参考文献，这些参考文献已经被纳入调查[26]、[35]、[36]、[37]、[52]0、[52]1、[52]3、[52]4、[52]5、[52]6、[52]7、[52]9、[52]9、[53]0、[50]、[53]2、[52]、[53]、[54]、[55]、[56]，共108篇选定的论文。所得到的材料已用于进行这项调查。

We analyzed the distribution of the selected papers per year and per publication venue. Fig. 1 shows the number of papers published on automatic program repair per year. After some early papers on this subject, we can notice a growing number of papers published every year starting from 2005. If we compare the number of papers published in the early 2000s to the papers published in the last few years, we can notice an order of magnitude increment in the number of published papers. This demonstrates a growing interesting of the research community on this subject.

我们分析了每年所选论文的分布情况和每个出版地点。图1为每年发表的《自动程序修复》论文数量。在这方面的早期论文之后，我们可以注意到从2005年开始每年都有越来越多的论文发表。如果我们将21世纪初发表的论文数量与最近几年发表的论文数量进行比较，我们会发现论文数量增加了一个数量级。这表明研究团体对这一课题越来越感兴趣。

Fig. 2 shows the number of papers published in each publication venue. We report explicitly every venue with at least 2 publications, while we collapse all the venues with a single publication in the column Other. We can notice a prevalence of software engineering conferences, with ICSE being by far the venue including the highest number of papers. Conferences related to genetic computation (e.g., GECCO) and programming languages (e.g., PLDI and OOPSLA) are also present. This is a consequence of the nature of the approaches to automatic program repair, which are often based on search and synthesis techniques, and can target a number of languages and environments.

图2为各出版地的论文发表数量。我们明确地报告每个至少有两个出版物的场所，而我们用一个出版物将所有场所折叠在另一个列中。我们可以注意到软件工程会议的流行，到目前为止，ICSE是论文数量最多的会议场所。与遗传计算(如GECCO)和编程语言(如PLDI和OOPSLA)有关的会议也会出席。这是自动程序修复方法的本质导致的结果，自动程序修复方法通常基于搜索和合成技术，可以针对多种语言和环境。

Finally, we checked the distribution of papers between journals and conferences, and found that only 8.3 percent of the publications appeared in journals, while 91.7 percent appeared in other venues, such as conferences and workshops.

最后，我们查看了期刊和会议之间的论文分布，发现只有8.3%的论文出现在期刊上，而91.7%的论文出现在会议和研讨会等其他场所。

## 3 SOFTWARE REPAIR

The problem of automatically generating a fix for a faulty program relates to the more general problem of automatically generating a program that obeys to a given specification.

自动生成故障程序修复的问题与自动生成符合给定规范的程序的更一般问题有关。

This problem is an instance of automatic programming, which is the process operated by a machine of translating a specification about a task into a machine executable program for doing that task [57], [58]. Although the problem of automatically synthesizing software programs has been considered from the early ages of computer science and is still an active area of research [59], [60], [61], the problem of automatically repairing faulty programs have been considered only recently.

- [57] A. W. Biermann, "Automatic programming: A tutorial on formal methodologies," J. Symbolic Comput., vol. 1, no. 2, pp. 119–142, 1985, doi: http://dx.doi.org/10.1016/S0747–7171(85)80010-9. 
- [58] D. L. Parnas, "Software aspects of strategic defense systems," Commun. ACM, vol. 28, no. 12, pp. 1326–1335, 1985, doi: 10.1145/ 214956.214961.

- [59] O. Polozov and S. Gulwani, "FlashMeta: A framework for inductive program synthesis," in Proc. Int. Conf. Object-Oriented Program. Syst. Languages Appl., 2015, pp. 107–126, doi: 10.1145/ 2814270.2814310. 
- [60] R. Alur, et al., "Syntax-guided synthesis," in Proc. Int. Conf. Formal Methods Comput.-Aided Des., 2013, pp. 1–8, doi: 10.1109/ FMCAD.2013.6679385. 
- [61] V. Le and S. Gulwani, "FlashExtract: A framework for data extraction by examples," in Proc. Conf. Program. Language Des. Implementation, 2014, pp. 542–553, doi: 10.1145/2594291.2594333.

这个问题是一个自动编程的实例，它是一个机器操作的过程，将一个关于任务的规范转换成一个机器可执行程序来完成这个任务[57]，[58]。虽然自动合成软件程序的问题在计算机科学的早期就已经被考虑，并且仍然是[59]、[60]、[61]等研究的活跃领域，但是自动修复有问题的程序的问题直到最近才被考虑。

A few approaches pioneered the area of automatic program repairing. Stumptner and Wotawa introduced the idea of defining fault modes to capture frequent faults in VHDL programs [46], [62]. Fault modes have been used to automatically check if a program misbehaves consistently with any known fault and attempt to automatically repair the program with strategies depending on the recognized fault mode. Staber et al. investigated how to automatically replace the components of a system to make it satisfy a given Linear Temporal Logic (LTL) specification [47]. Interestingly, they defined the repair process as a game between the system, which has to be repaired, and the environment, which acts against the system to make the repair fail [43], [47]. Weimer investigated how to repair a program that violates a safety policy specification that specifies the allowed paths in the control flow graph of the program under repair. His approach finds the smallest set of modifications to be performed on the method calls produced by a program to make it satisfy the specification [31]. Other researchers extended model checking with artificial intelligence techniques [48] or template-based strategies [49] to repair programs.

- [46] M. Stumptner and F. Wotawa, "A model-based approach to software debugging," in Proc. Int. Workshop Principles Diagnosis, 1996, pp. 233–239, doi: 10.1007/978-3-540-92814-0_36.
- [62] M. Stumptner and F. Wotawa, ‘‘Model-based program debugging and repair,’’ in Proc. Int. Conf. Ind., Eng. Other Appl. Appl. Intell. Syst., 1996, pp. 155–160.
- [47] S. Staber, B. Jobstmann, and R. Bloem, ‘‘Finding and fixing faults,’’ in Proc. Adv. Res. Work. Conf. Correct Hardware Des. Verification Methods, 2005, pp. 35–49, doi: 10.1007/11560548_6.
- [43] B. Jobstmann, A. Griesmayer, and R. Bloem, ‘‘Program repair as a game,’’ in Proc. Int. Conf. Comput. Aided Verification, 2005, pp. 226–238, doi: 10.1007/11513988_23.
- [31] W. Weimer, ‘‘Patches as better bug reports,’’ in Proc. Int. Conf. Generative Program. Component Eng., 2006, pp. 181–190, doi: 10.1145/1173706.1173734.
- [48] F. Buccafurri, T. Eiter, G. Gottlob, and N. Leone, ‘‘Enhancingmodel checking in verification by AI techniques,’’ Artif. Intell., vol. 112, no. 1–2, pp. 57–104, 1999, doi: 10.1016/S0004-3702(99)00039-9. 
- [49] A. Dennis, R. Monroy, and P. Nogueira, ‘‘Proof-directed debugging and repair,’’ in Proc. Symp. Trends Functional Program., 2006, pp. 131–140.

有几种方法是自动程序修复领域的先驱。Stumptner和Wotawa在VHDL程序[46]中引入了定义故障模式以捕获频繁故障的思想[62]。故障模式用于自动检查程序是否与任何已知故障行为一致，并尝试根据识别的故障模式使用策略自动修复程序。Staber等人研究了如何自动替换系统的组件，使其满足给定的线性时间逻辑(LTL)规范[47]。有趣的是，他们将修复过程定义为系统和环境之间的博弈，系统需要修复，而环境与系统相反，导致修复失败。Weimer研究了如何修复一个违反安全策略规范的程序，该规范在被修复程序的控制流图中指定了允许的路径。他的方法是对程序生成的方法调用执行最小的修改集合，使其满足规范[31]。其他研究人员使用人工智能技术[48]或基于模板的策略[49]扩展模型检查来修复程序。

Research in software repair has started proliferating after the publication of the seminal work by Arcuri et al. [35], [40], [63] and Weimer et al. [20], [64], [65], who have been the first ones to successfully exploit search-based algorithms to generate fixes.

在Arcuri等人的开创性著作[35]、[40]、[63]和Weimer等人的[20]、[64]、[65]发表后，软件修复方面的研究已经开始激增，他们是第一批成功利用基于搜索的算法来生成修复的人。

In the rest of this section, we introduce the basic concepts relevant to the software repair area, and present a conceptual framework that we use to show how the software repair technology works in general, and how it relates to the software self-healing technology.

在本节的其余部分，我们将介绍与软件修复领域相关的基本概念，并提供一个概念框架，我们将使用它来展示软件修复技术的一般工作方式，以及它与软件自修复技术的关系。

### 3.1 Basic Concepts

Two well-known approaches for automatically dealing with program failures are the software healing and software repairing technologies. Although similar in principle, they have different purposes and use different solutions to address faults.

自动处理程序故障的两种众所周知的方法是软件修复和软件修复技术。虽然在原则上相似，但是它们有不同的用途，并且使用不同的解决方案来处理错误。

Software healing solutions detect software failures in-the-field and respond to them by making the necessary adjustments to restore the normal operation of a system [66], [67], [68]. These adjustments are not deployed at the source code level, but rather applied at runtime on the deployed application to prevent or mitigate failures. Multiple occurrences of similar failures on the same instance of the software may trigger the same healing process multiple times.

- [66] H. Psaier and S. Dustdar, "A survey on self-healing systems: approaches and systems," Springer Comput., vol. 91, no. 1, pp. 43–73, 2011, doi: 10.1007/s00607-010-0107-y. 
- [67] D. Ghosh, R. Sharman, H. Rao, and S. Upadhyaya, "Self-healing systems - survey and synthesis," Elsevier Decision Support Syst., vol. 42, no. 4, pp. 2164–2185, 2007, doi: 10.1016/j.dss.2006.06.011. 
- [68] A. Keromytis, "Characterizing self-healing software systems," in Proc. Int. Conf. Math. Methods Models Archit. Comput. Netw. Secur, 2007, pp. 22–33, doi: 10.1007/978-3-540-73986-9_2.

软件修复解决方案在现场检测软件故障，并作出必要的调整以恢复系统的正常运行[66]、[67]、[68]。这些调整并不部署在源代码级别，而是在运行时应用于部署的应用程序，以防止或减轻故障。在软件的同一实例上多次出现类似故障可能会多次触发相同的修复过程。

Software repairing solutions detect software failures, localize where a fix could be applied, and make the necessary adjustments to fix the fault, and thus prevent further failures caused by the same fault [20], [21], [22]. The adjustments are generated in-house, such as at testing and design time, and deployed at the source code level.

软件修复解决方案检测软件故障，定位可以应用修复的位置，并进行必要的调整来修复故障，从而防止由相同的故障[20]、[21]、[22]引起的进一步故障。调整是在内部生成的，例如在测试和设计时，并部署在源代码级别。

Software healing and repairing may or may not involve human intervention. In automatic software healing and repairing, the human only supervises the process. While, when the human is completely out of the loop, these techniques are respectively called software self-healing and software self-repairing.

软件治疗和修复可能涉及人类干预，也可能不涉及人类干预。在自动软件的修复过程中，人类只负责监督这个过程。而当人类完全脱离这个循环时，这些技术分别被称为软件自修复和软件自修复。

Both healing and repairing techniques react to failures by executing some operations. We distinguish between healing operations, which are the operations applied at runtime to turn a failed or a failing execution into a successful execution, and repair operations, which are the operations performed on the program source code to remove the fault that caused a failure.

愈合和修复技术都是通过执行一些操作来应对失败的。我们区分了修复操作和修复操作，修复操作是在运行时将失败或失败的执行转换为成功执行的操作，修复操作是在程序源代码上执行的操作，以删除导致失败的错误。

Software healing and repair techniques may generate either workarounds or fixes. In this context, a workaround is a temporary solution to a software bug [69], while a fix (or a patch) is a permanent solution to a software bug [70]. Workarounds are not designed to implement optimal solutions. They rather consist of solutions that can be quickly generated and deployed, while waiting for a permanent fix from the developers. Workarounds can be applied at multiple levels and on different artifacts. For instance, a workaround may alter the program code, but can also modify the architecture of a system or change its configuration, or can simply alter a specific execution, for example letting users to interrupt unwanted infinite execution loops [71]. Contrarily, fixes always alter the program source code and are expected to implement solutions of the same quality of the fixes produced by the developers when debugging and fixing software faults.

软件修复技术可能会产生工作区或补丁。在这种情况下，工作区是软件缺陷的临时解决方案[69]，而修复(或补丁)是软件缺陷的永久解决方案[70]。工作区不是为了实现最佳解决方案而设计的。它们是由可以快速生成和部署的解决方案组成的，同时等待开发人员的永久修复。工作区可以应用在多个级别和不同的工件上。例如，一种变通方法可以改变程序代码，但也可以修改系统的体系结构或改变其配置，或者只是改变特定的执行，例如允许用户中断不需要的无限执行循环[71]。相反，修复总是改变程序源代码，并且期望实现与开发人员在调试和修复软件错误时产生的修复相同质量的解决方案。

## 3.2 Conceptual Framework

In this section, we present a conceptual framework that covers both healing and repairing solutions, and that illustrates the commonalities and differences between these two approaches.

在本节中，我们提出了一个概念框架，其中包括修复和修复解决方案，并说明了这两种方法之间的共同点和区别。

Although they both deal with failures, software healing and software repairing solutions have different objectives. Software healing solutions are concerned with granting software availability despite failure occurrences [66], [67], [68]. They usually execute healing operations to mask failures [69], [72], [73], or minimize their impact [74], [75]. Identifying and permanently removing the fault that caused a failure is not a primary objective of these techniques. Software repairing solutions instead are concerned with fixing faults, and do not necessarily prevent failures [20], [21], [22]. Indeed failures are tolerated and information extracted during failing executions is even exploited by software repairing solutions to better identify and fix faults. Due to these distinguishing characteristics, software healing solutions are used in-the-field to improve the reliability and the resilience of software systems, and software repairing solutions are used in-house to assist developers in bug fixing tasks.

虽然它们都处理失败，但是软件修复和软件修复解决方案有不同的目标。软件修复解决方案涉及在出现故障时授予软件可用性[66]、[67]、[68]。它们通常执行修复操作来掩盖失败[69]、[72]、[73]，或者最小化失败的影响[74]、[75]。识别并永久消除导致故障的错误并不是这些技术的主要目标。软件修复解决方案关注的是修复故障，不一定能防止故障[20]、[21]、[22]。实际上，失败是可以容忍的，在失败执行过程中提取的信息甚至可以通过软件修复解决方案来更好地识别和修复错误。由于这些不同的特性，软件修复解决方案被用于提高软件系统的可靠性和弹性，而软件修复解决方案被用于内部，以帮助开发人员完成修复错误的任务。

Fig. 3 graphically illustrates the activities involved in the healing and repairing processes, represented on the left and right side of the figure, respectively. Both processes start from a faulty program that fails deviating from its intended behavior, at least for some executions. The failure detection activity, which is common to both processes, is responsible of classifying executions as either failures or failure-free executions.

图3图形化地说明了愈合和修复过程中涉及的活动，分别在图的左侧和右侧表示。这两个进程都是从一个错误的程序开始的，这个错误的程序无法偏离其预期的行为，至少在某些执行中是这样。故障检测活动对两个进程都很常见，它负责将执行分为失败执行和无故障执行。

Failure detection might work differently depending on the process that must be activated. When serving the healing process, failure detection typically detects the early symptoms of program failures to timely trigger the healing process and prevent severe problems, such as system crashes and loss of data. When serving the repairing process, failure detection typically partitions a set of observations into failure and failure-free executions. These executions are then processed to identify and fix the faults that originated them. In contrast to the healing process, the repairing process does not prevent failures, but exploits the information collected during complete (failing) runs to identify faults.

根据必须激活的流程，故障检测的工作方式可能不同。在服务于修复过程时，故障检测通常检测程序故障的早期症状，以及时触发修复过程并防止严重问题，如系统崩溃和数据丢失。在提供修复过程时，故障检测通常将一组观察结果划分为故障和无故障执行。然后对这些执行进行处理，以识别和修复引发它们的错误。与修复过程相反，修复过程并不阻止失败，而是利用在完全(失败)运行期间收集的信息来识别错误。

The way failure detection serves the healing and repairing processes also influences the implementation of the failure detection component. When used to trigger healing operations, it is typically implemented as a runtime monitor that checks the behavior of the application [76], [77]. When used as part of a repairing process, it is typically implemented as a program oracle [78], for instance as assertions embedded in a set of test cases [79].

故障检测服务于修复过程的方式也影响故障检测组件的实现。当用于触发修复操作时，通常将其实现为检查应用程序行为的运行时监控器[76]、[77]。当作为修复过程的一部分使用时，它通常被实现为程序oracle[78]，例如作为嵌入到一组测试用例中的断言[79]。

The healing process shown on the left side of Fig. 3 is composed of two steps that might be executed iteratively. The healing step consists of executing a healing operation that can prevent or mitigate a failure that has been detected. The verification step checks if the application is running as expected after the healing operation has completed. Not all the healing techniques include a verification step, sometime healing operations are applied simply assuming they can only positively or neutrally affect the system. If the verification step detects that the healing operation has failed, the healing step might be repeated, for instance trying a different healing operation and verifying the system again, until the failure has been healed, or no further actions are possible [80], [81]. The healing process usually requires fast healing and verification operations because they are both executed while the application is running in-the-field.

图3左侧显示的愈合过程由两个可能迭代执行的步骤组成。修复步骤包括执行可防止或减轻已检测到的故障的修复操作。验证步骤检查修复操作完成后应用程序是否按预期运行。并不是所有的治疗技术都包括一个验证步骤，有时治疗操作只是简单地假设它们只能对系统产生积极或中性的影响。如果验证步骤检测到修复操作失败，则可能重复该修复步骤，例如尝试不同的修复操作并再次验证系统，直到该失败已被修复，或者不可能有进一步的操作[80]、[81]。修复过程通常需要快速修复和验证操作，因为它们都是在应用程序在字段中运行时执行的。

Since the primary objective of healing techniques is turning failing executions into successful executions, regardless of the fault that originated the problem, the healing process might produce two possible outcomes: (i) successful executions/faulty program, that is, the executions have been healed but the program is still faulty, (ii) failed executions/faulty program, that is, the executions have not been healed and the program is still faulty. In some cases, a successful workaround might be deployed persistently on the running application to automatically heal further occurrences of the same failure, although it can seldom cope with slightly different failures triggered by the same fault.

由于治疗技术的主要目的是将执行失败转化为成功的执行,不管错起源问题,康复过程可能会产生两种可能的结果:
(i)成功的执行/错误的程序,也就是说,死刑已经愈合,但项目仍然是错误的,(ii)执行失败/错误的程序,即死刑尚未痊愈,程序仍然是错误的。在某些情况下，可以在正在运行的应用程序上持久地部署一个成功的解决方案，以自动修复相同故障的进一步发生，尽管它很少能够处理由相同故障触发的稍微不同的故障。

The repairing process shown on the right side of Fig. 3 is composed of three steps. The localization step identifies the locations where a fix could be applied to (note that the faulty statements are not always the only good locations for fixes). The patch/fix step generates fixes that modify the software in the code locations returned by the localization step. The verification step checks if the synthesized fix has actually repaired the software. The fix and verification steps might be iterated several times for multiple locations until the fault has been fixed, until no further fixes can be generated, or until the time allocated to the repair process has been exhausted.

图3右侧所示的修复过程包括三个步骤。本地化步骤标识可以应用修复的位置(请注意，错误语句并不总是修复的惟一好位置)。补丁/修复步骤生成在本地化步骤返回的代码位置中修改软件的修复程序。验证步骤检查合成的修复是否实际修复了软件。修复和验证步骤可能会在多个位置重复几次，直到修复错误为止，直到不能生成进一步的修复，或者直到分配给修复过程的时间已经耗尽为止。

If we compare the repair process to the healing process, it is possible to notice that the repair process includes an extra step. While a healing process reacts directly to failures, the repair process needs to first identify locations suitable for fixes.

如果我们将修复过程与愈合过程进行比较，可能会注意到修复过程包含一个额外的步骤。虽然修复过程直接对失败作出反应，但修复过程需要首先确定适合修复的位置。

In contrast to the healing process, which aims to prevent failures, software repairing techniques exploit the observations collected with multiple runs, including both failures and failure-free executions, to generate fixes. For instance, many repair techniques generate fixes by using the information obtained from the execution of large test suites that include many passing and failing tests.

与旨在防止失败的修复过程相反，软件修复技术利用多次运行收集的观察结果(包括失败和无故障执行)来生成修复。例如，许多修复技术通过使用从大型测试套件(包括许多通过和失败的测试)执行中获得的信息来生成修复。

The repair process might produce two possible outcomes: (i) failed executions/fixed program, that is, the executions have failed but the program has been fixed, thus the fixed fault will not produce further failures, (ii) failed executions/faulty program, that is, the executions have failed and the program is still faulty. The first outcome corresponds to a successful repair, while the second outcome corresponds to an unsuccessful repair.

修复过程可能会产生两种可能的结果:(i)失败执行/固定程序,也就是说,执行失败了,但程序是固定的,因此,固定故障不会产生进一步的失败,(2)执行失败/错误的程序,也就是说,执行失败,程序仍然是错误的。第一个结果对应一个成功的修复，而第二个结果对应一个不成功的修复。

In the following sections, we describe automatic program repair solutions , focusing on the strategies for localization, fixing, and verification. The description is guided by several sample faulty programs that are introduced in the next section. We finally extend the discussion to nearly automatic approaches that can recommend fixes but require the intervention of the developer to finalize and incorporate the fixes in the program under repair.

在接下来的部分中，我们将描述自动程序修复解决方案，重点介绍本地化、修复和验证策略。这个描述是由下一节将介绍的几个示例错误程序所引导的。最后，我们将讨论扩展到几乎自动的方法，这些方法可以建议修复，但是需要开发人员的介入来完成并将修复合并到正在修复的程序中。

## 4 EXAMPLE PROGRAMS

## 5 LOCALIZATION

In this section, we discuss the algorithms used by program repairing techniques to identify the program locations where the fixes should be applied to. This is an important step of the program repair process because the success of a repair may depend not only on the fix itself but also on the location of the fix.

在本节中，我们将讨论程序修复技术使用的算法，这些算法用于识别应用修复的程序位置。这是程序修复过程中的一个重要步骤，因为修复的成功不仅取决于修复本身，还取决于修复的位置。

We distinguish two main classes of approaches for the choice of the location: fault localization and fix locus localization. Fault localization techniques aim at identifying the faulty statements. In this case, the repair is expected to turn the discovered faulty statements into correct statements. For example, in the case of Algorithm 2, a fault localization technique would aim at identifying that the if condition at line 1 is the right place for the generation of a fix.

我们区分了两类主要的定位方法:故障定位和定位定位。故障定位技术的目的是识别故障语句。在这种情况下，预期修复将把发现的错误语句转换成正确的语句。例如，在算法2中，故障定位技术的目标是确定行1中的if条件是生成修复的正确位置。

Fix locus localization techniques (also known as fix localization techniques) aim at detecting the statements where the effect of the fault can be observed and eliminated. These statements are not necessarily the statements with the fault, but they are potentially modifiable to let the program behave correctly. In fact, a faulty behavior may often be fixed by compensating the effect of the fault, rather than removing the fault. For instance, instead of fixing the condition at line 1 of Algorithm 2, it would be possible to fix that same program by adding another if condition that prints the value of b when a is equals to 0 between lines 3 and 4. Thus, a fix locus localization technique may determine line 4 as a suitable place for the generation of a fix that may compensate the fault at line 1.

定位技术(又称定位技术)的目的是检测可以观察和消除故障影响的语句。这些语句不一定是有错误的语句，但是它们可能是可修改的，以便让程序正确运行。实际上，错误行为通常可以通过补偿错误的影响来修复，而不是删除错误。例如，不将条件固定在算法2的第1行，而是可以通过添加另一个if条件来固定相同的程序，当a在第3行和第4行之间等于0时输出b的值。因此，定位技术可以将第4行确定为生成可以补偿第1行故障的定位的合适位置。

Although the approaches in the two classes have different purposes, in the practice a fault localization technique may end up identifying a fix location, and vice versa a fix locus localization technique may end up identifying the location of the fault.

虽然这两类方法有不同的目的，但在实践中，故障定位技术可能最终确定一个固定位置，反之亦然，固定位置定位技术可能最终确定故障的位置。

Both fault and fix locus localization strategies are often defined ad-hoc, coherently with the capabilities and the requirements of the corresponding program repair technique. For instance, AFix [82] can repair the atomicity violation faults specifically localized with CTrigger [83], which combines trace analysis and execution perturbation to extract information about the failure and the faulty code regions.

故障定位策略和定位定位策略通常都是根据相应的程序修复技术的能力和要求而临时定义的。例如，AFix[82]可以修复由CTrigger专门定位的原子性冲突错误[83]，它结合了跟踪分析和执行扰动来提取关于故障和错误代码区域的信息。

When necessary to understand the approach, we describe the fault localization strategy together with the repairing techniques. In this section, we discuss classes of localization approaches that have been exploited by multiple techniques. We refer to these approaches when relevant throughout the paper.

当需要了解该方法时，我们描述了故障定位策略和修复技术。在本节中，我们将讨论已经被多种技术所利用的各种本地化方法。我们引用这些方法时，相关的整个论文。

## 5.1 Fault Localization

Program repairing techniques extensively use SpectrumBased Fault Localization (SBFL) [84] as general mechanism to localize the statements that are likely to be faulty.

Program 修复 技术 广泛 使用 SpectrumBased Fault Localization (SBFL) [84] 一般 机制 本地化 可能 faulty. 的 语句

SBFL exploits information about the behavior of the software collected during testing to identify the program elements that are likely to be faulty. In particular, SBFL produces a list of program elements ranked according to their likelihood of being faulty based on the analysis of the program entities covered by passing and failing tests [6], [85], [86]. The general intuition is that the program elements executed by many failing test cases and few passing test cases are likely to be faulty, and vice versa the program elements executed by many passing test cases and few, or no, failing tests are likely to be correct. Program entities of different kind and granularity might be considered for the localization, such as statements, branches, and paths. Fault localization and program repair usually work at the same granularity level, that is, if the program repairing technique works at the level of program statements, the SBFL algorithm used to localize the fault also works at the statement level.

SBFL利用在测试期间收集到的有关软件行为的信息来识别可能有错误的程序元素。特别是，SBFL根据通过和失败测试[6][85]、[86]所覆盖的程序实体的分析，根据它们出错的可能性来排列程序元素的列表。一般的直觉是，由许多失败测试用例和少数通过测试用例执行的程序元素很可能是错误的，反之亦然，由许多通过测试用例执行的程序元素和少数(或者没有)失败测试可能是正确的。可以为本地化考虑不同种类和粒度的程序实体，例如语句、分支和路径。故障定位和程序修复通常在相同的粒度级别上工作，也就是说，如果程序修复技术在程序语句级别上工作，则用于定位故障的SBFL算法也在语句级别上工作。

We analyzed the papers presenting the techniques that exploit SBFL and we discovered that four fault localization solutions have been more frequently used than others so far: the fault localization algorithm defined in GenProg (32 percent of the papers that use SBFL) [87], [88], Tarantula [6] (18 percent of the papers that use SBFL), Ochiai (18 percent of the papers that use SBFL) [89], and Jaccard [90] (5 percent of the papers that use SBFL). Thus, to illustrate how SBFL works, we describe how these four techniques perform with the faulty program shown in Algorithm 6.

我们分析了论文提出的技术,利用SBFL我们发现四个故障定位方案比其他人更经常使用到目前为止:GenProg中定义的故障定位算法(32%的论文使用SBFL)[87],[88],狼蛛[6](18%的论文使用SBFL),落(18%的论文使用SBFL)[89],和Jaccard[90](5%的论文使用SBFL)。因此，为了说明SBFL是如何工作的，我们描述了这四种技术如何在算法6所示的错误程序中执行。

SBFL requires the availability of a test suite with passing and failing tests. The failing tests reveal the fault that must be localized and repaired. In the example, we assume a test suite with four passing test cases and one failing test case is available. Fig. 4 shows the faulty program (on the left), the test cases (as headers of the columns, the pair of numbers indicates the values of the program inputs a and b, respectively), the statements executed by each test case (each executed statement is indicated with an X), the test outcome (either P for pass or F for fail), and the suspiciousness score returned by Tarantula, Ochiai, GenProg’s fault localization, and Jaccard. The score is a value between 0 (least suspiciousness) and 1 (highest suspiciousness) and is used to rank the program statements. We use a grey background to highlight the statements with the highest score according to Tarantula, Ochiai, and Jaccard. Note that the faulty print statement is in the set of the most suspicious statements for all the techniques. In the following, we illustrate how the four techniques work.

SBFL需要测试套件的可用性，包括通过和失败的测试。失败的测试揭示了必须定位和修复的故障。在这个例子中，我们假设一个测试套件有四个通过的测试用例和一个失败的测试用例。图4显示了错误的程序(左边),测试用例(标题的列,两个数字表示程序输入a和b的值,分别),每个测试用例执行的语句(每个执行语句是用X表示),测试结果为通过或F失败(P),和疑心分数返回的蜘蛛,落,GenProg的错误定位,Jaccard。该值介于0(最不可疑)和1(最可疑)之间，用于对程序语句进行排序。我们使用灰色背景突出显示狼蛛、Ochiai和Jaccard得分最高的语句。请注意，错误的打印语句是所有技术中最可疑的语句之一。在下面，我们将演示这四种技术的工作原理。

We use failed(s) to indicate the number of failed test cases that execute the statement s, total failed to indicate the total number of failed test cases in the test suite, passed(s) to indicate the number of successful test cases that execute the statement s, total passed to indicate the total number of successful test cases in the test suite, and execute(s) to indicate the total number of test cases that execute s.

我们使用失败(s)来表示失败的测试用例执行语句的数量,总没能显示的总数失败的测试用例在测试套件中,通过(s)来指示成功的测试用例,执行该语句的数量,总传递给显示的总数成功的测试套件中的测试用例,并执行(s)来表示测试用例执行的总数。

Tarantula computes the suspiciousness of a statement as the ratio between the rate of failed test cases that execute that statement, and the sum between the rate of passed and failed test cases that execute that same statement. More formally, the suspiciousness suspT of a statement s is

我们使用失败(s)来表示失败的测试用例执行语句的数量,总没能显示的总数失败的测试用例在测试套件中,通过(s)来指示成功的测试用例,执行该语句的数量,总传递给显示的总数成功的测试套件中的测试用例,并执行(s)来表示测试用例执行的总数。

Ochiai [89] computes the suspiciousness of a statement as the ratio between the number of failed tests that execute s and the square radix of the product between the total number of failed tests and the total number of statements that execute s. More formally, given a statement s, its suspiciousness suspO is

落[89]计算的疑心语句执行的失败的测试数量之间的比例和基数广场之间的产品失败的测试的总数和总数量的语句执行。更正式,鉴于年代的一份声明中,疑心suspO

GenProg [87] assigns to each statement one of three possible scores representing the case the statement is never executed by failed test cases, is only executed by failed test cases, or is executed by both failed and passing test cases. More formally, the suspiciousness suspG of a statement s is:

GenProg[87]为每个语句分配三个可能的分数中的一个，这些分数代表了失败的测试用例从不执行语句，只由失败的测试用例执行语句，或者由失败的测试用例和通过的测试用例执行语句。更正式地说，一个声明的可疑之处是:

Jaccard [90] computes the suspiciousness of a statement s as the ratio between the number of failed tests that execute s and the total number of tests that execute s summed to the failing tests that do not execute s. More formally, given a statement s, its suspiciousness suspJ is

Jaccard[90]计算语句的疑心年代失败的数量之间的比率测试执行和测试执行的总数年代总结不执行的失败测试。更正式,鉴于年代的一份声明中,疑心suspJ

Note that the more failed tests execute a statement, the higher the Tarantula, Ochiai, and Jaccard scores are. Vice versa the more passed test cases execute a statement, the lower the Tarantula, Ochiai, and Jaccard scores are. GenProg’s fault localization follows a similar pattern, although based on three possible values only. In particular, all the techniques assign a score of 0 when no failed tests execute a statement, and a score of 1 when all and only the failed tests execute a statement. In the example shown in Fig. 4, the lowest suspiciousness is assigned to the statements in the while loop, since no failed test case executes them. The if statement has a non-zero suspiciousness score because it is executed by all the test cases, including the failed ones. Finally, the most suspicious statements are the ones in the then branch of the if condition (including the condition itself for GenProg’s fault localization), which includes the statement with the fault.

注意，执行语句失败的测试越多，狼蛛、Ochiai和Jaccard的得分就越高。反之亦然，测试用例执行的语句越多，狼蛛、Ochiai和Jaccard的分数越低。GenProg’s 故障 定位 类似 pattern, 虽然 只有 , 基于 三 种 可能 的 值特别是，当没有失败的测试执行一条语句时，所有这些技术的得分都是0，而当所有且只有失败的测试执行一条语句时，所有技术的得分都是1。在图4所示的示例中，while循环中的语句被分配了最低的可疑性，因为没有失败的测试用例执行它们。if语句有一个非零的可疑性评分，因为它是由所有测试用例执行的，包括失败的测试用例。最后，最可疑的语句是if条件的then分支中的语句(包括GenProg错误定位的条件本身)，其中包括带有错误的语句。

In the example, a program repair solution that uses one of these fault localization techniques would focus on the first three statements to synthesize a proper repair. Note that in the example the most suspicious statements include the faulty statement, although this is not guaranteed to happen in general, that is, the faulty statement might be ranked at lower positions if a different set of test cases is provided. The impact of these techniques on the effectiveness of the repair algorithms has been preliminary investigated by Qi et al. [91] and Jaccard has been observed to best support automatic program repair techniques.

在本例中，使用这些故障定位技术之一的程序修复解决方案将重点放在前三条语句上，以合成正确的修复。注意，在这个例子中，最可疑的语句包括错误的语句，尽管这并不能保证在一般情况下都会发生，也就是说，如果提供了一组不同的测试用例，错误的语句可能会排在较低的位置。Qi等人[91]初步研究了这些技术对修复算法有效性的影响，并观察到Jaccard是自动程序修复技术的最佳支持。

## 5.2 Fix Locus Localization

Fix locus localization techniques aim to identify the program locations suitable for the synthesis of fixes, regardless of the location of the faults. The idea is that a program location where the effect of a fault could be recognized could also be exploited to compensate its effect. In the following we describe model-based fix locus localization, which can identify statements that use objects illegally in object-oriented software [25], and angelic fix localization, which can localize the statements relevant to missing or faulty if conditions faults [23].

定位技术的目的是确定适合修复的程序位置，而不考虑故障的位置。其思想是，可以识别故障影响的程序位置也可以用来补偿其影响。在接下来的文章中，我们描述了基于模型的fix locus localization，它可以识别出在面向对象软件[25]中非法使用对象的语句，angelic fix localization，它可以在条件出错时定位到与缺失或错误相关的语句。

### 5.2.1 Model-Based Fix Locus Localization

Model-based fix locus localization has been experienced in the context of program repair techniques that can repair faults that cause the incorrect usage of class interfaces [25], [92], [93], [94].

基于模型的fix轨迹定位已经在程序修复技术的上下文中得到了应用，该技术可以修复导致错误使用类接口[25][92]、[93]、[94]的错误。

Model-based fix locus localization works by first running the passing test cases to collect runtime data and then mining models that represent how classes are used by the program during correct runs. The mined models consist of finite state machines that represent the object life-cycle, that is, how objects are created, when the methods of these objects can be invoked, and when the fields of these objects can be written and read. The mined models are then checked dynamically while the failing test cases are executed. A failed execution that violates a mined model indicates the presence of objects that are incorrectly used by the program, such as code that writes data on a file that has been opened in read-only mode. The program statements that incorrectly use objects are the locations targeted by the fix generation process. Note that these statements, although not necessarily faulty, are clearly appropriate places for enforcing the legal use of objects interfaces.

基于模型的fix轨迹定位首先运行通过的测试用例来收集运行时数据，然后挖掘模型来表示程序在正确运行期间如何使用类。所挖掘的模型由表示对象生命周期的有限状态机组成，即如何创建对象、何时可以调用这些对象的方法以及何时可以读写这些对象的字段。然后在执行失败测试用例时动态地检查挖掘的模型。如果执行失败而违反了挖掘的模型，则表示存在程序不正确使用的对象，例如在以只读模式打开的文件中写入数据的代码。错误使用对象的程序语句是修复生成过程所针对的位置。请注意，这些语句虽然不一定有错误，但显然是强制合法使用对象接口的适当位置。

Although not always experienced in the context of software repair, the idea of mining models from correct executions to successively check failing executions and identify the code elements likely responsible for the failure is shared with several others failure analysis techniques [14], [15].

尽管在软件修复的环境中并不总是有经验，但是从正确的执行到依次检查失败的执行并识别可能导致失败的代码元素的挖掘模型的思想与其他一些失败分析技术[14]、[15]是相同的。

### 5.2.2 Angelic Fix Localization

Angelic fix localization targets faults that can be repaired by modifying the if conditions in a program [23]. The idea is to first localize the suspicious conditions according to their suspiciousness score, and then identify which of these conditions might be wrong by systematically forcing the failing test cases to take alternative branches at decision points, regardless of the outcome of the evaluated condition. For instance, all the inputs with a equals to 0 and b>0 cause Algorithm 2 to fail because the condition at line 1 evaluates to false and the execution does not take the then branch. Angelic fix localization would check what happens when the then branch is taken by the execution, although the condition evaluates to false. Taking the then branch causes the execution to pass, in fact the program would terminate after printing b, which is the right result for all the inputs with a equals to 0 and b>0. These conditional values that make test cases pass are called angelic values. All the decision points that might be used to turn failing test cases into successful test cases according to this simple strategy are selected and returned as possible targets of an automatic program repairing solution designed to address the conditions in a program. The above approach works in a slightly different way when attempting to detect missing if conditions. Angelic fix localization checks if failing test cases can be turned into passing test cases by skipping the execution of single (simple or compound) statements. It investigates the effect of every suspicious statement and returns those statements whose execution should be skipped during failing test cases as targets for the synthesis of fixes. In this case, a fix should consist in the addition of new if statements that properly control the execution of the statements that must be skipped. The synthesis of the appropriate conditions in the if statements is not part of the fault localization approach, but of the fixing process.

Angelic修复定位目标错误，可以通过修改程序[23]中的if条件来修复。其思想是首先根据可疑条件的可疑性评分来定位可疑条件，然后通过系统地迫使失败的测试用例在决策点处选择分支来识别这些条件中的哪些可能是错误的，而不考虑评估条件的结果。例如，所有a = 0和b>的输入都会导致算法2失败，因为第1行条件的计算结果为false，并且执行不会执行then分支。Angelic fix本地化将检查执行时所采取的then分支的情况，尽管条件的计算结果为false。执行then分支会导致执行失败，实际上程序会在打印b之后终止，这对于所有a = 0和b>的输入来说都是正确的结果。这些使测试用例通过的条件值称为天使值。根据这个简单的策略，可以用来将失败的测试用例转换成成功的测试用例的所有决策点都被选择并作为自动程序修复解决方案的可能目标返回，该解决方案旨在解决程序中的条件。当试图检测缺少if条件时，上述方法的工作方式略有不同。Angelic修复本地化检查失败的测试用例是否可以通过跳过单个(简单或复合)语句的执行而转换为通过的测试用例。它调查每个可疑语句的效果，并返回那些在失败测试用例期间应该跳过执行的语句，作为修复的合成目标。在这种情况下，修复应该包括添加新的if语句，这些语句正确地控制必须跳过的语句的执行。if语句中适当条件的综合不是故障定位方法的一部分，而是定位过程的一部分。

An improved version of angelic fix localization [95] extends the original technique with the capability to consider any program expression (instead of only expressions in conditional branches) when extracting angelic values. This is done by first identifying the suspicious program locations and the corresponding expressions with SBFL, and then running a customized symbolic execution that determines the angelic values while replacing the suspicious expressions with symbols.

改进版的angelic fix localization[95]扩展了原有的技术，在提取angelic值时可以考虑任何程序表达式(而不仅仅是条件分支中的表达式)。这是通过首先用SBFL标识可疑的程序位置和相应的表达式来完成的，然后运行一个定制的符号执行来确定天使值，同时用符号替换可疑的表达式。

Note that the fix locus locations returned by angelic fix localization are not necessarily the fault locations. The locations identified by angelic fix localization are places where the flow of the executions could be opportunistically altered, by modifying or adding conditions, to mask or compensate the effect of a fault, and do not necessarily correspond to places with faulty statements.

请注意，天使修复定位返回的修复轨迹位置不一定是故障位置。由angelic fix localization确定的位置是执行的流程可以被机会主义地改变的地方，可以通过修改或添加条件来掩盖或补偿错误的影响，并不一定对应于有错误陈述的地方。

## 6 FIX GENERATION

## 7 FIX RECOMMENDERS

Fix recommenders are techniques that do not attempt to produce fixes, but simply suggest a few changes that might be operated on the software to repair the fault. In some cases the recommended changes might fully describe the required fix, in some other cases some effort might be required to the developers to produce the final fix. Although these techniques do not produce an actual repair, that is, they do not produce a new version of the software that is supposed to be correct, their output can be quickly turned into a fix in the best cases.

修复建议者是不尝试生成修复的技术，而只是建议对软件进行一些可能的更改来修复错误。在某些情况下，建议的更改可能完全描述所需的修复，而在另一些情况下，开发人员可能需要付出一些努力来生成最终的修复。虽然这些技术不会产生实际的修复，也就是说，它们不会产生应该是正确的软件新版本，但是在最好的情况下，它们的输出可以很快地变成修复。

A few fix recommender techniques have been proposed so far. Here we discuss these techniques classified according to their fault model. General techniques aim to be effective with a range of programs, not limiting their scope to a specific class of faults. The other techniques can address security faults, which make programs vulnerable to attacks; data type faults, which may cause failures due to the misuse of data structures and other types of data; concurrency faults, which may cause deadlocks and other concurrency issues; and performance faults, which may cause unacceptable execution time for some functionalities.

目前已经提出了一些修复推荐技术。在这里，我们将根据故障模型对这些技术进行分类讨论。通用技术的目标是对一系列程序有效，而不是将它们的范围限制在特定的故障类别上。其他技术可以解决安全故障，使程序容易受到攻击;数据类型错误，可能由于误用数据结构和其他类型的数据而导致故障;并发故障，可能导致死锁等并发问题;以及性能故障，这可能会导致某些功能无法接受的执行时间。

Table 4 summarizes the discussed techniques (column Techniques), the classes of faults addressed by each technique (column Fault model), and the section in which the technique is described (column Section).

表4总结了讨论的技术(列技术)、每种技术处理的故障类型(列故障模型)以及描述该技术的部分(列部分)。

### 7.1 General Techniques

BugFix [39] can analyze the debugging information at a specific program statement and report to developers a list of possible actions that may fix the faulty code. The technique exploits spectrum-based fault localization to identify the suspicious statements, and uses static and dynamic metrics to classify and relate the problem that must be fixed to the entries in a database of debugging rules. The fix suggestions extracted from the database are finally reported as a prioritized list to the developer.

[39]可以分析特定程序语句中的调试信息，并向开发人员报告可能修复错误代码的操作列表。该技术利用基于频谱的故障定位来识别可疑语句，并使用静态和动态度量来对必须与调试规则数据库中的条目固定的问题进行分类和关联。从数据库中提取的修复建议最终作为优先级列表报告给开发人员。

MintHint [141] also generates a ranked list of actions that can be performed to fix a fault. MintHint identifies the statements that are likely to be faulty using spectrum-based fault localization. Each faulty statement is replaced by a symbolic state transformer (i.e., a sort of abstract statement defined on the state of the program only) that satisfies the property of making the program pass all the available test cases. MintHint then explores a space of possible changes that can be performed on the faulty statement to obtain the same behavior defined by the state transformer. The possible solutions are finally returned to the developer ranked according to their likelihood of occurring in the repaired statement.

MintHint[141]还生成可用于修复错误的操作的排序列表。MintHint使用基于频谱的故障定位来识别可能出错的语句。每个错误语句都被一个符号状态转换器(即，一种只定义在程序状态上的抽象语句)，它满足使程序通过所有可用测试用例的属性。MintHint然后探索可能对错误语句执行的更改空间，以获得状态转换器定义的相同行为。可能的解决方案最终根据其在已修复语句中出现的可能性返回给开发人员。

## 8 EMPIRICAL EVIDENCE

## 9 OPEN CHALLENGES

## 10 CONCLUSIONS

Automatic program repair techniques address the ambitious challenge of automatically repairing faulty software. In the last decade, program repair techniques have produced relevant and impactful results demonstrating the potential of significantly affecting testing, validation, and debugging practices on the long term.

自动程序修复技术解决了自动修复错误软件的艰巨挑战。在过去的十年中，程序修复技术已经产生了相关的和有影响力的结果，证明了在长期内显著影响测试、验证和调试实践的潜力。

This paper organizes the knowledge in the area, surveying the existing work, and discussing the main results and challenges. In particular, this paper shows that program repairing approaches address the problem of repairing software according to two main strategies. Generate-and-validate approaches generate a search space of the possible fixes and then explore this search space looking for a correct fix. Semantics-driven approaches produce a representation of the problem of fixing a program and solve this problem to obtain the actual fixes.

本文组织了该领域的知识，调查了现有的工作，并讨论了主要结果和挑战。特别地，本文指出程序修复方法根据两种主要策略来解决软件修复问题。生成并验证方法生成可能的修复程序的搜索空间，然后在这个搜索空间中寻找正确的修复程序。语义驱动的方法生成修复程序问题的表示，并解决此问题以获得实际的修复。

We integrate and summarize the empirical evidence collected from multiple diverse studies into a set of key facts that show the tradeoffs and factors influencing the effectiveness of these approaches. We finally discuss the challenges we believe are the most important and that can influence the future research in the area.

我们将从多个不同研究中收集到的经验证据整合并总结为一组关键事实，这些事实显示了这些方法的权衡和影响其有效性的因素。最后，我们讨论了我们认为最重要的挑战，这些挑战将影响该领域未来的研究。
