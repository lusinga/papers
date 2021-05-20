# When Do Changes Induce Fixes?

## ABSTRACT

As a software system evolves, programmers make changes that sometimes cause problems. We analyze CVS archives for fix-inducing changes—changes that lead to problems, indicated by fixes. We show how to automatically locate fix-inducing changes by linking a version archive (such as CVS) to a bug database (such as BUGZILLA). In a first investigation of the MOZILLA and ECLIPSE history, it turns out that fix-inducing changes show distinct patterns with respect to their size and the day of week they were applied.

随着软件系统的发展，程序员所做的更改有时会导致问题。我们分析CVS存档，以了解导致修复的更改——由修复程序指出的导致问题的更改。我们将展示如何通过将版本存档(如CVS)链接到错误数据库(如BUGZILLA)来自动定位导致修复的更改。在对MOZILLA和ECLIPSE历史的第一次调查中，我们发现导致修复的更改在其大小和应用日期方面显示了不同的模式。

## 1. INTRODUCTION

When we mine software histories, we frequently do so in order to detect patterns that help us understanding the current state of the system. Unfortunately, not all changes in the past have been beneficial. Any bug database will show a significant fraction of problems that are reported some time after some change has been made.

当我们挖掘软件历史记录时，我们经常这样做是为了检测有助于我们理解系统当前状态的模式。不幸的是，并不是过去所有的改变都是有益的。任何错误数据库都将显示在做出一些更改后的一段时间内报告的问题的很大一部分。

In this work, we attempt to identify those changes that caused problems. The basic idea is as follows:

1. We start with a bug report in the bug database, indicating a fixed problem.
2. We extract the associated change from the version archive, thus giving us the location of the fix.
3. We determine the earlier change at this location that was applied before the bug was reported.

在这项工作中，我们试图识别那些引起问题的变化。基本思路如下:
1. 我们从bug数据库中的bug报告开始，指出一个已修复的问题。
2. 我们从版本存档中提取相关的更改，从而给出修复的位置。
3. 我们确定在报告错误之前在此位置应用的较早的更改。

This earlier change is the one that caused the later fix. We call such a change fix-inducing. What can one do with fix-inducing changes? Here are some potential applications:

这个早期的更改导致了后面的修复。我们把这种变化称为修正诱导。一个人能做什么修复诱导的变化?以下是一些潜在的应用:

Which change properties may lead to problems? We can investigate which properties of a change correlate with inducing fixes, for instance, changes made on a specific day or by a specific group of developers.

哪些更改属性可能会导致问题?我们可以调查变更的哪些属性与诱导修复相关，例如，特定日期或特定开发人员组所做的变更。

How error-prone is my product? We can assign a metric to the product—on average, how likely is it that a change induces a later fix?

我的产品有多容易出错?我们可以为产品指定一个度量标准——平均而言，一个更改引发以后修复的可能性有多大?

How can I filter out problematic changes? When extracting the architecture via co-changes from a version archive, there is no need to consider fix-inducing changes, as they get undone later.

如何过滤出有问题的更改?当从版本存档中通过共同更改提取架构时，不需要考虑导致修复的更改，因为它们稍后会被撤消。

Can I improve guidance along related changes? When using cochanges to guide programmers along related changes, we would like to avoid fix-inducing changes in our suggestions.

我能改进相关变更的指导吗?当使用cochange指导程序员进行相关更改时，我们希望在我们的建议中避免导致修复的更改。

This paper describes our first experiences with fix-inducing changes. We discuss how to extract data from version and bug archives (Section 2), and how we link bug reports to changes (Section 3). In Section 4, we describe how to identify and locate fix-inducing changes. Section 5 shows the results of our investigation of the MOZILLA and ECLIPSE: It turns out that fix-inducing changes show distinct patterns with respect to their size and the day of week they were applied. Sections 6 and 7 close with related and future work.

## 2. WHAT’S IN OUR ARCHIVES?

For our analysis we need all changes and all fixes of a project. We get this data from version archives like CVS and bug tracking systems like BUGZILLA.

对于我们的分析，我们需要项目的所有更改和所有修复。我们从版本档案(如CVS)和bug跟踪系统(如BUGZILLA)中获得这些数据。

