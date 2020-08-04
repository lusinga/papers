# Programming in Standard ML

# Part I Overview

StandardMLis a type-safe programming language that embodies many innovative ideas in programming language design. It is a statically typed language, with an extensible type system. It supports polymorphic type inference, which all but eliminates the burden of specifying types of variables and greatly facilitates code re-use. It provides efficient automatic storage management for data structures and functions. It encourages functional (effect-free) programming where appropriate, but allows imperative (effect-ful) programming where necessary. It facilitates programming with recursive and symbolic data structures by supporting the definition of functions by pattern matching. It features an extensible exception mechanism for handling error conditions and effecting non-local transfers of control. It provides a richly expressive and flexible module system for structuring large programs, including mechanisms for enforcing abstraction, imposing hierarchical structure, and building generic modules. It is portable across platforms and implementations because it has a precise definition. It provides a portable standard basis library that defines a rich collection of commonly-used types and routines.

StandardML是一种类型安全的编程语言，它在编程语言设计中体现了许多创新的思想。它是一种静态类型语言，具有可扩展的类型系统。它支持多态类型推断，这几乎消除了指定变量类型的负担，并极大地促进了代码的重用。它为数据结构和功能提供了高效的自动存储管理。它在适当的时候鼓励函数式(无效果)编程，但在必要的时候允许命令式(有效)编程。它通过模式匹配支持函数定义，从而简化了使用递归和符号数据结构的编程。它提供了一个可扩展的异常机制来处理错误条件和影响控制的非本地转移。它为构造大型程序提供了具有丰富表达能力和灵活性的模块系统，包括执行抽象、实施分层结构和构建通用模块的机制。它是跨平台和实现的可移植的，因为它有一个精确的定义。它提供了一个可移植的标准基础库，定义了丰富的常用类型和例程集合。

Many implementations go beyond the standard to provide experimental language features, extensive libraries of commonly-used routines, and useful program development tools. Details can be found with the documentation for your compiler, but here’s some of what you may expect. Most implementations provide an interactive system supporting on-line program development, including tools for compiling, linking, and analyzing the behavior of programs. A few implementations are “batch compilers” that rely on the ambient operating system to manage the construction of large programs from compiled parts. Nearly every compiler generates native machine code, even when used interactively, but some also generate code for a portable abstract machine. Most implementations support separate compilation and provide tools for managing large systems and shared libraries. Some implementations provide tools for tracing and stepping programs; many provide tools for time and space profiling. Most implementations supplement the standard basis library with a rich collection of handy components such as dictionaries, hash tables, or interfaces to the ambient operating system. Some implementations support language extensions such as support for concurrent programming (using messagepassing or locking), richer forms of modularity constructs, and support for “lazy” data structures.

许多实现都超越了标准，提供实验性的语言特性、广泛的常用例程库和有用的程序开发工具。您可以在编译器的文档中找到详细信息，但这里有一些您可能会想到的内容。大多数实现都提供了一个支持在线程序开发的交互式系统，包括编译、链接和分析程序行为的工具。一些实现是“批处理编译器”，依赖于环境操作系统从编译部分管理大型程序的构建。几乎每个编译器都会生成本机机器码，即使是交互使用时也是如此，但有些编译器还会为可移植的抽象机器生成代码。大多数实现都支持独立编译，并提供用于管理大型系统和共享库的工具。一些实现提供跟踪和步进程序的工具;许多公司提供时间和空间分析工具。大多数实现都使用丰富的便利组件(如字典、哈希表或环境操作系统接口)来补充标准基础库。有些实现支持语言扩展，比如支持并发编程(使用消息传递或锁定)、更丰富的模块化构造形式，以及支持“惰性”数据结构。

## Chapter 1 Programming in Standard ML

# Part II The Core Language

All Standard ML is divided into two parts. The first part, the core language, comprises the fundamental programming constructs of the language — the primitive types and operations, the means of defining and using functions, mechanisms for definining new types, and so on. The second part, the module language, comprises the mechanisms for structuring programs into separate units and is described in Part III. Here we introduce the core language.

所有的标准ML分为两部分。第一部分是核心语言，包括该语言的基本编程构造——基本类型和操作、定义和使用函数的方法、定义新类型的机制，等等。第二部分，模块语言，包括将程序构造成单独单元的机制，并在第三部分中描述。这里我们介绍一下核心语言。

## Chapter 2 Types, Values, and Effects

### 2.1 Evaluation and Execution

