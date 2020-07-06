# Cross Platform Bug Correlation using Stack Traces

## Abstract

Crashing of program is an annoying experience for users. Whenever a program crashes, an event log is generated. Sometimes built in crash reporting programs send crash reports automatically to developing site whereas sometimes, user is presented with an option to report the crash himself. This reporting is often useful for the development team to diagnose and fix the problem. It happens quite often that code that crashes a program is not the reason for the crash itself but crash is due to the use of some faulty function or library of some other program. We propose a method to identify cross platform bug correlation to detect faulty functions, using function call stack information given in bug reports. We collect and process bug reports from multiple platforms to compute similarity based on our similarity metric between occurrence sequences of the function calls within different bug reports. For the solved bug we extract information about faulty function by analyzing its bug report to propose fix of the similar bug with same function within correlated bug report. If there exists fix of one bug in one application, it can be used to resolve similar bug in some other application. Using our technique we found similar bug reports. We were able to find cases for similar bug reports that have same reason for the cause of bug and were using same fix.

程序崩溃对用户来说是一种恼人的体验。每当程序崩溃时，就会生成事件日志。有时内置的崩溃报告程序会自动将崩溃报告发送到开发站点，而有时，用户可以选择自己报告崩溃。这种报告对于开发团队诊断和修复问题通常很有用。通常情况下，导致程序崩溃的代码并不是崩溃本身的原因，而是由于使用了错误的函数或其他程序的库。我们提出了一种方法来识别跨平台的错误相关性来检测错误的函数，使用在错误报告中给出的函数调用堆栈信息。我们收集和处理来自多个平台的错误报告，根据不同错误报告中函数调用的发生序列之间的相似度度量来计算相似度。对于已解决的错误，我们通过分析错误函数的错误报告来提取关于错误函数的信息，从而在相关的错误报告中对具有相同功能的类似错误提出修复建议。如果在一个应用程序中存在一个错误的修复，它可以用于解决其他一些应用程序中的类似错误。使用我们的技术，我们发现了类似的错误报告。我们能够找到类似的错误报告的案例，这些案例具有相同的错误原因，并且使用相同的修复。

## I. INTRODUCTION

Technology is ever evolving. Due to high competition in the software industry and keeping on going demands of customers in view, software companies release their product at a very early stage of development that makes it impossible to test applications properly. Due to this problem, such applications are highly prone to errors(bugs) that can even lead to program crash. Crashing of a program causes annoyance for its users. Software producers use different methods to know about these errors in order to find the root cause of the problem to fix the faults. Many companies are investing a lot of money, effort and time to get these bugs reported because eventually these reports will help developers to find and fix bugs. Mozilla has paid out over 1.6 million dollars in Bug Bounty Program1 to its various researchers. Another way to report bugs is use of built in mechanism to report crashes automatically. Software such as Apple [3] and windows [8] use such mechanism to report crashes automatically to the development site. Some softwares provide an option to the user to report the error in form of a pop up window.

技术在不断发展。由于软件行业的激烈竞争和客户不断增长的需求，软件公司在开发的早期就发布了他们的产品，这使得它不可能正确地测试应用程序。由于这个问题，这样的应用程序很容易出现错误(bug)，甚至会导致程序崩溃。程序崩溃会给用户带来烦恼。软件生产者使用不同的方法来了解这些错误，以便找到问题的根本原因来修复错误。许多公司投入了大量的金钱、精力和时间来报告这些错误，因为这些报告最终将帮助开发人员发现和修复错误。Mozilla已经向其研究人员支付了超过160万美元的漏洞奖金。另一种报告错误的方法是使用内建机制来自动报告崩溃。苹果[3]和windows[8]等软件使用这种机制自动报告崩溃到开发站点。一些软件提供了一个选项，用户报告错误的形式，弹出窗口。

Fixing of a bug is not only a tedious task but also very expensive. Fixing of a bug has been an interesting topic for the researchers for almost one decade. It involves detailed analysis of structure of bug report [5], grouping of bugs, assignment of bugs to developers [4], [10] [11], fault localization and fault resolution. Grouping of bugs requires similar bugs to be placed in the same bucket. Then based on the number of bugs in each bucket, these bug reports are sent to the developing team for fault localization. When the faulty piece of the code is identified, developers resolve the bug. The process of reporting the bug to its resolution is resource constrained. Hence it gives rise to the need for automation of this process.

修复一个错误不仅是一项乏味的任务，而且是非常昂贵的。对研究人员来说，修复一个bug已经是近十年来一个有趣的话题了。详细分析bug报告[5]的结构，bug分组，bug分配给开发人员[4]，[10][11]，故障定位和故障解决。对bug进行分组需要将类似的bug放在同一个bucket中。然后，根据每个bucket中的错误数量，将这些错误报告发送给开发团队进行错误定位。当错误的代码片段被识别出来后，开发人员就会解决这个错误。将错误报告给解决方案的过程是资源受限的。因此，这就产生了对该过程自动化的需求。

