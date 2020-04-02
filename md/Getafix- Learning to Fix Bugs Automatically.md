# Getafix: Learning to Fix Bugs Automatically

## Abstract

Static analyzers help find bugs early by warning about recurring bug categories. While fixing these bugs still remains a mostly manual task in practice, we observe that fixes for a specific bug category often are repetitive. This paper addresses the problem of automatically fixing instances of common bugs by learning from past fixes. We present Getafix, an approach that produces human-like fixes while being fast enough to suggest fixes in time proportional to the amount of time needed to obtain static analysis results in the first place.

静态分析器通过警告重复出现的 bug 类别来帮助早期发现 bug。虽然在实践中修复这些 bug 仍然是一项主要的手动任务，但我们观察到特定 bug 类别的修复通常是重复的。本文通过学习过去的修复，解决了自动修复常见 bug 实例的问题。我们提出了 Getafix，一种产生类人修复的方法，同时足够快地建议修复时间与获得静态分析结果所需的时间成比例。

Getafix is based on a novel hierarchical clustering algorithm that summarizes fix patterns into a hierarchy ranging from general to specific patterns. Instead of a computationally expensive exploration of a potentially large space of candidate fixes, Getafix uses a simple yet effective ranking technique that uses the context of a code change to select the most appropriate fix for a given bug.

Getafix 基于一种新颖的分层聚类算法，该算法将 fix 模式总结为从一般模式到特定模式的层次结构。而不是一个潜在的巨大的候选修复空间的计算昂贵的探索,getafix 使用一种简单而有效的排名技术，该技术使用代码更改的上下文来为给定的 bug 选择最合适的修复。

Our evaluation applies Getafix to 1,268 bug fixes for six bug categories reported by popular static analyzers for Java, including null dereferences, incorrect API calls, and misuses of particular language constructs. The approach predicts exactly the human-written fix as the top-most suggestion between 12% and 91% of the time, depending on the bug category. The top-5 suggestions contain fixes for 526 of the 1,268 bugs. Moreover, we report on deploying the approach within Facebook, where it contributes to the reliability of software used by billions of people. To the best of our knowledge, Getafix is the first industrially-deployed automated bug-fixing tool that learns fix patterns from past, humanwritten fixes to produce human-like fixes.

我们的评估将 Getafix 应用于由流行的 Java 静态分析器报告的六个 bug 类别的 1,268 个 bug 修复，包括空取消引用、错误的 API 调用和特定语言构造的误用。该方法准确地预测了 12% 到 91% 的时间内人类编写的修复作为最重要的建议，这取决于 bug 类别。前 5 个建议包含 526 个 bug 中的 1,268 个的修复。此外，我们报告了在脸谱网上部署这种方法，它有助于提高数十亿人使用的软件的可靠性。据我们所知，Getafix 是第一个在工业上部署的自动化错误修复工具，它从过去学习修复模式，通过人类编写的修复来产生类似人类的修复。

## 1 Introduction

Modern production code bases are extremely complex and are updated constantly. Static analyzers can help developers find potential issues (referred to as bugs for the rest of this paper) in their code, which is necessary to keep code quality high in these large code bases. While finding bugs early via static analysis is helpful, the problem of fixing these bugs still remains a mostly manual task in practice, hampering the adoption of static analysis tools [Christakis and Bird 2016].

现代生产代码库极其复杂，并且不断更新。静态分析器可以帮助开发人员在他们的代码中发现潜在的问题 (本文其余部分称为 bug)，这是在这些大代码库中保持高代码质量所必需的。虽然通过静态分析尽早发现错误是有帮助的，但是修复这些错误的问题在实践中仍然是一个主要是手工的任务，阻碍了静态分析工具 [Christakis 和 Bird 2016] 的采用。

Maria Christakis and Christian Bird. 2016. What developers want and need from program analysis: an empirical study. In Proceedings of the 31st IEEE/ACM International Conference on Automated Software Engineering, ASE 2016, Singapore, September 3-7, 2016. 332–343. https://doi.org/10. 1145/2970276.2970347

Most static analyzers look for instances of common bug categories, such as potential null dereferences, incorrect usages of popular APIs, or misuses of particular language constructs. We observe that fixes for a specific bug category often resemble each other: there is a pattern to them. That is, past human fixes of the same bug category may offer insights into how future instances of the bug category should be fixed. Given this observation, can we automate fixing bugs identified by learning from past fixes?

大多数静态分析器都会查找常见 bug 类别的实例，例如潜在的 null 取消引用、流行 api 的错误用法或特定语言结构的误用。我们观察到，特定 bug 类别的修复通常彼此相似: 它们有一个模式。也就是说，过去对同一 bug 类别的人类修复可以提供对 bug 类别的未来实例应该如何修复的见解。鉴于这种观察，我们能否通过从过去的修复中学习来自动修复发现的错误？

