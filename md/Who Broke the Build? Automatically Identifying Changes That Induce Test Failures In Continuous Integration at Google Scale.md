# Who Broke the Build? Automatically Identifying Changes That Induce Test Failures In Continuous Integration at Google Scale

## Abstract

Quickly identifying and fixing code changes that introduce regressions is critical to keep the momentum on software development, especially in very large scale software repositories with rapid development cycles, such as at Google. Identifying and fixing such regressions is one of the most expensive, tedious, and time consuming tasks in the software development life-cycle. Therefore, there is a high demand for automated techniques that can help developers identify such changes while minimizing manual human intervention. Various techniques have recently been proposed to identify such code changes. However, these techniques have shortcomings that make them unsuitable for rapid development cycles as at Google. In this paper, we propose a novel algorithm to identify code changes that introduce regressions, and discuss case studies performed at Google on 140 projects. Based on our case studies, our algorithm automatically identifies the change that introduced the regression in the top-5 among thousands of candidates 82% of the time, and provides considerable savings on manual work developers need to perform.

快速识别和修复引入回归的代码更改对于保持软件开发的势头至关重要，特别是在具有快速开发周期的大型软件存储库中，例如在谷歌。识别和修复这样的回归是软件开发生命周期中最昂贵、最繁琐、最耗时的任务之一。因此，对自动化技术的需求非常高，这种技术可以帮助开发人员在尽量减少人工干预的同时识别此类更改。最近提出了各种技术来识别这种代码更改。但是，这些技术有缺点，不适合谷歌这样的快速开发周期。在本文中，我们提出了一种新的算法来识别引入回归的代码更改，并讨论了在谷歌上对140个项目进行的案例研究。基于我们的案例研究，我们的算法在82%的情况下自动识别在数千个候选者中引入前5名回归的更改，并为开发人员执行所需的手工工作节省了大量时间。

## I. INTRODUCTION

Google’s source code is big, on the order of 2 billion lines of code (LOC) and it evolves rapidly [1], [2]. On average, 40000 code changes are submitted to the Google code repository every day, and 15 million lines of code in 250000 files are modified every week [2].

谷歌的源代码很大，大约有20亿行代码(LOC)，并且它发展得很快。平均每天有40000个代码更改提交给谷歌代码存储库，每周有250000个文件中的1500万行代码被[2]修改。

To make such rapid development and evolution more reliable, Google has adopted Continuous Integration (CI) [3], where each code change triggers code to be compiled and tests to be executed so that regressions can be caught earlier in the development lifecycle [4].

为了使这种快速的开发和演化更加可靠，谷歌采用了持续集成(CI)[3]，其中每个代码更改都会触发代码的编译和测试的执行，以便在开发生命周期[4]的早期捕获回归。

Regressions have many causes, e.g. code does not compile, tests fail, performance of the system drops. This paper specifically addresses regressions where code compiles, but there are test failures.

回归有很多原因，例如代码不能编译，测试失败，系统性能下降。本文专门讨论了代码编译时的回归，但是测试失败。

Figure 1 shows the code repository and development workflow at Google. The Google code repository does not use multiple branches, i.e. there is a single branch for the entire codebase, called HEAD, where all developers submit their changes in total order [2]. Developers change the code in the repository by submitting atomic changes to the codebase, called a changelist (CL). System version refers to the version of HEAD as it is after a CL is submitted. So every CL submission results in a new system version.

图1显示了谷歌的代码存储库和开发工作流。谷歌代码库不使用多个分支，也就是说，整个代码库只有一个分支，称为HEAD，所有开发人员在这里按[2]的总顺序提交他们的更改。开发人员通过向代码库(称为变更列表(changelist, CL))提交原子性更改来更改存储库中的代码。系统版本是指提交CL后的HEAD版本。因此，每次CL提交都会产生一个新的系统版本。

Each CL is validated by executing tests. Some tests are run before the CL is submitted, while some tests are run after the submission. When a test executed after CL submission fails, the CL is said to have induced a regression. When a CL introduces such a regression into HEAD that results in test failures, it is important to identify it quickly, so that the development velocity is not disrupted. As an example, assume a regression is introduced to the codebase by changelist $CL_i$ in Figure 1, and some tests fail at all system versions after that CL. If a developer decides to debug the failing tests to locate their root cause, she can use existing debugging techniques in the literature on any failing version of the system (any version including and after $CL_i$). However, it is critical to identify that $CL_i$ was the changelist that introduced the regression, because:

每个CL都通过执行测试进行验证。有些测试在提交CL之前运行，有些测试在提交之后运行。当在CL提交失败后执行测试时，CL被认为已经导致了回归。当CL将这样的回归引入到导致测试失败的HEAD中时，快速识别它是很重要的，这样开发速度就不会被打乱。例如，假设图1中的changelist CLi向代码库引入了回归，并且在该CL之后的所有系统版本中，一些测试都失败了。如果开发人员决定调试失败的测试，以找到它们的根本原因，那么她可以在系统的任何失败版本(包括CLi之后的任何版本)上使用现有的调试技术。然而，确定CLi是引入回归的变更列表是很重要的，因为:

- The source code modifications in CLi might provide clues about the root cause of the regression, as it introduced the regression in the first place.
- CLi中的源代码修改可能提供关于回归的根本原因的线索，因为它首先引入了回归。
- Debugging the failing tests at version CLi is more beneficial than debugging at some later version, say CLi+k, since changes after CLi increase/change the code to be analyzed and the developer can be misled.
- 在版本CLi上调试失败的测试要比在以后的版本(比如CLi+k)上调试更有益处，因为在CLi之后所做的更改会增加/更改要分析的代码，并且会误导开发人员。
- More faults might have been introduced at later versions of the system, which makes it harder to debug and identify root causes of a regression.
- 在以后的系统版本中可能会引入更多的错误，这使得调试和确定回归的根本原因变得更加困难。
- Evidence suggests that the author of CLi might locate and fix the fault easier than any other developer [5].
- 有证据表明，CLi的作者可能比任何其他开发人员[5]更容易定位和修复错误。
- Reverting the change that introduces the regression, such as CLi, is a common practice to resolve the regression quickly [6].
- 恢复引入回归的更改(如CLi)是快速解决[6]回归的常见实践。

In summary, for code repositories where there is rapid evolution as at Google, finding the system version where a regression was first introduced is the crucial first step in debugging failing tests. In this paper:

总之，对于在谷歌上有快速发展的代码存储库，找到第一次引入回归的系统版本是调试失败测试的关键的第一步。摘要:

- We propose a novel algorithm that can automatically identify changes that introduce regressions into the codebase,
- 我们提出了一种新的算法，可以自动识别将回归引入代码库的更改，
- We evaluate our algorithm on a case study we ran on 140 projects in Google and discuss the results.
- 我们通过一个在谷歌上运行的140个项目的案例研究来评估我们的算法，并讨论结果。

## II. CONTINUOUS INTEGRATION AT GOOGLE

As summarized in Table I, tests are classified into four categories at Google according to their size: small, medium, large, enormous [7]. Size refers to the execution time of the test, excluding any overhead of setting up the test, e.g. compilation. At Google, given the size of each test in Table I, there are two typical development workflows regarding CLs where tests are executed (configured by developers manually, and enforced by infrastructure automatically):

如表一所示，根据谷歌的大小，将测试分为四类:小、中、大、巨[7]。大小指的是测试的执行时间，不包括设置测试的任何开销，例如编译。在谷歌中，给定表I中每个测试的大小，有两个关于CLs的典型开发工作流，其中执行测试(由开发人员手动配置，由基础设施自动执行):

1) Presubmit tests: Tests are run before a CL is submitted. Unless all tests pass, the CL is not allowed to be submitted. Typical presubmit tests are small/medium tests (although large tests are also allowed), so that developers are not blocked for a long time to get the results of test execution before submitting a CL [2].
1)预提交测试:在CL提交之前运行测试。除非所有测试通过，否则不允许提交CL。典型的预提交测试是小型/中型测试(尽管也允许大型测试)，因此在提交CL[2]之前，开发人员不会被长时间阻塞以获得测试执行的结果。
2) Postsubmit tests: A CL is submitted to the repository without waiting on the results of test execution. Tests are executed periodically to check if any of the submitted CLs caused a regression, and if so, developers are notified of the regression after the submission. Typical postsubmit tests are large/enormous tests, because such tests would take too long to execute and hinder development velocity if they were to be executed before CL submission.
2) Postsubmit测试:CL提交到存储库，而不等待测试执行的结果。定期执行测试，以检查是否有任何提交的CLs导致了回归，如果是，则在提交后通知开发人员回归。典型的postsubmit测试是大型/庞大的测试，因为如果在CL提交之前执行这些测试，那么这些测试将花费太长时间来执行，并且会阻碍开发速度。