Many open source programs [2] have built in systems for bug reporting. These softwares receive millions of bug reports each day. Developers examine these bug reports to find the faulty piece of code in order to fix the bug. Crashes, due to these bugs appearing to happen in different locations might be correlated, i.e., due to same faulty function used in different places. Identification of correlated bugs is yet another challenging task that involves analysis of crash signature in particular stack trace of the bug report. Correlation between bug reports can be used to improve fixing of bugs.

许多开源程序[2]都内置了用于错误报告的系统。这些软件每天都会收到上百万的错误报告。开发人员检查这些错误报告，以找到有问题的代码片段，从而修复错误。由于这些错误似乎发生在不同的位置而导致的崩溃可能是相关的。，由于相同的故障功能在不同的地方使用。识别相关的错误是另一项具有挑战性的任务，它涉及分析崩溃签名，特别是错误报告的堆栈跟踪。bug报告之间的相关性可以用于改进bug的修复。

It often happens that reason for crashing of two different applications is the single library function used by both of them. If fix is available for one application then it can be used to resolve issues in other application. The major focus of earlier approaches was on bug localization and bucketing of bugs [6] ,[7] by finding similarities among bug reports from the same platform. There are techniques that address this problem but at a time for one product only. To our knowledge, none has addressed the problem for cross platform bug correlation, which can be helpful in diagnosing and resolving bugs of one application using fix of other application. Our key idea is to compute cross platform correlation between bug reports.We propose a method which takes all bug reports belonging to all products and then find correlated bug reports.

两个不同的应用程序崩溃的原因通常是它们都使用了单一的库函数。如果某个应用程序可用修复程序，则可以使用它解决其他应用程序中的问题。早期方法的主要重点是通过查找来自同一平台的bug报告之间的相似性来对bug[6]和[7]进行定位和处理。有一些技术可以解决这个问题，但一次只能针对一种产品。据我们所知，没有一个解决了跨平台bug相关性的问题，这有助于诊断和解决一个应用程序的bug，并使用其他应用程序的修复程序。我们的关键思想是计算bug报告之间的跨平台相关性。我们提出了一种方法，将所有属于所有产品的缺陷报告都提取出来，然后找到相关的缺陷报告。

There are bug tracking systems that have on-line repositories that are used to store and keep track of each bug report. Bug reports once sent to the these systems are assigned to the developers for further processing.

有一些缺陷跟踪系统，它们拥有用于存储和跟踪每个缺陷报告的在线存储库。一旦将错误报告发送到这些系统，就会分配给开发人员进行进一步处理。

Format of Bug Report Each bug report has unique identifier. It contains many fields including date of issue, status of bug report (Fixed, Verified etc), date of assigning, comment id, comments etc. Details of error and/or stack traces are part of the comment section. Each bug report can have multiple comments which can have zero or more stack traces in them.

每个错误报告都有唯一的标识符。它包含了很多字段，包括问题日期，bug报告状态(修正，验证等)，分配日期，评论id，评论等。错误和/或堆栈跟踪的细节是评论部分的一部分。每个bug报告可以有多个注释，这些注释可以包含零个或多个堆栈跟踪。

Stack Trace is list of function calls with last function called on the top of the stack. Each line in the stack trace represents single function call and is called stack frame. Stack frame contains information about function, i.e., name, parameters, return type, line in the code at which function is located, file name, file path etc. Format of stack traces varies from application to application.

堆栈跟踪是最后一个函数在堆栈顶部调用的函数调用列表。堆栈跟踪中的每一行表示一个函数调用，称为堆栈帧。栈帧包含函数的相关信息。、名称、参数、返回类型、函数所在代码行、文件名、文件路径等。堆栈跟踪的格式因应用程序而异。

We make the following contributions 
- Automatic Extraction of Stack Traces: We identified different types of stack frames and from the comments of the bug reports we automatically extracted stack traces of each type. 
- Similarity Metric for Bug Reports: We define similarity metric for the stack traces in the different bug reports. 
- Clustering of Bug Reports: We grouped similar bug reports from different platforms by tuning distance threshold to analyze faulty function.
- Evaluation: We evaluated our similarity metric on 877000 bug reports collected from Bugzilla. Manual inspection of some of bug reports shows that bugs that are marked similar are due to the same fault.

我们做出以下贡献
- 自动提取堆栈跟踪:我们识别不同类型的堆栈帧，并从错误报告的注释中自动提取每种类型的堆栈跟踪。
- Bug报告的相似性度量:我们为不同的Bug报告中的堆栈跟踪定义相似性度量。
- 错误报告聚类:通过调优距离阈值，将来自不同平台的类似错误报告进行分组，分析错误功能。
- 评估:我们评估了从Bugzilla收集的877000个bug报告的相似性度量。对一些错误报告的手动检查显示，标记为类似的错误是由于相同的错误造成的。

## VI. RELATED WORK