This paper addresses the problem of automatically fixing instances of common bug categories by learning from past fixes. We assume two inputs: (1) A set of changes that fix a specific kind of bug, e.g., from the version history of a code base. These changes serve as training data to learn fix patterns from. (2) A piece of code with a static analysis warning that we want to fix. Given only these two inputs, the problem is to predict a fix that addresses the static analysis warning in a way similar or equal to what a human developer would do. By automating the fix generation and leaving to the human only the final decision of whether to apply the fix, the overall effort spent on addressing bugs pointed out by static analyzers can be greatly reduced.

本文通过学习过去的修复，解决了自动修复常见 bug 类别的实例的问题。我们假设有两个输入 :(1) 一组修复特定类型错误的更改，例如，从代码库的版本历史记录中。这些更改作为训练数据，从中学习修复模式。(2) 一段带有我们想要修复的静态分析警告的代码。仅给出这两个输入，问题是预测解决静态分析警告的方法，其类似于或等于人类开发人员的操作。通过自动生成修复，并将是否应用修复的最终决定留给人类，可以大大减少在解决静态分析器指出的错误上花费的整体精力。

We focus on kinds of bugs that have non-trivial yet repetitive fixes. On the one end of the spectrum, there are bug categories that trivially imply a specific fix. For example, for a warning that suggests a field to be final, implementing an automated fix suggestion is straightforward. Such an autofix can be defined by the author of that rule in the static analyzer, without knowing the specific context in which the rule is applied; indeed, some of Error Prone [Aftandilian et al. 2012] rules come with auto-fixes. On the other end of the spectrum are bugs that require complex, application-specific fixes, such as an issue with a UI tab not displaying after a specific series of interactions from a user. Here we target bug categories in between these two extremes, where finding a fix is non-trivial, yet typical fixes belong to a set of recurring fix patterns. For such bug categories, there often exists more than one way to fix the problem, and the “right” way to address a specific instance of the bug category depends on the context, e.g., the code surrounding the static analysis warning.

Edward Aftandilian, Raluca Sauciuc, Siddharth Priya, and Sundaresan Krishnan. 2012. Building Useful Program Analysis Tools Using an Extensible Java Compiler. In 12th IEEE International Working Conference on Source Code Analysis and Manipulation, SCAM 2012, Riva del Garda, Italy, September 23-24, 2012. 14–23.

我们专注于具有非平凡但重复性修复的各种 bug。在光谱的一端，有一些 bug 类别，这些类别简单地暗示了一个特定的修复。例如，对于建议字段为最终字段的警告，实现自动修复建议很简单。这种自动修复可以由静态分析器中规则的作者定义，而不知道规则应用的特定上下文; 事实上，一些容易出错的 [Aftandilian 等人。2012] 规则附带自动修复。另一端是需要复杂的特定于应用程序的修复的错误，例如在用户进行一系列特定交互后不显示 UI 选项卡的问题。在这里，我们将这两个极端之间的 bug 类别作为目标，在这两个极端中，找到一个修复是不平凡的，但典型的修复属于一组重复的修复模式。对于这样的 bug 类别，通常存在不止一种方法来修复问题，并且解决 bug 类别的特定实例的 “正确” 方法取决于上下文，例如,围绕静态分析警告的代码。

As an example of a bug category targeted in this work, consider NullPointerExceptions – still one of the most prevailing bugs in Java and other languages. If a static analyzer warns about a potential null dereference, developers may fix the problem in various ways. Figure 1 shows three anonymized examples of fixes of null dereference bugs, which add a conjunct to an existing if condition, replace a call with a ternary operation, and add an early return, respectively. While all these fixes introduce some kind of null check, the exact fix heavily depends on the already existing code. Beyond these examples, there are even more ways of fixing null dereference bugs, e.g., by adding a new if statement or by extending an existing if condition disjunctively. Learning all these fix patterns and deciding which one to apply to a given piece of buggy code is a non-trivial problem.

作为本工作中针对的 bug 类别的一个例子，考虑 nullpointerexception-仍然是 Java 和其他语言中最普遍的 bug 之一。如果静态分析器警告潜在的 null 取消引用，开发人员可能会以各种方式修复问题。图 1 展示了三个修复 null 取消引用错误的匿名示例，分别在现有 if 条件中添加一个联合，用三元操作替换一个调用，并添加一个早期返回。虽然所有这些修复都引入了某种空检查，但确切的修复在很大程度上取决于已经存在的代码。除了这些例子之外，还有更多的方法来修复空引用错误，例如，通过添加一个新的 if 语句或者通过分离扩展一个现有的 if 条件。学习所有这些修复模式并决定将哪一个应用于给定的错误代码是一个不平凡的问题。

