# Modern Code Review: A Case Study at Google

## ABSTRACT

Employing lightweight, tool-based code review of code changes (aka modern code review) has become the norm for a wide variety of open-source and industrial systems. In this paper, we make an exploratory investigation of modern code review at Google. Google introduced code review early on and evolved it over the years; our study sheds light on why Google introduced this practice and analyzes its current status, after the process has been refined through decades of code changes and millions of code reviews. By means of 12 interviews, a survey with 44 respondents, and the analysis of review logs for 9 million reviewed changes, we investigate motivations behind code review at Google, current practices, and developers’ satisfaction and challenges.

采用轻量级、基于工具的代码更改代码审查（又名现代代码审查）已成为各种开源和工业系统的规范。 在本文中，我们对 Google 的现代代码审查进行了探索性调查。 谷歌很早就引入了代码审查并经过多年的发展； 我们的研究阐明了 Google 引入这种做法的原因，并在该流程经过数十年的代码更改和数百万次代码审查完善后分析了其现状。 通过 12 次访谈、44 名受访者的调查以及对 900 万次审查变更的审查日志分析，我们调查了 Google 代码审查背后的动机、当前的做法以及开发人员的满意度和挑战。

## 1 INTRODUCTION

Peer code review, a manual inspection of source code by developers other than the author, is recognized as a valuable tool for improving the quality of software projects [2, 3]. In 1976, Fagan formalized a highly structured process for code reviewing—code inspections [16]. Over the years, researchers provided evidence on the benefits of code inspection, especially for defect finding, but the cumbersome, time-consuming, and synchronous nature of this approach hindered its universal adoption in practice [37]. Nowadays, most organizations adopt more lightweight code review practices to limit the inefficiencies of inspections [33]. Modern code review is (1) informal (in contrast to Fagan-style), (2) tool-based [32], (3) asynchronous, and (4) focused on reviewing code changes.

- [16] M.E. Fagan. 1976. Design and code inspections to reduce errors in program development. IBM Systems Journal 15, 3 (1976), 182–211.
- [37] F. Shull and C. Seaman. 2008. Inspecting the History of Inspections: An Example of Evidence-Based Technology Diffusion. IEEE Software 25, 1 (2008), 88–90.
- [33] Peter C Rigby and Christian Bird. 2013. Convergent software peer review practices. In FSE.

同行代码审查是由作者以外的开发人员手动检查源代码，被认为是提高软件项目质量的宝贵工具 [2, 3]。 1976 年，Fagan 正式制定了一个高度结构化的代码审查流程——代码检查 [16]。 多年来，研究人员提供了关于代码检查的好处的证据，尤其是在缺陷发现方面，但这种方法繁琐、耗时和同步的性质阻碍了其在实践中的普遍采用 [37]。 如今，大多数组织采用更轻量级的代码审查实践来限制检查的低效率 [33]。 现代代码审查是 (1) 非正式的（与 Fagan 风格相反），(2) 基于工具的 [32]，(3) 异步，以及 (4) 专注于审查代码更改。

An open research challenge is understanding which practices represent valuable and effective methods of review in this novel context. Rigby and Bird quantitatively analyzed code review data from software projects spanning varying domains as well as organizations and found five strongly convergent aspects [33], which they conjectured can be prescriptive to other projects. The analysis of Rigby and Bird is based on the value of a broad perspective (that analyzes multiple projects from different contexts). For the development of an empirical body of knowledge, championed by Basili [7], it is essential to also consider a focused and longitudinal perspective that analyzes a single case. This paper expands on work by Rigby and Bird to focus on the review practices and characteristics established at Google, i.e., a company with a multi-decade history of code review and a high-volume of daily reviews to learn from. This paper can be (1) prescriptive to practitioners performing code review and (2) compelling for researchers who want to understand and support this novel process.

一个开放的研究挑战是了解在这种新颖的背景下哪些实践代表了有价值和有效的审查方法。 Rigby 和 Bird 对来自不同领域和组织的软件项目的代码审查数据进行了定量分析，并发现了五个高度收敛的方面 [33]，他们推测这些方面可能对其他项目具有指导意义。 Rigby 和 Bird 的分析基于广泛视角的价值（分析来自不同背景的多个项目）。对于 Basili [7] 所倡导的经验性知识体系的发展，还必须考虑分析单个案例的重点和纵向视角。本文扩展了 Rigby 和 Bird 的工作，重点关注 Google 建立的审查实践和特征，即一家拥有数十年代码审查历史和大量日常审查可供学习的公司。这篇论文可以 (1) 对执行代码审查的从业者进行说明，以及 (2) 对想要理解和支持这一新颖过程的研究人员具有吸引力。 

