# A Survey of Compiler Testing

Virtually any software running on a computer has been processed by a compiler or a compiler-like tool. Because compilers are such a crucial piece of infrastructure for building software, their correctness is of paramount importance. To validate and increase the correctness of compilers, significant research efforts have been devoted to testing compilers. This survey article provides a comprehensive summary of the current state-of-the-art of research on compiler testing. The survey covers different aspects of the compiler testing problem, including how to construct test programs, what test oracles to use for determining whether a compiler behaves correctly, how to execute compiler tests efficiently, and how to help compiler developers take action on bugs discovered by compiler testing. Moreover, we survey work that empirically studies the strengths and weaknesses of current compiler testing research and practice. Based on the discussion of existing work, we outline several open challenges that remain to be addressed in future work.

实际上，在计算机上运行的任何软件都已由编译器或类似编译器的工具处理。因为编译器是构建软件的关键基础设施，所以它们的正确性至关重要。为了验证和增加编译器的正确性，大量的研究工作一直致力于测试编译器。这篇调查文章全面总结了当前编译器测试研究的最新进展。该调查涵盖了编译器测试问题的不同方面，包括如何构建测试程序，使用什么测试神谕来确定编译器的行为是否正确，如何高效地执行编译器测试,以及如何帮助编译器开发人员对编译器测试发现的 bug 采取行动。此外，我们还调查了实证研究当前编译器测试研究和实践的优缺点的工作。根据对现有工作的讨论，我们概述了未来工作中仍有待解决的几个公开挑战。

## 1 INTRODUCTION

Compilers are important tools, because they are a central piece of infrastructure for building other software. Virtually every program that runs on a computer, ranging from operating systems over web browsers to small scripts written by end-users, has been processed by a compiler or a compilerlike tool. Because compilers are such a central piece of infrastructure, they are very widely distributed. For example, popular compilers of widespread programming languages, such as GCC for C/C++, are run by millions of users. Beyond these direct users of compilers, typically developers, much more people indirectly rely on compilers when executing compiled programs.

编译器是重要的工具，因为它们是构建其他软件的核心基础设施。实际上，在计算机上运行的每一个程序，从网络浏览器上的操作系统到最终用户编写的小脚本，都已经被编译器或类似编译器的工具处理过了。因为编译器是基础设施的核心部分，所以它们的分布非常广泛。例如，流行的广泛编程语言 (如 C/C + + 的 GCC) 的编译器由数百万用户运行。除了这些编译器的直接用户 (通常是开发人员) 之外，更多的人在执行编译程序时间接依赖编译器。

Even though considerable efforts have been made to improve the quality of compilers, similar to all other software, compilers still contain bugs [72, 103, 123]. A compiler bug can cause incorrect binary code to be generated from correct source code. Also, a single bug in a production compiler can propagate to any application built upon it and cause surprising and possibly harmful misbehavior. For example, a miscompilation bug in the Java 7 implementation caused several popular Apache projects to crash.1 Sometimes, compiler bugs may even be injected on purpose to compromise the security of compiled applications. For example, a malicious variant of Apple’s Xcode development environment contained a compiler “bug” that introduces a backdoor into every compiled application. 2 Such compiler backdoors can also exploit accidentally introduced bugs, as evidenced by work on compromising the Unix sudo tool via a publicly known bug in LLVM [105].

即使已经做出了相当大的努力来提高编译器的质量，类似于所有其他软件，编译器仍然包含 bug [72,103，123]。编译器错误可能导致从正确的源代码生成不正确的二进制代码。此外，生产编译器中的单个 bug 可以传播到任何构建在其上的应用程序，并导致令人惊讶且可能有害的不当行为。例如，Java 7 实现中的一个编译错误导致几个流行的 Apache 项目崩溃。有时，编译器错误甚至可能被故意注入，以损害编译应用程序的安全性。例如，苹果 Xcode 开发环境的恶意变体包含一个编译器 “bug”，它在每个编译的应用程序中引入了后门。这样的编译器后门也可以利用意外引入的错误，通过在 LLVM [105] 中公开已知的错误来危害 Unix sudo 工具的工作就证明了这一点。

Compiler bugs not only cause unintended behavior with possibly severe consequences, but also make software debugging more difficult. The reason is that developers can hardly determine whether a software failure is caused by the program they are developing or the compilers they are using [26]. For example, when a buggy compiler optimizes a correct program into an executable that has incorrect runtime behavior, it is unclear to the developer of the program what causes the unexpected behavior. Since application developers usually assume the misbehavior tends to be caused by bugs introduced by themselves, they may spend a long time to eventually realize that a compiler bug is the root cause.