A CVS archive contains information about changes: Who changed what, when, why, and how? A change  transforms a revision r1 to a revision r2 by inserting, deleting, or changing lines. We will later investigate changes on the line level. Several changes 1, . . . , n form a transaction t if they were submitted to CVS by the same developer, at the same time, and with the same log message, i.e., they have been made with the same intention, e.g. to fix a bug or to introduce a new feature. As CVS records only individual changes to files, we group these to transactions with a sliding time window approach [12].

CVS存档包含有关更改的信息:谁更改了什么、何时更改、为什么更改以及如何更改?更改通过插入、删除或更改行，将修订版r1转换为修订版r2。稍后我们将研究行级别上的变化。几个变化1，…， n如果事务是由同一个开发人员在同一时间，以相同的日志消息提交给CVS的，也就是说，它们是出于相同的目的而创建的，例如修复一个bug或引入一个新特性。由于CVS只记录对文件的个别更改，因此我们使用滑动时间窗口方法[12]将这些更改分组到事务中。

A CVS archive also lacks information about the purpose of a change: Did it introduce a new feature or did it fix a bug? Although it is possible to identify such reasons solely with log messages [7], we combine both CVS and BUGZILLA for this step because this increases the precision of our approach.

CVS存档还缺乏关于更改目的的信息:它引入了新特性还是修复了一个bug?尽管仅通过日志消息[7]就可以确定这些原因，但我们将CVS和BUGZILLA结合在一起进行这一步，因为这提高了我们方法的精度。

A BUGZILLA database collects bug reports that are submitted by a reporter with a short description and a summary. After a bug has been submitted, it is discussed by developers and users who provide additional comments and may create attachments. After the bug has been confirmed, it is assigned to a developer who is responsible to fix the bug and finally commits her changes to the version control archive. BUGZILLA also captures the status of a bug, e.g., UNCONFIRMED, NEW, ASSIGNED, RESOLVED, or CLOSED and the resolution, e.g., FIXED, DUPLICATE, or INVALID. Details on the lifecycle of a bug can be found in the BUGZILLA documentation [10, Sections 6.3 and 6.4].

BUGZILLA数据库收集由记者提交的带有简短描述和摘要的bug报告。bug提交后，开发人员和提供附加评论并可能创建附件的用户会对其进行讨论。在bug被确认之后，它被分配给负责修复bug并最终将其更改提交到版本控制存档的开发人员。BUGZILLA还捕获bug的状态，例如，未确认的、新的、分配的、已解决的或已关闭的，以及解决的状态，例如，固定的、重复的或无效的。bug生命周期的细节可以在BUGZILLA文档[10，章节6.3和6.4]中找到。

For our analysis, we mirror both CVS and BUGZILLA in a local database. Our mirroing techniques for CVS are described in [12]. To mirror a BUGZILLA database, we use its XML export feature. Additionally, we import attachments and activities directly from the web interface of BUGZILLA. Our local BUGZILLA database schema is similar to the one described in [2].

对于我们的分析，我们将CVS和BUGZILLA镜像到本地数据库中。我们的CVS镜像技术在[12]中描述。为了镜像BUGZILLA数据库，我们使用它的XML导出特性。此外，我们直接从BUGZILLA的web界面导入附件和活动。我们的本地BUGZILLA数据库模式与[2]中描述的类似。

## 3. IDENTIFYING FIXES

In order to locate fix-inducing changes, we first need to know whether a change is a fix. A common practice among developers is to include a bug report number in the comment whenever they fix a defect associated with it. Cˇ ubranic´ and Murphy [4] as well as Fischer, Pinzger, and Gall [5, 6] exploited this practice to link changes with bugs. Figure 1 sketches the basic idea of this approach.

为了定位导致修复的更改，我们首先需要知道一个更改是否是修复。开发人员的一种常见做法是，在他们修复与之相关的缺陷时，在注释中包含错误报告编号。Cˇubranic´和Murphy[4]以及Fischer, Pinzger和Gall[5,6]利用这种做法将更改与bug联系起来。图1概述了这种方法的基本思想。

