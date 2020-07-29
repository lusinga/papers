# Notes on Programming Standard ML of New Jersey

## Preface

The impetus behind these notes was the desire to provide a cohesive description of Standard ML of New Jersey, an interactive compiler and environment for Standard ML. The goal is to end up with a complete user guide to the system, inclduing the libraries, the tools and the extensions, as well as a tutorial on how to write “real” applications, centered around the use of the module system and the compilation manager. Other reasons include the desire to provide hands-on examples of how to use some maybe poorly understood features or features with an interface different from what one may be used to. Examples of such features include sockets, the input and output subsystem, the readers approach to text scanning and the use of continuations to handle non-local control-flow. All in all, this would be a repository for snippets of SML and SML/NJ programming lore.

这些音符背后的动力是为了提供一个有凝聚力的描述标准毫升的新泽西州,一个交互式的编译器和环境标准毫升。我们的目标是得到一个完整的系统用户指南,包括图书馆、工具和扩展,以及关于如何编写的教程“真正的”应用程序,围绕使用模块的编译系统和经理。其他原因包括希望提供一些实际的例子，说明如何使用一些可能不太了解的特性，或者使用与人们可能习惯的界面不同的特性。此类特性的示例包括套接字、输入和输出子系统、文本扫描的阅读器方法以及使用延续来处理非本地控制流。总而言之，这将是一个存储SML和SML/NJ编程知识片段的资源库。

These notes are not a tutorial introduction to Standard ML. There exists excellent introductory material, available both commercially or freely over the Internet. These notes complement this literature by focusing on the Standard ML of New Jersey environment. The first part of these notes does given an overview of the language, but a quick one and without highlighting some of the subtleties of the language. Better writers than I have written better introductory material and I urge you to read those first. References are given in the chapter notes of Chapter 1. I go in somewhat more details when describing the Basis Library, since some of the underlying ideas are fundamental to the overall programming philosophy. Unfortunately, that chapter is long, boring and reads more like a reference manual than a tutorial. Throughness and precision at odds with readability. With luck, enough sample code and tutorial material is interspersed to lighten the mood. In the course of the notes, I take the opportunity to describe more advanced topics typically not covered in introductory material, such as sockets programming, signals handling, continuations, concurrency. Some of these subjects are discussed in advanced programming language courses, which not every one has taken or plan to take. Some of these subjects are hardly discussed and one needs to rummage technical papers or source code.

这些说明并不是标准ML的入门教程，而是一些非常好的介绍性材料，可以通过商业渠道获得，也可以从Internet上免费获得。这些注释通过关注新泽西州环境的标准ML来补充本文献。这些注释的第一部分确实给出了该语言的概述，但只是一个快速的概述，并没有突出该语言的一些微妙之处。比我更好的作家写了更好的介绍性材料，我建议你们先读一下。参考文献见第一章的章节注释。在描述Basis库时，我会更详细地介绍，因为其中一些基本思想是整个编程哲学的基础。不幸的是，那一章很长，很无聊，读起来更像是参考手册而不是教程。清晰度和精确度与可读性不一致。如果幸运的话，可以穿插足够的示例代码和教程材料来减轻气氛。在讲义中，我利用这个机会描述了介绍性材料中通常没有涉及的更高级的主题，比如套接字编程、信号处理、延续、并发性。其中一些课程会在高级编程语言课程中讨论，并不是每个人都修过或打算修。其中一些主题几乎没有讨论过，人们需要翻找技术论文或源代码。

## Chapter 1 Introduction

These notes describe Standard ML of New Jersey (SML/NJ), an interactive compiler, programming environment and associated tools for the programming language Standard ML (SML). The compiler is being developped in collaboration between Bell Laboratories, Princeton University and Yale University. This chapter provides an introductory overview of the language and the environment.

这些注释描述了用于编程语言标准ML (SML/NJ)的交互式编译器、编程环境和相关工具。该编译器正在贝尔实验室、普林斯顿大学和耶鲁大学合作开发。本章提供了语言和环境的介绍性概述。

### 1.1 Standard ML