编译器错误不仅会导致可能导致严重后果的意外行为，还会增加软件调试的难度。原因是开发人员很难确定软件故障是由他们开发的程序引起的，还是由他们使用的[26]编译器引起的。例如，当有bug的编译器将正确的程序优化为具有不正确的运行时行为的可执行文件时，程序开发人员不清楚是什么导致了意外的行为。由于应用程序开发人员通常假定错误行为是由他们自己引入的bug引起的，所以他们可能要花很长时间才能最终认识到编译器bug是根本原因。

Given the importance of compilers, there is a huge interest in implementing them correctly. However, reaching this goal is non-trivial since compilers are complex pieces of software. A typical compiler consists of a pipeline of interacting components that address, e.g., the lexical analysis of the source language, parsing the source language into an intermediate representation, applying semantic checks, optimizing the code, and generating code in the target language. Currently, the optimization phase and implementation of parallelism and object-oriented features make it more complex. Because of this complexity, traditional computer science curricula typically dedicate at least one entire course to the art of constructing compilers. In contrast to other complex software, the domain that compilers deal with is particularly rich. Both the input and the output of compilers usually are programs written in Turing-complete languages. Moreover, both the inputs and output domains are infinitely large, since programs can be arbitrarily long. As a result, reasoning about the behavior of a compiler is all but trivial.

考虑到编译器的重要性，正确地实现它们是很有意义的。然而，达到这个目标并非易事，因为编译器是复杂的软件。典型的编译器由一系列相互作用的组件组成，这些组件处理源语言的词法分析、将源语言解析为中间表示形式、应用语义检查、优化代码和用目标语言生成代码。目前，并行性和面向对象特性的优化阶段和实现使其更加复杂。由于这种复杂性，传统的计算机科学课程通常至少有一门完整的课程专门用于构建编译器的艺术。与其他复杂的软件相比，编译器处理的领域特别丰富。编译器的输入和输出通常都是用图灵完备语言编写的程序。此外，输入域和输出域都是无穷大的，因为程序可以任意长。因此，关于编译器行为的推理几乎是无关紧要的。

The difficulties of correctly implementing a compiler lead to several challenges for testing the implementation. One challenge is the lack of a formal specification of what exactly a compiler is supposed to do. While the high-level specification is implicitly known—compilers translate a source program into a target program in a semantics-preserving way—the lower level details are usually unspecified. For instance, when to apply what optimizations is rarely specified, making it difficult to check whether a compiler applies all desired optimizations. As an example, the LLVM compiler has 58 optimizing transformation passes, which can be combined in various ways, but no specification exists when to apply which of these passes.

正确实现编译器的困难导致了测试实现的几个挑战。一个挑战是缺乏正式的规范来说明编译器到底应该做什么。虽然高级规范是隐式知道的——编译器以一种语义保留的方式将源程序转换成目标程序——但是低层的细节通常是未指定的。例如，什么时候应用什么优化很少被指定，这使得检查编译器是否应用了所有想要的优化变得很困难。例如，LLVM编译器有58个优化转换传递，它们可以以各种方式组合在一起，但是不存在何时应用这些传递的规范。

Another challenge for testing is the semantic richness of the input and output languages that compilers deal with. Because source code can express a wide range of computations, including many computations that cause the program to crash early, automatically creating source code with non-trivial behavior is difficult. However, such non-trivial programs are often required to reach deep into the pipelined workflow of a compiler and to test hard-to-reach code. For example, an input program must pass all sanity checks and type checks in the lexer and parser before it can test optimizations performed by the compiler. On the other hand, a non-compiler program often does not have such complex checks. A related challenge is that small changes in the input can cause huge differences in the expected behavior. For example, changing a single character in an input program may cause the compiler to take a completely different path and to expose completely different behaviors.

测试的另一个挑战是编译器处理的输入和输出语言的语义丰富性。因为源代码可以表达范围很广的计算，包括许多导致程序早期崩溃的计算，所以用非平凡的行为自动创建源代码是很困难的。然而，这些重要的程序通常需要深入到编译器的流水线工作流程中，并测试难以到达的代码。例如，输入程序必须通过lexer和解析器中的所有完整性检查和类型检查，然后才能测试编译器执行的优化。另一方面，非编译器程序通常没有这种复杂的检查。一个相关的挑战是，输入中的小变化可能导致预期行为的巨大差异。例如，在输入程序中更改一个字符可能会导致编译器采用完全不同的路径并公开完全不同的行为。

A third important challenge is that compilers have various options and features. For example, most compilers offer different optimization levels, support several variants of the source language, and consider multiple target platforms. This multitude of configurations creates a large space that is difficult to explore exhaustively and orthogonal to the regular input space.