When a presubmit test fails, the CL is not allowed to be submitted. Therefore, there is no possibility of introducing a regression due to test failures. However, this does not hold for postsubmit tests. Such tests do not block CL submission, and regressions can surface after a certain time period.
当预提交测试失败时，不允许提交CL。因此，由于测试失败而引入回归是不可能的。但是，这不适用于postsubmit测试。这样的测试不会阻碍CL的提交，并且回归可能在一段时间之后出现。

Figure 2 shows an overview of how such tests can fail after some CLs are submitted. The CI system executes tests periodically (e.g. every 10 minutes or every N CLs). In this case, it has executed the tests at versions CLG (green CL) with all tests passing and CLR (red CL) with some tests failing. Given this, some culprit CL in the range (CLG, CLR] must have caused a regression.
图2显示了在提交一些CLs之后，此类测试如何失败的概述。CI系统定期执行测试(例如每10分钟或每N个CLs)。在本例中，它在版本CLG(绿色CL)上执行了测试，所有测试都通过了，CLR(红色CL)执行了一些测试失败。鉴于此，一定是范围内的某个罪魁祸首CL (CLG, CLR)导致了回归。

A well-known solution to finding the culprit CL for postsubmit tests in literature is performing a search over the CL range (CLG, CLR]. An example is binary search as done in ”git bisect” [8], where the tests are executed at the CL CLG+CLR 2 . If tests are failing at that version of the system, the search is carried out between (CLG, CLG+CLR 2 ]. Otherwise, it is carried out between (CLG+CLR 2 , CLR]. This procedure continues recursively, until the culprit CL is identified. To obtain the result faster, an n-ary search, instead of binary, can also be used, but the idea still holds.

在文献中找到提交后测试的罪魁祸首CL的一个众所周知的解决方案是在CL范围内执行搜索(CLG, CLR)。一个例子是在“git bisect”[8]中执行的二分查找，其中的测试在clclg +CLR 2上执行。如果该版本的系统测试失败，则在(CLG, CLG+CLR 2)之间执行搜索。否则，在(CLG+CLR 2, CLR)之间执行。这个过程递归地继续下去，直到找到罪魁祸首CL。为了更快地获得结果，也可以使用n元搜索，而不是二进制搜索，但这个想法仍然成立。

Searching as discussed above is a possible solution for small/medium sized tests, as they don’t take too long to execute (5 minutes is the upper limit). For instance, if there are 1000 CLs in the search window, the culprit CL can possibly be identified within 10 iterations, hence 50 minutes.
正如上面所讨论的，搜索对于小型/中型测试是一个可能的解决方案，因为它们不会花费太长的时间来执行(5分钟是上限)。例如，如果搜索窗口中有1000个CLs，那么可以在10次迭代(即50分钟)内确定罪魁祸首CL。

However, for large and enormous tests, this approach quickly becomes undesirable: 
- If a test takes 45 minutes to run, it would potentially take 7.5 hours to find the culprit CL in a 1000 CL search window, 
- If code development continues in the presence of failing tests (such as at Google), other developers might introduce more regressions along the way before the current regression is resolved, 
- Re-running tests will use a large amount of resources that can be used for other purposes.

但是，对于大型的测试，这种方法很快就变得不受欢迎:
- 如果一个测试需要45分钟运行，那么在1000个CL搜索窗口中找到罪魁祸首CL可能需要7.5个小时，
- 如果在测试失败的情况下继续进行代码开发(例如在谷歌)，其他开发人员可能会在当前的回归被解决之前引入更多的回归，
- 重新运行的测试将使用大量的资源，可用于其他目的。

Therefore, identifying the culprit CL quickly, resolving the regression, and getting back to green is important to keep development momentum. This paper focuses on solving the problem of finding such culprit CLs that cause regressions for large and enormous postsubmit tests. The technique in this paper focuses on solving this problem such that: 
- Regressions are automatically detected without any human intervention or input (e.g. creation of a bug report), 
- A list of likely culprit CLs is provided to developers rapidly, 
- Tests are not re-executed to conserve resources.