In our work, we refine these techniques by assigning every link (t, b) between a transaction t and a bug b two independent levels of confidence: a syntactic level, inferring links from a CVS log to a bug report, and a semantic level, validating a link via the bug report data. These levels are later used to decide which links shall be taken into account in our experiments.

在我们的工作中,我们改进这些技术通过分配每一个环节(t, b)事务t和bug b两个独立的信心水平:句法层面,推断从CVS日志链接到一个bug报告,和语义层面,通过错误报告数据验证链接。这些层次稍后将用于决定在我们的实验中应该考虑哪些环节。

### 3.1 Syntactic Analysis

### 3.2 Semantic Analysis

### 3.3 Results

## 4. LOCATING FIXINDUCING CHANGES

A fix-inducing change is a change that later gets undone by a fix. In this section, we show how to automatically locate fix-inducing changes.

导致修复的更改是以后会被修复撤消的更改。在本节中，我们将展示如何自动定位导致修复的更改。

Suppose that a change $\delta\in t$, which is known to be a fix for bug b (thus a link (t, b) must exist), transforms the revision r1 = 1.17 of Foo.java into r2 = 1.18 (see Figure 2), i.e.,  introduces new lines to r2 or changes and removes lines of r1. First, we detect the lines L that have been touched by  in r1. These are the locations of the fix. To locate them, we use the CVS diff command. In our example, we assume that line 20 and 40 have been changed and line 60 has been deleted, thus the fix locations in r1 are L = {20; 40; 60}.

假设一个变化δ∈t,这是修复bug b(因此一个链接(t, b)必须存在),将修订r1 Foo.java = 1.17为r2 = 1.18(见图2),也就是说,引入了新的线r2或更改和删除行r1。首先，我们检测在r1中接触过的行L。这些是修复的位置。为了定位它们，我们使用CVS diff命令。在我们的例子中，假设第20行和第40行已经更改，第60行已经删除，因此r1中的固定位置为L = {20;40;60}。

Next, we call the CVS annotate command for revision r1 = 1.17 because this was the last revision without the fix; in contrast, revision r2 = 1.18 already contains the applied fix. The annotations prepend each line with the most recent revision that touched this line. Additionally, CVS includes the developer and the date in the output. We show an excerpt of the annotated file in Figure 3. The CVS annotate command is only reliable for text files, thus we ignore all files that are marked as binary in the repository.

接下来，我们为版本r1 = 1.17调用CVS注释命令，因为这是最后一个没有修复的版本;相比之下，r2 = 1.18版本已经包含了应用的修复。注释在每行前面加上触及该行的最新修订。此外，CVS在输出中包含开发人员和日期。我们展示了图3中注释文件的摘录。CVS annotate命令只对文本文件可靠，因此我们忽略存储库中所有标记为二进制的文件。

We scan the output and take for each line l 2 L the revision r0 that annotates line l. These revisions are candidates for fix-inducing changes. We add (r0, r2) to the candidate set S, which is in our example S = {(1.11, 1.18); (1.14, 1.18); (1.16, 1.18)}.

From this set, we remove pairs (ra, rb) for which it is not possible that ra induced the fix rb—for instance, because ra was committed to CVS after the bug fixed by rb has been reported. In particular, we say that such a pair (ra, rb) is a suspect if ra was committed after the latest reported bug linked with the revision rb. Suspect changes could not contribute to the failure observed in the bug report. In Figure 2 the pairs (1.14, 1.18) and (1.16, 1.18) are examples of suspects.

## 5. FIRST RESULTS

### 5.1 FixInducing Transactions are Large

### 5.2 Don’t Program on Fridays

## 6. RELATED WORK

To our knowledge, this is the first work that shows how to locate fix-inducing changes in version archives. However, fix-inducing changes have been used previously under the name dependencies by Purushothaman and Perry [9] to measure the likelihood that small changes introduce errors. Baker and Eick proposed a similar concept of fix-on-fix changes [1]. Fix-on-fix changes are less general than fix-inducing changes because they require both changes to be fixes.