第三个重要的挑战是编译器有各种各样的选项和特性。例如，大多数编译器提供不同的优化级别，支持源语言的多个变体，并考虑多个目标平台。这种大量的配置创建了一个大的空间，很难进行彻底的探索，并且与常规的输入空间正交。

In addition to these challenges, compilers fortunately also have some properties that simplify the problem of validating their correctness. One such property is that the inputs to compilers are written in a programming language, i.e., the space of possible inputs is clearly defined by the language grammar. In contrast, general-purpose fuzzing tools, such as AFL4, may not have a grammar to help control the generation of syntactically valid inputs. Another property that eases compiler testing is that the semantics of the source language are usually specified, either informally in a language specification or formally, e.g., for a core of the programming language. As a result, the expected behavior of (most) programs and thereby also of the compiler output, is known when running the program. Finally, compiler testing benefits from the fact that for most popular programming languages, there are multiple supposedly equivalent implementations, which compiler testing can exploit as an oracle for differential testing [81] (discussed in detail in Section 4).

除了这些挑战之外，幸运的是，编译器还有一些属性可以简化验证其正确性的问题。其中一个特性是编译器的输入是用编程语言编写的，即。在美国，可能的输入空间由语言语法明确定义。相反，通用的模糊化工具，比如AFL，可能没有语法来帮助控制语法有效输入的生成。简化编译器测试的另一个特性是，通常会指定源语言的语义，要么是在语言规范中非正式地指定，要么是正式地指定(例如，作为编程语言的核心)。因此，在运行程序时，(大多数)程序的预期行为以及编译器输出的预期行为是已知的。最后，编译器测试受益于这样一个事实:对于大多数流行的编程语言，存在多个被认为是等价的实现，编译器测试可以利用这些实现作为差异测试的oracle[81](在第4节中详细讨论)。

Motivated both by the importance of compilers and by the interesting challenges involved in improving their reliability and correctness, the area has received significant attention from researchers and practitioners. A particularly successful line of work is compiler testing, which has made tremendous advances in recent years. Several widely adopted tools and approaches have been developed in the past decade, some of which found hundreds of bugs in production compilers of popular languages [72, 123].

由于编译器的重要性以及在提高其可靠性和正确性方面所涉及的有趣挑战，该领域受到了研究人员和实践人员的极大关注。一个特别成功的工作是编译器测试，它在最近几年取得了巨大的进步。在过去的十年中，一些被广泛采用的工具和方法被开发出来，其中一些在流行语言的生产编译器中发现了数百个bug[72,123]。

The impressive progress made in compiler testing is a good news for developers and users of compilers. However, the huge amount of existing work makes it difficult for interested non-experts to understand the state of the art and how to improve upon it. This article summarizes existing work and provides a retrospection of the compiler testing field after years of development. The readers can thus have an in-depth understanding of the advantages and limitations of the existing approaches. Our findings can help researchers and practitioners understand more about the implications of the existing compiler testing approaches and help prompt their adoption in practice. Based on our analysis, we also point out the current challenges and suggest possible future directions for compiler testing research.

编译器测试取得了令人印象深刻的进展，这对编译器的开发人员和用户来说是个好消息。然而，现有的大量工作使得有兴趣的非专业人员很难理解这种技术的状态以及如何改进它。本文总结了现有的工作，并回顾了编译器测试领域多年来的发展。因此，读者可以深入了解现有方法的优点和局限性。我们的发现可以帮助研究人员和实践人员更多地了解现有编译器测试方法的含义，并帮助他们在实践中采用这些方法。在此基础上，指出了当前的挑战，并提出了今后编译器测试研究的可能方向。

We present the work on compiler testing from the following six perspectives, as illustrated in Figure 1:

- Constructing test programs: Section 3 presents approaches to creating programs that serve as inputs for testing compilers.
- Test oracles: Section 4 describes approaches to determine whether the behavior or output of a compiler is correct or not.
- Optimizing the test process: Section 5 discusses how to make compiler testing more efficient.
- Post-processing of test results: Section 6 presents approaches that help developers prioritize and understand bugs detected through compiler testing.
- Empirical studies: Section 7 discusses empirical studies performed to better understand compiler bugs and the process of compiler testing.
- Outlook and challenges for future work: Section 8 presents open challenges that remain to be addressed by future work.