因此，快速识别问题根源、解决回归、回归绿色对于保持发展势头非常重要。本文的重点是解决寻找导致大量提交后测试回归的罪魁祸首CLs的问题。本文所采用的技术主要解决以下问题:
- 在没有任何人为干预或输入的情况下自动检测回归(例如创建bug报告)，
- 迅速向开发人员提供可能的罪魁祸首CLs列表，
- 测试不会重新执行以节省资源。

## III. BUILD SYSTEM AT GOOGLE

In this section, we discuss the build system used at Google, called blaze [9] (partially open sourced as bazel [10]), and define terms used to describe our algorithm in the next section. 

At the lowest level, the build system at Google is composed of files f that can have dependencies on each other. As an example, Figure 3 depicts files and their dependencies for a simple chat system in Java. 

Files can be grouped into logical units. For the chat system, there are three logical groups: server side code composed of ChatServer.java and ServerUtils.java, client side code composed of ChatClient.java and ClientUtils.java, and finally network communication related code in TcpClient.java, TcpServer.java and TcpSocket.java. Such logical groups are called build targets.

在本节中，我们将讨论谷歌中使用的构建系统blaze[9](部分开源为bazel[10])，并在下一节中定义用于描述我们的算法的术语。

在最低级别上，谷歌的构建系统由相互依赖的文件f组成。例如，图3描述了Java中一个简单聊天系统的文件及其依赖关系。

文件可以分组成逻辑单元。对于聊天系统，有三个逻辑组:由ChatServer.java和ServerUtils组成的服务器端代码。java，客户端代码由客户端。java和客户端。最后是TcpClient中的网络通信相关代码。java, TcpServer.java和TcpSocket.java。这样的逻辑组称为构建目标。

Definition 1. A build target T is a logical grouping of files (e.g. test code, source code, data) in the code repository that explicitly lists the files it encapsulates and its dependencies. 
Figure 4 shows the files and the build targets that encapsulate them for the chat system: network, server and client code. Targets also depend on each other based on the required file level dependencies. As an example, the target for ChatClient.java depends on the target for TcpClient.java.
定义1: 构建目标T是代码存储库中文件(例如测试代码、源代码、数据)的逻辑分组，它显式地列出了它封装的文件及其依赖项。
图4显示了用于聊天系统的文件和构建目标:网络、服务器和客户机代码。基于所需的文件级依赖项，目标之间也相互依赖。例如，ChatClient.java的目标取决于TcpClient.java的目标。

Build targets are defined in BUILD files. Figure 5 shows the definitions of the targets for the chat system: network_lib, chat_client and chat_server; and a target that is an executable binary: chat_system. As expected, dependencies are specified for each build target: e.g. chat_server depends on network_lib.

构建目标在构建文件中定义。图5显示了聊天系统目标的定义:network_lib、chat_client和chat_server;以及一个可执行二进制文件的目标:chat_system。与预期一样，每个构建目标都指定了依赖项:例如，chat_server依赖于network_lib。

Definition 2. Build graph (or build tree) B is a directed acyclic graph (DAG) where nodes T are build targets and there is an edge from Ti to Tj if Ti depends on Tj . 
As shown in Figure 6, targets in the entire code repository comprise the build graph. Below are functions that denote relationships of files and targets.

定义2: 构建图(或构建树)B是一个有向无环图(DAG)，其中节点T是构建目标，如果Ti依赖于Tj，则有一条从Ti到Tj的边。
如图6所示，整个代码存储库中的目标组成了构建图。下面是表示文件和目标之间关系的函数。

Definition 3. SFT denotes the set of targets that a file belongs to in the build tree: SFT (fi) = {T : fi is listed as a source file in T ∈ B} Typically, a file is part of a single target. But it is not uncommon to have multiple targets that include the same file, e.g. there might be a target that exposes fewer features than another target, and both may need the same file. In the build tree, targets have direct and transitive dependencies on each other.

定义3: SFT表示构建树中文件所属的目标集:SFT (fi) = {T: fi在T∈B}中作为源文件列出。通常，文件是单个目标的一部分。但是包含相同文件的多个目标并不少见，例如，某个目标比另一个目标公开的特性更少，并且两者可能需要相同的文件。在构建树中，目标之间有直接依赖关系和传递依赖关系。

Definition 4. D(Ti) denotes the direct and transitive dependencies of a build target in the build tree: 
$D(T_i) = \{T : there is a path from T_i \in B to T \in B\}$