The programming language SML has its roots as a meta-language for defining proof tactics in interactive theorem provers. Over the years, the language evolved into a full-fledged programming language, with excellent features for both smallscale and large-scale programming. Several properties make SML an interesting language. Consider the following:

- SML is mostly functional. It is based on the model of evaluating expressions as opposed to the model of executing sequences of commands found in imperative languages. The ability to treat functions as first-class values allows so-called higher-order programming, a very powerful programming technique. In contrast with purely functional languages, SML allows the use of imperative constructs such as variables, assignment and sequencing of side-effecting operations.
- SML is strongly and statically typed. Each expression in the language is assigned a type describing the values it can evaluate to, and type checking at the time of compilation ensures that only compatible operations are performed. This process eliminates many of the bugs during preliminary stages of programming an application, and greatly facilitates tracking changes to the code during revisions and upgrades. SML provides the common basic types such as integers, floating points and strings, as well as compound types such as tuples, records, lists and arrays to create complex data objects. New types can be easily defined, and moreover can be made abstract, where the representation of the values cannot be seen or examined outside of a prescribed region of the code.
- SML extends this basic notion of types with parametric polymorphism. A function gets a polymorphic type when it can operate uniformly over any value of any given type. For example, reversing a list can be done uniformly for all lists, irrespectively of the type of value stored in the list.
- SML provides an easy-to-use exception mechanism for handling exceptional conditions. At any point in the code, an exception can be raised, with the effect of aborting the current operation and returning control to the last exception handler defined for that exception. Exceptions can carry arbitrary values, including functions.

编程语言SML是一种用于定义交互式定理证明策略的元语言。多年来，该语言已经发展成为一种成熟的编程语言，具有适用于小规模和大规模编程的优秀特性。几个属性使SML成为一种有趣的语言。考虑以下:
- SML主要是函数式的。它基于对表达式求值的模型，而不是命令式语言中执行命令序列的模型。将函数视为一级值的能力允许所谓的高阶编程，这是一种非常强大的编程技术。与纯函数式语言相比，SML允许使用命令式结构，如变量、赋值和副作用操作的排序。
- SML是强静态类型的。该语言中的每个表达式都被分配了一个类型，该类型描述它可以计算的值，编译时的类型检查确保只执行兼容的操作。这个过程消除了应用程序编程初期阶段的许多bug，并且极大地方便了在修订和升级期间跟踪对代码的更改。SML提供了常见的基本类型，如整数、浮点数和字符串，以及复合类型，如元组、记录、列表和数组，以创建复杂的数据对象。新类型可以很容易地定义，而且可以变得抽象，在指定的代码区域之外不能看到或检查值的表示。
- SML使用参数多态扩展了类型的这个基本概念。当函数可以对任何给定类型的任何值进行一致操作时，它将获得多态类型。例如，反转一个链表可以对所有链表统一执行，而与链表中存储的值的类型无关。
- SML提供了一个易于使用的异常机制来处理异常条件。在代码中的任何时刻，都可能引发异常，其结果是中止当前操作并将控制权返回给为该异常定义的最后一个异常处理程序。异常可以包含任意值，包括函数。

These basic features of the language are complemented by advanced facilities for the management of large-scale programs. SML boasts a state-of-the-art module system, based on the notions of structures (containing the actual code), signatures (the type of structures) and functors (parametrized structures).

语言的这些基本特性是由管理大型项目的先进设施补充的。SML拥有一个最先进的模块系统，它基于结构(包含实际代码)、签名(结构类型)和仿函数(参数化结构)的概念。

A concrete instance of the use of the module system is the definition of the Basis Library, a set of standard modules providing basic facilities such as input and output, mathematical operations, string and list processing, and basic operating system interfaces. The Basis Library is supported by all compliant implementations of SML.

模块系统使用的一个具体实例是Basis库的定义，它是一组提供输入输出、数学运算、字符串和列表处理等基本设施的标准模块，以及基本操作系统接口。所有兼容的SML实现都支持基础库。

In addition to those key features of the language, SML provides facilities that ease the programming burden. Although the language is statically typed, the programmer rarely needs to write down type annotations in the code, as most types can be inferred by the compiler. Moreover, the management of memory is automatic, with a garbage collector in charge of reclaiming memory when data is not used anymore. This eliminates most problems surrounding stray pointers in languages with explicit memory management, such as C and C++.

