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

SBFL
[
- [1] X. Xie, T. Y. Chen, F.-C. Kuo, and B. Xu, “A theoretical analysis of the risk evaluation formulas for spectrum-based fault localization,” ACM Transactions on Software Engineering and Methodology (TOSEM), vol. 22, no. 4, p. 31, 2013. 
- [2] R. Abreu, P. Zoeteweij, and A. J. Van Gemund, “On the accuracy of spectrum-based fault localization,” in Testing: Academic and Industrial Conference Practice and Research Techniques-MUTATION, 2007. TAICPART-MUTATION 2007. IEEE, 2007, pp. 89–98. 
- [3] M. J. Harrold, G. Rothermel, K. Sayre, R.Wu, and L. Yi, “An empirical investigation of the relationship between spectra differences and regression faults,” Software Testing Verification and Reliability, vol. 10, no. 3, pp. 171–194, 2000.
]
[
- [4] M. Papadakis and Y. Le Traon, “Metallaxis-fl: mutation-based fault localization,” Software Testing, Verification and Reliability, vol. 25, no. 5-7, pp. 605–628, 2015. 
- [5] S. Moon, Y. Kim, M. Kim, and S. Yoo, “Ask the mutants: Mutating faulty programs for fault localization,” in Software Testing, Verification and Validation (ICST), 2014 IEEE Seventh International Conference on. IEEE, 2014, pp. 153–162.
]
- [6] H. Agrawal, J. R. Horgan, S. London, and W. E. Wong, “Fault localization using execution slices and dataflow tests,” in Software Reliability Engineering, 1995. Proceedings., Sixth International Symposium on. IEEE, 1995, pp. 143–151. 
- [7] M. Renieres and S. P. Reiss, “Fault localization with nearest neighbor queries,” in Automated Software Engineering, 2003. Proceedings. 18th IEEE International Conference on. IEEE, 2003, pp. 30–39.

- [8] C.-P. Wong, Y. Xiong, H. Zhang, D. Hao, L. Zhang, and H. Mei, “Boosting bug-report-oriented fault localization with segmentation and stack-trace analysis,” in Software Maintenance and Evolution (ICSME), 2014 IEEE International Conference on. IEEE, 2014, pp. 181–190. 
- [9] R. Wu, H. Zhang, S.-C. Cheung, and S. Kim, “Crashlocator: locating crashing faults based on crash stacks,” in Proceedings of the 2014 International Symposium on Software Testing and Analysis. ACM, 2014, pp. 204–214.

- [10] X. Zhang, N. Gupta, and R. Gupta, “Locating faults through automated predicate switching,” in International Conference on Software Engineering, 2006, pp. 272–281. 
- [11] J. Zhou, H. Zhang, and D. Lo, “Where should the bugs be fixed? more accurate information retrieval-based bug localization based on bug reports,” in Software Engineering (ICSE), 2012 34th International Conference on. IEEE, 2012, pp. 14–24. 
- [12] S. Kim, T. Zimmermann, E. J. Whitehead Jr, and A. Zeller, “Predicting faults from cached history,” in Proceedings of the 29th international conference on Software Engineering. IEEE Computer Society, 2007, pp. 489–498. 
- [13] F. Rahman, D. Posnett, A. Hindle, E. Barr, and P. Devanbu, “Bugcache for inspections: hit or miss?” in Proceedings of the 19th ACM SIGSOFT symposium and the 13th European conference on Foundations of software engineering. ACM, 2011, pp. 322–331.

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

Finally, we have released our experimental infrastructure. This experimental infrastructure can be used by other researchers to evaluate fault localization techniques and to combine different fault localization techniques.

最后，我们发布了我们的实验基础设施。这个实验基础设施可以被其他研究人员用来评估故障定位技术，并结合不同的故障定位技术。