Our work aims at automating bug fixing in large-scale, industrial software development. This setting leads to several interesting challenges to deal with: 
- To reduce the human time spent on fixing a bug, the approach may propose only a small number of potential fixes, ideally only one fix. 
- To make this fix acceptable to developers, the suggested fix should be human-like: very similar to or exactly the same as a fix a human developer would implement. 
- To suggest fixes quickly, as well as to keep the computing resources required to find fixes within bounds, the approach cannot explore a large space of candidate fixes and validate each of them against a test suite or any other computationally expensive validation routine.

我们的工作旨在自动化大规模工业软件开发中的 bug 修复。这个设置导致了几个有趣的挑战要处理:

- 为了减少修复 bug 所花费的人力时间，该方法可能只提出少量潜在的修复，理想情况下只有一个修复。
- 为了让开发人员可以接受这个修复，建议的修复应该是类似人类的: 与人类开发人员将实现的修复非常相似或完全相同。
- 为了快速建议修复，以及保持查找修复所需的计算资源在边界内,该方法无法探索大量候选修复空间，并根据测试套件或任何其他计算昂贵的验证例程来验证它们中的每一个。

Addressing these challenges, we present Getafix, an automated technique that learns recurring fix patterns for static analysis warnings and suggests fixes for future occurrences of the same bug category. Getafix produces human-like fixes, and does so fast enough (typically within 10 seconds) to offer a fix suggestion in roughly the same magnitude of time as a human developer waits for static analysis results anyway. In a nutshell, the approach consists of three main steps. First, it splits a given set of example fixes into AST-level edit steps. Second, it learns recurring fix patterns from these edit steps, based on a novel hierarchical, agglomerative clustering technique that produces a hierarchy of fix patterns ranging from very general to very specific fixes. Third, given a previously unseen bug to fix, Getafix finds suitable fix patterns, ranks all candidate fixes, and suggests the top-most fix(es) to the developer. As a check during the third step, Getafix validates each suggested fix against the static analyzer to ensure that the fix removes the warning. Note that the validation against the static analyzer is a one-time effort per fix, keeping the computational resources within reasonable bounds.

针对这些挑战，我们提出了 Getafix，这是一种自动化技术，它学习静态分析警告的重复修复模式，并为将来出现相同的 bug 类别提出修复建议。Getafix 产生类似人类的修复，并且速度足够快 (通常在 10 秒内) 在与人类开发人员大致相同的时间内提供修复建议，无论如何都要等待静态分析结果。简而言之，该方法包括三个主要步骤。首先，它将一组给定的示例修复拆分为 AST 级别的编辑步骤。第二，它从这些编辑步骤中学习重复的修复模式，基于一种新的分层，凝聚聚类技术，产生从非常一般到非常具体的修复模式层次。第三，给定一个以前看不见的 bug 来修复，Getafix 找到合适的修复模式，对所有候选修复进行排名，并向开发人员建议最顶级的修复。作为第三步中的检查，Getafix 针对静态分析器验证每个建议的修复，以确保修复删除警告。请注意，针对静态分析器的验证是一次性修复，将计算资源保持在合理的范围内。

The Getafix approach and the constraints that motivated it differ from the assumptions made by automated program repair techniques [Goues et al. 2019], the most practical of which for large systems are generate-and-validate repair systems [Goues et al. 2012; Kim et al. 2013; Le et al. 2016].

- Claire Le Goues, Michael Pradel, and Abhik Roychoudhury. 2019. Automated Program Repair. Commun. ACM (2019). To appear.
- Claire Le Goues, ThanhVu Nguyen, Stephanie Forrest, and Westley Weimer. 2012. GenProg: A Generic Method for Automatic Software Repair. IEEE Transactions on Software Engineering 38 (2012), 54–72.
- Dongsun Kim, Jaechang Nam, Jaewoo Song, and Sunghun Kim. 2013. Automatic patch generation learned from human-written patches. In Proceedings of the 2013 International Conference on Software Engineering. IEEE Press, 802–811.

Getafix 方法和激励它的约束与自动程序修复技术做出的假设不同 [Goues 等人。2019]，其中对于大型系统最实用的是生成和验证修复系统 [Goues et al. 2012; Kim 等人。2013; Le 等人。2016]。

1. Because it must produce fixes quickly, Getafix neither generates, nor validates a large number of fix candidates. Getafix produces a ranked list of fix candidates based entirely on past fixes it knows about and on the context in which a fix is applied. This is done without any validation; Getafix’s ranking reflects the confidence it has in the fix. It then offers to the developer one (or a few, configurable) top-ranked fix(es) after a validation step. Note that Getafix does not go down its list exploring additional fix candidates until a validated fix is found.

因为它必须快速生成修复，所以 Getafix 既不会生成也不会验证大量的候选修复。Getafix 完全基于它所知道的过去的修复以及应用修复的上下文来生成修复候选列表。这是在没有任何验证的情况下完成的; Getafix 的排名反映了它对修复的信心。然后，它在验证步骤后向开发人员提供一个 (或几个可配置的) 顶级修复。请注意，在找到经过验证的修补程序之前，Getafix 不会在其列表中搜索其他候选修补程序。

