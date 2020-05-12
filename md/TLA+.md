# TLA+

## A High-Level View of TLA+

### Introduction

TLA+ is a language for modeling software above the code level and hardware above the circuit level.  It has an IDE (Integrated Development Environment) for writing models and running tools to check them.  The tool most commonly used by engineers is the TLC model checker, but there is also a proof checker.  TLA+ is based on mathematics and does not resemble any programming language.  Most engineers will find PlusCal, described below, to be the easiest way to start using TLA+.

TLA+是一种用于在代码层之上建模软件和在电路层之上建模硬件的语言。它有一个IDE(集成开发环境)，用于编写模型和运行工具来检查它们。工程师最常用的工具是TLC模型检查器，但也有一个验证检查器。TLA+基于数学，不像任何编程语言。大多数工程师会发现下面描述的PlusCal是开始使用TLA+的最简单方法。

TLA+ models are usually called specifications.  They are called models in this introduction.

TLA +模型通常称为规范。在本介绍中，它们被称为模型。

### PlusCal

PlusCal is a language for writing algorithms—especially concurrent and distributed ones.  It is meant to replace pseudocode with precise, testable code.  PlusCal looks like a simple toy programming language, but with constructs for describing concurrency and nondeterminacy.  It is infinitely more expressive than any programming language because any mathematical formula can be used as a PlusCal expression.  A PlusCal algorithm is translated into a TLA+ model that can be checked with the TLA+ tools.  Because it looks like a programming language, most engineers find PlusCal easier to learn than TLA+.  But because it looks like a programming language, PlusCal cannot structure complex models as well as TLA+ can.

PlusCal是一种用于编写算法的语言，特别是并发和分布式算法。它的目的是用精确的、可测试的代码替换伪代码。PlusCal看起来像一个简单的玩具编程语言，但它具有用于描述并发性和不确定性的结构。它比任何编程语言都更有表现力，因为任何数学公式都可以用作PlusCal表达式。PlusCal算法被转换成TLA+模型，可以使用TLA+工具进行检查。因为它看起来像一种编程语言，大多数工程师发现PlusCal比TLA+更容易学习。但是因为它看起来像一种编程语言，所以PlusCal不能像TLA+那样构造复杂的模型。

### Models

Computers and computer networks are physical objects whose behaviors are described by continuous physical laws.  They differ from most other kinds of physical objects in that their behaviors are naturally modeled as sets of discrete events.  Programming, software engineering, and most of computer science is concerned with models in which a behavior of a system is described as a set of discrete events. No model is a completely accurate description of a real system.  A model is a description of some aspect of the system, written for some purpose. 

计算机和计算机网络是物理对象，其行为由连续物理定律描述。它们与大多数其他类型的物理对象的不同之处在于，它们的行为被自然地建模为离散事件的集合。编程、软件工程和大多数计算机科学都与模型有关，在模型中，系统的行为被描述为一组离散事件。没有一个模型是对真实系统的完全准确的描述。模型是为了某种目的而编写的对系统某个方面的描述。

TLA+ is state-based, meaning that it models an execution of a system as a sequence of states, where an event is represented by a pair of consecutive states.  We call a sequence of states a behavior; and we call a pair of consecutive states a step rather than an event.  A system is modeled as the set of behaviors describing all of its possible executions.

TLA+是基于状态的，这意味着它将系统的执行建模为状态序列，其中一个事件由一对连续的状态表示。我们称一系列的状态为行为;我们称一对连续的状态为一个步骤，而不是一个事件。系统被建模为描述其所有可能执行的行为集。

### Modeling Above the Code Level

TLA+ is used to model systems above the code level.  To see what this, means consider Euclid's algorithm for computing  GCD(M,N) , the greatest common divisor of two positive integers  M  and  N . Here is the algorithm:
- Let  x  equal  M  and  y  equal  N .
- Repeated subtract the smaller of  x  and  y  from the larger.
- Stop when  x  and  y  have the same value. That value is the GCD of  M  and  N .

TLA+用于在代码级之上建模系统。为了理解这一点，可以考虑欧几里德的GCD(M,N)算法，这是两个正整数M和N的最大公约数。算法如下:
- 设x = M y = N。
- 重复地从较大的x和y中减去较小的。
- 当x和y值相同时停止。这个值就是M和N的GCD。

This description is above the code level.  Code to compute  GCD(M,N)  would have to specify additional details such as the type of  M  and  N  (is it  int?  long?  BigInteger?) and what to do if  M  or  N  is not positive (throw an exception? return an error value?).