Code review has been a required part of software development at Google since very early on in the company’s history; because it was introduced so early on, it has become a core part of Google culture. The process and tooling for code review at Google have been iteratively refined for more than a decade and is applied by more than 25,000 developers making more than 20,000 source code changes each workday, in dozens of offices around the world [30].

从公司历史的早期开始，代码审查就一直是谷歌软件开发的必要组成部分。 因为它很早就被引入，它已经成为谷歌文化的核心部分。 谷歌代码审查的流程和工具经过十多年的迭代改进，被超过 25,000 名开发人员应用，每个工作日进行 20,000 多次源代码更改，分布在世界各地的数十个办公室 [30]。

We conduct our analysis in the form of an exploratory investigation focusing on three aspects of code review, in line with and expanding on the work by Rigby and Bird [33]: (1) The motivations driving code review, (2) the current practices, and (3) the perception of developers on code review, focusing on challenges encountered with a specific review (breakdowns in the review process) and satisfaction. Our research method combines input from multiple data sources: 12 semi-structured interviews with Google developers, an internal survey sent to engineers who recently sent changes to review with 44 responses, and log data from Google’s code review tool pertaining to 9 million reviews over two years.

我们以探索性调查的形式进行分析，重点关注代码审查的三个方面，符合并扩展了 Rigby 和 Bird [33] 的工作：（1）报告代码审查的动机，（2）当前的实践，以及（ 3) 开发人员对代码审查的看法，重点关注特定审查遇到的挑战（审查过程中的问题）和来自我们的研究方法结合了一些数据源的输入：对谷歌开发人员的 12 次 半结构化调查，发送给最近发送更改以进行审查的工程师的内部，其中有 44 条回复，以及来自谷歌审查工具的日志数据，这些数据涉及到 900 万次审查年。

We find that the process at Google is markedly lighter weight than in other contexts, based on a single reviewer, quick iterations, small changes, and a tight integration with the code review tool. Breakdowns still exist, however, due to the complexity of the interactions that occur around code review. Nevertheless, developers consider this process valuable, confirm that it works well at scale, and conduct it for several reasons that also depend on the relationship between author and reviewers. Finally, we find evidence on the use of the code review tool beyond collaborative review and corroboration for the importance of code review as an educational tool.

我们发现 Google 的流程比其他环境中的流程要轻得多，它基于单个审阅者、快速迭代、小的更改以及与代码审阅工具的紧密集成。 然而，由于围绕代码审查发生的交互的复杂性，故障仍然存在。 尽管如此，开发人员认为这个过程很有价值，确认它在规模上运行良好，并出于多种原因进行，也取决于作者和审阅者之间的关系。 最后，我们找到了使用代码审查工具超越协作审查和证实代码审查作为教育工具的重要性的证据。

## 8 CONCLUSION

Our study found code review is an important aspect of the development workflow at Google. Developers in all roles see it as providing multiple benefits and a context where developers can teach each other about the codebase, maintain the integrity of their teams’ codebases, and build, establish, and evolve norms that ensure readability and consistency of the codebase. Developers reported they were happy with the requirement to review code. The majority of changes are small, have one reviewer and no comments other than the authorization to commit. During the week, 70% of changes are committed less than 24 hours after they are mailed out for an initial review. These characteristics make code review at Google lighter weight than the other projects adopting a similar process. Moreover, we found that Google includes several research ideas in its practice, making the practical implications of current research trends visible.

我们的研究发现代码审查是 Google 开发工作流程的一个重要方面。 所有角色的开发人员都认为它提供了多种好处和上下文，开发人员可以在其中相互教授代码库，维护团队代码库的完整性，并构建、建立和发展确保代码库可读性和一致性的规范。 开发人员报告说他们对审查代码的要求感到满意。 大多数更改都很小，只有一个审阅者，除了提交授权之外没有其他评论。 在一周内，70% 的更改在寄出进行初步审查后不到 24 小时内提交。 这些特性使得 Google 的代码审查比采用类似过程的其他项目更轻。 此外，我们发现谷歌在其实践中包含了一些研究思路，使得当前研究趋势的实际影响可见。

