# Automatically Generating Fix Suggestions in Response to Static Code Analysis Warnings

## Abstract

Static code analysis tools such as FindBugs and SonarQube are widely used on open-source and industrial projects to detect a variety of issues that may negatively affect the quality of software. Despite these tools’ popularity and high level of automation, several empirical studies report that developers normally fix only a small fraction (typically, less than 10% [1]) of the reported issues—so-called “warnings”. If these analysis tools could also automatically provide suggestions on how to fix the issues that trigger some of the warnings, their feedback would become more actionable and more directly useful to developers.

- [1] D. Marcilio, R. Bonifácio, E. Monteiro, E. Canedo, W. Luz, and G. Pinto, “Are static analysis violations really fixed?: A closer look at realistic usage of SonarQube,” in Proceedings of the 27th International Conference on Program Comprehension, ser. ICPC ’19. Piscataway, NJ, USA: IEEE Press, 2019, pp. 209–219. [Online]. Available: https://doi.org/10.1109/ICPC.2019.00040

FindBugs和SonarQube等静态代码分析工具在开源和工业项目中广泛使用，用于检测各种可能对软件质量产生负面影响的问题。尽管这些工具很受欢迎，自动化程度也很高，但一些实证研究报告称，开发人员通常只修复报告问题中的一小部分(通常少于10%的[1])，即所谓的“警告”。如果这些分析工具还能够自动提供关于如何修复触发某些警告的问题的建议，那么它们的反馈将变得更有可操作性，并且对开发人员更直接有用。

In this work, we investigate whether it is feasible to automatically generate fix suggestions for common warnings issued by static code analysis tools, and to what extent developers are willing to accept such suggestions into the codebases they’re maintaining. To this end, we implemented a Java program transformation technique that fixes 11 distinct rules checked by two well-known static code analysis tools (SonarQube and SpotBugs). Fix suggestions are generated automatically based on templates, which are instantiated in a way that removes the source of the warnings; templates for some rules are even capable of producing multi-line patches. We submitted 38 pull requests, including 920 fixes generated automatically by our technique for various open-source Java projects, including the Eclipse IDE and both SonarQube and SpotBugs tools. At the time of writing, project maintainers accepted 84% of our fix suggestions (95% of them without any modifications). These results indicate that our approach to generating fix suggestions is feasible, and can help increase the applicability of static code analysis tools.

在这项工作中，我们研究了为静态代码分析工具发出的常见警告自动生成修复建议是否可行，以及开发人员愿意在多大程度上将这些建议加入到他们维护的代码库中。为此，我们实现了一个Java程序转换技术，该技术修复了由两个著名的静态代码分析工具(SonarQube和SpotBugs)检查的11个不同规则。修复建议根据模板自动生成，模板的实例化方式消除了警告的来源;一些规则的模板甚至能够生成多行补丁。我们提交了38个pull请求，包括920个由我们的技术为各种开源Java项目自动生成的修复，包括Eclipse IDE和SonarQube和SpotBugs工具。在撰写本文时，项目维护者接受了84%的修复建议(其中95%没有进行任何修改)。这些结果表明我们生成修复建议的方法是可行的，并且可以帮助增加静态代码分析工具的适用性。

## I. INTRODUCTION

Static code analysis tools (SATs) are becoming increasingly popular as a way of detecting possible sources of defects earlier in the development process [2]. By working statically on the source or byte code of a project, these tools are applicable to large code bases [3], [4], where they quickly search for patterns that may indicate problems—bugs, questionable design choices, or failures to follow stylistic conventions [5], [6]—and report them to users. There is evidence [7] that using these tools can help developers monitor and improve software code quality; indeed, static code analysis tools are widely used for both commercial and open-source software development [1], [2], [4]. Some projects’ development rules even require that code has to clear the checks of a certain SAT before it can be released [1], [7], [8].