除了该语言的这些关键特性之外，SML还提供了减轻编程负担的工具。尽管该语言是静态类型的，程序员很少需要在代码中写下类型注释，因为大多数类型都可以由编译器推断出来。此外，内存的管理是自动的，当数据不再使用时，垃圾收集器负责回收内存。这消除了显式内存管理语言(如C和c++)中围绕游离指针的大多数问题。

### 1.2 Standard ML of New Jersey

SML/NJ is an interactive compiler for SML. It is interactive in that the compiler sports a toplevel loop which compiles declarations and expressions entered by the user. Such entries are compiled to native machine code, and then executed. Compiled code can be exported to a file and turned into an executable. This process contrasts with most compilers for traditional languages, which are usually batchoriented: the compiler is invoked on a set of files and produces object-code in a file. It is also in contrast with interpreters for various languages, where the expression are not compiled to native code and executed, but rather stepped through by the evaluator. SML/NJ provides a convenient blend of efficiency and flexibility.

SML/NJ是一个用于SML的交互式编译器。它是交互式的，因为编译器运行一个顶级循环，用来编译用户输入的声明和表达式。这些条目被编译成本机机器码，然后执行。编译后的代码可以导出到文件并转换为可执行文件。这个过程与传统语言的大多数编译器不同，传统语言通常是面向批处理的:编译器在一组文件上调用，并在文件中生成对象代码。它还与用于各种语言的解释器形成了对比，在解释器中，表达式不是被编译成本地代码并执行，而是由求值器逐步执行。SML/NJ提供了效率和灵活性的方便结合。

SML/NJ provides libraries of modules beyond the Basis Library, modules implementing commonly used data structures such as hash tables, dynamic arrays and search trees, algorithms such as sorting, and packages such as regular expressions, HTML parsing and prettyprinting.

SML/NJ提供了基础库之外的模块库，模块实现了常用的数据结构(如哈希表、动态数组和搜索树)、算法(如排序)和包(如正则表达式)、HTML解析和漂亮打印。

SML/NJ supplies tools for managing projects. The compilation manager CM keeps track of dependencies between the various modules making up a project and is the preferred way of managing the compilation of an application. Roughly speacking, an application can be defined by a file listing the various components of the application. CM provides all the benefits of the Unix tools make and makedepend, but specialized to SML and automatically tracking dependencies between modules. CM also allows for hierarchical descriptions of application components, something make is known to have problems with.

SML/NJ提供项目管理工具。编译管理器CM跟踪组成项目的各个模块之间的依赖关系，是管理应用程序编译的首选方法。粗略地说，应用程序可以通过列出应用程序的各种组件的文件来定义。CM提供了Unix make和make depend工具的所有优点，但是专门用于SML，并自动跟踪模块之间的依赖关系。CM还允许对应用程序组件进行分层描述，这是已知的存在问题的地方。

The tools ML-Lex and ML-Yacc are the SML/NJ versions of the popular lex and yacc tools (or flex and bison) for Unix. ML-Lex is used to generate lexical analysers from descriptions of the tokens to recognize, and ML-Yacc generates parsers from descriptions of a grammar. Other tools exist for more specialized compiler-writing activities, such as MLBurg, a code-generator generator. ML-RISC, not properly speaking a tool, is a backend for code generation used by SML/NJ itself. Moreover, as we shall see later in these notes, SML/NJ is itself quite suited for writing tools.

工具ML-Lex和ML-Yacc是流行的Unix lex和yacc工具(或flex和bison)的SML/NJ版本。使用ML-Lex根据要识别的标记的描述生成词汇分析器，使用ML-Yacc根据语法的描述生成解析器。还有其他用于更专门的编译器编写活动的工具，如MLBurg，代码生成器生成器。不确切地说，ML-RISC是一个工具，它是SML/NJ本身用于生成代码的后端。此外，我们将在后面的注释中看到，SML/NJ本身非常适合编写工具。