Our study has the following main findings: 
- On real-world faults, all techniques except for Bugspots localize more than 6% faults at top 10, while the best family, SBFL, localizes about 44% faults at top 10. 
- Most techniques in our study have a weak correlation, especially those in different families, indicating the potential of combining them. 
- Combining techniques improves performance significantly: 200/63/51/31% increase in top 1/3/5/10 localized defects, and 48% decrease in examined elements compared to the best standalone technique. 
- This combined technique also outperforms the four state-of-art fault localization approaches, MULTRIC [23], Savant [24], FLUCCS [25], and TraPT [26] by 133%, 167%, 11% and 18% in Top 1 correspondingly. 
- Time costs of different fault localization families can be categorized into several levels. When using a technique at one time cost level, it does not affect run time, but does improve fault localization effectiveness, to include all techniques from the preceding levels. 
- The above findings hold at both statement and method granularities — that is, when the FL technique is identifying suspicious statements and when it is identifying suspicious methods.

[
- [23] J. Xuan and M. Monperrus, “Learning to combine multiple ranking metrics for fault localization,” in Software Maintenance and Evolution (ICSME), 2014 IEEE International Conference on. IEEE, 2014, pp. 191–200.
- [24] T.-D. B Le, D. Lo, C. Le Goues, and L. Grunske, “A learning-torank based fault localization approach using likely invariants,” in Proceedings of the 25th International Symposium on Software Testing and Analysis. ACM, 2016, pp. 177–188. 
- [25] J. Sohn and S. Yoo, “Fluccs: using code and change metrics to improve fault localization,” in Proceedings of the 26th ACM SIGSOFT International Symposium on Software Testing and Analysis. ACM, 2017, pp. 273–283.
- [26] X. Li and L. Zhang, “Transforming programs and tests in tandem for fault localization,”
]

我们的研究有以下主要发现:

- 在真实世界的故障中，除了故障点以外的所有技术都将6%以上的故障定位在前10位，而最好的家族SBFL将44%的故障定位在前10位。
- 我们研究的大多数技术，特别是不同家庭的技术，相关性较弱，表明它们有结合的潜力。
- 组合技术显著提高了性能:与最好的单机技术相比，前1/3/5/10局部缺陷增加了200/63/51/31%，检测元素减少了48%。
- 该组合技术也比四种最先进的故障定位方法MULTRIC[23]、Savant[24]、cs[25]和TraPT[26]分别高出133%、167%、11%和18%。
- 不同故障定位族的时间成本可以分为几个层次。当在一个时间成本级别上使用一种技术时，它不会影响运行时，但是会提高故障定位的效率，包括前面级别的所有技术。
- 上述发现同时适用于语句和方法的粒度-即当FL技术识别可疑语句时和当它识别可疑方法时。

To sum up, the paper makes the following contributions.
- The first empirical study that compares a wide range of fault localization techniques on real faults. 
- A combined technique that is configurable based on the time cost, and the peak performance of the technique significantly outperforms single techniques. 
- An infrastructure for evaluating and combining fault localization techniques for future research.

综上所述，本文的贡献如下:

- 第一次实证研究，比较大范围的故障定位技术对实际故障。
- 基于时间成本可配置的组合技术，并且该技术的峰值性能显著优于单个技术。
- 用于评估和组合故障定位技术以供未来研究的基础设施。

The rest of the paper is organized as follows. Section 2 introduces the background of several fault localization families. Section 3 presents the empirical evaluation setups. Section 4 shows the experiment results and answers the research questions. Section 5 discusses the related studies. Section 6 discusses the implications for future research. And Section 7 concludes.

## 2 BACKGROUND

Commonly, a fault localization technique takes as input a faulty program and a set of test cases with at least one failed test, and generates as output a potentially ranked list of suspicious program elements. Recently, some approaches [11], [13], [27] considered other input information, such as the bug report or the develop history. This paper also considers these approaches. The common levels of granularity for program elements are statements, methods, and files. This paper uses statements as program elements, except for Section 4.5 which compares results for different granularities.

通常，故障定位技术将错误的程序和一组至少有一个失败测试的测试用例作为输入，并生成可疑程序元素的潜在排序列表作为输出。最近，一些方法[11]、[13]、[27]考虑了其他输入信息，如错误报告或开发历史。本文也考虑了这些方法。程序元素的常见粒度级别是语句、方法和文件。本文使用语句作为程序元素，除了4.5节比较了不同粒度的结果。