定义4。D(Ti)表示构建树中构建目标的直接依赖关系和传递依赖关系:
$D(T_i) = \{T:有一条从T_i \in B到T \in B\}$的路径

Definition 5. dT (Ti, Tj) denotes the length of the shortest path between two build targets (0 if there is no path): 
dT (Ti, Tj) : shortest distance from Ti ∈ B to Tj ∈ B

定义5。dT (Ti, Tj)表示两个构建目标之间最短路径的长度(无路径为0):
dT (Ti, Tj): Ti∈B到Tj∈B的最短距离

## IV. DEVELOPER WORKFLOW AT GOOGLE

In this section, we discuss the typical development workflow at Google and define some functions that relate the workflow to the build system.
在本节中，我们将讨论谷歌的典型开发工作流，并定义一些将工作流与构建系统相关联的功能。

Definition 6. A changelist (CL) is an atomic change to the code repository that can add / update / delete one or more files upon submission. 

A CL can contain any number of files, and upon submission of the CL, the code repository is updated with the files contained in the CL using transactional semantics (i.e. all or nothing). 

定义6: 变更列表(CL)是对代码存储库的原子性更改，它可以在提交时添加/更新/删除一个或多个文件。
一个CL可以包含任意数量的文件，在提交CL时，代码存储库使用事务语义(即全部或无)更新CL中包含的文件。

Definition 7. SF (CLi) denotes the set of files contained (added / updated / deleted) in the changelist CLi. 

定义7: SF (CLi)表示变更列表CLi中包含(添加/更新/删除)的文件集。

Definition 8. ST (CLi) denotes the set of targets touched by the changelist CLi: 
ST (CLi) = {T : f ∈ SF (CLi) ∧ T ∈ SFT (f)} 
CLs are submitted to a single code repository at Google, called HEAD [2], and there is a total order between them.

定义8: ST (CLi)表示变更列表CLi所触及的目标集:
ST (CLi) = {T: f∈SF (CLi)∧T∈SFT (f)}
CLs被提交到一个位于谷歌的代码存储库，称为HEAD[2]，它们之间有一个总顺序。

Section II discussed the problem of finding the culprit CL, the CL that introduced a regression into the code repository. As in Figure 2, if the CI system determines that at system version CLG all tests were passing, while at system version CLR some tests are failing, the CLs in the range (CLG, CLR] are suspect CLs.

第二部分讨论了寻找罪魁祸首CL的问题，该CL向代码存储库引入了回归。如图2所示，如果CI系统确定在系统版本CLG中所有测试都通过了，而在系统版本CLR中一些测试失败了，那么范围(CLG, CLR)中的CLs就是可疑的CLs。

In this section, we discuss our algorithm which ranks these CLs according to their suspiciousness using a combination of several heuristics, and notifies developers with the list of suspect CLs ordered by suspiciousness to help them identify the culprit CL.

在本节中，我们将讨论我们的算法，该算法根据这些CLs的可疑性对它们进行排序，并通过可疑性排序的可疑CLs列表通知开发人员，以帮助他们识别罪魁祸首CL。

### A. Heuristics

1) Changelog: The first heuristic eliminates CLs that cannot possibly be the cause of a test failure. Assume that Ttest is a build target that contains tests.

Definition 9. SL(Ttest, CLG, CLR) denotes the changelog of Ttest between the system versions CLG and CLR, i.e. any CL that touched a target Ttest depends on: SL(Ttest, CLG, CLR) = {CL : CL in (CLG, CLR] ∧ T ∈ ST (CL) ∧ T ∈ D(Ttest)}

1)变更日志:第一个启发式排除了不可能导致测试失败的CLs。假设Ttest是一个包含测试的构建目标。

定义9：SL(Ttest, CLG, CLR)表示系统版本CLG与CLR之间的Ttest的变化量，即任何触及到target Ttest的CL取决于:SL(Ttest, CLG, CLR) = {CL: CL in (CLG, CLR)∧T∈ST (CL)∧T∈D(Ttest)}

A CL CLi can only be a suspect CL if it is in the changelog SL(Ttest, CLG, CLR), i.e. if and only if it contains at least one file (hence a target) that Ttest depends on. As an example, assume the build tree looks like the one in Figure 6. A CL that modifies T1 would be in the changelog of Ttest, while one that only modifies T3 would not be.