我们从以下六个方面展示了编译器测试的工作，如图1所示:
- 构建测试程序:第3节介绍了创建作为测试编译器输入的程序的方法。
- 测试预言:第4节描述了确定编译器的行为或输出是否正确的方法。
- 优化测试过程:第5节讨论如何使编译器测试更有效。
- 测试结果的后处理:第6节介绍了帮助开发人员对通过编译器测试检测到的bug进行优先级排序和理解的方法。
- 经验研究:第7节讨论经验研究，以更好地理解编译器错误和编译器测试的过程。
- 未来工作的展望和挑战:第8节提出了有待未来工作处理的尚未解决的挑战。

Orthogonal to the efforts on compiler testing discussed in this article, formally verifying compilers is another attempt towards more reliable compilers. Work on compilers verification [36] checks whether the implementation of a compiler complies with a formal specification of some correctness properties, such as, to not crash during compilation or to preserve the semantics of the input program in the produced machine code. A particularly noticeable example is the CompCert compiler [76], which targets a subset of the C language and which has been formally verified. For this article, we focus on compiler testing and do not cover work on compiler verification in detail.

- [36] Maulik A Dave. 2003. Compiler verification: a bibliography. ACM SIGSOFT Software Engineering Notes 28, 6 (2003), 2–2.
- [76] Xavier Leroy. 2009. Formal verification of a realistic compiler. Commun. ACM 52, 7 (2009), 107–115.

与本文讨论的编译器测试工作正交，正式验证编译器是另一种更可靠的编译器尝试。编译器验证[36]检查编译器的实现是否符合某些正确性属性的正式规范，例如，在编译期间不崩溃或在生成的机器码中保留输入程序的语义。一个特别值得注意的例子是CompCert编译器[76]，它的目标是C语言的一个子集，并且已经得到了正式的验证。对于本文，我们将重点放在编译器测试上，而不详细讨论编译器验证的工作。

## 2 SURVEY METHODOLOGY

For this survey, we carefully collect 85 papers from relevant international journals and conferences. To collect these papers,we systematically search the DBLP publication database5 using the following keywords: “compiler test”, “compiler validation”, “compiler defect”, “compiler bug”, “compiler vulnerabilit(y/ies)”, “compiler fault”, “compiler error”, “compiler issue”, and “compiler debug”. Then, we manually filter the results by removing irrelevant papers. Finally, we retrieve additional papers by following references in the already found papers.

在这次调查中，我们仔细收集了相关国际期刊和会议上的85篇论文。为了收集这些论文，我们使用以下关键词系统地搜索DBLP出版物数据库:“编译器测试”、“编译器验证”、“编译器缺陷”、“编译器缺陷”、“编译器漏洞”、“编译器故障”、“编译器错误”、“编译器问题”、“编译器调试”。然后，我们通过删除不相关的论文来手动筛选结果。最后，我们根据已找到的论文中的参考文献检索其他论文。

Figure 2 shows the collected papers from 1970 to 2018. We can see that the number of relevant papers has increased significantly since 2011, indicating that the problem of compiler testing has received significant attention since then. We further analyze the reason behind this phenomenon. One possible reason is that some easy-to-use and effective tools such as Csmith [123] and CReduce [97] are developed and released around 2011, since nearly 50% papers from 2011 to 2018 are based on these tools. Actually, this is as expected. For example, constructing test programs is one of the most important challenges in compiler testing, and is the initial step of the testing process. Csmith makes this step convenient, and thus promotes compiler testing research.

- [123] Xuejun Yang, Yang Chen, Eric Eide, and John Regehr. 2011. Finding and Understanding Bugs in C Compilers. In Proceedings of the 32nd ACM SIGPLAN Conference on Programming Language Design and Implementation. 283–294.
- [97] John Regehr, Yang Chen, Pascal Cuoq, Eric Eide, Chucky Ellison, and Xuejun Yang. 2012. Test-case reduction for C compiler bugs. In Proceedings of the 33rd Conference on Programming Language Design and Implementation, Vol. 47. 335–346.

图2显示了从1970年到2018年收集的论文。我们可以看到，自2011年以来，相关论文的数量有了明显的增加，这说明编译器测试的问题从那时起就受到了极大的关注。我们进一步分析了这一现象背后的原因。一个可能的原因是一些易于使用和有效的工具，如Csmith[123]和CReduce[97]是在2011年左右开发和发布的，因为从2011年到2018年将近50%的论文都是基于这些工具。事实上，这是意料之中的。例如，构建测试程序是编译器测试中最重要的挑战之一，也是测试过程的初始步骤。Csmith为这一步提供了方便，从而促进了编译器测试研究。