- [2] A. Habib and M. Pradel, “How many of all bugs do we find? a study of static bug detectors,” in Proceedings of the 33rd ACM/IEEE International Conference on Automated Software Engineering, ser. ASE 2018. New York, NY, USA: ACM, 2018, pp. 317–328. [Online]. Available: http://doi.acm.org/10.1145/3238147.3238213
- [3] B. Johnson, Y. Song, E. Murphy-Hill, and R. Bowdidge, “Why don’t software developers use static analysis tools to find bugs?” in Proceedings of the 2013 International Conference on Software Engineering, ser. ICSE ’13. Piscataway, NJ, USA: IEEE Press, 2013, pp. 672–681. [Online]. Available: http://dl.acm.org/citation.cfm?id=2486788.2486877
- [4] K. Liu, A. Koyuncu, D. Kim, and T. F. Bissyandè, “AVATAR: Fixing semantic bugs with fix patterns of static analysis violations,” in 2019 IEEE 26th International Conference on Software Analysis, Evolution and Reengineering (SANER), Feb 2019, pp. 1–12.
- [5] T. Barik, Y. Song, B. Johnson, and E. Murphy-Hill, “From quick fixes to slow fixes: Reimagining static analysis resolutions to enable design space exploration,” in 2016 IEEE International Conference on Software Maintenance and Evolution (ICSME), Oct 2016, pp. 211–221.
- [6] K. F. Tómasdóttir, M. Aniche, and A. van Deursen, “Why and how JavaScript developers use linters,” in 2017 32nd IEEE/ACM International Conference on Automated Software Engineering (ASE), Oct 2017, pp. 578–589.
- [7] M. Beller, R. Bholanath, S. McIntosh, and A. Zaidman, “Analyzing the state of static analysis: A large-scale evaluation in open source software,” in 2016 IEEE 23rd International Conference on Software Analysis, Evolution, and Reengineering (SANER), vol. 1, March 2016, pp. 470–481.
- [8] N. Ayewah, W. Pugh, D. Hovemeyer, J. D. Morgenthaler, and J. Penix, “Using static analysis to find bugs,” IEEE Software, vol. 25, no. 5, pp. 22–29, Sep. 2008.

静态代码分析工具(SATs)作为一种在开发过程[2]的早期检测缺陷的可能来源的方法，正变得越来越流行。通过静态地处理项目的源代码或字节代码，这些工具适用于大型代码库[3]和[4]，在这些代码库中，它们可以快速搜索可能指出问题(bug、有问题的设计选择或未能遵循[5]和[6]的风格约定)的模式，并向用户报告这些问题。有证据表明，使用这些工具可以帮助开发人员监视和改进软件代码质量;事实上，静态代码分析工具被广泛用于商业和开源软件开发的[1]、[2]、[4]。一些项目的开发规则甚至要求代码在发布[1]、[7]、[8]之前必须清除特定SAT的检查。

At the same time, some features of SATs limit their wider applicability in practice. One key problem is that SATs are necessarily imprecise in checking for rule violations; in other words, they report warnings that may or may not correspond to an actual mistake. As a result, the first time a static analysis tool is run on a project, it is likely to report thousands of warnings [2], [3], which saturates the developers’ capability of sifting through them to select those that are more relevant and should be fixed [1]. Another related issue with using SATs in practice is that understanding the problem highlighted by a warning and coming up with a suitable fix is often nontrivial [1], [3].

与此同时，sat考试的一些特点限制了其在实践中的广泛适用性。一个关键问题是，sat在检查违反规则的情况时必然不够精确;换句话说，他们报告的警告可能与实际的错误相符，也可能不相符。因此，第一次在项目上运行静态分析工具时，它可能会报告成千上万个警告[2]、[3]，这将使开发人员的筛选能力达到饱和，以选择那些更相关且应该修复的[1]。在实践中使用SATs的另一个相关问题是，理解由警告突出显示的问题并提出合适的修复方案通常是非常重要的[1]和[3]。

Our research aims at improving the practical usability of SATs by automatically providing fix suggestions: modifications to the source code that make it compliant with the rules checked by the analysis tools. We developed an approach, called SpongeBugs and described in Section III, whose current implementation works on Java code. SpongeBugs detects violations of 11 different rules checked by SonarQube and SpotBugs (successor to FindBugs [2])—two well-known static code analysis tools, routinely used by very many software companies and consortia, including large ones such as the Apache Software Foundation and the Eclipse Foundation. The rules checked by SpongeBugs are among the most widely used in these two tools, and cover different kinds of code issues (ranging from performance, to correct behavior, style, and other aspects). For each violation it detects, SpongeBugs automatically generates a fix suggestion and presents it to the user.