一个clcli只能是一个可疑的CL，如果它是在变更日志SL(Ttest, CLG, CLR)，即当且仅当它包含至少一个Ttest所依赖的文件(因此是一个目标)。例如，假设构建树类似于图6。修改T1的CL将在Ttest的更改日志中，而只修改T3的CL将不在其中。

2) CL Size: The second heuristic uses the size of the CL: given two CLs, the one that contains more files is more suspicious. This heuristic is trivial, since it simply states that a target is more likely to cause a test failure if it touches more code that the failing test target depends on.

CL大小:第二个启发式使用CL的大小:给定两个CLs，包含更多文件的那个更可疑。这种启发式是不重要的，因为它简单地指出，如果一个目标接触了失败的测试目标所依赖的更多代码，那么这个目标更有可能导致测试失败。

3）Target Distance: The final heuristic uses proximity between targets in the build tree. In the build tree in Figure 6, this heuristic proposes that when Ttest fails, a CL modifying T1 is more suspicious than one modifying T2. This is based on two observations in the rapid development cycles at Google: (i) A target closer to the root in the build tree, such as T2, has more dependencies in the entire build tree, hence, if broken, has higher risk of disrupting the development for many developers compared to T1. Therefore, it is potentially tested better and/or in more depth before the CL is submitted. (ii) In the case that a CL submission that changes T2 does cause a breakage, it is likely to be noticed quicker than a target like T1. In the sample build tree, T1 has a single dependency, namely Ttest. So it depends on Ttest failing for regressions to be noticed due to any CLs changing T1. T2, on the other hand, has many dependencies (direct and transitive). Therefore, if it causes a breakage, any one of those dependencies will likely notice the breakage, and pave the way for a quick fix by notifying the developer that submitted any CLs modifying T2.

3)目标距离:最后的启发式使用构建树中目标之间的接近度。在图6的构建树中，这一启发提出，当Ttest失败时，CL修改T1比修改T2更可疑。这是基于两个观察谷歌的快速开发周期:(i)目标接近构建树的根,如T2,在整个构建树更多依赖关系,因此,如果坏了,有更高的风险破坏发展对于许多开发人员相比,T1。因此，在提交CL之前，可能会对它进行更好的测试和/或更深入的测试。(ii)如果更改T2的CL提交确实会导致破坏，那么它可能会比T1这样的目标更快被注意到。在样例构建树中，T1有一个独立的依赖项，即Ttest。因此，它取决于由于任何CLs改变T1而引起的回归的Ttest失败。另一方面，T2有许多依赖项(直接的和传递的)。因此，如果它导致了破坏，这些依赖中的任何一个都可能注意到破坏，并通过通知提交任何CLs修改T2的开发人员来为快速修复铺平道路。

### B. Suspiciousness

The heuristics discussed in the previous section are combined into a suspiciousness score for a CL, given a test target Ttest is failing.

上一节讨论的启发法被合并为CL的可疑性评分，假设测试目标Ttest失败。

Definition 10. dTW(Ti, Tj) denotes the weighted distance between two targets Ti and Tj (0 if there is no path from Ti to Tj ):

定义10: dTW(Ti, Tj)表示两个目标Ti与Tj之间的加权距离(如果没有Ti到Tj的路径，则为0):

$d^{TW}(T_i,T_j)=\frac{11}{\sqrt{40\times\pi}\times exp{\frac{-0.5\times(d^T(T_i,T_j)-1)^2}{20}}}$

Weighted distance dTW(Ti, Tj) is based on the distance between two targets dT (Ti, Tj). Keeping Ti constant, the weighted distance between Ti and another target is shown in Figure 7. When dT (Ti, Tj) = 1, the weighted distance dTW(Ti, Tj) ≈ 1. As the distance between targets increases, the weighted distance rapidly decreases. Using the example
in Figure 6:
dT (Ttest, T1) = 1 ⇔ dTW(Ttest, T1) ≈ 0.98
dT (Ttest, T2) = 2 ⇔ dTW(Ttest, T2) ≈ 0.95
dT (Ttest, T3) = 0 ⇔ dTW(Ttest, T3) = 0

加权距离dTW(Ti, Tj)是基于两个目标之间的距离dT (Ti, Tj)。在Ti不变的情况下，Ti与另一个目标之间的加权距离如图7所示。当dT (Ti, Tj) = 1时，加权距离dTW(Ti, Tj)≈1。随着目标间距离的增大，加权距离迅速减小。使用图6中的例子:

Definition 11. rCL(Ttest, CLi) denotes the suspiciousness score of CLi given test target Ttest fails: 
rCL(Ttest, CLi) = Ti∈ST (CLi) dTW(Ttest, Ti)

定义11。rCL(Ttest, CLi) 表示 疑心 得分 CLi 给定 的 测试 目标 Ttest fails: 
rCL(Ttest, CLi) = Ti∈ST (CLi) dTW(Ttest, Ti)

Higher suspiciousness scores imply higher likelihood for a CL to have caused the test failure. The suspiciousness function encapsulates the heuristics discussed in the previous section: 
- Changelog: In the build tree in Figure 6 T3 ∈ D(Ttest). If a CL only modifies T3, it will not be in the changelog for Ttest. 
- CL size: A CL that modifies T1 and T2 together will have a higher suspiciousness score than a CL that only modifies T1. 
- Distance: A CL that only modifies T1 will have a higher suspiciousness score than a CL that only modifies T2.

怀疑分数越高，CL导致考试失败的可能性就越大。可疑性函数封装了前一节讨论的启发:

- Changelog: In Figure 6 中的 构建 树 T3 ∈ D(Ttest).如果一个CL只修改T3，它不会在更改日志中进行Ttest。
- CL大小:同时修改T1和T2的CL比只修改T1的CL有更高的可疑性分数。
- 距离:只修改T1的CL比只修改T2的CL有更高的怀疑分数。

### C. Notification

 As in Figure 2, given all tests pass at system version CLG and some test target Ttest fails at system version CLR, culprit finder calculates the suspiciousness score for all CLs in the changelog SL(Ttest, CLG, CLR) and notifies developers via email with a report that ranks CLs from more suspicious to less suspicious. 
 
如图2所示,系统版本的所有测试通过轻型导弹巡洋舰和一些测试目标系统版本CLR tt失败,罪魁祸首仪计算疑心分数所有CLs changelog SL (tt,轻型导弹巡洋舰,CLR)并通知开发人员通过电子邮件报告,排名CLs来自可疑的不那么可疑。

 A sample report is shown in Figure 8. A developer that receives the email can look at the CLs in the suspect list in the given order to identify the culprit CL (e.g. they typically check if the failing target Ttest has any relation to the description/files in the CL, or they run the tests at that CL). Once the developer identifies the culprit CL from the suspects list, she can then debug to find the root cause of the regression in the codebase.

示例报告如图8所示。开发人员接收电子邮件可以看的CLs怀疑按照给定的顺序列表找出罪魁祸首CL(如他们一般检查如果没有目标tt有任何关系描述在CL /文件,或者他们在CL)运行测试。一旦开发人员从疑点列表中确定了罪魁祸首CL，她就可以进行调试，找到代码库中回归的根本原因。

## VIII. RELATED WORK

There are several research areas that are relevant, but not directly comparable, to the work in this paper. 
Change impact analysis aims to identify program edits on the codebase and which of those edits might have induced test failures, to help developers during debugging [12], [13]. These techniques focus on finer-grained edits than what is described as a changelist in this paper.

有几个研究领域是相关的，但不是直接可比的，在这篇论文的工作。
变更影响分析的目的是识别代码基上的程序编辑，以及哪些编辑可能导致了测试失败，以帮助开发人员在调试[12]、[13]期间进行调试。这些技术关注于比本文中描述的变更列表更细粒度的编辑。

Fault localization aids developers to find the root cause of a failing test in source/test code [14]. Existing techniques, such as Tarantula [15], focus on locating faults in the code at a specific version of the system, whereas the work in this paper focuses on identifying the change that introduced the fault in the first place before it can be located in code.

故障定位帮助开发人员在源代码/测试代码[14]中找到失败测试的根本原因。现有的技术(如Tarantula[15])主要关注于在系统的特定版本中定位代码中的错误，而本文的工作则主要关注于在将错误定位到代码中之前首先确定引入错误的更改。

There are also existing techniques that are directly comparable to the work in this paper.

There are machine learning (ML) based models that use information in changes such as author, modified files and size of change. Several ’just-in-time’ techniques can predict a change to be risky to cause regressions before it is submitted to the repository [16], [17]. Other ML based techniques identify regression introducing changes postsubmit [18], [19]. However, unlike the work in this paper, these ML based techniques require training data, and the ML model needs to be maintained/updated over time.