SML/NJ supports a powerful library for concurrent programming, CML, which is based on a notion of very lightweight threads and first-class synchronous operations, providing power and flexibility at very low overhead cost. EXene is a graphical interface toolkit for X-Windows implemented in CML.

SML/NJ支持一个用于并发编程的强大库CML，它基于非常轻量级的线程和一级同步操作的概念，以非常低的开销成本提供强大的功能和灵活性。EXene是一个用CML实现的X-Windows图形界面工具包。

### 1.3 History of the system

The SML/NJ project was started in 1986 by David MacQueen at Bell Laboratories and Andrew Appel at Princeton University. Initially a project to build a SML front end for research purposes, it evolved into a complete and portable programming environment for SML, with the purpose of being employed as a “language laboratory” for programming language research. In order to back claims efficiency and to motivate the implementation of useful optimizations, the decision was made to write all supporting library code in SML. The only part of the system not implemented in SML is the runtime system (written in C), in charge mostly of the memory allocation, the garbage collection and communication with the underlying operating system.

SML/NJ项目始于1986年，由贝尔实验室的David MacQueen和普林斯顿大学的Andrew Appel发起。它最初是一个为了研究目的而构建SML前端的项目，后来发展成为一个完整的、可移植的SML编程环境，目的是作为一个用于编程语言研究的“语言实验室”。为了保证声明的有效性并促进有用优化的实现，我们决定用SML编写所有支持库代码。系统中唯一没有在SML中实现的部分是运行时系统(用C编写)，主要负责内存分配、垃圾收集和与底层操作系统的通信。

With the convergence towards satisfying the 1997 revision of SML, version 110 came out in January 1998. Various patches to the release version corrected bugs and updated libraries. At the time of writing, the current patch release version is 110.0.6. Release version 110 is the standard stable version for general use. Internal infrastructure changes and experimental features are being tested in a series of working versions not intended to be stable or generally usable. At the time of writing, the current working version is 110.29, with major changes in the intermediate representation language. Eventually, once the working versions converge to a workable and stable system, release 111 will come out incorporating the improvements.

为了满足1997年SML的修订，1998年1月推出了110版本。发布版本的各种补丁纠正了错误并更新了库。在撰写本文时，当前的补丁发布版本是110.0.6。发布版本110是通用的标准稳定版本。内部基础设施更改和实验特性正在一系列工作版本中进行测试，这些版本并不打算保持稳定或普遍可用。在撰写本文时，当前的工作版本是110.29，中间表示语言发生了重大变化。最终，一旦工作版本汇聚成一个可行且稳定的系统，第111版将会包含这些改进。

### 1.6 Getting started

The “-” is called the (primary) prompt, and indicates that the compiler is ready to process input. The SML language is expression-based, meaning that the computational model is that of evaluating expressions. Evaluating an expression can be as simple as computing a given value (for example, the number of different ways you can pick k object from a bag containing n objects), or have complex side effects (for example, printing information to the terminal, or creating a user interface). Although expressions which correspond to whole applications are very complex, SML provides mechanisms to help manage the complexity, in the form of both data and functional abstractions.

“-”称为(主)提示符，表示编译器已准备好处理输入。SML语言是基于表达式的，这意味着计算模型是对表达式求值的模型。对表达式求值可以像计算给定值一样简单(例如，从包含n个对象的包中选择k个对象的不同方法的数量)，也可以具有复杂的副作用(例如，将信息打印到终端，或创建用户界面)。尽管与整个应用程序对应的表达式非常复杂，但SML提供了以数据和功能抽象的形式帮助管理复杂性的机制。

We begin by looking at simple expressions. The simplest expression is a constant, such as 1, 2, 3, or true or false, or 3.141592. To evaluate an expression at the prompt (we also use the term “evaluate an expression at top level”), you simply enter the expression to evaluate, followed by a “;” (semicolon) and pressing RETURN. The semicolon indicates to the compiler that you have finished entering the expression and that it should start evaluating. If a semicolon is not entered, but RETURN is pressed, the compiler will present a new prompt (the secondary prompt, by default “=”) and again ask for input, which will be considered a continuation of the previously entered expression. In this way, expressions spanning multiple lines can be easily entered.2 In any case, let’s evaluate some simple expressions:

```sml
- 1;
val it = 1 : int
- true;
val it = true : bool
- 3.141592;
val it = 3.141592 : real
```

我们从简单的表达式开始。最简单的表达式是一个常数，例如1、2、3、真或假或3.141592。要在提示符处对表达式求值(我们也使用术语“在顶层对表达式求值”)，只需输入要求值的表达式，后跟一个“;”(分号)，然后按RETURN。分号向编译器表明您已经完成了表达式的输入，并且应该开始求值。如果没有输入分号，但按下了RETURN，编译器将显示一个新的提示符(默认情况下是次要提示符“=”)，并再次请求输入，该输入将被认为是先前输入的表达式的延续。通过这种方式，可以很容易地输入跨多行的表达式。在任何情况下，让我们计算一些简单的表达式:

Although it is not clear from the above interaction, SML/NJ is a compiler, albeit an interactive one. When you enter an expression to evaluate, SML/NJ compiles the expression to machine code and then executes it. It turns out that for constants, evaluation is trivial: a constant simply evaluates to itself.

尽管从上面的交互不能清楚地看出SML/NJ是一个编译器，尽管是一个交互的编译器。当您输入要计算的表达式时，SML/NJ将该表达式编译为机器码，然后执行它。对于常量来说，求值是很简单的:一个常量简单地求值给它自己。

Studying the above interaction, you notice that the compiler has returned more information than just the resulting value of the evaluation. Every value in SML belongs to a type, which is really a set of values. Thus values 1, 2 belong to the type int of integers, true and false belong to the type bool of booleans, and 3.141592 belongs to the type real of floating-point numbers.

研究上面的交互，您会注意到编译器返回的信息不仅仅是求值的结果值。SML中的每个值都属于一个类型，它实际上是一组值。因此，值1,2属于整型的int类型，true和false属于布尔型的bool类型，3.141592属于浮点数的实数类型。

Other compilers and environments for SML are available. Up until recently, Harlequin Ltd sold MLWorks, a commercial SML environment. A lightweight byte-code interpreter called MoscowML is also available. A compiler called MLKit [14] was developped at Diku to provide a reference implementation of SML by directly translating the semantics of the Definition. The compiler is currently used as a testbed for the study of the very interesting notion of region-based memory management, which allows the system to efficiently reclaim memory without requiring the use of a garbage collector [108]. Finally, the TIL project [105] at Carnegie-Mellon University is working on a type-based compiler that carries type information down to the lowest levels of the compilation process. Subsequent work by Morrisett and others at Cornell University on Typed Assembly Language (TAL) showed how types can be pushed down to the level of the assembly language [82, 80]. Until recently, a compiler from ML to Java called MLJ was under development [?]. Finally, an optimizing compiler called ML-Ton is being developed by Stephen Weeks [?]. This compiler uses whole-program analysis to generate extremely efficient machine code. The price to pay for such efficiency is that separate compilation is not supported.

还有其他用于SML的编译器和环境。直到最近，禾林有限公司出售了MLWorks，一个商业SML环境。还有一种轻量级的字节码解释器MoscowML。Diku开发了一个名为MLKit[14]的编译器，通过直接翻译定义的语义来提供SML的参考实现。编译器目前被用作研究非常有趣的基于区域的内存管理概念的试验台，它允许系统在不需要使用垃圾收集器的情况下有效地回收内存[108]。最后，Carnegie-Mellon大学的TIL项目[105]正在开发一种基于类型的编译器，它将类型信息传输到编译过程的最低级别。Morrisett和康奈尔大学的其他人关于类型化汇编语言(TAL)的后续工作展示了如何将类型下推到汇编语言的级别[82,80]。直到最近，一种从ML到Java的编译器MLJ还在开发中[?]。最后，Stephen Weeks[?]正在开发一个名为ML-Ton的优化编译器。该编译器使用整个程序分析来生成极其高效的机器码。这种效率的代价是不支持单独编译。

Several dialects of ML derive from the original ML aside from SML. Most proeminent is Leroy’s Objective Caml (OCaml) [63], a descendant of Caml [24] developped at INRIA. OCaml provides an efficient native code compiler, and a portable byte-code interpreter. OCaml also has support for object-oriented programming, based on the work of R´emy and Vouillon [92].