Figure 3 shows the distribution of papers on each research perspective, including constructing test programs, test oracles, optimizing the test process, post-processing of test results, and empirical studies. If a paper addresses more than one research perspectives, we categorize it based on its most important contribution. Figure 3 shows that, like general software testing, most papers on compiler testing also focus on constructing test programs and test oracles. In particular, constructing test programs attracts most of the attention. This is as expected, because it is the initial step of the testing process (only after test programs are constructed, the testing process can be started).

图3展示了各个研究视角的论文分布，包括构建测试方案、测试oracle、优化测试流程、测试结果后处理和实证研究。如果一篇论文涉及一个以上的研究视角，我们会根据其最重要的贡献对其进行分类。图3显示，像一般的软件测试一样，大多数关于编译器测试的论文也关注于构建测试程序和测试神谕。特别是，构建测试程序是最受关注的。这是预期的，因为它是测试过程的初始步骤(只有在构建了测试程序之后，才能启动测试过程)。

Furthermore, we are aware of four existing surveys on compiler testing, all of which were published before 2005. Burgess [15] summarizes the main automatic compiler testing approaches before 1994. Boujarwah and Saleh [13] assess and compare various compiler test-program construction approaches before 1997. Tonndorf [117] presents tool-based Ada compiler validations before 1998. Kossatchev and Posypkin [68] present compiler testing approaches based on formal specifications of the programming language syntax and semantics before 2005. These existing survey papers mainly focus on constructing test programs and do not address other research challenges. Meanwhile, these survey papers were completed 10-20 years ago, which may have limited aids for current research on compiler testing. Therefore, we believe it is necessary to conduct a new survey of the field of compiler testing.

此外，我们注意到已有四份关于编译器测试的调查报告，它们都是在2005年之前发布的。Burgess[15]总结了1994年以前主要的自动编译器测试方法。Boujarwah和Saleh[13]对1997年以前的各种编译器测试程序构建方法进行了评估和比较。Tonndorf[117]在1998年之前提出了基于工具的Ada编译器验证。Kossatchev和Posypkin[68]在2005年之前提出了基于编程语言语法和语义的正式规范的编译器测试方法。这些现有的调查论文主要集中在构建测试程序，而没有解决其他研究的挑战。同时，这些调查论文是在10-20年前完成的，这可能限制了对当前编译器测试研究的帮助。因此，我们认为有必要对编译器测试领域进行一次新的调查。

## 3 CONSTRUCTING TEST PROGRAMS

Testing of any kind of software requires test cases. In the realm of compiler testing, programs form part of the test cases. In this article, we call such programs test programs. Constructing test programs for testing compilers is not trivial. We describe the challenges faced when constructing test programs in Section 3.1.

测试任何类型的软件都需要测试用例。在编译器测试领域，程序是测试用例的一部分。在本文中，我们将这些程序称为测试程序。为测试编译器构建测试程序并非易事。我们将在3.1节中描述构建测试程序时所面临的挑战。

Approaches to constructing test programs can be broadly classified into three categories. Table 1 gives an overview of the broad classification and also shows how each category can be further divided. Test programs are either manually written or constructed automatically. We explain the manual approaches in Section 3.2. The automated approaches either generate program fragments and concatenate them into test programs, or mutate existing programs. Section 3.3 gives details on automated test program generation and Section 3.4 discusses mutation based test program construction approaches. Because automated approaches for test program construction can create a large number of test programs with little effort, they are widely used in compiler testing.

构建测试程序的方法大致可以分为三类。表1概述了广泛的分类，并展示了如何进一步划分每个类别。测试程序要么是手动编写的，要么是自动构建的。我们将在第3.2节中解释手动方法。自动化方法要么生成程序片段并将它们连接到测试程序中，要么对现有程序进行修改。第3.3节详细介绍了自动测试程序生成，第3.4节讨论了基于突变的测试程序构建方法。由于用于测试程序构造的自动化方法可以轻松地创建大量测试程序，所以它们在编译器测试中得到了广泛的应用。

Table 2 gives an overview of the programming languages targeted by the various approaches discussed in this section. Overall, the approaches cover a wide range of languages, with C/C++ being the most popular language.

表2概述了本节中讨论的各种方法所针对的编程语言。总的来说，这些方法涵盖了各种各样的语言，其中C/ c++是最流行的语言。

### 3.1 Challenges For Constructing Test Programs

Constructing test programs for testing compilers is challenging. In particular, it is difficult to construct test programs that are valid, diverse, and meet certain requirements for testing.

为测试编译器构建测试程序是具有挑战性的。特别是，很难构建有效的、多样化的、满足特定测试需求的测试程序。