我们的研究旨在通过自动提供修复建议来提高SATs的实际可用性:对源代码进行修改，使其符合分析工具检查的规则。我们开发了一种称为SpongeBugs的方法，并在第三节中进行了描述，该方法的当前实现工作在Java代码上。SpongeBugs检测违反SonarQube和SpotBugs (FindBugs[2]的继承者)检查的11种不同规则的行为，这是两个著名的静态代码分析工具，经常被很多软件公司和协会使用，包括大型的Apache软件基金会和Eclipse基金会。海绵bug检查的规则是这两个工具中使用最广泛的规则之一，涵盖了不同类型的代码问题(从性能到正确的行为、样式和其他方面)。对于它检测到的每一个违规，海绵漏洞会自动生成一个修复建议并呈现给用户。

By construction, SpongeBugs’s suggestions remove the origin of a rule’s violation, but the maintainer still has to decide—based on their overall knowledge of the project— whether to accept and merge each suggestion. To assess whether developers are indeed willing to accept SpongeBugs’s suggestions, Section V describes the results of an empirical evaluation where we applied SpongeBugs to 12 Java projects, and submitted 920 fix suggestions as pull requests to the projects. At the time of writing, project maintainers accepted 775 (84%) fix suggestions—95% of them without any modifications. This high acceptance rate suggests that SpongeBugs often generates patches of high quality, which developers find adequate and useful. The empirical evaluation also indicates that SpongeBugs is applicable with good performance to large code bases; and reports (in Section V-D) several qualitative findings that can inform further progress in this line of work.

通过构造，SpongeBugs的建议消除了规则违反的来源，但是维护人员仍然需要根据他们对项目的总体了解来决定——是否接受并合并每一个建议。为了评估开发人员是否真的愿意接受SpongeBugs的建议，第五部分描述了一个经验评估的结果，我们将SpongeBugs应用到12个Java项目中，并将920个修复建议作为拉请求提交给项目。在撰写本文时，项目维护者接受了775(84%)个修复建议——其中95%没有进行任何修改。如此高的通过率表明，海绵细菌通常会生成高质量的补丁，开发人员认为这些补丁足够且有用。实证评价还表明，海绵漏洞在大型代码库中具有良好的应用性能;并报告(V-D节)几项质量发现，这些发现可为这一行工作的进一步进展提供资料。

The work reported in this paper is part of a large body of research (see Section II) that deals with helping developers detecting and fixing bugs and code smells. SpongeBugs’ approach is characterized by the following features: i) it targets static rules that correspond to frequent mistakes that are often fixable syntactically; ii) it builds fix suggestions that remove the source of warning by construction; iii) it scales to large code bases because it is based on lightweight program transformation techniques. Despite the focus on conceptually simple rule violations, SpongeBugs can generate nontrivial patches, including some that modify multiple hunks of code at once. In summary, SpongeBugs’s focus privileges generating a large number of practically useful fixes over being as broadly applicable as possible. Based on our empirical evaluation, Section VI discusses the main limitations of SpongeBugs’s approach, and Section VII outlines directions for further progress in this line of work.

本文中报告的工作是大量研究的一部分(参见第二部分)，这些研究旨在帮助开发人员检测和修复bug和代码气味。海绵宝宝的方法有以下特点:1)它针对的是静态规则，这些规则对应的是经常出现的错误，这些错误在语法上通常是可以修复的;ii)通过构建修正建议，消除构建的警告源;它可以扩展到大型的代码库，因为它基于轻量级的程序转换技术。尽管海绵漏洞关注的是概念上的简单规则违规，但它可以生成重要的补丁，包括一些一次性修改多块代码的补丁。总而言之，海绵宝宝的焦点特权产生了大量实际有用的修复比尽可能广泛地应用。基于我们的经验评估，第六部分讨论了海绵宝宝的方法的主要局限性，第七部分概述了在这方面工作的进一步发展方向。

## II. BACKGROUND AND RELATED WORK

Static analysis techniques reason about program behavior statically, that is without running the program [9]. This is in contrast to dynamic analysis techniques, which are instead driven by specific program inputs (provided, for example, by unit tests). Thus, static analysis techniques are often more scalable (because they do not require complete executions) but also less precise (because they over-approximate program behavior to encompass all possible inputs) than dynamic analysis techniques. In practice, there is a broad range of static analysis techniques from purely syntactic ones—based on code patterns—to complex semantic ones—which infer behavioral properties that can be used to perform program optimizations [10], [11] as well as performance problems and other kinds of vulnerabilities [12], [13].