2. In contrast to generate-and-validate repair systems that use a test suite as validation filter, Getafix uses the same static analyzer as the validation filter to make sure the warning goes away. This is because at this point in the development, we cannot assume that the code is ready to pass a test suite.

与使用测试套件作为验证过滤器生成并验证修复系统相比，Getafix 使用与验证过滤器相同的静态分析器来确保警告消失。这是因为在开发的这一点上，我们不能假设代码已经准备好通过测试套件。

3. Prior repair techniques rely on statistical fault localization (e.g., based on coverage of passing and failing test cases) that determines where in the code a bug should be fixed. Getafix avoids this step by exploiting the fact that static analyzers pinpoint the location of the bug. Consequently, it also avoids the need to try applying fix candidates at various potential fault locations.

先前的修复技术依赖于统计故障定位 (例如，基于通过和失败测试用例的覆盖范围)，这决定了错误应该在代码中的什么地方被修复。Getafix 通过利用静态分析器精确定位 bug 的位置这一事实来避免这一步骤。因此，它也避免了在各种潜在故障位置尝试应用修复候选项的需要。

4. Finally, the goal that Getafix sets for itself is not merely a fix that makes its validation pass, but is as close as possible to what a human would do in that situation. (While a generic null check would suppress a null dereference warning, as Figure 1 shows, a human might choose a very specific kind of fix in a particular context.) By contrast, most program repair systems aim to produce a fix that is functionally correct, but may not match a human fix.

最后，Getafix 为自己设定的目标不仅仅是使其验证通过的修复，而是尽可能接近人类在这种情况下会做什么。(虽然一般的 null 检查会抑制 null 取消引用警告，如图 1 所示，但人类可能会在特定上下文中选择非常具体的修复类型。)相比之下，大多数程序修复系统的目标是产生一个功能正确的修复，但可能与人类修复不匹配。

Another related line of work is on learning edit patterns, including potential bug fix patterns, from version histories [Brown et al. 2017; Rolim et al. 2017, 2018]. In contrast to Getafix, these techniques learn from all code changes, leaving the task of identifying interesting fix patterns to a human. A key insight of our work is that learning from fixes for a specific static analysis warning avoids this human effort.

另一个相关的工作是学习编辑模式，包括来自版本历史的潜在错误修复模式 [Brown et al. 2017; Rolim et al. 2017，2018]。与 Getafix 相反，这些技术从所有代码更改中学习，将识别有趣的修复模式的任务留给了人类。我们工作的一个关键见解是，从特定静态分析警告的修复中学习可以避免这种人类的努力。

We evaluate Getafix in two ways. One part of our evaluation applies the approach to a total of 1,268 bug fixes for six kinds of warnings reported by two publicly available and widely used static analyzers for Java. The bug categories include potential null dereferences, incorrect uses of Java’s reference equality, and common API misuses. After learning fix patterns from several dozens to several hundreds of examples, Getafix predicts exactly the human fix as the top-most suggestion for 12% to 91% of all fixes, depending on the bug category. In a setting where developers are willing to inspect up to five fix suggestions, the percentage of correctly predicted fixes even ranges between 19% and 92%, containing fixes for 526 of the 1,268 bugs. Because these results indicate how often the predicted fix exactly matches the human fix, as opposed to producing any human-acceptable fix, these results provide a lower bound of the effectiveness of Getafix.

我们以两种方式评估 Getafix。我们评估的一部分将该方法应用于总共 1,268 个错误修复，用于由两个公开可用且广泛使用的 Java 静态分析器报告的六种警告。Bug 类别包括潜在的 null 取消引用、不正确使用 Java 的引用相等性以及常见的 API 误用。在学习了几十到几百个例子的修复模式后，Getafix 准确地预测了人类修复是 12% 到 91% 修复的最高建议，这取决于 bug 类别。在开发人员愿意检查多达五个修复建议的环境中，正确预测的修复百分比甚至在 19% 到 92% 之间，包含 526 个 bug 中的 1,268 的修复。因为这些结果表明预测的修复与人类修复精确匹配的频率，而不是产生任何人类可接受的修复，所以这些结果提供了 Getafix 有效性的下限。

The other part of our evaluation deploys Getafix to production at Facebook, where it now contributes to the stability of apps used by billions of people. At Facebook, Getafix currently suggests fixes for bugs found by Infer [Calcagno et al. 2015]1, a static analysis tool that identifies issues, such as null dereferences, in Android and Java code. For example, the fixes in Figure 1 have been suggested by Getafix and accepted by developers at Facebook.We find that developers accept around 42% of all fixes suggested by Getafix, helping to save precious developer time by addressing bugs with a single click.