Most familiar programming languages, such as C or Java, are based on an imperative model of computation. Programs are thought of as specifying a sequence of commands that modify the memory of the computer. Each step of execution examines the current contents of memory, performs a simple computation, modifies the memory, and continues with the next instruction. The individual commands are executed for their effect on the memory (which we may take to include both the internal memory and registers and the external input/output devices). The progress of the computation is controlled by evaluation of expressions, such as boolean tests or arithmetic operations, that are executed for their value. Conditional commands branch according to the value of some expression. Many languages maintain a distinction between expressions and commands, but often (in C, for example) expressions may also modify the memory, so that even expression evaluation has an effect.

大多数熟悉的编程语言，如C或Java，都是基于命令式计算模型的。程序被认为是指定一系列命令来修改计算机的内存。执行的每个步骤检查内存的当前内容，执行一个简单的计算，修改内存，然后继续执行下一条指令。每个命令的执行是为了它们对内存的影响(我们可以采取包括内部内存和寄存器以及外部输入/输出设备)。计算的进程由表达式的求值控制，例如为其值执行布尔测试或算术运算。条件命令根据某个表达式的值进行分支。许多语言保持表达式和命令之间的区别，但通常(例如在C语言中)表达式也可能修改内存，因此甚至表达式求值也会产生影响。

Computation in ML is of a somewhat different nature. The emphasis in ML is on computation by evaluation of expressions, rather than execution of commands. The idea of computation is as a generalization of your experience from high school algebra in which you are given a polynomial in a variable x and are asked to calculate its value at a given value of x. We proceed by “plugging in” the given value for x, and then, using the rules of arithmetic, determine the value of the polynomial. The evaluation model of computation used in ML is based on the same idea, but rather than restrict ourselves to arithmetic operations on the reals, we admit a richer variety of values and a richer variety of primitive operations on them.

ML格式的计算具有某种不同的性质。ML的重点是通过表达式求值进行计算，而不是执行命令。计算的想法是作为你的经验从高中代数的推广,你给出了一个多项式在一个变量x和要求计算它的值在给定值x,我们进行的“插入”x的给定值,然后使用算术规则,确定多项式的值。ML中使用的计算计算的计算模型基于相同的思想，但我们不局限于对实数的算术运算，而是允许对它们进行更丰富的值和更丰富的原始操作。

The evaluation model of computation enjoys several advantages over the more familiar imperative model. Because of its close relationship to mathematics, it is much easier to develop mathematical techniques for reasoning about the behavior of programs. These techniques are important tools for helping us to ensure that programs work properly without having to resort to tedious testing and debugging that can only show the presence of errors, never their absence. Moreover, they provide important tools for documenting the reasoning that went into the formulation of a program, making the code easier to understand and maintain.

计算的评估模型与我们更熟悉的命令式模型相比有几个优点。由于它与数学的密切关系，开发用于推理程序行为的数学技术要容易得多。这些技术是帮助我们确保程序正常工作的重要工具，而不必诉诸于冗长乏味的测试和调试，这些测试和调试只能显示错误的存在，而不能显示错误的缺失。此外，它们还提供了重要的工具，用于记录程序形成过程中的推理，使代码更容易理解和维护。

What is more, the evaluation model subsumes the imperative model as a special case. Execution of commands for the effect on memory can be seen as a special case of evaluation of expressions by introducing primitive operations for allocating, accessing, and modifying memory. Rather than forcing all aspects of computation into the framework of memory modification, we instead take expression evaluation as the primary notion. Doing so allows us to support imperative programming without destroying the mathematical elegance of the evaluation model for programs that don’t use memory. As we will see, it is quite remarkable how seldom memory modification is required. Nevertheless, the language provides for storage-based computation for those few times that it is actually necessary.

此外，评价模型还将命令式模型作为特例纳入其中。通过引入分配、访问和修改内存的基本操作，可以将命令对内存的影响的执行看作是表达式求值的一种特殊情况。我们没有强迫所有方面的计算进入内存修改的框架，而是将表达式求值作为主要的概念。这样做允许我们支持命令式编程，而不会破坏不使用内存的程序的计算模型的数学优雅。正如我们将看到的，需要修改内存的情况非常少。然而，该语言提供了一些实际需要的基于存储的计算。

### 2.2 The ML Computation Model

Computation in ML consists of evaluation of expressions. Each expression has three important characteristics:
- It may or may not have a type.
- It may or may not have a value.
- It may or may not engender an effect.

These characteristics are all that you need to know to compute with an expression.