Validity of test programs. It is non-trivial to construct valid programs because of the restrictions some languages have on using certain language constructs only in a certain way or in a certain context. Constructing invalid programs is of limited usefulness for testing compilers since a program goes through multiple phases of processing by the compiler; if a compiler is presented with an invalid input program, then the program tends to get discarded in the initial stages of the processing. For example, if a constructed JavaScript program contains a return statement that is not inside a function, then the program is considered syntactically invalid. Such a program does not reach the code generation or the optimization phases of a JavaScript engine. In C for example, generating a program with undefined behavior cannot be considered as a valid test program since the result is invariably correct. Similarly, for many languages, an identifier must be declared before being used in the program. Constructing a program while maintaining such restrictions is not always straightforward. For typed languages in particular, maintaining type constraints during test program construction is not easy, which often leads to the construction of invalid test programs.

测试程序的有效性。构造有效的程序是很重要的，因为一些语言只在特定的方式或特定的上下文中使用特定的语言构造是有限制的。构造无效的程序对于测试编译器来说用处有限，因为一个程序要经过编译器的多个处理阶段;如果编译器显示了一个无效的输入程序，那么这个程序在处理的初始阶段就会被丢弃。例如，如果构造的JavaScript程序包含一个不在函数内的返回语句，则该程序在语法上是无效的。这样的程序不会到达JavaScript引擎的代码生成或优化阶段。例如，在C语言中，生成具有未定义行为的程序不能被视为有效的测试程序，因为结果总是正确的。类似地，对于许多语言，在程序中使用之前必须声明标识符。在维护这些限制的同时构建一个程序并不总是那么简单。特别是对于类型化语言，在测试程序构造期间维护类型约束是不容易的，这常常导致构造无效的测试程序。

Diversity of test programs. As with all test inputs, constructed test programs should be diverse. A syntactically diverse set of test programs will exercise different parts of the compiler and potentially increase code coverage of the compiler under test. This can potentially aid in uncovering bugs. In the realm of testing compilers, diversity metrics such as distance between test programs [30] have been proposed. Although desirable, it is difficult to construct a diverse set of test programs since an increase in the number of syntactic language constructs can adversely affect the validity of the generated programs. For example, it might be desirable that a set of constructed C test programs contain variables and their corresponding operations using all available types. Unfortunately, it is not very straightforward to for a test program constructor to do so. With the use of more features, the constructed test program becomes more prone to be generated as invalid. This is probably the reason, many test program generators that we discuss later in Section 3.3 only generate programs using a subset of all available language features.

测试程序的多样性。与所有的测试输入一样，构建的测试程序应该是多样化的。一组语法上不同的测试程序将运行编译器的不同部分，并可能增加被测试编译器的代码覆盖率。这可能有助于发现bug。在测试编译器领域，已经提出了多样性度量，例如测试程序之间的距离[30]。尽管想要构建一组不同的测试程序是很困难的，因为增加语法语言结构的数量会对生成的程序的有效性产生不利的影响。例如，可能希望一组构造的C测试程序包含变量及其使用所有可用类型的相应操作。不幸的是，对于测试程序构造函数来说，这样做并不是很简单。随着更多特性的使用，所构建的测试程序更容易被生成为无效的。这可能就是为什么我们在3.3节后面讨论的许多测试程序生成器只使用所有可用语言特性的子集来生成程序的原因。

Specific requirements imposed by a testing method. The construction of test programs also becomes difficult when certain testing method imposes restrictions. For example, a common testing method is to compile a program with two compilers and compare the execution results. In such a case, the input program should be free of undefined behavior. On the other hand, if we just test for compiler crashes, the input program does not necessarily have to be free of undefined behavior. Constructing test programs that follow these restrictions or conformance in addition to the validity and diversity requirements can be challenging.

测试方法规定的具体要求。当某些测试方法施加限制时，测试程序的构建也变得困难。例如，一个常见的测试方法是用两个编译器编译一个程序，并比较执行结果。在这种情况下，输入程序应该没有未定义的行为。另一方面，如果我们只是测试编译器崩溃，那么输入程序不一定没有未定义的行为。除了有效性和多样性需求之外，构建遵循这些限制或一致性的测试程序是具有挑战性的。

### 3.2 Manually Constructing Test Programs

Manually constructed test programs have been used since the early days of compiler testing. Such test programs can be effective in uncovering bugs since the programs can be tuned to a particular need and they are often written to test newly implemented features.

从早期的编译器测试开始，就一直使用手动构建的测试程序。这样的测试程序可以有效地发现bug，因为这些程序可以根据特定的需要进行调优，而且它们常常是为了测试新实现的功能而编写的。

