# The TLA+ Toolbox

We discuss the workflows supported by the TLA+ Toolbox to write and verify specifications. We focus on features that are useful in industry because its users are primarily engineers. Two features are novel in the scope of formal IDEs: CloudTLC connects the Toolbox with cloud computing to scale up model checking. A Profiler helps to debug inefficient expressions and to pinpoint the source of state space explosion. For those who wish to contribute to the Toolbox or learn from its flaws, we present its technical architecture.

我们将讨论TLA+工具箱支持的编写和验证规范的工作流。我们关注在工业中有用的特性，因为它的用户主要是工程师。在正式ide的范围内，有两个新特性:CloudTLC将工具箱与云计算连接起来以扩展模型检查。分析器可以帮助调试低效的表达式，并查明状态空间爆炸的根源。对于那些希望为工具箱做出贡献或希望从它的缺陷中学习的人，我们将介绍它的技术体系结构。

## 1 Introduction

The TLA+ Toolbox combines a set of (command-line) tools into an integrated development environment (IDE). Its primary goal it to make TLA+ more widely used. To encourage the use of the Toolbox, the Toolbox is made freely available. It is developed by an open-source community. Section 2 describes the main Toolbox workflows by showing how the Simple spec is model-checked and mechanically verified inside the Toolbox.2 Section 3 shifts the discussion to the architecture and test infrastructure of the Toolbox. We conclude with outlining future engineering and research-related work.

TLA + 工具箱将一组 (命令行) 工具组合到集成开发环境 (IDE) 中。它的主要目标是使 TLA + 得到更广泛的应用。为了鼓励使用工具箱，工具箱是免费提供的。它是由开源社区开发的。第 2 节通过展示如何在工具箱内对简单规范进行模型检查和机械验证，描述了主要工具箱工作流。第 3 节将讨论转移到工具箱的架构和测试基础设施上。我们总结了未来的工程和研究相关的工作。

### 1.1 Background

TLA+ is a high-level, math-based, formal specification language used at companies such as Amazon and Microsoft to design, specify, and document systems [16]. A system is specified by formulas expressed in the Temporal Logic of Actions [6, 8], a variant of Pnueli’s original linear-time temporal logic [18]. Data structures are represented with Zermelo-Fränkel set theory with choice. TLA+ is an untyped language and thus simpler and more expressive than programming languages [13]. Generating code is not in the scope of TLA+. It is implementation language agnostic and meant to find bugs above the code level. Further material about TLA+ is available [8, 9, 10, 12], but is not needed for what follows.

- [16] Chris Newcombe, Tim Rath, Fan Zhang, Bogdan Munteanu, Marc Brooker & Michael Deardeuff (2015): How Amazon Web Services Uses Formal Methods. Communications of the ACM 58(4), pp. 66–73, doi:10.1145/2699417. Available at http://dl.acm.org/citation.cfm?doid=2749359.2699417.
- [6] Leslie Lamport (1994): The Temporal Logic of Actions. ACM Transactions on Programming Languages and Systems 16(3), pp. 872–923, doi:10.1145/177492.177726. Available at http://portal.acm.org/citation.cfm?doid=177492.177726.
- [8] Leslie Lamport (2003): Specifying Systems: The TLA+ Language and Tools for Hardware and Software Engineers. Addison-Wesley, Boston.
- [18] Amir Pnueli (1977): The Temporal Logic of Programs. IEEE, pp. 46–57, doi:10.1109/SFCS.1977.32. Available at http://ieeexplore.ieee.org/lpdocs/epic03/wrapper.htm?arnumber=4567924.
- [13] Leslie Lamport & Lawrence C. Paulson (1999): Should Your Specification Language Be Typed. ACM Transactions on Programming Languages and Systems 21(3), pp. 502–526, doi:10.1145/319301.319317. Available at http://portal.acm.org/citation.cfm?doid=319301.319317.

TLA+是一种高级的、基于数学的正式规范语言，用于Amazon和Microsoft等公司设计、指定和文档系统[16]。系统由动作的时间逻辑[6,8]中表示的公式指定，这是Pnueli最初的线性时间时间逻辑[18]的一个变体。数据结构是用Zermelo-Frankel集合理论与选择来表示的。TLA+是一种无类型语言，因此比编程语言[13]更简单、更有表现力。生成代码不在TLA+的范围内。它与实现语言无关，旨在发现代码级别之上的bug。关于TLA+的更多资料可以参考[8,9,10,12]，但是接下来的内容不需要。

## 2 Toolbox Features

The main parts of the Toolbox are the Spec Explorer, spec editors, model editors, and a Trace Explorer. The goal is to keep the interface simple and intuitive. Functionality not needed by beginning users is initially hidden.

工具箱的主要部分是Spec Explorer、Spec编辑器、模型编辑器和跟踪资源管理器。目标是保持界面简单直观。初始用户不需要的功能最初是隐藏的。

### 2.1 Spec Explorer

The Toolbox can host multiple TLA+ specifications. A spec has a root module, a set of additional modules and — optionally — a collection of models. All specifications are accessible from the Spec Explorer (figure 1), but users can work on only one spec at a time. Contrary to most Eclipse-based IDEs, specs can be exported from and imported into the Toolbox from the file system.

工具箱可以托管多个TLA+规范。spec有一个根模块、一组附加模块和一组模型(可选)。所有规范都可以从Spec Explorer中访问(图1)，但是用户一次只能处理一个规范。与大多数基于eclipse的ide相反，spec可以从文件系统导出并导入到工具箱中。

### 2.2 Spec Editor

A spec editor provides the usual commands that are helpful to create and edit TLA+ modules. The editor can also generate and show a pretty-printed version of the module. It shows annotations when SANY finds parsing errors.