还有一些现有的技术可以直接与本文的工作相媲美。

有基于机器学习(ML)的模型，该模型在更改中使用信息，如作者、修改的文件和更改的大小。有几种“准时”技术可以在变更提交到存储库[16]、[17]之前预测其带来的风险。其他基于ML的技术识别回归，引入变更后提交[18]，[19]。然而，与本文的工作不同，这些基于ML的技术需要训练数据，并且ML模型需要随着时间的推移进行维护/更新。

A recent technique, called Locus, uses information retrieval to match a bug report description to a change using change hunks, i.e. code that is modified by a change [5]. Unlike the work in this paper, Locus needs a textual bug report that describes the regression, which requires a developer to manually acknowledge and describe the regression in a bug report, and may not be available immediately.

最近的一种技术称为轨迹，它使用信息检索来匹配bug报告描述和使用变更块(即被变更[5]修改的代码)的变更。与本文的工作不同，轨迹需要一个描述回归的文本性错误报告，这需要开发人员在错误报告中手动确认并描述回归，并且可能不会立即可用。

Finally, the ’git bisect’ tool [8] helps perform a manual binary search in the CL window to help a developer find the culprit CL. This is a manual task, and requires re-running tests.

最后，“git bisect”工具[8]帮助在CL窗口中执行手工的二分查找，以帮助开发人员找到罪魁祸首CL。这是一个手动任务，需要重新运行测试。

## IX. CONCLUSION

Identifying changes that introduce regressions to a codebase is critical to keep the momentum on software development, especially in very large scale software repositories with rapid development cycles, such as at Google. Therefore, there is a high demand for automated techniques that can help developers locate the change that introduced a regression while minimizing manual human intervention. 
In this paper, we introduce a novel technique to identify changes that induce failures for large/enormous tests, called CULPRIT FINDER. Our technique uses heuristics to automatically filter and rank CLs that might have introduced the regression to help developers identify the root cause. 
We evaluate our technique on case studies we ran on 140 projects in Google. Results of our case studies show that each heuristic in our algorithm provides benefit to developers on identifying the change that induces test failures. 
Our case studies also suggest some bias in developer behavior, where they report feedback when CULPRIT FINDER is not as successful as they expect.

识别向代码基引入回归的更改对于保持软件开发的势头至关重要，特别是在具有快速开发周期的大型软件存储库中，例如在谷歌。因此，自动化技术的需求很高，它可以帮助开发人员定位引入回归的变更，同时最小化人工干预。
在本文中，我们介绍了一种新的技术来识别导致大型/大型测试失败的变化，这种技术称为罪魁祸首查找器。我们的技术使用启发式来自动过滤并对可能引入回归的CLs进行排序，以帮助开发人员识别根本原因。
我们通过在谷歌中运行的140个项目的案例研究来评估我们的技术。我们的案例研究结果表明，我们算法中的每一个启发式算法都为开发人员识别导致测试失败的变化提供了帮助。
我们的案例研究还表明，在开发人员的行为中存在一定的偏差，当罪犯发现者没有他们预期的那么成功时，他们会报告反馈。

### A. Future Work

Future work consists of augmenting our work with more heuristics, such as logs analysis, where failure logs and stack traces can be compared to the code changes in a CL to check for overlaps. Existing ML based models can also be combined with our technique to improve results. 
Another important avenue is testing CULPRIT FINDER on small/medium tests with a more systematic evaluation. Such tests can be re-executed to pinpoint the culprit change and CULPRIT FINDER can be evaluated automatically. There is ongoing work on this at Google. 
Finally, another avenue to explore is helping large source code repositories, such as github [20], adopt our algorithm (similar to git bisect [8]) and reproduce our results.

未来的工作包括使用更多的启发式方法来扩展我们的工作，例如日志分析，其中可以将故障日志和堆栈跟踪与CL中的代码更改进行比较，以检查重叠。现有的基于ML的模型也可以与我们的技术相结合以改进结果。
另一个重要的方法是在中小测试中测试罪犯发现者，并进行更系统的评估。这样的测试可以重新执行，以查明罪魁祸首的更改，并且可以自动评估罪魁祸首查找程序。谷歌正在进行这方面的工作。
最后，另一种方法是帮助大型源代码存储库(如github[20])采用我们的算法(类似于git平分[8])并重现结果。