据我们所知，这是第一个展示如何在版本存档中定位导致修复的更改的工作。然而，之前Purushothaman和Perry[9]在名称dependencies下使用了诱导修复的更改来测量小更改引入错误的可能性。Baker和Eick提出了一个类似的固定对固定的更改[1]的概念。Fix-on-fix更改不如引发fix的更改通用，因为它们都需要对两个更改进行修复。

In order to locate fix-inducing changes, we need first to identify fixes in the version archive. Mockus and Votta developed a technique that identifies reasons for changes (e.g., fixes) in the log message of a transaction [7]. In our approach, we refine the techniques of Cˇ ubranic´ and Murphy [4] and of Fischer, Pinzger, and Gall [6, 5], who identified references to bug databases in log messages and used these references to infer links from CVS archives to BUGZILLA databases.

为了定位导致修复的更改，我们首先需要在版本存档中识别修复。Mockus和Votta开发了一种技术，可以在事务[7]的日志消息中识别更改(例如，修复)的原因。在我们的方法中，我们改进了Cˇubranic´和Murphy[4]以及Fischer, Pinzger和Gall[6,5]的技术，他们在日志消息中识别对bug数据库的引用，并使用这些引用来推断从CVS档案到BUGZILLA数据库的链接。

Cˇ ubranic´ and Murphy additionally inferred links in the other direction, from BUGZILLA databases to CVS archives, by relating bug activities to changes. This has the advantage to identify fixes that are not referenced in log messages. For more details about this approach, we refer to [3].

Cˇubranic和Murphy还推断了另一个方向的链接，从BUGZILLA数据库到CVS档案，通过将bug活动与更改联系起来。这样做的好处是可以识别日志消息中没有引用的修复。关于这种方法的更多细节，请参考[3]。

Rather than searching for fix-inducing changes, one can also directly determine failure-inducing changes, where the presence of the failure is determined by an automated test. This was explored by Zeller, applying Delta Debugging on multiple versions [11].

与其搜索导致修复的更改，还可以直接确定导致失败的更改，其中失败的存在是由自动化测试确定的。Zeller在多个版本[11]上应用了增量调试，探索了这一点。

## 7. CONCLUSION

As soon as a project has a bug database as well as a version archive, we can link the two to identify those changes that caused a problem. Such fix-inducing changes have a wide range of applications. In this paper, we examined the properties of fix-inducing changes in the ECLIPSE and MOZILLA projects and found, among others, that the larger a change, the more likely it is to induce a fix; checking for other correlated properties is straight-forward. We also found that in the ECLIPSE project, fixes are three times as likely to induce a later change than ordinary enhancements. Such findings can be generated automatically for arbitrary projects.

一旦项目有了bug数据库和版本存档，我们就可以将两者联系起来，以确定哪些更改导致了问题。这种诱导固定的变化有广泛的应用。在本文中，我们研究了ECLIPSE和MOZILLA项目中诱导修复的变更的属性，发现，变更越大，越有可能诱导修复;检查其他相关属性很简单。我们还发现，在ECLIPSE项目中，修复引起后续更改的可能性是普通增强的三倍。这样的发现可以为任意的项目自动生成。

Besides the applications listed in Section 1, our future work will focus on the following topics:

Which properties are correlated with inducing fixes? These can be properties of the change itself, but also properties or metrics of the object being changed. This is a wide area with several future applications.

哪些属性与诱导修复相关?这些可以是更改本身的属性，也可以是被更改对象的属性或度量。这是一个广阔的领域，未来有几种应用。

How do we disambiguate earlier changes? If a fixed location has been changed multiple times in the past, which of these changes should we consider as inducing the fix? We are currently evaluating a number of disambiguation techniques.

我们如何消除早期变化的歧义?如果一个固定的位置在过去被多次更改，我们应该把这些更改中的哪一个看作是导致这个修复的原因?我们目前正在评估一些消歧技术。

How do we present the results? Simply knowing which changes are fix-inducing is one thing, but we also need to present our findings. We are currently exploring visualization techniques to help managers as well as programmers.

我们如何呈现结果?简单地知道哪些变化是导致固定变化的原因是一回事，但我们也需要展示我们的发现。我们目前正在探索可视化技术来帮助经理和程序员。

## 8. REFERENCES