spec编辑器提供了有助于创建和编辑TLA+模块的常用命令。编辑器还可以生成和显示打印良好的模块版本。当SANY发现解析错误时，它会显示注释。

#### 2.2.1 PlusCal

The PlusCal algorithm language is a formally defined and verifiable pseudocode that is translated into TLA+ [9]. PlusCal resembles an imperative programming language and is especially well suited to express sequential and shared-memory multithreaded algorithms. Neither the TLA+ concept of refinement nor fine-grained fairness constraints can be expressed in PlusCal.

PlusCal算法语言是一种正式定义并可验证的伪代码，它被翻译成TLA+[9]。PlusCal类似于命令式编程语言，特别适合于表示顺序和共享内存多线程算法。无论是TLA+细化的概念还是细粒度的公平约束都不能用PlusCal表示。

A PlusCal algorithm and its TLA+ translation go in the same TLA+ module (figure 3). The editor has a command to automatically translate a PlusCal algorithm into TLA+. Users can quickly navigate to and from its TLA+ translation, which can help them understand the PlusCal code. Note that PlusCal is designed to make the TLA+ translation easy to read. The exposure to TLA+ is intended to encourage users to learn TLA+. Also, as mentioned above, refinement and some fairness constraints can be expressed only in the TLA+ translation.

PlusCal算法和它的TLA+转换位于同一个TLA+模块中(图3)。用户可以快速导航到它的TLA+翻译，这可以帮助他们理解PlusCal代码。请注意，PlusCal的设计使TLA+翻译易于阅读。TLA+的曝光是为了鼓励用户学习TLA+。此外，如上所述，细化和一些公平约束只能在TLA+翻译中表达。

Combining a PlusCal algorithm and its TLA+ translation into a single spec poses the risk that they get out of sync unnoticed. To alleviate this problem, a TLC feature of the next Toolbox release will issue a warning if a translation has become stale.

将PlusCal算法和它的TLA+转换组合到一个规范中，存在不同步的风险。为了缓解这个问题，下一个工具箱版本的TLC特性将在翻译变得过时时发出警告。

As seen in figure 2, a spec editor provides templates for PlusCal expressions. The goal of templates is not to speed up typing—specs are usually short—but to guide novice users by putting the syntactical and semantic documentation of PlusCal expressions at their fingertips. The user experience also resembles that of familiar programming IDEs, but the lack of types and type inference limits word completion to the syntax level.

如图2所示，spec编辑器为PlusCal表达式提供了模板。模板的目标不是加快输入速度—规范通常很短—而是通过将PlusCal表达式的语法和语义文档放在用户的指尖来指导新手。用户体验也类似于熟悉的编程ide，但是缺少类型和类型推断将单词完成限制在语法级别。

#### 2.2.2 Proofs

TLA+ proofs are not written interactively but are constructed hierarchically [7]. Hierarchical proofs enable a non-linear proof style: Individual steps of a proof can be checked independently of other steps. Also, steps can be decomposed into simpler steps should TLAPS fail to prove the higher-level steps.

- [7] Leslie Lamport (1995): How to Write a Proof. The American mathematical monthly 102(7), pp. 600–608, doi:10.2307/2974556. Available at http://www.jstor.org/stable/2974556.

TLA+证明不是交互式编写的，而是分层构造的[7]。分层证明支持非线性证明样式:证明的单个步骤可以独立于其他步骤进行检查。此外，如果TLAPS不能证明更高级别的步骤，则可以将步骤分解为更简单的步骤。

In figure 3 we see a hierarchical TLA+ proof where lower-level, proven steps have been collapsed. The green color indicates steps that TLAPS proved successfully while red indicates steps that it failed to prove. A failed step is additionally displayed in the logging part, showing exactly what TLAPS was trying to prove (see the right-hand side of figure 3).

在图3中，我们看到了一个分层的TLA+证明，其中较低级别的、经过验证的步骤已被分解。绿色表示TLAPS成功证明的步骤，红色表示未能证明的步骤。日志记录部分还显示了一个失败的步骤，准确地显示了TLAPS试图证明的内容(参见图3的右侧)。

A wizard exists to decompose a step to be proved into a sequence of simpler steps, based on the logical structure of the step. For example, a step asserting the conjunction of properties can be proved by steps that each assert one of those properties. The wizard can add these steps as a lower level proof, and they can be verified separately with TLAPS.

存在一个向导，用于根据步骤的逻辑结构将要证明的步骤分解为一系列更简单的步骤。例如，断言属性关联的步骤可以由每个断言其中一个属性的步骤来证明。向导可以将这些步骤添加为较低级别的证明，并且可以用TLAPS分别对它们进行验证。

TLAPS maintains a fingerprint of every proof obligation so that it does not need to re-prove previously verified obligations. A user tells TLAPS to forget fingerprints from the Toolbox.

TLAPS保留每个证明义务的指纹，因此不需要重新证明以前已验证的义务。用户告诉TLAPS忘记工具箱中的指纹。

Unlike TLC, which is part of the Toolbox, users have to manually install TLAPS. The Toolbox will automatically find the TLAPS installation.

与工具箱中的TLC不同，用户必须手动安装TLAPS。工具箱将自动找到TLAPS安装。

### 2.3 Model

TLAPS can verify specifications with an infinite set of possible states. The explicit-state model checker, however, would never finish if a user tries to verify a spec with an infinite state space. A primary goal of TLC is that the spec need not be modified to be checked. Instead, a TLC model restricts the spec’s state space to a finite set of possible states by assigning concrete values to parameters and by stating additional bounds. To gain higher confidence, users typically check a spec on many separate models.

TLAPS可以验证具有无限可能状态集的规范。但是，如果用户试图用无限的状态空间来验证规范，那么明确性状态模型检查器就永远不会完成。TLC的主要目标是不需要修改规范来进行检查。相反，TLC模型将规范的状态空间限制为有限的可能状态集，方法是为参数指定具体的值并声明附加的界限。为了获得更高的信心，用户通常会在许多不同的模型上检查一个规范。