这个描述在代码级别之上。计算GCD(M,N)的代码必须指定额外的细节，比如M和N的类型(它是int型的吗?很长时间吗?如果M或N不为正(抛出异常?返回一个错误值?)。

A programmer who didn't know Euclid's algorithm might decide to compute  GCD(M,N)  by a naive algorithm that sets  x  to the minimum of  M  and  N  and keeps decreasing  x  until it divides both  M  and  N .  The best coder in the world will not produce a good GCD program by coding the naive algorithm.  Moreover, thinking in terms of code makes it harder for a programmer to find a better algorithm.  Finding a good algorithm requires thinking above the code level.

一个不知道欧几里得算法的程序员可能会决定用一个幼稚的算法来计算GCD(M,N)，该算法将x设为M和N的最小值，并不断减少x，直到它同时除以M和N。即使是世界上最好的编码者，也不可能通过编写简单的算法就能写出好的GCD程序。此外，从代码的角度思考会让程序员更难找到更好的算法。找到一个好的算法需要在代码层之上进行思考。

No one writes a piece of code without first having a high-level model of what the code should do and how it should do it.  A programmer never starts by deciding to declare some variables, adding a  while  statement, then adding an  if  statement, and so on—only discovering when finished that she's written a sorting program.  But programmers rarely start with a precise model of the code. Having only a vague, incomplete model leads to basic design errors that the best coding won't correct.

没有人在编写一段代码之前，首先要有一个关于代码应该做什么以及如何做的高级模型。程序员从来不会先决定声明一些变量，然后添加while语句，再添加if语句，等等——直到完成之后才发现她已经编写了一个排序程序。但是程序员很少从一个精确的代码模型开始。如果只有一个模糊的、不完整的模型，就会导致基本的设计错误，即使最好的编码也无法纠正这些错误。

TLA+ is a language for writing precise high-level models of what code does and how it does it.  Most programmers believe that precise models are good only for tiny well-defined problems like computing the GCD, but are useless for implementing complex systems.  They're wrong.  The more complex a system is, the more important it is to make it as simple as possible.  In complex systems, simplicity isn't achieved by coding tricks.  It's achieved by rigorous thinking above the code level.

TLA+是一种用于编写精确的高级模型的语言，该模型描述了代码的功能和实现方式。大多数程序员认为，精确的模型只适用于像计算GCD这样定义良好的小问题，而不适用于实现复杂的系统。他们错了。一个系统越复杂，使它尽可能简单就越重要。在复杂的系统中，简单性不是通过编码技巧来实现的。它是通过代码层之上的严格思考实现的。

In one industrial project, starting with a TLA+ model reduced the size of a real-time operating system's code by a factor of ten.  Such a reduction in code size isn't obtained by better coding; it comes from thinking rigorously above the code level.

在一个工业项目中，使用TLA+模型使实时操作系统的代码减少了10倍。这样的代码缩减并不是通过更好的编码来实现的;它来自于代码层之上的严格思考。

Writing a model above the code level doesn't prevent coding errors.  Many methods and tools have been developed for finding coding errors, and they should be used.  But they are not good for finding errors in the high-level model from which the code is derived.  And it's impossible to test that a high-level model is implemented correctly if the model is just a vague idea in the programmer's mind, with no precise description.

编写高于代码级别的模型并不能防止编码错误。已经开发了许多用于查找编码错误的方法和工具，应该使用它们。但是它们不适合在代码派生的高级模型中查找错误。如果模型只是程序员头脑中一个模糊的概念，没有精确的描述，那么测试高级模型是否正确实现是不可能的。

Testing the code is not an effective way to find fundamental design errors—especially in concurrent and distributed systems.  Moreover, a design error found after the code has been written is usually fixed with an ad hoc patch that is unlikely to eliminate all instances of the problem and is likely to introduce new errors.  Design errors should be caught by writing a precise high-level model, before the code is written.

测试代码并不是发现基本设计错误的有效方法——尤其是在并发和分布式系统中。此外，在编写代码之后发现的设计错误通常用一个临时补丁修复，这个补丁不太可能消除问题的所有实例，并且很可能引入新的错误。在编写代码之前，应该通过编写精确的高级模型来捕获设计错误。

### Modeling Concurrent Systems

A concurrent system is one that we think of as composed of multiple concurrently operating components called processes .  A distributed system is a concurrent system in which we think of the processes as being spatially separated, usually communicating with one other by sending messages.

我们认为并发系统是由多个称为进程的并发操作组件组成的系统。分布式系统是一个并发系统，在这个系统中，我们认为进程是在空间上分离的，通常通过发送消息与其他进程通信。

In a state-based model, a state represents the entire physical state of a system.  Some people find it hard to believe that one can or should model a distributed system in terms of a single global state.  Over 40 years of experience has taught me that this is the most generally useful way to model distributed algorithms and systems.

在基于状态的模型中，状态表示系统的整个物理状态。有些人很难相信一个人可以或者应该根据一个单一的全局状态来为一个分布式系统建模。40多年的经验告诉我，这是对分布式算法和系统建模最常用的方法。

### State Machines

Like many state-based methods, TLA+ describes a set of behaviors with two things:
- An initial condition that specifies the possible starting states.
- A next-state relation that specifies the possible steps (pairs of successive states).

像许多基于状态的方法一样，TLA+用两件事描述了一组行为:
- 指定可能启动状态的初始条件。
- 指定可能步骤的下一状态关系(连续状态对)。

They specify the set of behaviors whose first state satisfies the initial condition and whose every step satisfies the next-state relation. 

它们指定了一组行为，其第一个状态满足初始条件，其每一步都满足下一个状态关系。

This kind of model is often called a state machine.  (A finite-state machine is a state machine with a finite set of possible states.  Finite-state machines are not nearly as useful as general state machines.)  A Turing machine is an example of a state machine.  In a deterministic Turing machine, the next-state relation allows at most one next state for any state, and it allows no next state for a terminating state. 

这种模型通常被称为状态机。有限状态机是具有有限可能状态集的状态机。有限状态机不如一般状态机有用。图灵机器是状态机的一个例子。在确定性的图灵机中，下一状态关系最多允许下一个状态用于任何状态，并且不允许下一个状态用于终止状态。

The simplest and most practical method of precisely describing the semantics of a programming language, called operational semantics, essentially consists of showing how to compile a program in the language to a state machine.  Given an operational semantics, any program in the language can be viewed as a state machine.  I suspect that most programmers intuitively think of a program in that way.

精确描述一种编程语言的语义的最简单和最实用的方法，称为操作语义，本质上是展示如何将语言中的程序编译成状态机。给定一个操作语义，该语言中的任何程序都可以看作是一个状态机。我怀疑大多数程序员凭直觉会以这种方式思考程序。

The next-state action specifies what steps may happen; it doesn't specify what steps, if any, must happen.  That requires an additional condition, called a fairness property.  A state machine that models a sequential program usually includes the fairness property that some step must be taken (the behavior must not stop) if the next-state relation allows a step to be taken.  Models of concurrent and distributed programs often have more complicated fairness properties.

下一个状态操作指定可能发生的步骤;它没有指定必须执行哪些步骤(如果有的话)。这需要一个额外的条件，称为公平性属性。为顺序程序建模的状态机通常包含这样的公平性属性:如果下一个状态关系允许执行某个步骤，则必须执行某个步骤(行为不能停止)。并发和分布式程序的模型通常具有更复杂的公平性属性。

A state-machine model without a fairness condition can be used to catch errors of commision, in which the system does something wrong.  It can't be used to catch errors of omission, in which the system fails to do something.  In practice, errors of commission are more numerous and harder to find than errors of omission.  Often, engineers don't bother adding fairness conditions.  Therefore, you should first learn to write the initial condition and next-state relation in your TLA+ models.  Later, you can learn to write fairness conditions.

没有公平条件的状态机模型可以用来捕获合成误差，其中系统做错了一些事情。它不能用于捕获遗漏错误，即系统不能执行某些操作。在实践中，委托错误比不作为错误要多得多，也更难发现。通常情况下，工程师不会费心添加公平条件。因此，您应该首先学会在您的TLA+模型中编写初始条件和下一状态关系。稍后，您可以学习编写公平条件。

### Checking Properties

One reason for modeling a system is to check if it does what we want it to.  We do that by checking if the model satisfies properties that we believe assert that the system does what it should.  TLA+ can assert, and its tools can check, only that some property of an individual behavior is true of every possible behavior of the model.  Thus, TLA+ cannot assert that 99% of all possible behaviors terminate in a correct state.  However, it can assert (and its tools can check) that every possible behavior terminates in a correct state if its initial state belongs to a particular set containing 99% of all possible initial states.

为系统建模的一个原因是检查它是否做了我们想要它做的事情。我们通过检查模型是否满足我们认为系统做它应该做的属性来做到这一点。TLA+可以断言，它的工具可以检查，只有个别行为的某些属性对模型的每个可能行为都是正确的。因此，TLA+不能断言99%的所有可能行为都以正确的状态终止。但是，它可以断言(并且它的工具可以检查)，如果每个可能的行为的初始状态属于包含99%的所有可能初始状态的特定集合，那么每个行为都将以正确的状态终止。

The most useful type of property to check is an invariance property, which asserts that something is true of every state of every possible behavior.  Often, an engineer will check only invariance properties of a model.

要检查的最有用的属性类型是一个不变属性，它断言某物对于每一种可能行为的每一种状态都是正确的。通常，工程师只检查模型的不变性。

For a model containing a fairness condition, you should also check simple properties asserting that something eventually happens—for example, that every execution eventually halts.  Those properties, called liveness properties, are easily expressed in TLA+.

对于包含公平条件的模型，您还应该检查断言最终会发生某些事情的简单属性——例如，每个执行最终都会停止。这些属性称为活性属性，在TLA+中很容易表达。

The rich variety of properties that we want to check for concurrent systems can't all be expressed as invariance and simple liveness properties.  They can be expressed as state machines (possibly with fairness conditions).  A state machine can be viewed as the property that is satisfied by the possible behaviors of the state machine.  We can check whether another state machine satisfies this property.  If it does, we say that the other state machine implements the state machine.

我们想要检查并发系统的丰富多样的属性不能都表示为不变性和简单的活性属性。它们可以表示为状态机(可能带有公平条件)。状态机可以被看作是状态机的可能行为所满足的属性。我们可以检查另一个状态机是否满足这个属性。如果是，我们就说另一个状态机实现了状态机。

In TLA+ there is no formal distinction between a state machine and a property.  Both are described by mathematical formulas.  A state machine is a formula having a particular shape, different from the shape of an invariance or liveness property.  Both satisfying a property and implementing a state machine mean that one formula implies another.

在TLA+中，状态机和属性之间没有正式的区别。两者都可以用数学公式来描述。状态机是具有特定形状的公式，不同于不变性或活性属性的形状。满足属性和实现状态机都意味着一个公式意味着另一个公式。

Today, most engineers check only invariance properties and simple liveness properties.  However, even if you never do it, knowing how it is done explains what it means for a program to implement a model, which can help you avoid making errors in your code. 

现在，大多数工程师只检查不变性和简单的活性性质。但是，即使您从来没有这样做过，了解它是如何完成的也可以解释程序实现模型的意义，这可以帮助您避免在代码中出错。

### The TLA+ Language

TLA+ is based on mathematics and does not resemble a programming language.  Most engineers are familiar with programming languages, but not with precise mathematical notation.  We naturally find what we're familiar with to be simpler than anything else.  It's hard for me to believe that English is not inherently simpler than German.  Upon first seeing a TLA+ model, some engineers find TLA+ intimidating.  Read the Industrial Use of TLA+ page to see that TLA+ is not very hard to learn.

TLA+基于数学，不像编程语言。大多数工程师都熟悉编程语言，但不熟悉精确的数学符号。我们自然会发现我们所熟悉的东西比其他任何东西都要简单。我很难相信英语在本质上并不比德语简单。在第一次看到TLA+模型时，一些工程师发现TLA+令人生畏。阅读TLA+的工业使用页面，可以看到TLA+并不是很难学习。

Using TLA+ teaches you that math is inherently more expressive than programming languages because it can describe a value without having to describe how the value is computed.  For example, it can describe the greatest common divisor (GCD) of two numbers as the largest positive integer that divides both numbers.  This makes it possible to write a model for a specific purpose, abstracting away irrelevant details such as how to calculate the GCD.  (Putting code in a procedure doesn't abstract it away;  it just makes you go elsewhere to read the code, requiring that you understand the semantics of procedure call as well as the code.) 

使用TLA+教会您，数学天生就比编程语言更能表达，因为它可以描述一个值，而不必描述值是如何计算的。例如，它可以将两个数的最大公约数(GCD)描述为两个数的最大正整数。这使得为特定目的编写模型成为可能，抽象出不相关的细节，例如如何计算GCD。(在一个过程中放入代码并不会把它抽象掉;它只是让你去别处阅读代码，要求你理解过程调用的语义和代码一样。)

Starting with PlusCal provides a gentle entry to TLA+. Even if you know TLA+, it's easier to write some models in PlusCal rather than directly in TLA+. But to get the full benefit of thinking mathematically above the code level, you should learn TLA+.

从PlusCal开始提供了TLA+的一个温和入口。即使您知道TLA+，用PlusCal而不是直接用TLA+编写一些模型也更容易。但是要想从代码级以上的数学思维中获得全部好处，您应该学习TLA+。

## Industrial Use of TLA+

### Introduction

### Intel

### Amazon

### Microsoft

### OpenComRTOS

### Other Use