我们评估的另一部分将 Getafix 部署到 Facebook 的生产中，现在它有助于数十亿人使用的应用程序的稳定性。在 Facebook，Getafix 目前建议修复 Infer [Calcagno et al. 2015]1 发现的错误，这是一种静态分析工具，用于识别 Android 和 Java 代码中的问题，如空取消引用。例如，图 1 中的修复已被 Getafix 建议并被 Facebook 的开发人员接受。我们发现开发人员接受 Getafix 建议的所有修复的 42% 左右，通过一次点击解决 bug 来帮助节省宝贵的开发时间。

Contributions This paper makes the following contributions: 
- Fully automated suggestion of bug fixes for static analysis bug reports, computed in seconds, without requiring computationally expensive search over a large space of candidate bug fixes. 
- A novel clustering technique for discovering repetitive fix patterns in a way that is more general than a previous greedy approach and that preserves important context needed for applying fixes. 
- Simple yet effective statistical ranking technique to select the best fix(es) to suggest, which enables predicting human-like fixes among the top few suggestions. 
- Empirical evidence that the technique works well for a diverse set of bug categories taken from Error Prone and Infer.

贡献本文做出以下贡献:
- 为静态分析错误报告提供完全自动化的错误修复建议，以秒为单位计算，而不需要在大量候选错误修复空间中进行计算昂贵的搜索。
- 一种新的聚类技术，用于以比以前的贪婪方法更通用的方式发现重复的修复模式，并保留应用修复所需的重要上下文。
- 简单而有效的统计排名技术，以选择建议的最佳修复，这使得能够预测前几个建议中的类人修复。
- 经验证据表明，该技术适用于从易错和推断中获取的各种 bug 类别。

To the best of our knowledge, Getafix is the first industrially deployed automated bug-fixing tool that learns fix patterns from past, human-written commits, and produces humanlike fixes in a short amount of time.

据我们所知，Getafix 是第一个在工业上部署的自动化错误修复工具，它从过去的人工提交中学习修复模式,并在短时间内产生类似人类的修复。

## 2 Overview

Getafix consists of three main components, organized in a learning phase and a prediction phase. In the following we will describe their functionality and challenges at a high level, followed by a more detailed description in later sections. Figure 2 gives an overview of the approach. During the learning phase, a set of pairs of bugs and their fixes is given as training data to Getafix. As training data can serve any collection of past human code changes tied to a specific signal, such as a static analysis warning, a type error, a lint message, or simply the fact that a change was suggested during human code review. Our evaluation focuses on static analysis warnings as the signal, i.e., all bugs and fixes have been detected as instances of a specific bug category by a static analyzer, e.g., as potential null dereferences. During the prediction phase, Getafix then takes previously unseen code that comes with the same signal as the training examples and produces a bug fix.

Getafix 由三个主要组成部分组成，组织在学习阶段和预测阶段。在下面，我们将在高层次上描述它们的功能和挑战，然后在后面的章节中进行更详细的描述。图 2 给出了该方法的概述。在学习阶段，会将一组 bug 及其修复程序作为训练数据提供给 Getafix。由于训练数据可以服务于与特定信号相关联的任何过去人类代码更改集合，例如静态分析警告、类型错误、 lint 消息,或者仅仅是在人类代码审查期间建议进行更改的事实。我们的评估侧重于作为信号的静态分析警告，即，所有错误和修复已被静态分析器检测为特定错误类别的实例，例如，作为潜在的空取消引用。在预测阶段，Getafix 会获取与训练示例具有相同信号的以前未见过的代码，并生成 bug 修复。

The first step of the learning phase is a tree differencer, which identifies changes at the AST level. It generates concrete edits, which are pairs of sub-ASTs of the original before and after ASTs, representing a specific change that could be replayed on different code by replacing instances of the edit’s before AST with its after AST.

学习阶段的第一步是树差异器，它识别 AST 级别的变化。它产生具体的编辑，这是原始的 ASTs 之前和之后的一对子 ASTs,通过将编辑的 AST 之前的实例替换为其 AST 之后，表示可以在不同代码上重播的特定更改。

Based on the concrete, AST-level edits, the second step is a new way of learning fix patterns. To generalize from specific fix examples, fix patterns have “holes”, i.e., pattern variables, that may match specific subtrees, similar to the representation used by Rolim et al. [2017] and Long et al. [2017]. A technical contribution is a novel hierarchical, agglomerative clustering technique that organizes fix patterns into a hierarchical structure. The approach derives different variants of fix patterns, ranging from very specific patterns that match few concrete edits to very general fix patterns that match many concrete edits. Another contribution is to include into the fix patterns not only the code changes themselves, but also some surrounding context. This context is important to decide which out of multiple possible fix patterns to apply to a given piece of code.