The Toolbox represents a TLC model by a model part, which also contains settings for how TLC should be executed. Seasoned users will tune these settings and define additional checks that TLC should perform.

工具箱通过模型部分表示TLC模型，其中还包含应该如何执行TLC的设置。经验丰富的用户将调整这些设置并定义TLC应该执行的其他检查。

Contrary to the proof system (section 2.2), the Toolbox provides a structural representation of a model by grouping related inputs into tabs and sections. To guide users, inputs are validated when entered. Warnings and suggested fixes are placed next to the inputs. The model is kept in sync with the spec so that relevant changes, for example the addition or removal of a constant, are automatically reflected in the model.

与证明系统(第2.2节)相反，工具箱通过将相关输入分组到选项卡和节中，提供了模型的结构化表示。为了指导用户，输入时将验证输入。警告和修复建议放在输入旁边。模型与规范保持同步，以便相关更改(例如添加或删除常量)自动反映到模型中。

A model is associated with its respective spec in the Spec Explorer. The Spec Explorer also maintains a history of model checker runs, each history entry storing the model checking result and snapshots of the spec’s modules (figure 1 and section 2.3.2). A user can compare modules, which for example will remind her of the differences between long model checking runs.

在spec Explorer中，模型与其相应的规范相关联。Spec Explorer还维护了运行模型检查器的历史记录，每个历史记录条目存储了模型检查结果和Spec模块的快照(图1和2.3.2节)。用户可以比较模块，例如，这将提醒她长时间模型检查运行之间的差异。

A model is stored as an XML file in the file system, so it can be checked into a source code management system. This way, the user can reproduce model checking runs.

模型以XML文件的形式存储在文件系统中，因此可以将其签入到源代码管理系统中。通过这种方式，用户可以重现模型检查运行。

#### 2.3.1 CloudTLC

With CloudTLC users can shift from a primarily sequential model checking workflow to one where any number of models can be checked concurrently. Additionally, CloudTLC scales up explicit-state model checking to more powerful hardware. This way, design variants or competing optimizations can be quickly explored in parallel. This can be used either to reduce overall specification time or to enable a broader exploration of the design space.

使用CloudTLC，用户可以从主要的顺序模型检查工作流转换到任意数量的模型可以同时检查的工作流。此外，CloudTLC还将显性状态模型检查扩展到更强大的硬件。通过这种方式，可以并行地快速探索设计变体或竞争优化。这可以用于减少总体规范时间，也可以用于更广泛地探索设计空间。

CloudTLC provisions a set of cloud instances and runs TLC on them. If a user opted to run model checking on multiple instances, CloudTLC starts TLC in distributed mode [4]. Upon completion of model checking, CloudTLC instances terminate automatically after a grace period. If the user keeps the Toolbox open, it will show model checking progress and final results. Alternatively, the Toolbox may be disconnected at any time; CloudTLC sends the results to a user-provided email address in a format that can be imported into the Toolbox. In other words, CloudTLC is completely transparent: Toolbox features such as the Profiler or Trace Explorer (sections 2.3.3 and 2.3.4 below) work as if model checking runs locally.

CloudTLC提供一组云实例，并在这些实例上运行TLC。如果用户选择在多个实例上运行模型检查，CloudTLC将以分布式模式[4]启动TLC。在完成模型检查后，CloudTLC实例将在一段宽限期后自动终止。如果用户打开工具箱，它将显示模型检查进度和最终结果。或者，工具箱可以在任何时候断开;CloudTLC将结果以可导入工具箱的格式发送到用户提供的电子邮件地址。换句话说，CloudTLC是完全透明的:工具箱特性，如分析器或跟踪资源管理器(下面的2.3.3和2.3.4节)，工作起来就像模型检查在本地运行一样。

CloudTLC’s startup time is dominated by the time it takes an IaaS provider to spin-up instances. To reduce startup time, subsequent model checker runs re-use previously provisioned instances unless they have already terminated. CloudTLC can also be started by running the Toolbox in command-line mode. This is useful for automation such as running the TLC performance test suite (section 3.2).

CloudTLC的启动时间由IaaS提供程序启动实例的时间决定。为了减少启动时间，后续的模型检查器运行重用以前供应的实例，除非它们已经终止。CloudTLC也可以通过以命令行模式运行工具箱来启动。这对于自动化非常有用，例如运行TLC性能测试套件(第3.2节)。

#### 2.3.2 Results

The status and progress of TLC checking a model is shown on the results part. The results include the start and end wall-clock time, the probability of an incomplete state space exploration due to distinct states falsely being considered equivalent, as well as global state-graph and action-level statistics. Stategraph statistics are:  
- Diameter: The depth that TLC has reached in its exploration of the state-graph  
- Distinct States: The cardinality of the set of reachable vertices of the state-graph 
- (Total) States: The number of states examined by TLC as part of model checking

结果部分给出了模型TLC检查的现状和进展。结果包括开始和结束壁钟时间，由于不同的状态被错误地认为是等价的而导致的不完整状态空间探索的概率，以及全局状态图和操作级统计。Stategraph统计数据:
- 直径:TLC在状态图的探索中所达到的深度
- 不同的状态:状态图的可达顶点集合的基数
- (总数)状态:TLC作为模型检查的一部分检查的状态数

The ratio of distinct to examined states approximates the degree of the state-graph. Action statistics — further discussed in section 2.3.3 — are similar to global state-graph statistics, except that they are reported at the level of individual TLA+ actions and the diameter is undefined.

不同状态与被检查状态的比值近似于状态图的程度。操作统计—将在2.3.3节中进一步讨论—类似于全局状态图统计，除了它们是在单个TLA+操作级别上报告的，并且直径未定义之外。