除了SML之外，ML的一些方言都是从原始的ML派生出来的。最突出的是Leroy的Objective Caml (OCaml) [63]， INRIA开发的Caml[24]的后代。OCaml提供了一个高效的本机代码编译器和一个可移植的字节码解释器。OCaml还支持面向对象编程，基于R ' emy和Vouillon的工作[92]。

Since the introduction of the polymorphic type discipline, many languages have picked up on the idea. A relevant branch of developpment is that of purely functional languages, languages which do not allow side-effects such as assignment and exceptions. Such languages are also typically based on a lazy evaluation mechanism, where parameters to functions are not evaluated until they are needed by the body of the function. In contrast, SML is strict (or eager). Lazy ML was a lazy purely functional version of ML developped by Augustsson and Johnsson in the early 80’s [10, 11]. Modern lazy languages that are not based on ML but borrowing the basic polymorphic type discipline include Miranda [109] and Haskell [88].

自从引入多态类型规程以来，许多语言已经开始采用这种思想。一个相关的开发分支是纯函数式语言，这些语言不允许副作用，如赋值和异常。这类语言通常还基于一种惰性求值机制，在这种机制中，只有在函数体需要时才计算函数的参数。相反，SML是严格的(或迫切的)。Lazy ML是Augustsson和Johnsson在80年代早期开发的ML的一个懒惰的纯功能性版本[10,11]。现代的惰性语言不是基于ML，而是借用了基本的多态类型规则，包括Miranda[109]和Haskell[88]。

The recent working versions of SML/NJ considerably modified the internal workings of the compiler. A fully-typed intermediate language called FLINT developped at Yale by Shao and others [98] is being integrated. Similarly, the code generation backend is being reimplemented using George’s ML-RISC, a powerful library [36] that uses the full power of the SML module system to factor out commonalities between code generation for various architectures.

SML/NJ的最新工作版本在很大程度上修改了编译器的内部工作方式。邵等人在耶鲁大学开发的名为FLINT的全类型中间语言[98]正在被整合。类似地，代码生成后端正在使用George的ML-RISC重新实现，这是一个强大的库[36]，它使用SML模块系统的全部功能来提取不同架构的代码生成之间的共性。

## Chapter 2 The Core Language

In this chapter, we give a brief description of the core language of SML. The core language is the small-scale part of SML, in charge of expressing types and computations. Managing the name space and separating programs into independent communicating units is the province of the module system described in Chapter 3.

在本章中，我们简要描述了SML的核心语言。核心语言是SML中较小的部分，负责表示类型和计算。管理命名空间和将程序分离为独立的通信单元是第三章中描述的模块系统的职责所在。

As we noted in the introduction, SML is a mostly functional language, based on the notion of expression evaluation. A program is really an expression to be evaluated, returning a value. The expression may be simply to compute a mathematical value such as the roots of a polynomial, or factoring an integer, or it can be large and have visible side-effects, such as printing data or displaying a user interface for interaction with the user. A program is usually not made up of a single expression, but consists of a set of function definitions that are used by a main expression, as well as type and value declarations. In contrast, a program written in a traditional imperative language is a set of procedures made up of sequences of commands, used by a main procedure that is invoked when the program is executed.

正如我们在介绍中指出的，SML是一种函数式语言，基于表达式求值的概念。程序实际上是一个要计算的表达式，返回一个值。表达式可以简单地计算一个数学值，比如多项式的根，或者分解一个整数，也可以很大并且有可见的副作用，比如打印数据或显示用户界面以便与用户交互。程序通常不是由单个表达式组成，而是由主表达式使用的一组函数定义以及类型和值声明组成。相比之下，用传统命令式语言编写的程序是一组由命令序列组成的过程，由在程序执行时调用的主过程使用。

We will presently describe the syntax and operations for writing expressions, as well as the associated type information. We then introduce more complex features such as compound types and functions, as well as imperative features such as references and exceptions.

我们将描述用于编写表达式的语法和操作，以及相关的类型信息。然后介绍更复杂的特性，如复合类型和函数，以及命令式特性，如引用和异常。