- Reudismam Rolim, Gustavo Soares, Loris D’Antoni, Oleksandr Polozov, Sumit Gulwani, Rohit Gheyi, Ryo Suzuki, and Björn Hartmann. 2017. Learning Syntactic Program Transformations from Examples. In Proceedings of the 39th International Conference on Software Engineering (ICSE ’17). IEEE Press, Piscataway, NJ, USA, 404–415. https://doi.org/10.1109/ ICSE.2017.44
- Fan Long, Peter Amidon, and Martin Rinard. 2017. Automatic inference of code transforms for patch generation. In Proceedings of the 2017 11th Joint Meeting on Foundations of Software Engineering. ACM, 727–739.

基于具体的 AST 级别编辑，第二步是学习修复模式的新方法。为了从特定的 fix 示例中概括，fix 模式具有 “孔”，即模式变量，可以匹配特定的子树,类似于 Rolim 等人 [2017] 和 Long 等人 [2017] 使用的表示。技术贡献是一种新颖的分层，凝聚聚类技术，将固定模式组织成一个层次结构。该方法衍生出修复模式的不同变体，从匹配少数具体编辑的非常具体的模式到匹配许多具体编辑的非常一般的修复模式。另一个贡献是不仅在修复模式中包含代码更改本身，还包括一些周围的上下文。此上下文对于决定将多个可能的修复模式中的哪一个应用于给定的代码很重要。

After learning fix patterns, which is a once-per-bug-category effort, the prediction phase of Getafix applies the patterns to previously unseen buggy code to produce a suitable fix. Since Getafix aims at predicting fixes without a computationally expensive validation of many fix candidates, it internally ranks candidate fixes. We present a simple yet effective, statistical ranking technique that uses the additional context extracted along with fix patterns.

在学习了修复模式之后，这是一个每个 bug 类别一次的工作，Getafix 的预测阶段将模式应用到以前看不见的错误代码中，以产生合适的修复。由于 Getafix 旨在预测修复，而不需要对许多候选修复进行计算昂贵的验证，因此它在内部对候选修复进行排名。我们提出了一种简单而有效的统计排名技术，它使用了与固定模式一起提取的附加上下文。

Before suggesting a fix to the developer, Getafix validates the predicted fix against the same tool that provided the signal for the bug category, e.g., a static analysis, type checker, or linter. Getafix is agnostic to the signal used, so we assume a possibly computationally expensive black box component.

在向开发人员建议修复之前，Getafix 针对为 bug 类别提供信号的同一工具 (例如静态分析，类型检查器或 linter) 验证预测的修复。Getafix 对所使用的信号是不可知的，所以我们假设一个可能计算昂贵的黑盒组件。

## 3 Tree differencer

The first step of Getafix takes a set of example bug fixes, and decomposes each fix into fine-grained edits. These edits provide the basic ingredients for learning and applying fix patterns in later steps. For example, Figure 3 shows a code change and the three fine-grained edits that Getafix extracts: (i) inserting an early return if task is null (shown in green); (ii) changing public to private (shown in red); and (iii) moving the doWork method (shown in blue).

Getafix 的第一步采用一组示例错误修复，并将每个修复分解为细粒度的编辑。这些编辑提供了在后面的步骤中学习和应用修复模式的基本要素。例如，图 3 显示了一个代码更改和 Getafix 提取的三个细粒度编辑: (i) 如果任务为 null (以绿色显示)，则插入早期返回; (ii) 将 public 改为 private (以红色显示); 和 (iii) 移动 doworkk 方法 (以蓝色显示)。

### 3.1 Trees

Getafix extracts fine-grained edits based on ASTs. For our
purposes, a node in an AST has:

## 7 Related Work

### 7.1 Automated Program Repair

Getafix has goals similar to those of existing automated program repair techniques [Goues et al. 2019], but fills a previously unoccupied spot in the design space. In contrast to generate-and-validate approaches, such as GenProg [Goues et al. 2012], we focus on learning patterns from past fixes for specific kinds of warnings. Specifically, Getafix does not attempt to find generic solutions from any sort of ingredient space, or by generically mutating the code. While Genesis [Long et al. 2017] follows a similar approach of learning transformations between before and after ASTs, its learned templates contain generators, which increase the size of the search space. SketchFix [Hua et al. 2018] also aims at validating as few fix candidates as possible, but relies on runtime information from repeated test executions. In contrast, Getafix does not use any tests. Kim et al. [2013] automatically group human-written fixes by similarity. However, inspecting these groups and creating fix patterns from them remains a manual step, whereas Getafix learns fix patterns fully automatically. History-driven program repair [Le et al. 2016] has a diffing and mining pipeline similar Getafix, but uses it to infer abstract edit steps (like "insert statement"), which then help rank fix candidates produced by a generate-and-validate repair technique. Prophet learns a generic model of how natural a fix is and uses it for ranking fix candidates [Long and Rinard 2016]. Their approach could be plugged into Getafix, as an alternative or addition to our ranking algorithm. Section 5.3 discusses their ranking in more detail. CapGen [Wen et al. 2018] uses AST context to select mutation operators and fixing ingredients. Getafix implicitly uses a similar prioritization using its clustering and ranking strategy.