Popular compilers, such as GCC [38], compiler infrastructures, such as LLVM [39], implementations of the Java platform, such as OpenJDK [40], and browsers, such as Chromium [37], use extensive manually written test suites to test their implementations. For example, GCC comes with several test suites for both the runtime libraries and the language front ends. The documentation of OpenJDK 6, GCC 7 and Chromium 8 all have guides for developers on how a test case should be written. In addition, there are independently developed conformance test suites, such as the Plum Hall Validation Suite [62] for C and C++ and Test262 for ECMAScript [46].

流行的编译器(如GCC[38])、编译器基础设施(如LLVM[39])、Java平台的实现(如OpenJDK[40])和浏览器(如Chromium[37])都使用大量的手动编写的测试套件来测试它们的实现。例如，GCC为运行库和语言前端提供了几个测试套件。OpenJDK 6、GCC 7和Chromium 8的文档都为开发人员提供了关于如何编写测试用例的指南。此外，还有独立开发的一致性测试套件，如用于C和c++的Plum Hall Validation Suite[62]和用于ECMAScript[46]的Test262。

Although popular in practice, there has been very limited academic work on manually constructing test programs for testing compilers. One exception is by Callahan et al. [17], who describe the manual construction of 100 Fortran loops for testing a vectorizing compiler. Such compilers should be able to determine if a loop can be expressed using hardware-supported vector operations, i.e., if a loop can be vectorized. The primary goal of the test programs manually constructed by Callahan et al. is to check whether the compiler vectorizes vectorizable loops rather than to test the correctness of the generated code. To this end, each of the manually written loop contains some code that can be vectorized by the compiler. When passing these loops to compilers, Callahan et al. find that some compilers miss vectorizable loop statements.

尽管在实践中很流行，但是在为测试编译器手工构建测试程序方面的学术工作非常有限。一个例外是Callahan等人的[17]，他们描述了用于测试向量化编译器的100个Fortran循环的手工构造。这样的编译器应该能够确定一个循环是否可以用硬件支持的向量操作来表示，即，如果循环可以向量化。Callahan等人手工构建的测试程序的主要目标是检查编译器是否向量化了可向量化的循环，而不是测试生成的代码的正确性。为此，每个手动编写的循环都包含一些可以由编译器向量化的代码。当将这些循环传递给编译器时，Callahan等人发现一些编译器遗漏了可向量化的循环语句。

Another description of manually constructing test programs is by Dongarra et al. [44], who collect subroutines and loops that contain parallelism opportunities written by other developers. Additionally, they themselves also manually write programs containing parallelism opportunities. The end goal is to measure if Fortran compilers automatically parallelize the loops.

Dongarra等人对手工构建测试程序的另一种描述是[44]，他们收集包含其他开发人员编写的并行机会的子例程和循环。此外，他们自己也手动编写包含并行机会的程序。最终目标是测量Fortran编译器是否自动并行化循环。

Wolf et al. [120] describe their experience of manually constructing test programs based on a natural language specification of the programming language. Their approach is to study the specification sentence by sentence, and to create a test program for every testable requirement given in the specification. Their work targets the Arden language, a domain-specific programming language to describe medical knowledge.

Wolf等人[120]描述了他们基于编程语言的自然语言规范手工构建测试程序的经验。他们的方法是逐句研究规范，并为规范中给出的每个可测试需求创建测试程序。他们的工作目标是Arden语言，一种描述医学知识的领域特定的编程语言。

### 3.3 Test Program Generation

Although effective, writing test programs manually needs significant effort. As a result, there have been constant efforts towards designing automatic test program generators for testing compilers. These efforts can be broadly classified into three categories: grammar-directed, grammar-aided and other approaches. We explain these approaches in the following.

尽管有效，手动编写测试程序需要大量的工作。因此，人们一直致力于为测试编译器设计自动测试程序生成器。这些努力大致可分为三类:语法指导、语法辅助和其他方法。我们将在下面解释这些方法。

#### 3.3.1 Grammar-directed Approaches.

Grammar-directed approaches for test program generation take a language grammar as their input and generate programs based on this grammar. Grammardirected approaches are the first approaches proposed for automatic test program generation to test compilers. Given the context-free grammar of a language, it is natural to walk over its productions to generate strings of the language. To this end, Purdom [96] presents an approach to testing the correctness of parsers and grammars based on context-free grammars. The primary focus is to validate parsers that are automatically generated from context-free grammars and to find non-reduced grammars (where the grammar contains symbols that cannot be used for a sentence derivation). The generation starts from a unique start symbol and proceeds by applying left-right rewriting rules from the grammar. The approach uses some heuristics to generate short sentences by recursively going over the grammar. For testing parsers, it is desirable that the generated programs cover different states and transitions of the parser. Purdom evaluates his approach on automatically generated LALR(1) parsers and shows that in many cases, the generated test programs are able to cover multiple states and transitions of the parser.

