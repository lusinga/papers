# The Impact of Refactoring Changes on the SZZ Algorithm: An Empirical Study

## Abstract

SZZ is a widely used algorithm in the software engineering community to identify changes that are likely to introduce bugs (i.e., bug-introducing changes). Despite its wide adoption, SZZ still has room for improvements. For example, current SZZ implementations may still flag refactoring changes as bug-introducing. Refactorings should be disregarded as bugintroducing because they do not change the system behaviour. In this paper, we empirically investigate how refactorings impact both the input (bug-fix changes) and the output (bug-introducing changes) of the SZZ algorithm. We analyse 31,518 issues of ten Apache projects with 20,298 bug-introducing changes. We use an existing tool that automatically detects refactorings in code changes. We observe that 6.5% of lines that are flagged as bug-introducing changes by SZZ are in fact refactoring changes. Regarding bug-fix changes, we observe that 19.9% of lines that are removed during a fix are related to refactorings and, therefore, their respective inducing changes are false positives. We then incorporate the refactoring-detection tool in our Refactoring Aware SZZ Implementation (RA-SZZ). Our results reveal that RA-SZZ reduces 20.8% of the lines that are flagged as bug-introducing changes compared to the stateof- the-art SZZ implementations. Finally, we perform a manual analysis to identify change patterns that are not captured by the refactoring identification tool used in our study. Our results reveal that 47.95% of the analyzed bug-introducing changes contain additional change patterns that RA-SZZ should not flag as bug-introducing.

SZZ是软件工程社区中广泛使用的一种算法，用于识别可能引入bug的更改(例如，引入bug的更改)。尽管被广泛采用，SZZ仍然有改进的空间。例如，当前的SZZ实现仍然可能将重构更改标记为引入bug。重构应该被视为引入错误，因为它们不会改变系统的行为。在本文中，我们以经验研究重构如何影响SZZ算法的输入(bug修复更改)和输出(bug引入更改)。我们分析了十个Apache项目的31,518个问题，其中20,298个引入bug的更改。我们使用一个现有的工具，可以自动检测代码更改中的重构。我们观察到，被SZZ标记为引入bug的更改的6.5%的行实际上是重构更改。关于bug修复的变化，我们观察到19.9%在修复期间被删除的行与重构有关，因此，它们各自的诱导更改是假阳性的。然后，我们将重构检测工具合并到我们的感知重构的SZZ实现(RA-SZZ)中。我们的结果显示，与最先进的SZZ实现相比，RA-SZZ减少了20.8%被标记为引入bug的更改的行。最后，我们执行一个手工分析来识别我们研究中使用的重构识别工具没有捕捉到的变更模式。我们的结果显示，47.95%分析的引入bug的变更包含了RA-SZZ不应该标记为引入bug的额外变更模式。

## I. INTRODUCTION

Much research has been invested in bug prediction [1]–[6], [6]–[11]. For example, prior work predicts bugs using information from source code repositories. Examples of information used in the bug prediction are code churn metrics [1] [12] [2], object-oriented design metrics [3], complexity of code changes [5] [4], change meta-data [6] etc. Bug prediction is helpful to development teams to prioritize code regions with a greater probability of bugs occurrence [13].

在错误预测[1] – [6]，[6] – [11]方面已进行了大量研究。 例如，先前的工作使用源代码存储库中的信息来预测错误。 错误预测中使用的信息示例包括代码流失度量[1] [12] [2]，面向对象的设计度量[3]，代码更改的复杂性[5] [4]，更改元数据[6]等 错误预测有助于开发团队确定发生错误的可能性更大的代码区域的优先级[13]。

Although bug prediction is an important tool, it was not possible to study the origin of bugs in large-scale scenarios until the introduction of the SZZ algorithm [14]. The SZZ algorithm traces back the code history to find changes that are likely to introduce bugs, i.e., the so-called bug-introducing changes. SZZ was initially proposed by Sliwerski, Zimmermann and Zeller [14] – hence the acronym – and improved by Kim et al. [15]. However, SZZ is not without limitations [16]–[18], such as the recognition of equivalent changes, i.e., changes that do not modify system behaviour.

尽管错误预测是一个重要的工具，但是直到引入SZZ算法[14]才可能在大规模场景中研究错误的起源。 SZZ算法追溯代码历史，以查找可能会引入错误的更改，即所谓的错误引入更改。 SZZ最初是由Sliwerski，Zimmermann和Zeller [14]提出的-因此是首字母缩写-由Kim等人进行了改进。 [15]。 但是，SZZ并非没有局限性[16] – [18]，例如对等效更改的识别，即，不会更改系统行为的更改。

Several studies [19]–[21] state that code refactoring is a frequently used technique by developers during bug-fix changes. SZZ may produce inaccurate data by not recognizing that bugfix changes may contain interleaved refactorings, since code refactoring does not directly fix a bug [22]. Similarly, SZZ may erroneously flag refactoring changes as bug-introducing changes. For example, if SZZ flags a line as potentially bugintroducing, but such a line is the result of a method rename, SZZ should trace the history further using the previous method name.

几项研究[19] – [21]指出，代码重构是开发人员在错误修复更改期间经常使用的技术。 由于代码重构不能直接修复错误，因此SZZ可能无法识别错误修正更改可能包含交错的重构，从而产生不准确的数据[22]。 类似地，SZZ可能错误地将重构更改标记为引入错误的更改。 例如，如果SZZ将某行标记为可能引入了错误，但是该行是方法重命名的结果，则SZZ应该使用以前的方法名称进一步跟踪历史记录。

Prior research has evaluated the SZZ algorithm [16]–[18]. For example, da Costa et al. [17] used an evaluation framework to appraise the results of SZZ. In addition, Prechelt et al. [16] also evaluated SZZ in an industrial setting. These studies help to identify possible enhancements to be implemented to SZZ and what are the hindrances to perform better evaluations of SZZ.

先前的研究已经评估了SZZ算法[16] – [18]。 例如，da Costa等。 [17]使用评估框架评估SZZ的结果。 此外，Prechelt等。 [16]还评估了工业环境下的SZZ。 这些研究有助于确定可能对SZZ进行的增强，以及对SZZ进行更好评估的障碍是什么。

Nevertheless, little is known about the impact of refactoring changes on the results of the SZZ algorithm. Studying the impact of refactoring changes is important because, differently from other SZZ limitations (e.g., the identification of renames in directories and files [17]), such changes may impact the SZZ algorithm regardless the Version Control System (VCS) that is used to implement SZZ. The goal of our research is to quantify the refactoring changes that occur both in bugfixing changes (i.e., the input of the SZZ algorithm) and bugintroducing changes (i.e., the SZZ-generated data). We use RefDiff [23], a recent proposed state-of-the-art tool to detect refactoring changes in our analyses. We address the following research questions (RQs):

但是，关于重构更改对SZZ算法结果的影响知之甚少。 研究重构更改的影响非常重要，因为与其他SZZ限制（例如，目录和文件中的重命名的标识[17]）不同，此类更改可能会影响SZZ算法，而与版本控制系统（VCS）无关 实施SZZ。 我们研究的目标是量化在错误修正更改（即SZZ算法的输入）和错误引入更改（即SZZ生成的数据）中发生的重构更改。 我们使用RefDiff [23]，这是最近提出的最先进的工具，用于检测分析中的重构变化。 我们解决以下研究问题（RQ）：




## II. BACKGROUND & RELATED WORK

## III. STUDY SETTINGS

## IV. STUDY RESULTS

## V. THREATS TO VALIDITY

## VI. CONCLUSIONS