Plots of some statistics can be shown that indicate the remaining model checking time.4 Furthermore, a state-graph may be visualized graphically. However, this is possible only for small graphs due to the inherent complexity of laying out directed graphs. The description of a graph can be exported to specialized tools such as Cytoscape.

可以显示一些统计数据图，以指示剩余的模型检查时间。此外，一个状态图可以被图形化地可视化。然而，由于布局有向图的固有复杂性，这仅适用于小图。图形的描述可以导出到诸如Cytoscape之类的专门工具。

#### 2.3.3 Profiler

Engineers primarily rely on model checking, not theorem proving, and they want to verify large models. This makes them not only users of CloudTLC (section 2.3.1), but also of the Toolbox’s Profiler. Profiling a TLA+ model and its spec produces four different types of data:
- Invocation count: The number of times a TLA+ expression is evaluated
- Cost: The number of operations performed to enumerate the elements of a set or sets, should enumeration be required to evaluate an expression
- States/Action: The number of states a particular TLA+ action caused to be examined
- Distinct States/Action: The number of distinct states an action produced

工程师们主要依靠模型检验，而不是定理证明，他们想要验证大型模型。这使得他们不仅是CloudTLC的用户(2.3.1节)，而且是工具箱的分析器的用户。剖析TLA+模型及其规范可以产生四种不同类型的数据:
- 调用计数:计算TLA+表达式的次数
- Cost:枚举一个或多个集合的元素所执行的操作数，如果需要枚举来计算表达式的话
- 状态/动作:特定TLA+动作被检查的状态数
- 不同状态/行动:一个行动产生的不同状态的数目

We call the first two data types evaluation statistics and collect them at the global as well as at the callchain level. To explain their differences, we assume an identical, constant cost for all expressions. This way, a user can identify the biggest contributor to overall model checking time simply by looking at the number of invocations. However, some expressions require the model checker to explicitly enumerate data structures for which costs are the quantitative measure: For example, let S be the set of natural numbers from N to M such that N << M in the TLA+ expression $\forall s \in SUBSET s,: s\subset S$ . The cost of evaluating the expression is determined by the number of operations required by TLC to enumerate all the subsets in SUBSETS (the powerset of S). This expression will be a major contributor to model checking time even if its number of invocations is low.

我们调用前两种数据类型评估统计信息，并在全局和调用链级别收集它们。为了解释它们之间的差异，我们假设所有表达式都有一个相同的、恒定的代价。这样，用户就可以通过查看调用的数量来确定整个模型检查时间的最大贡献者。但是，有些表达式需要模型检查器显式地枚举数据结构，其中成本是量化度量:例如，让S是N到M的自然数集合，这样在TLA+表达式$\ < M中所有S \在子集S中，:S \子集S$。计算表达式的成本取决于TLC在子集(S的幂集)中枚举所有子集所需的操作数。即使该表达式的调用次数很低，它也会成为模型检查时间的主要贡献者。

With action statistics, a user can pinpoint the source of state space explosion at the action level. To explain their application, consider figure 4a, which is a deliberately inefficient TLA+ translation of the Simple algorithm shown in figure 3. The enablement predicate on line 50 of action b, consisting of the two expressions x [s] = 1 and y [s] = 0, has been globally invoked the most. Related, action b has been found to produce 87 states of which only 29 are distinct (see hover help in figure 4b). In contrast, action a is much more efficient in terms of its total states to distinct states ratio. This draws the user’s attention to the enablement predicate of action b, which is weaker compared to the predicate pc [s] = “a” of action a. The enablement predicate of b is true of states for any value of pc. While this does not violate the specification’s safety properties, changing the enablement predicate of b to pc [s] = “b” puts the ratio of total to distinct states in the region of the same ratio of action a. It also corresponds to the regular translation of the Simple algorithm. Additionally, the enablement of action d on line 54 is evaluated 153 times. Yet, the numbers of total and distinct states are zero as indicated by the red boxes. This suggests that there is either an error in the spec, or else that the user can delete action d.

通过操作统计数据，用户可以在操作级别确定状态空间爆炸的来源。要解释它们的应用，请考虑图4a，图4a故意对图3所示的简单算法进行了低效的TLA+转换。操作b的第50行上的启用谓词，由两个表达式x [s] = 1和y [s] = 0组成，在全局调用得最多。与此相关的是，已经发现动作b产生了87个状态，其中只有29个是不同的(参见图4b中的hover帮助)。相比之下，行动a的效率要高得多，因为它的总状态与不同状态之比。这引起了用户对行动b的启用谓词的注意，与行动a的谓词pc [s] = " a "相比，行动b的启用谓词要弱一些。虽然这并没有违反规范的安全属性，但是将b的启用谓词改为pc [s] = " b "会使相同动作a的区域内的总状态与不同状态的比例相同。它也对应于简单算法的常规转换。此外，对第54行d操作的实现进行了153次评估。然而，正如红色方框所示，总的和不同的状态数为零。这表明要么规范中有错误，要么用户可以删除操作d。

As shown in figure 4a and 4b, the Toolbox highlights expressions with colors chosen from a onedimensional heatmap based on an expressions’ corresponding evaluation statistic. Expressions with the highest number of invocations will be highlighted with a red color whereas those with no invocations will be dark blue. The heatmap appears as a legend at the bottom of the editor and reveals the corresponding editor location when clicked. Users can switch what the highlighting is based on between invocation counts, costs, and the number of examined or distinct states. Additionally, users focus on a single callchain when they select an expression in the editor.

如图4a和4b所示，工具箱突出显示从基于表达式的相应计算统计值的一维热图中选择的颜色的表达式。调用次数最多的表达式将用红色突出显示，而没有调用的表达式将用深蓝色突出显示。热图显示为编辑器底部的一个图例，单击时显示相应的编辑器位置。用户可以根据调用计数、成本和已检查或不同状态的数量来切换突出显示的内容。此外，当用户在编辑器中选择表达式时，他们只关注一个调用链。