### 2.1 Basic types and expressions

Expressions are ways to express computations, and computations act on values. Every value has a type, such as integer or real, denoting what type of value it is. Formally, a type can be viewed as a set of values, and the type of a value simply indicates which set the value belongs to. Generalizing, every expression has a type, namely the type of the value it evaluates to. We start our description of the language by talking about the basic values and their types. Evaluation in SML is eager. When an operation is applied to arguments, the arguments are first evaluated to values, and the operation is applied to the values so obtained. Syntactic forms have their own evaluation rules. In addition, any expression can be annotated with a type, as in exp:ty, or (exp):ty, where exp is an expression and ty is a type, which the compiler can then check.

表达式是表示计算的方法，而计算作用于值。每个值都有一个类型，比如integer或real，表示它是什么类型的值。形式上，类型可以看作是一组值，值的类型只是表示该值属于哪个设置。一般化后，每个表达式都有一个类型，即它求值的类型。我们从讨论基本值及其类型开始描述该语言。SML中的评估是迫切的。当操作应用于参数时，首先将参数计算为值，然后将操作应用于所获得的值。句法形式有自己的评价规则。此外，任何表达式都可以用类型进行注释，如exp:ty或(exp):ty，其中exp是表达式而ty是类型，然后编译器可以检查它。

The simplest value is () (pronounced unit), and it has type unit. The only value of type unit is (). Although seemingly useless, unit-valued expressions are typically used only for their side-effect — for all intents and purposes, they do not return a value.

最简单的值是()(发音为unit)，它的类型是unit。类型unit的唯一值是()。尽管看起来毫无用处，但单元值表达式通常只用于其副作用——无论出于什么目的，它们都不返回值。

Other basic values are booleans. A boolean is a value true or false of type bool. Boolean expressions can be built using the syntactic forms andalso and orelse, which are short-circuiting: e1 andalso e2 evaluates e1 to a value v, and if v is false, the whole expression evaluates to false; it v is true, e2 is evaluated. The orelse form evaluates similarly. The operation not is also available. The syntactic form if e1 then e2 else e3 is used to branch on the value of a boolean expression e1: if e1 evaluates to true, then e2 is evaluated otherwise e3 is evaluated. Both e2 and e3 are required to be expressions of the same type.

其他基本值是布尔值。布尔值是bool类型的true或false值。布尔表达式可以建立使用语法形式和also和orelse，这是短路的e1和e2 e1计算一个值v，如果v是假的，整个表达式计算为假;v是对的，e2被求值。orelse表单的计算结果类似。操作not也可用。语法形式if e1 then e2 else e3用于对布尔表达式e1的值进行分支:如果e1计算结果为true，则e2被计算，否则e3被计算。e2和e3都需要是相同类型的表达式。

Integers have type int. Negating an integer is done by applying the  operation to an integer expression. Thus, 􀀀5 is written 5. Other common operations on integers are available: addition (+), subtraction (-) and multiplication (*), all infix operations. Division is not implemented for integers, since in general division produces a real number. An integer division operation div (also infix) is available and discards the decimal portion of the result of the division. One can also compare integers with the operations =, <,<=, >, >=, resulting in a boolean value. SML does not impose prescribed sizes for integers, but SML/NJ uses 31-bit integers that can represent integer in the range 1073741824 to 1073741823. Chapter 4 gives alternatives if larger integers are desired. Users used to languages such as C providing bit-twiddling operations on integers will note that such operations are not available for SML integers.

Integers 有 类型 int. Negating 整数 是 通过 应用 整数 expression.  操作Thus, 􀀀5 是 5. 写 的对整数的其他常见操作还有:加法(+)、减法(-)和乘法(*)，都是中缀操作。除法不用于整数，因为一般情况下除法产生的是实数。一个整数除法操作div(也称为中缀)可用，它会丢弃除法结果的小数部分。您还可以将整数与操作=、<、<=、>、>=进行比较，从而得到一个布尔值。SML 不 征收 规定 大小 integers, 但 SML/NJ 使用 31 位 整数 能 表示 的 整数 范围 1073741824 至 1073741823.第4章给出了需要更大整数时的备选方案。习惯在整数上提供位旋转操作的语言(如C)的用户会注意到，这种操作对于SML整数不可用。