This section first introduces seven families of fault localization techniques, and then introduces the learning to rank model for combining different techniques.

### 2.1 Spectrum-Based Fault Localization

A program spectrum is a measurement of run-time behavior, such as code coverage [3]. Collofello and Cousins proposed that program spectra be used for fault localization [28]. Comparing program spectra on passed and failed test cases enable ranking of program elements. The more frequently an element is executed in failed tests, and the less frequently it is executed in passed tests, the more suspicious the element is.

程序频谱是对运行时行为(如代码覆盖率[3])的度量。Collofello和Cousins提出将程序谱用于故障定位[28]。比较通过和失败测试用例上的程序谱可以对程序元素进行排序。一个元素在失败的测试中执行的频率越高，在通过的测试中执行的频率越低，这个元素就越可疑。

Typically, an SBFL approach calculates suspiciousness scores using a ranking metric [29], or risk evaluation formula [1], [30], based on four values collected from the executions of the tests, as shown in Table 1. For example, Ochiai [2] is an effective SBFL technique [30] using the formula:

$Ochiai(element)=\frac{}{}$

通常，SBFL方法使用排名度量[29]或风险评估公式[1]、[30]来计算可疑性分数，该公式基于从执行测试中收集的四个值，如表1所示。例如，Ochiai[2]是一种有效的SBFL技术[30]，其公式为:

DStar [31] is another effective technique [17], [32] using
the formula:
DStar (element) =
e
∗
f
ep + nf
DStar’s notation ‘*’ is a variable, which we set to 2 based on
the recommendation from Wong et al. [31].

### 2.2 Mutation-Based Fault Localization

Mutation-based fault localization uses information from mutation analysis [33], rather than from regular program execution, as inputs to its ranking metric or risk evaluation formula. While SBFL techniques consider whether a statement is executed or not, MBFL techniques consider whether the execution of a statement affects the result of a test [17]. If a program statement affects failed tests more frequently and affects passed tests more rarely, it is more suspicious.

基于突变的故障定位使用来自突变分析[33]的信息，而不是来自常规程序执行的信息，作为其排名指标或风险评估公式的输入。当SBFL技术考虑语句是否被执行时，MBFL技术考虑语句的执行是否会影响测试[17]的结果。如果一个程序语句更频繁地影响失败的测试，更少地影响通过的测试，那么它就更值得怀疑。

A mutant is said to be killed by a test case if the test case has different execution results on the mutant and the original program [34]. A test case that kills mutants may carry diagnostic information. MBFL injects mutants into the program under test. MUSE [5] and Metallaxis-FL [4] are two state-of-the-art MBFL techniques. For a statement s, a MBFL technique:

如果测试用例对突变体和原始程序[34]有不同的执行结果，则突变体被测试用例杀死。杀死突变体的测试用例可能携带诊断信息。MBFL将突变体注入被测程序。MUSE[5]和Metallaxis-FL[4]是两种最先进的MBFL技术。对于一个语句s, MBFL技术:

...

### 2.3 Program Slicing

A program slice is a subset of program elements that potentially affect the slicing criterion: a set of specific variables [35]. For example, a slicing criterion could be a pair hl, V i, where l is a program location and V is a set of variables. Program slicing determines the program elements that have a direct or indirect effect on the values of variables in V at the program location l. 
Program slicing was introduced as a debugging tool to reduce a program to a minimal form while still maintaining a given behavior [36]. Static slicing only uses the source code and accounts for all possible executions of the program. 
Dynamic slicing focuses on one execution for a specific input [37]. A dynamic slice contains all statements that may affect the values in the slicing criterion for a specific execution. The key difference between dynamic slicing and static slicing is that dynamic slicing only includes executed statements for specific input, but static slicing includes possiblyexecuted statements for all potential inputs. Since dynamic slices are significantly smaller, they are more suitable and effective for program debugging [38]. 
The following example shows the difference between static slicing and dynamic slicing.