以语法为导向的测试程序生成方法以语言语法为输入，并根据该语法生成程序。语法指导方法是第一个被提出的用于自动测试程序生成到测试编译器的方法。考虑到语言的上下文无关语法，很自然地要遍历其结果以生成语言的字符串。为此，Purdom[96]提出了一种基于上下文无关语法来测试解析器和语法正确性的方法。主要的重点是验证从上下文无关语法自动生成的解析器，并找到非简化语法(其中语法包含不能用于句子派生的符号)。生成从一个独特的开始符号开始，并通过应用来自语法的左右重写规则进行。该方法使用一些启发式方法通过递归遍历语法来生成短句子。对于测试解析器，生成的程序最好覆盖解析器的不同状态和转换。Purdom在自动生成的LALR(1)解析器上评估他的方法，并显示在许多情况下，生成的测试程序能够覆盖解析器的多个状态和转换。

Purdom’s [96] test program generation based on context-free grammar faces shortcomings when the objective is to test all parts of a compiler. By only using a context-free grammar, it is difficult to express context-sensitive features of a language. Since the end goal of Purdom is to test parsers that do not expect semantically correct programs, this shortcoming does not affect the approach.

当目标是测试编译器的所有部分时，Purdom[96]基于上下文无关语法的测试程序生成面临着缺陷。仅使用上下文无关的语法很难表达语言的上下文敏感特性。由于Purdom的最终目标是测试那些不期望语义正确的程序的解析器，所以这个缺点并不影响该方法。

Approaches that focus on testing harder-to-reach parts of the compiler, instead of only the parser, use different ways to address context sensitivity during test program generation. The initial attempts at generating compilable programs based on language grammars tend to extend the context-free productions with context-sensitive features. The extended grammars form a family of grammars known as two-level grammars and are introduced by Adriaan van Wijngaarden to specify ALGOL 68 [118]. Generation of test programs for the goal of testing compilers use three particular two-level grammars, namely W-grammars, attribute grammars and affix grammars. The following gives a brief introduction of these three variants of two-level grammars and also explains the approaches that use each of them. For a comprehensive description about these three types of grammars, readers are referred to a tutorial by Koster [69].

专注于测试难以触及的编译器部分的方法，而不是只测试解析器，在测试程序生成期间使用不同的方法来处理上下文敏感性。基于语言语法生成可编译程序的最初尝试倾向于使用上下文敏感的特性来扩展上下文无关的结果。扩展语法形成了一个语法家族，称为两级语法，由Adriaan van Wijngaarden引入，以指定ALGOL 68[118]。以测试编译器为目标的测试程序的生成使用了三种特殊的两级语法，即W-grammars、attribute grammars和affix grammars。下面简要介绍两级语法的这三种变体，并解释使用它们的方法。对于这三种语法的全面描述，读者可以参考Koster的教程[69]。

## 9 CONCLUSION

This article provides a survey of approaches for testing compilers. Given the importance of compilers as a basic part of every developer’s tool chain and the disastrous consequences of compiler bugs, testing compilers is an extremely important topic. Recent years have seen significant improvements and activities in the field of compiler testing. Our article enables interested outsiders to obtain an overview of this thriving field, and may enable experts to fill any gaps in their knowledge of the state-of-the-art. Based on our discussion of existing work, we conclude compiler testing has evolved into a mature field that has already made significant impacts on real-world compiler development.

本文介绍了测试编译器的方法。考虑到编译器作为每个开发人员的工具链的基本部分的重要性和编译器错误的灾难性后果，测试编译器是一个非常重要的主题。近年来，编译器测试领域出现了显著的改进和活动。我们的文章使感兴趣的局外人获得这一蓬勃发展的领域的概述，并可能使专家填补他们的最新知识的任何空白。基于我们对现有工作的讨论，我们得出结论，编译器测试已经发展成为一个成熟的领域，已经对真实世界的编译器开发产生了重大影响。

Despite all the advances on compiler testing, there remain several interesting challenges to be addressed in the future, including how to generalize existing approaches and how to further improve both their effectiveness and efficiency. We hope that our survey allows researchers to make progress towards these goals.

尽管在编译器测试方面取得了很多进步，但在未来仍有几个有趣的挑战需要解决，包括如何推广现有的方法以及如何进一步提高它们的有效性和效率。我们希望我们的调查能让研究人员朝着这些目标取得进展。