Claire Le Goues, Michael Pradel, and Abhik Roychoudhury. 2019. Automated Program Repair. Commun. ACM (2019). To appear.



Getafix 的目标与现有的自动化程序修复技术的目标相似 [Goues et al. 2019]，但填补了设计空间中一个以前未被占用的地方。与生成和验证方法相比，如 GenProg [Goues et al. 2012]，我们专注于从过去针对特定类型警告的修正中学习模式。具体来说，Getafix 不会试图从任何类型的成分空间中找到通用解决方案，也不会通过对代码进行通用突变来找到通用解决方案。虽然 Genesis [Long et al. 2017] 遵循 ASTs 之前和之后的类似学习转换方法，但其学习的模板包含生成器，这增加了搜索空间的大小。SketchFix [Hua et al. 2018] 也旨在验证尽可能少的 fix 候选，但依赖于重复测试执行的运行时信息。相比之下，Getafix 不使用任何测试。Kim et al. [2013] 通过相似性自动分组人类编写的修复。然而，检查这些组并从它们创建修复模式仍然是一个手动步骤，而 Getafix 完全自动地学习修复模式。历史驱动的程序修复 [Le et al. 2016] 具有类似 Getafix 的 diffing 和 mining 管道，但使用它来推断抽象的编辑步骤 (如 “insert 语句”),然后帮助排名修复由生成和验证修复技术产生的候选人。先知学习一个修复是多么自然的通用模型，并使用它来排名修复候选人 [Long 和 Rinard 2016]。他们的方法可以插入 Getafix，作为我们排名算法的替代或补充。第 5.3 节更详细地讨论了他们的排名。CapGen [Wen et al. 2018] 使用 AST 上下文选择突变运算符和固定成分。Getafix 使用其聚类和排名策略隐式使用类似的优先级。

Soto et al. [2016] observe that fix patterns differ across programming languages and analyze the structure of typical patterns in Java. Martinez et al. [Martinez and Monperrus 2012, 2015, 2018] also performed extensive AST analysis on Java repositories to statistically analyze code change patterns, which can guide program repair. Soto and Goues [2018] propose a probabilistic model-based approach for Java, which produces higher quality fixes more likely. As Getafix works on the level of ASTs and learns from human fixes, it can learn language-specific fix patterns automatically. NPEfix [Cornu et al. 2015] addresses NullPointerExceptions by selecting at runtime from a set of manually defined strategies, such as skipping the statement or replacing the null values with a fresh object. In contrast, Getafix addresses arbitrary bug categories and applies fixes statically.

Soto 等人 [2016] 观察到不同编程语言的 fix 模式不同，并分析 Java 中典型模式的结构。Martinez 等人 [Martinez 和 Monperrus 2012，2015，2018] 也对 Java 存储库进行了广泛的 AST 分析，以统计分析代码更改模式，这可以指导程序修复。Soto 和 Goues [2018] 为 Java 提出了一种基于概率模型的方法，更有可能产生更高质量的修复。当 Getafix 在 ASTs 水平上工作并从人类修复中学习时，它可以自动学习特定于语言的修复模式。NPEfix [Cornu et al. 2015] 通过从一组手动定义的策略中选择 at runtime 来解决 nullpointerexception，例如跳过语句或将 null 值替换为 fresh 对象。相比之下，Getafix 解决任意 bug 类别并静态应用修复。

### 7.2 Mining of Edit Patterns

Refazer [Rolim et al. 2017] uses PROSE [Polozov and Gulwani 2015] and a DSL of program transformations to learn repetitive edits. Revisar [Rolim et al. 2018] influenced Getafix by also using anti-unification [Kutsia et al. 2014] to derive edit patterns from concrete edits, but based on greedy clustering and without extracting surrounding context. Another difference is that Revisar learns from arbitrary code changes, not fixes of specific bug categories, requiring manual selection of interesting patterns from hundreds of candidates. See Sections 4.4 and 6.4 for a detailed comparison. Brown et al. [2017] mine recurring bug-introducing changes as the reverse operations of commits. Their goal is to infer mutation operations, i.e., code changes that inject artificial bugs into arbitrary code, whereas Getafix aims at fixing bugs.

Refazer [Rolim et al. 2017] 使用 PROSE [Polozov 和 Gulwani 2015] 和程序转换的 DSL 来学习重复编辑。Revisar [Rolim 等人 2018] 也通过使用反统一 [Kutsia et al. 2014] 从具体编辑中导出编辑模式，但基于贪婪聚类，不提取周围上下文。另一个区别是，Revisar 从任意代码更改中学习，而不是修复特定的 bug 类别，需要从数百个候选者中手动选择有趣的模式。有关详细比较，请参见第 4.4 节和第 6.4 节。Brown 等人 [2017] 挖掘反复出现的 bug-引入变更作为提交的反向操作。他们的目标是推断突变操作，即将人造 bug 注入任意代码的代码更改，而 Getafix 旨在修复 bug。