程序切片是可能影响切片条件的程序元素的子集:一组特定的变量[35]。例如，一个切片标准可以是一对hl, V i，其中l是一个程序位置，V是一组变量。程序切片确定对程序位置l上的V中的变量值有直接或间接影响的程序元素。

程序切片是作为一种调试工具引入的，它可以在保持给定行为[36]的同时将程序缩减到最小的形式。静态切片仅使用源代码和帐户的所有可能执行的程序。

动态切片主要关注特定输入[37]的一次执行。动态切片包含所有可能影响特定执行的切片条件中的值的语句。动态切片和静态切片之间的关键区别在于，动态切片只包含针对特定输入的已执行语句，而静态切片包含针对所有潜在输入的可能已执行语句。由于动态片要小得多，所以它们更适合用于程序调试[38]。

下面的示例展示了静态切片和动态切片之间的区别。

### 2.4 Stack trace Analysis

A stack trace is the list of active stack frames during execution of a program. Each stack frame corresponds to a function call that has not yet returned. Stack traces are useful information sources for developers during debugging tasks. When the system crashes, the stack trace indicates the sequence of function calls and the point where the crash occurred.

堆栈跟踪是程序执行期间活动堆栈帧的列表。每个堆栈帧对应一个尚未返回的函数调用。堆栈跟踪是开发人员在调试任务期间的有用信息源。当系统崩溃时，堆栈跟踪指示函数调用的顺序和崩溃发生的位置。

### 2.5 Predicate Switching

Predicate switching [10] is a fault localization technique designed for the faults related to control flow. A predicate, or conditional expression, controls the execution of different branches. If a failed test case can be changed to a passed test case by modifying the evaluated result of a predicate, the predicate is called a critical predicate and may be the root cause of the fault. 

The technique first traces the execution of the failed test and identifies all instances of branch predicates. Then it repeatedly re-runs the test, forcibly switching the outcome of a different predicate each time. Once switching a predicate produces the correct output, it reports this predicate as a critical predicate. The critical predicate is potentially the cause of the fault.

Predicate switching seems to be similar to MBFL techniques, as they both apply mutations and examine the change of the execution results. However, we still treat predicate switching as a different family because predicate switching mutates program states rather than the program itself. For example, if a conditional expression has been evaluated multiple times during the program execution, predicate switching inverses one evaluation at a time instead of all evaluations. Furthermore, the existing works [17], [26] do not include predicate switching as an MBFL approach as far as we are aware.

谓词切换[10]是针对与控制流相关的故障而设计的故障定位技术。谓词或条件表达式控制不同分支的执行。如果通过修改谓词的计算结果可以将失败的测试用例更改为通过的测试用例，则该谓词称为临界谓词，它可能是故障的根本原因。

该技术首先跟踪失败测试的执行，并标识分支谓词的所有实例。然后它重复地重新运行测试，强制每次切换不同谓词的结果。一旦切换一个谓词产生正确的输出，它就将这个谓词报告为一个临界谓词。临界谓词可能是故障的原因。

谓词切换似乎类似于MBFL技术，因为它们都应用了突变并检查执行结果的变化。但是，我们仍然将谓词切换视为一个不同的家族，因为谓词切换使程序状态发生突变，而不是程序本身。例如，如果一个条件表达式在程序执行期间被求值多次，则谓词切换一次与一个求值相反，而不是与所有求值相反。此外，现有的工作[17]，[26]不包括谓词切换作为MBFL的方法，据我们所知。

### 2.6 Information Retrieval-Based Fault Localization

The early goals of Information retrieval (IR) area are indexing text and searching for useful documents in a collection [39]. Recently, some studies [11], [27], [40] have applied information retrieval techniques to fault localization. These approaches take as input a bug report, rather than a set of test cases, and generates as output a list of relevant source code files [41].

These approaches treat the bug reports as a query and then rank the source code files by their relevance to the query. Unlike aforementioned fault localization families, IRbased fault localization techniques do not require program execution information, such as passed and failed test cases. They locate relevant files based on the bug report [11].