In summary, the Profiler makes different kinds of inefficiencies explicit. For those inefficiencies related to the evaluation of expressions, the model provides a way for users to override TLA+ operators with more efficient variants. An even more extreme optimization is to override TLA+ operators with equivalent Java functions. Action statistics expose weaknesses in enablement predicates or spec errors.

总之，剖析器明确了各种各样的低效率。对于那些与表达式计算相关的低效性，该模型为用户提供了一种使用更高效的变体覆盖TLA+操作符的方法。一个更极端的优化是用等价的Java函数覆盖TLA+操作符。操作统计信息暴露了启用谓词或规范错误中的弱点。

The Profiler requires neither the specification nor the model to be modified to collect statistics. However, profiling has a non-negligible performance overhead and should be disabled when checking large models. Profiling is unavailable when TLC runs in distributed mode.

分析器既不需要修改规范，也不需要修改模型来收集统计信息。然而，性能分析有不可忽视的性能开销，在检查大型模型时应该禁用它。当TLC以分布式模式运行时，分析不可用。

#### 2.3.4 Trace Explorer

Should model checking find a violation of any of the stated safety or liveness properties, the corresponding error trace will be displayed in the Trace Explorer as shown in figure 5. An error trace is a sequence of states, and a state is an assignment of values to variables.

如果模型检查发现违反了任何指定的安全性或活性属性，相应的错误跟踪将显示在trace Explorer中，如图5所示。错误跟踪是状态序列，状态是变量值的赋值。

The Trace Explorer colors the variables of a state that have changed from the predecessor state. Navigating from a state in the trace to the location in the editor of the corresponding TLA+ action (and if applicable, the corresponding PlusCal expression) is also supported. The Trace Explorer can handle traces with thousands of states.

跟踪资源管理器为状态的变量上色，这些变量已经从前一个状态更改。还支持从跟踪中的状态导航到相应TLA+操作的编辑器中的位置(如果适用，还支持相应的PlusCal表达式)。Trace Explorer可以处理带有数千个状态的跟踪。

To study traces, the Trace Explorer supports the evaluation of trace expressions. A trace expression can be a function of a state or a pair of successive states [6, p. 4-5]. A trace expression may optionally be named to facilitate expression composition. A trace expression may be built from all operators in the scope of the root module. In addition, two built-in operators are available:

要研究跟踪，跟踪资源管理器支持对跟踪表达式进行评估。跟踪表达式可以是一个状态的函数，也可以是一对连续的状态[6,p. 4-5]。跟踪表达式可以随意命名，以促进表达式的组合。可以从根模块范围内的所有操作符构建跟踪表达式。此外，还有两种内置运算符:

_TEPosition Equal to the position of the corresponding state in the error trace

_TETrace A TLA+ sequence of states such that TETrace[ TEPosition] equals the state at position TEPosition in the error trace

_TEPosition等于错误跟踪中相应状态的位置

_TETrace一个TLA+状态序列，使TETrace[TEPosition]等于错误跟踪中位置TEPosition处的状态

Note that these operators increase the expressiveness of trace expressions such that they can be formed from the collection of variables of all states of the trace. For example, trace expressions can compare the values of variables of two or more arbitrary states (see trace variables Clock, Process, X, and Y in figure 5 and listing 1). Users can write such expressions to format traces that can be directly copied to third-party tools [5]. Internally, the Toolbox evaluates trace expressions by generating a special TLA+ module that is checked by using the functionality discussed in section 3.1.

注意，这些操作符增强了跟踪表达式的表达能力，这样它们就可以从跟踪的所有状态的变量集合中形成。例如，跟踪表达式可以比较两个或多个任意状态的变量的值(参见图5和清单1中的跟踪变量Clock、Process、X和Y)。在内部，工具箱通过生成一个特殊的TLA+模块来评估跟踪表达式，该模块通过使用3.1节中讨论的功能进行检查。

## 3 Toolbox Architecture

We are trying to make the Toolbox an industrial strength integrated development environment with support for all aspects of writing TLA+ specs. If possible, features are implemented at the back-end layer to satisfy command-line aficionados, to support re-use, and to integrate with automation. Features not core to TLA+, such as visualizations of the state-graph and error traces, are left to specialized third-party tools to which relevant information is exported in compatible data formats.

我们正在努力使工具箱成为一个工业实力的集成开发环境，支持编写TLA+规范的所有方面。如果可能，特性在后端层实现，以满足命令行爱好者的需求，支持重用，并与自动化集成。不是TLA+核心的特性，例如状态图和错误跟踪的可视化，留给了专门的第三方工具，相关信息以兼容的数据格式导出到这些工具中。

To attract a wide range of users, the Toolbox is compatible with the three most common operating systems: macOS, Windows, and Linux. The Toolbox requires no external dependencies except to generate PDFs. Primarily, this is because the Toolbox is written in Java and built on the Eclipse Rich Client Platform (RCP) [15]. RCP provides a number of IDE features such as a help system, a desktop notification system, and an update manager. RCP also defines usability guidelines and best practices that, while not always applicable to a formal integrated development environment, help enforce a consistent user experience. Others can add functionality by contributing extensions and OSGi services.

为了吸引广泛的用户，工具箱兼容三种最常见的操作系统:macOS、Windows和Linux。除了生成pdf外，工具箱不需要任何外部依赖项。这主要是因为工具箱是用Java编写的，并构建在Eclipse富客户端平台(RCP)[15]上。RCP提供了许多IDE特性，如帮助系统、桌面通知系统和更新管理器。RCP还定义了可用性指导原则和最佳实践，虽然它们并不总是适用于正式的集成开发环境，但有助于增强一致的用户体验。其他人可以通过提供扩展和OSGi服务来添加功能。