- [9] F. Nielson, H. R. Nielson, and C. Hankin, Principles of Program Analysis. Berlin, Heidelberg: Springer-Verlag, 1999.
- [10] A. V. Aho, M. S. Lam, R. Sethi, and J. D. Ullman, Compilers: Principles, Techniques, and Tools (2nd Edition). Boston, MA, USA: Addison- Wesley Longman Publishing Co., Inc., 2006.
- [11] U. Khedker, A. Sanyal, and B. Karkare, Data Flow Analysis: Theory and Practice, 1st ed. Boca Raton, FL, USA: CRC Press, Inc., 2009.
- [12] S. Cherem, L. Princehouse, and R. Rugina, “Practical memory leak detection using guarded value-flow analysis,” in Proceedings of the 28th ACM SIGPLAN Conference on Programming Language Design and Implementation, ser. PLDI ’07. New York, NY, USA: ACM, 2007, pp. 480–491. [Online]. Available: http://doi.acm.org/10.1145/1250734.1250789
- [13] Y. Sui and J. Xue, “SVF: Interprocedural static value-flow analysis in LLVM,” in Proceedings of the 25th International Conference on Compiler Construction, ser. CC 2016. New York, NY, USA: ACM, 2016, pp. 265–266. [Online]. Available: http://doi.acm.org/10.1145/2892208.2892235

静态分析技术静态地分析程序行为，即不运行程序[9]。这与动态分析技术相反，动态分析技术是由特定的程序输入驱动的(例如，由单元测试提供)。因此，静态分析技术通常比动态分析技术具有更大的可伸缩性(因为它们不需要完全执行)，但也不像动态分析技术那样精确(因为它们过于接近程序行为以包含所有可能的输入)。在实践中，有很广泛的静态分析技术，从基于代码模式的纯语法技术到复杂的语义技术，这些技术可以推断出行为属性，可以用来执行程序优化[10]、[11]以及性能问题和其他类型的漏洞[12]、[13]。

- Static Code Analysis Tools. 

Static code analysis tools (SATs) typically combine different kinds of analyses. This paper focuses on the output of tools such as SonarQube, FindBugs, and SpotBugs, because they are widely available and practically used [2]. The analysis performed by a SAT consists of several independent rules, which users can select in every run of the tool. Each rule describes a requirement that correct, high-quality code should meet. For example, a rule may require that Constructors should not be used to instantiate String—one should write String s = "SpongeBugs" instead of String s = new String("SpongeBugs").

静态代码分析工具。
静态代码分析工具(SATs)通常结合不同类型的分析。本文主要关注SonarQube、FindBugs和SpotBugs等工具的输出，因为它们广泛可用，并且在实际中使用了[2]。SAT执行的分析由几个独立的规则组成，用户可以在每次运行该工具时选择这些规则。每个规则都描述了正确的、高质量的代码应该满足的需求。例如，规则可能要求构造函数不能用来实例化字符串——应该写String s = "SpongeBugs"而不是String s = new String("SpongeBugs")。

Whenever a SAT finds a piece of code that violates one of the rules, it outputs a warning, typically reporting the incriminated piece of code and a reference to the rule that it violates. It is then up to the developer to inspect the warning to decide whether the violation is real and significant enough to be addressed; if it is, the developer also has to come up with a fix that modifies the source code and removes the rule violation without otherwise affecting program behavior.

每当SAT发现一段代码违反了其中一条规则，它就会输出一个警告，通常会报告这段代码并引用它违反的规则。然后由开发人员检查警告，以确定违规是否真实且严重到足以处理的程度;如果是，开发人员还必须提出修改源代码并删除违反规则的修正，而不影响程序行为。

- Empirical Studies on Static Code Analysis Tools. 

Software engineering researchers have become increasingly interested [2] in studying how SATs are used in practice by developers, and how to increase their level of automation.

软件工程研究人员对研究SATs如何在开发人员的实践中使用，以及如何提高他们的自动化水平越来越感兴趣。