### 7.3 Machine Learning on Code

Several approaches delegate parts of or the entire fix generation task to a neural network. SSC combines a set of manually written generators of fix candidates with learned models that decide which candidate to apply [Devlin et al. 2017]. Sarfgen uses embeddings of code to formulate repair as a search for a similar reference solution [Wang et al. 2018]. Instead, Getafix aims at suggesting fixes for newly developed software that has no reference solution. DeepFix learns an end-to-end model for predicting fixes of compilation errors [Gupta et al. 2017]. Combing end-to-end learning with our idea of focusing on fixes for particular bug categories may be interesting future work.

几种方法将部分或整个修复生成任务委托给神经网络。SSC 将一组手动编写的 fix 候选生成器与决定应用哪个候选的学习模型相结合 [Devlin et al. 2017]。Sarfgen 使用代码的嵌入来制定修复作为类似参考解决方案的搜索 [Wang et al. 2018]。相反，Getafix 旨在为新开发的没有参考解决方案的软件提出修复建议。DeepFix 学习用于预测编译错误修复的端到端模型 [Gupta et al. 2017]。将端到端学习与我们专注于特定 bug 类别修复的想法结合起来可能是有趣的未来工作。

Getafix relates to a stream of work on machine learning applied to code [Allamanis et al. 2018]. Pradel and Sen [2018] demonstrate the power of identifier names for reasoning about buggy code. A similar technique could benefit our learning and ranking phase, e.g., by using word embeddings of identifiers and literals to guide which variables to substitute holes with, or by mimicking human intuition about return values to pick in case of an early return. Work on learning to represent code edits may also provide a starting point for learning to suggest fixes [Yin et al. 2018].

Getafix 涉及应用于代码的机器学习工作流 [Allamanis et al. 2018]。Pradel 和 Sen [2018] 展示了标识符名称对于错误代码推理的力量。类似的技术可以使我们的学习和排名阶段受益，例如，通过使用标识符和文字的单词嵌入来指导哪些变量来替换漏洞,或者模仿人类的直觉，在提前返回的情况下选择返回值。学习表示代码编辑的工作也可能为学习建议修复提供一个起点 [Yin et al. 2018]。

## 8 Conclusion

Fixing incorrect code is a long-standing problem that consumes significant developer time. Fortunately, many fixes, in particular bugs identified by static analyzers, are instances of recurring patterns. This paper presents Getafix, the first industrially deployed automated repair tool that fully automatically learns fix patterns from past, human-written commits. The approach is based on a novel hierarchical, agglomerative clustering algorithm, which summarizes a given set of fix commits into a hierarchy of fix patterns. Based on this hierarchy and a ranking technique that decides which fix pattern is most appropriate for a new occurrence of a bug category, Getafix proposes a ranked list of fixes to the developer. An evaluation with 1,268 real-world bug fixes and our experience of deploying Getafix within Facebook show that the approach accurately predicts human-like fixes for various bugs, reducing the time developers have to spend on fixing recurring kinds of bugs.

修复不正确的代码是一个长期存在的问题，会消耗大量的开发人员时间。幸运的是，许多修复，特别是由静态分析器识别的错误，都是重复模式的实例。本文介绍了 Getafix，第一个工业上部署的自动化修复工具，它完全自动地从过去的人类编写的提交中学习修复模式。该方法基于一种新颖的分层、凝聚聚类算法，该算法将给定的一组修复提交总结为修复模式的层次结构。基于这种层次结构和一种排名技术来决定哪种修复模式最适合 bug 类别的新出现，Getafix 向开发人员提出了一个排名列表的修复。一项包含 1,268 个真实世界错误修复的评估以及我们在 Facebook 中部署 Getafix 的经验表明，该方法准确地预测了各种错误的类人修复,减少开发人员花费在修复重复出现的 bug 上的时间。

We believe that the potential of the Getafix approach is broader than fixing only static analysis bugs. As long as the bug category and bug location is known, and a training set of fixes is available, Getafix can offer fixes learned from the training set. Case in point, Getafix suggests fixes — via SapFix4 [Marginean et al. 2019] — for null pointer exceptions detected by Sapienz5, an automated testing system for mobile apps.

Alexandru Marginean, Johannes Bader, Satish Chandra, Mark Harman, Yue Jia, Ke Mao, Alexander Mols, and Andrew Scott. 2019. SapFix: Automated End-to-End Repair at Scale (ICSE-SEIP ’19).

我们相信 Getafix 方法的潜力比只修复静态分析错误更广泛。只要知道 bug 类别和 bug 位置，并且有可用的修复训练集，Getafix 就可以提供从训练集中学习到的修复。举个例子，Getafix 建议修复 -- 通过 SapFix4 [Marginean 等人 2019] -- 由 Sapienz5 检测到的空指针异常，Sapienz5 是一个移动应用程序的自动测试系统。