Building the Toolbox on top of RCP is not without drawbacks. RCP follows a quarterly release schedule. Upgrading the Toolbox to a new RCP release frequently leads to subtle bugs that are not detected by our tests. The rate at which macOS, Windows, and Linux innovate forces us to upgrade. This causes significant manual work.

在RCP上构建工具箱并非没有缺点。RCP遵循季度发布时间表。将工具箱升级到新的RCP版本经常会导致我们的测试无法检测到的细微错误。macOS、Windows和Linux的创新速度迫使我们升级。这会导致大量的手工工作。

The Spec Explorer (section 2.1) is the primary interface to work with specs. However, users can also create, move, or modify files at the OS level. This feature causes many incompatibilities and bugs, since RCP wants files to be modified through its file system abstraction. As a result, the Spec Explorer and editors get out of sync with the actual files. In hindsight, file operations should have been restricted to the Toolbox or proper support should have been added to RPC; the workarounds at the Toolbox layer continue to cause bugs.

Spec Explorer(第2.1节)是使用Spec的主要接口。但是，用户也可以在操作系统级别创建、移动或修改文件。由于RCP希望通过文件系统抽象来修改文件，因此该特性会导致许多不兼容和bug。因此，Spec Explorer和编辑器与实际文件不同步。事后看来，文件操作应该限制在工具箱中，或者应该向RPC添加适当的支持;工具箱层的工作区继续导致错误。

Throughout the development of the Toolbox, it has become clear that RCP has to be considered a whitebox component. In other words, projects built on top of RCP have to accept the burden of coownership and contribute to its development.

在开发工具箱的过程中，有一点变得很清楚，RCP必须被视为一个白盒组件。换句话说，建立在RCP之上的项目必须接受共同所有权的负担，并为其开发做出贡献。

### 3.1 Back-end integration

For latency reasons, the short-lived lightweight TLA+ tools such as the SANY parser, the PlusCal translator, and the pretty-printer are executed as part of the Toolbox process. Extending this functionality is possible only via extensions and OSGi services, as discussed in section 3. However, the heavyweight model checker and proof system spawn as separate processes. There are three reasons to spawn separate, per-invocation processes:  
- A back-end cannot be executed in-process because it is implemented in a language that cannot execute on the Java VM. This is true for TLAPS, which is written in OCaml.  
- Process separation acts as a circuit breaker; a crash of either a back-end or the Toolbox does not interfere with the other process. For example, we do not want a Toolbox crash to also crash a long-running model checker run. This safeguard is especially important while a back-end matures.  
- The Java VM’s runtime parameters are fixed after Toolbox startup. Running a back-end as part of the Toolbox causes the back-end to inherit the Toolbox’s parameters. The resource requirements of back-ends are usually different from those of the Toolbox—for example, model checkers have very high resource requirements (section 2.3.1).

由于延迟原因，短期存在的轻量级TLA+工具(如SANY解析器、PlusCal转换器和漂亮的打印机)作为工具箱过程的一部分执行。如第3节所述，只有通过扩展和OSGi服务才能扩展此功能。但是，重量级的模型检查器和证明系统是作为独立的过程产生的。有三个原因可以产生单独的、每次调用的进程:

- 后端不能在进程中执行，因为它是用一种不能在Java VM上执行的语言实现的。这对于用OCaml编写的TLAPS是正确的。
- 工艺分离起断路器作用;后端或工具箱的崩溃都不会影响其他进程。例如，我们不希望工具箱崩溃也导致长期运行的模型检查器运行崩溃。当后端成熟时，这种保护尤其重要。
- Java VM的运行时参数是固定的工具箱启动后。作为工具箱的一部分运行后端会导致后端继承工具箱的参数。后端的资源需求通常不同于工具箱的资源需求——例如，模型检查器有非常高的资源需求(2.3.1节)。

#### 3.1.1 Toolbox to Back-end

The Toolbox relies on an RCP framework to provide user-visible progress reports and cancellation support for the back-ends that it spawns.5 For that, back-ends have to implement an adapter that sets command-line parameters. The primary parameters include the path to the TLA+ spec. In addition, parameters may include performance-specific settings such as the number of cores a back-end may use.

工具箱依赖于RCP框架来为它生成的后端提供用户可见的进度报告和取消支持。为此，后端必须实现一个设置命令行参数的适配器。主要参数包括TLA+规范的路径。此外，参数可能包括特定于性能的设置，例如后端可能使用的内核数量。

The Toolbox serializes a subset of the model into a plain-text configuration file (section 2.3). This file contains the TLA+ behavior spec as well as the invariants and properties to be checked. It also includes definitions for all declared constants. Optionally, it may contain definition and operator overrides as well as state and action constraints. The configuration is not specific to TLC and is therefore reusable by other back-ends.

工具箱将模型的子集序列化为纯文本配置文件(第2.3节)。这个文件包含TLA+行为规范以及要检查的不变量和属性。它还包括所有声明的常量的定义。可选地，它可以包含定义和操作符重写以及状态和操作约束。该配置不是特定于TLC的，因此可以由其他后端重用。

#### 3.1.2 Back-end to Toolbox

The Toolbox parses back-end progress and results with a framework that is connected to the Toolbox UI via the Model-View-Presenter (MVP) design pattern.

工具箱使用通过模型-视图-演示者(MVP)设计模式连接到工具箱UI的框架来解析后端进度和结果。

A back-end specific parser has to read a stream of back-end output that can consist of variably sized chunks of incomplete print statements. For efficiency reasons — error traces occasionally contain thousands of states — and to simplify the implementation, parsing is based on special tokens and constants that wrap the output. Parsing is performed in three stages:

1. It buffers chunks of characters into lines separated by a newline character
2. It identifies the lines that belong to a multi-line statement with the help of special tokens
3. It de-serializes a multi-line statement into Java objects via MVP

特定于后端解析器必须读取后端输出流，后者可以由大小不一的未完成打印语句块组成。出于效率原因(错误跟踪有时包含数千种状态)和简化实现，解析基于包装输出的特殊标记和常量。解析分三个阶段进行:

1. 它将字符块缓冲成以换行符分隔的行
2. 它在特殊标记的帮助下识别属于多行语句的行
3. 它通过MVP将多行语句反序列化到Java对象中

Listing 1 shows an example of a multi-line statement. In some cases, such as when error traces are serialized, the multi-line statement is partially formatted to be valid TLA+.

清单1显示了一个多行语句的示例。在某些情况下，例如在序列化错误跟踪时，多行语句被部分格式化为有效的TLA+。

In summary, the back-end integration is at a low enough level to free back-ends from providing highlevel interfaces such as a REST API. Instead, a back-end’s existing text-based IO can be reused. The flexibility of this low-level integration comes at a price: the lack of compile-time validation makes the evolution of back-ends more difficult. For the model checker, this problem is alleviated by following a synchronized release schedule. However, the evolution is still error-prone and requires significant testing of the back-end and the Toolbox (section 3.2). Performance problems, related to parsing large outputs, require low-level implementation optimizations. The maturity of the TLAPS back-end means that its inputs and outputs seldom change. The Toolbox does not support graceful back-end termination because the integration makes this impossible. Instead, the Toolbox could control TLC via its existing Java Management Extension.

总之，后端集成处于足够低的级别，可以将后端从提供高级接口(如REST API)中解放出来。相反，后端现有的基于文本的IO可以重用。这种低级集成的灵活性是有代价的:缺少编译时验证使得后端开发更加困难。对于模型检查器来说，遵循同步的发布计划可以缓解这个问题。但是，这种演进仍然容易出错，需要对后端和工具箱进行大量测试(第3.2节)。与解析大型输出相关的性能问题需要低级实现优化。TLAPS后端的成熟度意味着它的输入和输出很少更改。工具箱不支持优雅的后端终止，因为集成使这成为不可能。相反，工具箱可以通过其现有的Java管理扩展来控制TLC。

#### 3.1.3 CloudTLC Back-end

CloudTLC has been implemented directly as part of the Toolbox (section 2.3.1 above). CloudTLC is built on top of the frameworks discussed in section 3.1 and a multi-cloud toolkit that provides an abstraction from individual IaaS providers.7 The sequence diagram in figure 6 shows the interaction between the building blocks of CloudTLC: Deploy The Toolbox queries the chosen IaaS provider via https for specifically tagged CloudTLC instances. If the query result is empty, the Toolkit requests the IaaS provider to launch the given number of instances. If it is non-empty, the Toolbox starts the returned instances and skips the next provisioning phase. Provision The Toolbox configures the stock OS and installs dependencies of the model checker. Authentication credentials, needed by later phases of CloudTLC, are copied from the Toolbox’s environment variables. The provisioning phase directly communicates with each instance via ssh. Launch The Toolbox copies the spec and the model to the instance and starts the model checker. The model checker continuously streams its output to the local Toolbox. TLC also sends the result to a user-provided email address. Terminate The instance will wait for a grace period for subsequent connection attempts. Afterwards, it terminates itself if and only if email delivery has succeeded in the previous phase. Some IaaS providers require the termination request to be authenticated, in which case the previously mentioned credentials are used.

CloudTLC已经作为工具箱的一部分直接实现(上面的2.3.1节)。CloudTLC构建在第3.1节讨论的框架和一个多云工具包之上，该工具包提供了来自各个IaaS供应商的抽象。图6中的序列图显示了CloudTLC构建块之间的交互:Deploy工具箱通过https查询所选的IaaS提供程序，用于特定标记的CloudTLC实例。如果查询结果为空，工具箱将请求IaaS提供程序启动给定数量的实例。如果它是非空的，工具箱将启动返回的实例并跳过下一个供应阶段。提供工具箱配置常用操作系统并安装模型检查器的依赖项。CloudTLC的后续阶段需要的身份验证凭据是从工具箱的环境变量中复制的。供应阶段直接通过ssh与每个实例通信。启动工具箱将规范和模型复制到实例并启动模型检查器。模型检查器不断地将其输出流到本地工具箱。TLC还将结果发送到用户提供的电子邮件地址。Terminate实例将等待后续连接尝试的宽限期。之后，当且仅当电子邮件在前一阶段成功发送时，它才会终止。一些IaaS提供程序要求对终止请求进行身份验证，在这种情况下使用前面提到的凭据。

Because the hardware specifications of cloud instances are known during development, the model checker can be optimally deployed and configured. For example, the OS and TLC runtime parameters are hardcoded. CloudTLC has been implemented for TLC, but it can be easily extended to new back-ends. Likewise, it currently supports only Microsoft Azure, Amazon AWS, and Packet Net. Support for other IaaS providers is possible because of the multi-cloud toolkit.

因为云实例的硬件规范在开发过程中是已知的，所以可以优化部署和配置模型检查器。例如，操作系统和TLC运行时参数是硬编码的。CloudTLC已经为TLC实现了，但是它可以很容易地扩展到新的后端。同样，它目前只支持Microsoft Azure、Amazon AWS和包网络。由于有了多云工具包，对其他IaaS供应商的支持是可能的。

### 3.2 Testing

The Toolbox development follows a combination of the test-driven and the test-last methodologies. UI tests are written by Toolbox developers and dedicated test engineers. A test suite of 178 unit, functional, and end-to-end tests validates the main workflows of the Toolbox. However, the tests do not cover TLAPS. Dedicated test suites exist for the model checker and the proof system.