Rausch et al. [14] performed an extensive study on the build failures of 14 projects, finding a frequent association between build failures and issues reported by SATs (when the latter are used as a component of continuous integration). Zampetti et al. [15] found that lack of a consistent coding style across a project is a frequent source of build failures.

Rausch等人对14个项目的构建失败进行了广泛的研究，发现构建失败与SATs报告的问题之间存在频繁的关联(当SATs被用作持续集成的组件时)。Zampetti等人发现，在整个项目中缺乏一致的编码风格是构建失败的一个常见原因。

Recent studies focused on the kinds of rule violations developers are more likely to fix. Liu et al. [16] compared a large number of fixed and not fixed FindBugs rule violations across revisions of 730 Java projects. They characterized the categories of violations that are often fixed, and reported several situations in which the violations are systematically ignored. They also concluded that developers disregard most of FindBugs violations as not being severe enough to be fixed during development process.

最近的研究集中在开发人员更可能修复的规则违规类型上。Liu等人在730个Java项目的修订版本中比较了大量的固定和非固定FindBugs违规。他们描述了经常固定的侵犯行为类别，并报告了有几次侵犯行为被有系统地忽略的情况。他们还得出结论，开发人员忽略了大多数FindBugs违规，因为在开发过程中没有严重到需要修复的程度。

Digkas et al. [17] performed a similar analysis for SonarQube rules, revealing that a small number of all rules accounts for the majority of programmer-written fixes. Marcilio et al. [1] characterized the use of SonarQube in 246 Java projects, reporting a low resolution rate of issues (around 9% of the project’s issues have been fixed), and also finding that a small subset of the rules reveal real design and coding flaws that developers consider serious. These findings suggest that a fix generation tool that focuses on a selection of rules is likely to be highly effective and relevant in practice to real project developers.

Digkas等人对SonarQube规则进行了类似的分析，发现所有规则中的一小部分占了程序员编写的修复程序的大部分。Marcilio等人在[1]中描述了在246个Java项目中使用SonarQube的特点，报告了问题的低分辨率(项目问题的大约9%已经得到修复)，并且还发现了一小部分规则揭示了开发人员认为严重的实际设计和编码缺陷。这些发现表明，关注于规则选择的修复生成工具很可能是非常有效的，并且在实践中与真正的项目开发人员相关。

- Automatic Fix Suggestions and Program Repair. 

Several researchers have developed techniques that propose fix suggestions for rules of the popular FindBugs in different ways: interactively, with the user exploring different alternative fixes for the same warning [5]; and automatically, by systematically generating fixes by mining patterns of programmer-written fixes [4], [16]. These studies focus on behavioral bugs such as those in Defects4J [18]—a curated collection of 395 real bugs of open-source Java projects. In contrast, SpongeBugs mainly targets rules that characterize so-called code smells— patterns that may be indicative of poor programming practices, and mainly encompass design and stylistic aspects. We focus on these because they are simpler to characterize and fix “syntactically” but are also the categories of warnings that developers using SATs are more likely to address and fix [1], [16], [17]. This focus helps SpongeBugs achieve high precision and scalability, as well as be practically useful.

- 自动修复建议和程序修复。

一些研究人员开发了一些技术，这些技术以不同的方式为流行的FindBugs规则提出修复建议:交互地，用户为相同的警告[5]探索不同的替代修复;通过挖掘程序员编写的修复程序的模式来自动地、系统地生成修复程序[4]、[16]。这些研究关注的是行为bug，比如Defects4J[18]中的bug——一个由395个开源Java项目的真实bug组成的集合。相比之下，SpongeBugs主要针对那些描述所谓代码味道的规则——这些模式可能暗示了糟糕的编程实践，主要包括设计和风格方面。我们之所以关注这些警告，是因为它们更易于“语法地”描述和修复，而且也是使用SATs的开发人员更有可能处理和修复[1]、[16]、[17]的警告类别。这种关注有助于海绵宝宝达到高精确度和可扩展性，同时也非常实用。

The work closest to ours is probably Liu et al.’s study [4], which presents the AVATAR automatic program repair system. AVATAR recommends code changes based on the output of SAT tools. In its experimental evaluation, AVATAR generated correct fixes for 34 bugs among those of the Defects4J benchmark. This suggests that responding to warnings of SATs can be an effective way to fix some behavioral “semantic” bugs. While we may run SpongeBugs on Defects4J bugs as well, AVATAR’s and SpongeBugs’ scopes are mostly complementary in the kind of design trade-offs they target. Our approach focuses on “syntactic” design flaws that often admit simple yet effective fixes, with the main goal of being immediately and generally useful for the kinds of static analysis flaws that developers fix more often.