There are tools for bug reporting, such as Windows Error Reporting [8] , Apple Crash Reporter [3], Mozilla Crash Reporter [2] etc. These tools focus on collecting bug reports and then grouping similar reports together. Much work has also been done in the field of bug localization and bucketing of the bugs based on crash report. Bug localization [4] techniques are used to recover potentially buggy files from bug reports. Bucketing of bugs[5], targets to group similar bugs in a bucket which helps developer to prioritize fixing of the bugs, depending on the size of the bucket. However all of these techniques work on bugs related to one product at a time. In a closely related work on improving bug localization using correlations in crash reports [9], authors exploit significance of crash signatures, stack frames of traces and order of frequent subsets to create crash correlation groups (CCG).Crash Correlation Groups offer a diversity of crashing scenarios that help developers identify the cause of bugs more efficiently. Our work is different from theirs in a way that they applied their technique on single application at a time, whereas we find correlation among stack traces of different applications.

有错误报告工具，如Windows错误报告[8]，苹果崩溃报告[3]，Mozilla崩溃报告[2]等。这些工具专注于收集错误报告，然后将类似的报告分组在一起。在错误定位和基于崩溃报告的错误处理方面也做了大量的工作。Bug本地化[4]技术用于从Bug报告中恢复潜在的Bug文件。[5]的目标是将类似的bug分组在一个bucket中，这可以帮助开发人员根据bucket的大小对修复bug进行优先排序。然而，所有这些技术每次都针对与一个产品相关的bug。在一个与使用崩溃报告[9]中的相关性改进bug定位密切相关的工作中，作者利用崩溃签名的重要性、跟踪的堆栈帧和频繁子集的顺序来创建崩溃关联组(CCG)。崩溃关联组提供了多种崩溃场景，帮助开发人员更有效地识别错误的原因。我们的工作与他们的不同之处在于，他们将他们的技术一次应用于单个应用程序，而我们发现不同应用程序的堆栈痕迹之间的相关性。

In their paper, Yingnong Dang Rongxin and Hongyu proposed a method ReBucket [6], to cluster crash reports based on the similarity of stack traces. It firstly calculates similarity among stack traces and then based on similarity; it assigns the crash reports to appropriate buckets. They evaluated their technique using crash data of five Microsoft products Microsoft Publisher, Microsoft OneNote, Microsoft PowerPoint, Microsoft Project and Microsoft Access.

在他们的论文中，Yingnong Dang Rongxin和Hongyu提出了一种[6]重桶的方法，基于堆栈轨迹的相似性来集群崩溃报告。首先计算叠加轨迹之间的相似度，然后基于相似度;它将崩溃报告分配给适当的桶。他们使用五个微软产品的崩溃数据来评估他们的技术:Microsoft Publisher、Microsoft OneNote、Microsoft PowerPoint、Microsoft Project和Microsoft Access。

In another work for locating crashing faults, authors proposed a novel technique CrashLocator [13] for automatically locating crashing faults using stack traces. CrashLocator computes the suspiciousness scores of all functions by generating approximate crash traces through iterative stack expansions, using function call graph generated from source code.

- [13] Rongxin Wu, Hongyu Zhang, Shing-Chi Cheung, and Sunghun Kim. Crashlocator: locating crashing faults based on crash stacks. In Proceedings of the 2014 International Symposium on Software Testing and Analysis, pages 204–214. ACM, 2014.

在另一篇定位崩溃故障的文章中，作者提出了一种新的利用堆栈跟踪自动定位崩溃故障的崩溃定位器[13]。CrashLocator使用从源代码生成的函数调用图，通过迭代堆栈扩展生成近似崩溃跟踪，计算所有函数的可疑得分。

Another approach [12] for correlated bugs revolve around call graph of program. This is altogether different from our work as according to their approach, two bugs are correlated if occurrence of one bug triggers other. Whereas focus of our work is to find two programs using same buggy function.

- [12] Shaohua Wang, Foutse Khomh, and Ying Zou. Improving bug localization using correlations in crash reports. In Mining Software Repositories (MSR), 2013 10th IEEE Working Conference on, pages 247–256. IEEE, 2013.

另一种解决相关bug的方法是围绕程序的调用图。这与我们的工作完全不同，因为根据他们的方法，如果一个错误的发生触发了另一个错误，那么两个错误是相关的。而我们工作的重点是找到两个使用相同的错误函数的程序。

## VII. CONCLUSION AND FUTURE WORK

We find correlated bugs belonging to different products that arise due to same fault. Finding correlated bugs will help to automatically fix the bug by using fix of one bug for other. Focus of this work was to find structural matching of function calls in stack traces of bug reports. We plan to add more metrics to compute similarity. Moreover this work can be extended to find semantic similarities among function calls present in stack trace.

我们发现属于不同产品的相关bug是由于相同的故障而产生的。找到相关的bug将有助于通过对一个bug进行修复来自动修复该bug。这项工作的重点是在错误报告的堆栈跟踪中找到函数调用的结构匹配。我们计划增加更多的度量来计算相似度。此外，该工作可以扩展到查找栈跟踪中函数调用之间的语义相似性。