Floating point numbers have type real. They can be written as 3.141592 or as 3141592e6. They can also be negated by the operation , and the standard operations such as +, -, * and / (division) are available. A special real value NaN exists, to represent results of computations which do not define a real number (for example, the square root of a negative number). The special value inf represents 1, for example as the result of dividing a non-zero number by zero. Real numbers can be compared by <, <=, > and >=, but cannot be compared for equality using =.1. Various operations are available for approximating equality tests in the Basis Library 4. A final point to mention is that reals and integers are not interchangeable: if an integer is for example added to a real, a type error occurs. No automatic coercion of either an integer to a real or a real to an integer is performed. Coercion functions are available through the Basis Library, but they must be applied by the programmer. Although seemingly an annoying restriction, this prevents many hard-to-find problems and fits in the general safety-first approach underlying the language.

浮点数的类型是实数。They 可以 写成 3.141592 或 3141592 e6.They , 也 可以 否定 的 操作 和 标准 操作 , 比如 +, , * 和 / (division) available.存在一个特殊的实值NaN，用于表示没有定义实数(例如，负数的平方根)的计算结果。特殊值inf表示1，例如表示非零数除以零的结果。实数可以通过<、<=、>和>=进行比较，但不能通过=.1进行相等比较。在Basis Library 4中，可以使用各种操作来近似相等性测试。最后要提到的一点是实数和整数是不可互换的:例如，如果一个整数被添加到实数中，就会发生类型错误。不会自动执行将整数强制为实数或将实数强制为整数的操作。强制函数可以通过Basis库使用，但是它们必须由程序员应用。尽管这看起来是一个恼人的限制，但它防止了许多难以发现的问题，并且符合该语言底层的一般安全第一的方法。

Characters have type char, and are written #”a”. Operations ord and chr for converting a character to an integer (its ASCII value) or an integer to a character are provided.

字符类型为char，被写入# " a "。提供了用于将字符转换为整数(其ASCII值)或将整数转换为字符的操作ord和chr。

Strings have type string, and are written in quotes, as ”this is a string”. Top level operations on strings include size to return the length of a string, ˆ to concatenate two strings (an infix operation), and others. Many more operations, such as subscripting to extract a character from a string and so on are available through the Basis Library.

字符串的类型是string，并且用引号括起来，如“这是一个字符串”。顶级操作字符串返回一个字符串的长度,包括大小ˆ连接两个字符串(中缀操作),等等。还有很多操作，比如下标从字符串中提取字符等等，都可以通过Basis库使用。

### 2.2 Tuples and records

### 2.3 Declarations

### 2.4 Pattern matching

### 2.5 Functions

### 2.6 Polymorphism

### 2.7 Recursion

### 2.8 Lists

### 2.9 Equality

### 2.10 References

### 2.11 Exceptions

## Chapter 3 The Module System

### 3.1 Structures

### 3.2 Signatures

### 3.3 Functors

### 3.4 Programming with modules

## Chapter 4 The Basis Library

### 4.1 Overview

### 4.2 Basic types

### 4.3 More on strings

### 4.4 Aggregate types

### 4.5 Input and Output

### 4.6 System Functions

### 4.7 Time and dates

### 4.8 Compatibility with SML’90

## Chapter 5 The Interactive Compiler

### 5.1 Controlling the runtime system

### 5.2 Controlling the compiler

### 5.3 Prettyprinting

### 5.4 Heap images

### 5.5 Unsafe operations

## Chapter 6 The Compilation Manager

### 6.1 Overview of CM

### 6.2 Group hierarchies

### 6.3 Tools

### 6.4 A simple configuration tool

### 6.5 Technicalities

## Chapter 7 The SML/NJ Library

### 7.1 Overview

### 7.2 Types and data structures

### 7.3 Arrays and vectors

### 7.4 Sets and maps

### 7.5 Hash tables

### 7.6 Sorting

### 7.7 Formatting

### 7.8 Handling command-line arguments

### 7.9 Miscellaneous functionality

