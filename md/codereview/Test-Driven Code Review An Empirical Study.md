# Test-Driven Code Review: An Empirical Study

## Abstract

Test-Driven Code Review (TDR) is a code review practice in which a reviewer inspects a patch by examining the changed test code before the changed production code. Although this practice has been mentioned positively by practitioners in informal literature and interviews, there is no systematic knowledge of its effects, prevalence, problems, and advantages.

测试驱动代码审查 (TDR) 是一种代码审查实践，其中审查者通过在更改生产代码之前检查更改的测试代码来检查补丁。 尽管从业者在非正式文献和访谈中积极提及这种做法，但对其影响、普遍性、问题和优势还没有系统的了解。

In this paper, we aim at empirically understanding whether this practice has an effect on code review effectiveness and how developers’ perceive TDR.We conduct (i) a controlled experiment with 93 developers that perform more than 150 reviews, and (ii) 9 semi-structured interviews and a survey with 103 respondents to gather information on how TDR is perceived. Key results from the experiment show that developers adopting TDR find the same proportion of defects in production code, but more in test code, at the expenses of fewer maintainability issues in production code. Furthermore, we found that most developers prefer to review production code as they deem it more critical and tests should follow from it. Moreover, general poor test code quality and no tool support hinder the adoption of TDR. Public preprint: [https: //doi.org/10.5281/zenodo.2551217], data and materials: [https:// doi.org/10.5281/zenodo.2553139].

在本文中，我们旨在从经验上了解这种做法是否对代码审查有效性以及开发人员如何看待 TDR 产生影响。 - 结构化访谈和对 103 名受访者的调查，以收集有关如何看待 TDR 的信息。 实验的主要结果表明，采用 TDR 的开发人员在生产代码中发现的缺陷比例相同，但在测试代码中发现的缺陷更多，代价是生产代码中的可维护性问题更少。 此外，我们发现大多数开发人员更喜欢审查生产代码，因为他们认为它更重要，测试应该紧随其后。 此外，普遍较差的测试代码质量和没有工具支持阻碍了 TDR 的采用。 公开预印本：[https://doi.org/10.5281/zenodo.2551217]，数据资料：[https://doi.org/10.5281/zenodo.2553139]。

## I. INTRODUCTION

Peer code review is a well-established and widely adopted practice aimed at maintaining and promoting software quality [4]. In a code review, developers other than the code change author manually inspect a code change to find as many issues as possible and provide feedbacks that need to be addressed before accepting the code in production [8].

同行代码审查是一种成熟且广泛采用的实践，旨在维护和提升软件质量 [4]。 在代码审查中，除代码更改作者之外的开发人员手动检查代码更改以找到尽可能多的问题，并在接受生产中的代码之前提供需要解决的反馈[8]。

The academic research community is conducting empirical studies to better understand the code review process [42], [41], [4], [27], [43], as well as to obtain empirical evidence on aspects and practices that are related to more efficient and effective reviews [49], [32].

学术研究界正在进行实证研究，以更好地理解代码审查过程 [42]、[41]、[4]、[27]、[43]，以及获得与代码审查相关的方面和实践的经验证据。 更高效和有效的评论 [49]、[32]。

A code review practice that has only been touched upon in academic literature [47], but has been described in gray literature almost ten years ago [56] is that of test-driven code review (TDR, henceforth). By following TDR, a reviewer inspects a patch by examining the changed test code before the changed production code.

仅在学术文献 [47] 中涉及但在大约十年前在灰色文献 [56] 中描述的代码审查实践是测试驱动代码审查（TDR，此后）。 通过遵循 TDR，审阅者通过在更改生产代码之前检查更改的测试代码来检查补丁。

To motivate TDR, P. Zembrod—Senior Software Engineer in Test at Google—explained in the Google Blog [56]: “When I look at new code or a code change, I ask: What is this about? What is it supposed to do? Questions that tests often have a good answer for. They expose interfaces and state use cases”. Among the comments, also S. Freeman—one of the ideators of Mocks [46] and TDD [11]—commented how he covered similar ground [22]. Recently, in a popular online forum for programmers, another article supported TDR (collecting more than 1,200 likes): “By looking at the requirements and checking them against the test cases, the developer can have a pretty good understanding of what the implementation should be like, what functionality it covers and if the developer omitted any use cases.” Interviewed developers reported preferring to review test code first to better understanding the code change before looking for defects in production [47].

为了激励 TDR，谷歌测试高级软件工程师 P. Zembrod 在谷歌博客 [56] 中解释说：“当我查看新代码或代码更改时，我会问：这是关于什么的？ 它应该做什么？ 测试的问题通常有很好的答案。 它们公开接口和状态用例”。 在评论中，还有 S. Freeman——Mocks [46] 和 TDD [11] 的构思者之一——评论了他如何涵盖类似的领域 [22]。 最近，在一个流行的程序员在线论坛上，另一篇支持TDR的文章（收集了1200多个赞）：“通过查看需求并对照测试用例进行检查，开发人员可以很好地了解实现应该是什么 例如，它涵盖了哪些功能以及开发人员是否省略了任何用例。” 接受采访的开发人员报告说，在寻找生产中的缺陷之前，他们更愿意先审查测试代码，以便更好地了解代码更改[47]。

Despite these compelling arguments in favor of TDR, we have no systematic knowledge on this practice: its effectiveness in finding defects during code review, its prominence in practice, and what are its potential problems/advantages. This knowledge can provide insights for both practitioners and researchers. Developers and project stakeholders can use empirical evidence about TDR effects, problems, and advantages to make informed decisions about when to adopt it. Researchers can focus their attention on the novel aspects of TDR and challenges reviewers face to inform future research.

尽管有这些支持 TDR 的令人信服的论据，但我们对这种做法没有系统的了解：它在代码审查期间发现缺陷的有效性，它在实践中的突出地位，以及它的潜在问题/优势是什么。 这些知识可以为从业者和研究人员提供见解。 开发人员和项目利益相关者可以使用有关 TDR 效果、问题和优势的经验证据，就何时采用它做出明智的决定。 研究人员可以将注意力集中在 TDR 的新方面以及审稿人面临的挑战上，以便为未来的研究提供信息。

In this paper, our goal is to obtain a deeper understanding of TDR. We do this by conducting an empirical study set up in two phases: An experiment, followed by an investigation of developers’ practices and perceptions.

在本文中，我们的目标是更深入地了解 TDR。 我们通过分两个阶段进行实证研究来做到这一点：实验，然后是对开发人员的实践和看法的调查。

In the first phase, we study the effects of TDR in terms of the proportion of defects and maintainability issues found in a review. To this aim, we devise and analyze the results of an online experiment in which 92 developers (77 with at least two years of professional development experience) complete 154 reviews, using TDR or two alternative strategies (i.e., production first or only production). Two external developers rated the quality of the review comments. In the second phase, we investigate problems, advantages, and frequency of adoption of TDR – valuable aspects that could not be studied in the experiment. To this aim, we conduct nine interviews with experiment participants and deploy an online survey with 103 respondents.

在第一阶段，我们研究了 TDR 对审查中发现的缺陷比例和可维护性问题的影响。 为此，我们设计并分析了一项在线实验的结果，其中 92 位开发人员（77 位具有至少两年的专业开发经验）使用 TDR 或两种替代策略（即先生产或仅生产）完成了 154 条评论。 两名外部开发人员对审查意见的质量进行了评级。 在第二阶段，我们调查问题、优势和采用 TDR 的频率——实验中无法研究的有价值的方面。 为此，我们对实验参与者进行了 9 次访谈，并对 103 名受访者进行了在线调查。

Key findings of our study include: With TDR, the proportion of functional defects (bugs henceforth) found in production code and maintainability issues (issues henceforth) found in test code does not change. However, TDR leads to the discovery of more bugs in test code, at the expenses of fewer issues found in production code. The external raters judged the quality of the review comments as comparable across all review strategies. Furthermore, most developers seem to be reluctant to devote much attention to tests, as they deem production code more important; moreover applying TDR is problematic, due to widespread poor test quality (reducing TDR’s applicability) and no tool support (not easing TDR).

我们研究的主要发现包括： 使用 TDR，在生产代码中发现的功能缺陷（此后的错误）和测试代码中发现的可维护性问题（此后的问题）的比例不会改变。 然而，TDR 会导致在测试代码中发现更多错误，代价是在生产代码中发现的问题更少。 外部评估者认为所有审查策略的审查意见质量具有可比性。 此外，大多数开发人员似乎不愿意把太多注意力放在测试上，因为他们认为生产代码更重要； 此外，由于普遍存在较差的测试质量（降低 TDR 的适用性）和没有工具支持（不缓解 TDR），因此应用 TDR 是有问题的。

## II. RELATED WORK

To some extent, TDR can be considered as an evolution of classical reading techniques [6], as it shares the general idea to guide code inspectors with software artifacts (i.e., test cases) and help them with the code review task.

在某种程度上，TDR 可以被视为经典阅读技术 [6] 的演变，因为它分享了指导代码检查员使用软件工件（即测试用例）并帮助他们完成代码审查任务的总体思路。

Scenario-based inspections. Among reading techniques, Porter & Votta [37] defined the scenario-based approach, based on scenarios that provide inspectors with more specific instructions than a typical checklist and focus on a wider variety of defects. They discovered that such technique is significantly more useful for requirements inspectors. Later on, Porter et al. [38], [36] and Miller et al. [33] replicated the original study confirming the results. Other studies by Fusaro et al. [23] and Sandahl et al. [44] reported contradictory results, however without providing explanations on the circumstances leading scenario-based code inspection to fail. A significant advance in this field was then provided by Basili et al. [7], who re-visited the original scenario-based as a technique that needs to be specialized for the specific issues to be analyzed. They also defined a new scenario based technique called perspective-based reading: The basic idea is that different aspects of the source code should be inspected by inspectors having different skills [7]. All in all, the papers mentioned above, provided evidence of the usefulness of reading techniques; their similarities with TDR, give an interesting rationale on why TDR could bring benefits.

基于场景的检查。在阅读技术中，Porter & Votta [37] 定义了基于场景的方法，基于场景为检查员提供比典型检查表更具体的指令，并专注于更广泛的缺陷。他们发现这种技术对于需求检查员来说更有用。后来，波特等人。 [38]、[36] 和米勒等人。 [33] 重复了原始研究，证实了结果。 Fusaro 等人的其他研究。 [23] 和 Sandahl 等人。 [44] 报告了相互矛盾的结果，但是没有解释导致基于场景的代码检查失败的情况。 Basili 等人随后在该领域取得了重大进展。 [7]，他重新访问了原始的基于场景的技术，将其作为一种需要专门用于要分析的特定问题的技术。他们还定义了一种新的基于场景的技术，称为基于透视的阅读：基本思想是源代码的不同方面应该由具有不同技能的检查员进行检查 [7]。总而言之，上述论文提供了阅读技巧有用的证据；它们与 TDR 的相似之处，为 TDR 带来好处的原因提供了一个有趣的理由。

- [37] A. A. Porter and L. G. Votta. An experiment to assess different defect detection methods for software requirements inspections. In Proceedings of the 16th international conference on Software engineering, pages 103–112. IEEE Computer Society Press, 1994.
- [38] A. A. Porter, L. G. Votta, and V. R. Basili. Comparing detection methods for software requirements inspections: A replicated experiment. IEEE Transactions on software Engineering, 21(6):563–575, 1995.
- [36] A. Porter and L. Votta. Comparing detection methods for software requirements inspections: A replication using professional subjects. Empirical software engineering, 3(4):355–379, 1998.
- [33] J. Miller, M. Wood, and M. Roper. Further experiences with scenarios and checklists. Empirical Software Engineering, 3(1):37–64, 1998.


Ordering of code changes. Research on the ordering of code changes is also related to TDR. In particular, Baum et al. argued that an optimal ordering of code changes would help reviewers by reducing the cognitive load and improving the alignment with their cognitive processes [10], even though they made no explicit reference to ordering tests. This may give theoretical value to the TDR practice. Code ordering and its relation to understanding, yet without explicit reference to tests or reviews, has also been the subject of studies [25], [12].

代码更改的顺序。 代码变更排序的研究也与TDR有关。 特别是，鲍姆等人。 认为代码更改的最佳排序将通过减少认知负荷和改善与其认知过程的一致性来帮助审查者 [10]，即使他们没有明确提及排序测试。 这可能会为 TDR 实践提供理论价值。 代码排序及其与理解的关系，但没有明确提及测试或审查，也是研究的主题 [25]、[12]。

Reviewing test code. Many articles on classical inspection (e.g., [29], [54]) underline the importance of reviewing tests; however, they do not leave any specific recommendation. The benefits of reviewing tests are also highlighted in two case studies [30], [35]. Already in Fagan’s seminal paper [17], the inspection of tests is discussed, in this case noting fewer benefits compared to the inspection of production code. Winkler et al. [55] experimented with writing tests during inspection and found neither large gains nor losses in efficiency and effectiveness. Elberzhager et al. [16], [15] proposed to use results from code reviews to focus testing efforts. To our knowledge, in academic literature TDR has been explicitly referred to only by Spadini et al. [47]. In a more general investigation on how test files are reviewed, the authors reported that some practitioners indeed prefer to review test code first as to get a better understanding of a code change before looking for defects in production code. Our work builds upon the research on reviewing test code, by investigating how reviewing test code can(not) be beneficial for the whole reviewing process.

审查测试代码。许多关于经典检验的文章（例如，[29]、[54]）强调了审查测试的重要性；但是，他们没有留下任何具体建议。两个案例研究 [30]、[35] 也强调了审查测试的好处。已经在 Fagan 的开创性论文 [17] 中讨论了测试检查，在这种情况下，与生产代码检查相比，它的好处更少。温克勒等人。 [55] 在检查过程中对编写测试进行了试验，发现在效率和有效性方面既没有大的收益也没有大的损失。 Elberzhager 等人。 [16]、[15] 建议使用代码审查的结果来集中测试工作。据我们所知，在学术文献中，只有 Spadini 等人明确提到了 TDR。 [47]。在关于如何审查测试文件的更一般的调查中，作者报告说，一些从业者确实更喜欢先审查测试代码，以便在查找生产代码中的缺陷之前更好地了解代码更改。我们的工作建立在审查测试代码的研究之上，通过调查审查测试代码如何（不）对整个审查过程有益。

## III. METHODOLOGY

## VI. THREATS TO VALIDITY

## VII. DISCUSSION AND IMPLICATIONS

## VIII. CONCLUSION

In this paper, we empirically investigated a code review practice mentioned among practitioners’ blogs and only touched upon in academia: Test-Driven Code Review. We performed a study set up in two phases: an experiment with 92 developers and two external raters, followed by a qualitative investigation with nine semi-structured interviews and a survey with 103 respondents. Among our results, we found that with TDR the quality of the review comments does not change and neither does the time spent and the proportion of found production bugs and test issues. TDR leads to the discovery of more bugs in test code, at the expenses of fewer maintainability issues found in production. We report that developers see the application of TDR as problematic because of the perceived low importance of reviewing tests, poor test quality, and no tool support. However, test first review is also deemed to offer a concise, high-level overview of a patch that is considered helpful for developers not familiar with the changed production code.

在本文中，我们实证研究了从业者博客中提到且仅在学术界触及的代码审查实践：测试驱动代码审查。我们进行了一项分两个阶段进行的研究：对 92 名开发人员和两名外部评估者进行的实验，然后是包含 9 次半结构化访谈的定性调查和对 103 名受访者的调查。在我们的结果中，我们发现使用 TDR，审查意见的质量没有改变，花费的时间和发现的生产错误和测试问题的比例也没有改变。 TDR 导致在测试代码中发现更多错误，代价是生产中发现的可维护性问题更少。我们报告说，开发人员认为 TDR 的应用存在问题，因为人们认为审查测试的重要性不高、测试质量差且没有工具支持。但是，测试优先审查也被视为提供了对补丁的简明、高级概述，这被认为对不熟悉更改的生产代码的开发人员有帮助。