信息检索(IR)领域的早期目标是索引文本和在集合[39]中搜索有用的文档。近年来，[11]、[27]、[40]等研究将信息检索技术应用于故障定位。这些方法将错误报告作为输入，而不是一组测试用例，并生成相关源代码文件[41]的列表作为输出。

这些方法将错误报告视为查询，然后根据其与查询的相关性对源代码文件进行排序。与前面提到的故障定位系列不同，基于IRbased的故障定位技术不需要程序执行信息，例如通过和失败的测试用例。他们根据错误报告[11]定位相关文件。

### 2.7 History-Based Fault Localization

As a general rule, program files that contained more bugs in the past are likely to have more bugs in the future [42]. Development history can be used for fault prediction, which ranks the elements in a program by their likelihood to be defective before any failure has been discovered [12]. Traditionally, fault prediction and fault localization are considered as different problems. However, since they both produce a list of suspicious elements, we also consider fault prediction techniques in this paper.

In particular, we consider a simple fault prediction technique introduced by Rahman et al. [13]. This technique simply ranks files by the number of fixing changes applied on them. This simple technique has the same utility for inspections as a more sophisticated fault prediction technique FixCache [12].

一般来说，过去包含更多bug的程序文件在未来[42]中可能会有更多bug。开发历史可用于故障预测，即在发现任何故障之前，根据程序中元素的缺陷可能性对其进行排序。传统上，故障预测和故障定位被认为是两个不同的问题。然而，由于它们都产生了一系列的可疑元素，我们在本文中也考虑了故障预测技术。

特别地，我们考虑了Rahman等人提出的一种简单的故障预测技术。这种技术只是根据应用于文件上的修复更改的数量对文件进行排序。这个简单的技术与更复杂的故障预测技术FixCache[12]具有相同的检查功能。

### 2.8 Learning to Rank

Learning to rank techniques train a machine learning model for a ranking task [43]. Learning to rank is widely used in Information Retrieval (IR) and Natural Language Processing (NLP) [44]. For example, in document retrieval, the task is to sort documents by the relevance to a query. One way to create the ranking model is with expert knowledge. By contrast, learning to rank techniques improve ranking performance and automatically create the ranking model, integrating many features (or signals).

学习排序技术训练了一个用于排序任务[43]的机器学习模型。学习排序在信息检索(IR)和自然语言处理(NLP)[44]中得到了广泛的应用。例如，在文档检索中，任务是根据与查询的相关性对文档进行排序。创建排名模型的一种方法是利用专家知识。相比之下，学习排名技术可以提高排名性能，并自动创建排名模型，集成了许多特性(或信号)。

Liu categorized learning to rank models into three groups [44]. Pointwise techniques transform the rank problem into a regression or ordinal classification problem for the ordinal score in the training data. Pairwise techniques approximate the problem by a classification problem: creating a classifier for classifying item pairs according to their ordinal position. The goal of pairwise techniques is to minimize ordinal inversions. Listwise techniques take ranking lists as input and evaluate the ranking lists directly by the loss functions.

刘将模型的学习分成三组[44]。点态化技术将秩问题转化为训练数据中序数分数的回归或序数分类问题。两两配对技术通过一个分类问题来近似这个问题:创建一个分类器，根据它们的序号位置对项目对进行分类。两两配对技术的目标是最小化序对反转。列表技术以排序列表为输入，利用损失函数直接对排序列表进行评估。

Recently, Xuan and Monperrus showed that learning to rank model can be used to combine different formulae in SBFL [23]. The basic idea is to treat the suspiciousness score produced by different formulae as features and use learning to rank to find a model that ranks the faulty element as high as possible. In this paper we apply learning to rank similarly to combine approaches from different families.

最近，Xuan和Monperrus证明了学习rank模型可以用于组合SBFL[23]中不同的公式。其基本思想是将不同公式所产生的可疑性分数作为特征，利用学习排序的方法，找到一个将错误元素尽可能高的排序模型。在这篇论文中，我们将学习应用于相似的排序，以结合来自不同家庭的方法。