工具箱开发遵循测试驱动和最后测试方法的组合。UI测试是由工具箱开发人员和专门的测试工程师编写的。由178个单元、功能和端到端测试组成的测试套件验证工具箱的主要工作流。但是，测试不包括TLAPS。模型检查器和验证系统有专门的测试套件。

The Toolbox’s test execution is fully automated and runs on macOS, Linux, and Windows as part of the automated build. Builds are executed by a continuous integration system. While test results do not get published, the build output of the continuous integration system is publicly available. A test suite of user-provided, real-world specifications continuously checks the performance of TLC executed with CloudTLC. Overall, the test suites provide a useful safety measure to catch functional and performance-related regressions early in the development life-cycle.

工具箱的测试执行是完全自动化的，并作为自动化构建的一部分在macOS、Linux和Windows上运行。构建由持续集成系统执行。虽然测试结果不会发布，但是持续集成系统的构建输出是公开的。一个由用户提供的测试套件，不断地检查用CloudTLC执行的TLC的性能。总的来说，测试套件提供了一种有用的安全措施，可以在开发生命周期的早期捕获功能和性能相关的回归。

## 4 Conclusion

Toolbox users write, model check, and deductively reason about TLA+ specs. The Toolbox has two features, which formal IDEs usually do not provide:
- CloudTLC connects the TLA+ Toolbox with cloud computing to enable users to check larger models and to explore the design space faster
- The Profiler assists users in identifying computationally expensive TLA+ expressions and to diagnose state space explosion

The discussion of its architecture and test infrastructure should enable others to add new Toolbox features and will hopefully inspire the development of more formal IDEs.

Whether or not the Toolbox can be considered successful in making the TLA+ specification language more widely used is difficult to answer.9 Its previous release has been downloaded approximately 20k times whereas the standalone model checker has seen one-tenth of this number. The Toolbox has users at companies such as Amazon and Microsoft and it has an active community of contributors.


## 5 Future Work

### Proofs

The main challenge in writing an invariance proof is finding the inductive invariant. Trying to prove an invalid inductive invariant wastes a lot of time. Model checking is useful to validate inductive invariant candidates despite the enormous state space attached to it [11]. The trick is to randomly select and check a subset of all type-correct initial states. With this approach, repeated model checking finds violations with high probability after only a few runs. However, the Toolbox does not aggregate the results of repeated model checking. 
Taming state space explosion by randomly selecting subsets of all reachable states is also useful to validate for model checking lower-level proof steps [12]. The Toolbox should provide help for doing this.

写不变量证明的主要挑战是找到归纳不变量。试图证明一个无效的归纳不变式会浪费很多时间。模型检查对于验证归纳不变候选变量是有用的，尽管它附加了巨大的状态空间[11]。诀窍是随机选择并检查所有类型正确的初始状态的子集。使用这种方法，重复的模型检查在仅仅运行几次之后就发现了具有高概率的违规。但是，工具箱不会聚合重复模型检查的结果。
通过随机选择所有可达状态的子集来抑制状态空间爆炸，对于验证较低级别的证明步骤[12]也是有用的。工具箱应该为此提供帮助。

- [11] Leslie Lamport (2018): Using TLC to Check Inductive Invariance. Available at http://lamport.azurewebsites.net/tla/inductive-invariant.pdf. (Accessed 2018-08-16).
- [12] Leslie Lamport (2019): Proving Safety Properties. Available at https://lamport.azurewebsites.net/tla/proving-safety.pdf.

### Profiler 

Engineers have expressed interest in overriding inefficient expressions with Java functions. This is not easy to set up, especially for users unfamiliar with Java. Besides the setup of overrides, it is an open question how users can assert the equivalence of Java functions with the TLA+ expression it is overriding.

工程师们对用Java函数重写低效表达式很感兴趣。这并不容易设置，特别是对于不熟悉Java的用户。除了覆盖的设置之外，如何用它覆盖的TLA+表达式断言Java函数的等价性也是一个悬而未决的问题。

### Trace Exploration

The Trace Explorer is good for finding the source of an error, but its textural representation is not very good for understanding the dynamics of a system. Schultz pioneered a graphical trace animator that visualizes traces at the level of the problem domain [19]. The layout is specified in TLA+, so users need not learn a new visualization language. The next Toolbox release will incorporate this graphical trace animator.

Trace Explorer可以很好地找到错误的来源，但是它的文本表示对于理解系统的动态特性不是很好。Schultz首创了一个图形跟踪动画器，它可以在问题域[19]的级别上可视化跟踪。布局是在TLA+中指定的，因此用户不需要学习新的可视化语言。下一个工具箱版本将包含这个图形跟踪动画器。

### Back-ends

Konnov et al. built a symbolic model checker for TLA+, the results of which are encouraging [3]. We wish to integrate this model checker into the Toolbox as a new back-end. However, the symbolic model checker requires users to provide type information when its type inference fails. How the Toolbox can help users to work with types is unclear. Note that the type inference of the symbolic checker could also be used to provide a more powerful completion support in the spec editor.

- [3] Igor Konnov, Jure Kukovec & Thanh-Hai Tran (2019): TLA+ Model Checking Made Symbolic. Proceedings of the ACM on Programming Languages 3(OOPSLA), pp. 1–30, doi:10.1145/3360549. Available at http: //dl.acm.org/citation.cfm?doid=3366395.3360549.

Konnov等人为TLA+构建了一个符号模型检验器，其结果是令人鼓舞的[3]。我们希望将这个模型检查器作为新的后端集成到工具箱中。但是，符号模型检查器要求用户在其类型推断失败时提供类型信息。工具箱如何帮助用户使用类型尚不清楚。注意，符号检查器的类型推断也可以用于在spec编辑器中提供更强大的完成支持。