最接近我们的工作可能是Liu等人的研究[4]，它提出了化身自动程序修复系统。AVATAR建议根据SAT工具的输出改变代码。在它的实验性评估中，AVATAR为34个缺陷生成了正确的修复。这表明，对sat的警告做出反应是修复一些行为“语义”bug的有效方法。虽然我们可能也会在缺陷4j bug上运行SpongeBugs，但AVATAR和SpongeBugs的作用域在它们所针对的设计权衡方面大多是互补的。我们的方法侧重于“语法”设计缺陷，这些缺陷通常允许进行简单但有效的修复，其主要目标是对开发人员更经常修复的静态分析缺陷进行立即且通常有用的修复。

Aftandilian et al. [19] presented an extension of the OpenJDK compiler that detects potential bugs and recommends fixes at compile time, based on patterns that are similar to those used by SATs like FindBugs. Other approaches learn transformations from examples, using sets of bug fixes [20], bug reports [21], or source-code editing patterns [22]. We directly implemented SpongeBugs’ transformations based on our expertise and the standard recommendations for fixing warnings from static analysis tools. Even though SpongeBugs cannot learn new fixing rules at the moment, this remains an interesting direction for further improving its capabilities.

Aftandilian等人提出了OpenJDK编译器的一个扩展[19]，它可以根据与FindBugs等SATs使用的模式类似的模式，检测潜在的bug并在编译时提出修复建议。其他方法从示例中学习转换，使用错误修复[20]、错误报告[21]或源代码编辑模式[22]的集合。我们直接实现了海绵错误的转换，基于我们的专业知识和修复来自静态分析工具的警告的标准建议。尽管海绵宝宝目前还不能学习新的修复规则，但这仍然是进一步提高其功能的一个有趣的方向。

- [20] T. T. Nguyen, H. A. Nguyen, N. H. Pham, J. Al-Kofahi, and T. N. Nguyen, “Recurring bug fixes in object-oriented programs,” in Proceedings of the 32nd ACM/IEEE International Conference on Software Engineering - Volume 1, ser. ICSE ’10. New York, NY, USA: ACM, 2010, pp. 315–324. [Online]. Available: http://doi.acm.org/10.1145/1806799.1806847
- [21] C. Liu, J. Yang, L. Tan, and M. Hafiz, “R2fix: Automatically generating bug fixes from bug reports,” in 2013 IEEE Sixth International Conference on Software Testing, Verification and Validation, March 2013, pp. 282–291.
- [22] R. Rolim, G. Soares, R. Gheyi, and L. D’Antoni, “Learning quick fixes from code repositories,” CoRR, vol. abs/1803.03806, 2018. [Online]. Available: http://arxiv.org/abs/1803.03806

Behavioral bugs are also the primary focus of techniques for “automated program repair”, a research area that has grown considerably in the last decade. The most popular approaches to automated program repair are mainly driven by dynamic analysis (i.e., tests) [23], [24] and targets generic bugs. In contrast, SpongeBugs’ approach is based on static codetransformation techniques, which makes it of more limited scope but also more easily and widely applicable.

- [23] L. Gazzola, D. Micucci, and L. Mariani, “Automatic software repair: A survey,” IEEE Trans. Software Eng., vol. 45, no. 1, pp. 34–67, 2019. [Online]. Available: https://doi.org/10.1109/TSE.2017.2755013
- [24] M. Monperrus, “Automatic software repair: A bibliography,” ACM Comput. Surv., vol. 51, no. 1, pp. 17:1–17:24, Jan. 2018. [Online]. Available: http://doi.acm.org/10.1145/3105906

行为缺陷也是“自动程序修复”技术的主要关注点，这是一个在过去十年中发展迅速的研究领域。最流行的自动程序修复方法主要是由动态分析驱动的。测试)[23]、[24]和针对一般的错误。相比之下，SpongeBugs的方法是基于静态代码转换技术，这使得它的应用范围更有限，但也更容易更广泛地应用。

