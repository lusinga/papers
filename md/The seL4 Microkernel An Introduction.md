# The seL4 Microkernel An Introduction

## Chapter 1 What Is seL4?

- seL4 is an operating system microkernel

An operating system (OS) is the low-level system software that controls a computer system’s resources and enforces security. Unlike application software, the OS has exclusive access to a more privileged execution mode of the processor (kernel mode) that gives it direct access to hardware. Applications only ever execute in user mode and can only access hardware as permitted by the OS.

操作系统(OS)是控制计算机系统资源和加强安全性的底层系统软件。与应用程序软件不同，操作系统拥有对处理器更有特权的执行模式(内核模式)的独占访问权，从而可以直接访问硬件。应用程序只能以用户模式执行，并且只能访问操作系统允许的硬件。

An OS microkernel is a minimal core of an OS, reducing the code executing at higher privilege to a minimum. seL4 is a member of the L4 family of microkernels that goes back to the mid-1990s. (And no, seL4 has nothing to do with seLinux.)

操作系统微内核是操作系统的最小内核，将在更高权限下执行的代码减少到最小。seL4是微内核L4家族的成员，该家族可以追溯到20世纪90年代中期。(不，seL4与seLinux没有关系。)

- seL4 is also a hypervisor

seL4 supports virtual machines that can run a fully fledged guest OS such as Linux. Subject to seL4’s enforcement of communication channels, guests and their applications can communicate with each other as well as with native applications.

支持能够运行完全成熟的客户操作系统(如Linux)的虚拟机。根据seL4对通信通道的强制执行，来宾及其应用程序可以相互通信，也可以与本机应用程序通信。

Learn more about what it means that seL4 is a microkernel and its use as a hypervisor in Chapter 2. And learn about real-world deployment scenarios, including approaches for retrofitting security into legacy systems in Chapter 7.

要了解更多关于seL4是微内核的含义以及它作为hypervisor的用法，请参阅第2章。并在第7章中了解真实的部署场景，包括在遗留系统中改进安全性的方法。

- seL4 is proved correct

seL4 comes with a formal, mathematical, machine-checked proof of implementation correctness, meaning the kernel is in a very strong sense “bug free” with respect to its specification. In fact, seL4 is the world’s first OS kernel with such a proof at the code level [Klein et al., 2009].

seL4提供了一个正式的、数学的、机器检查的实现正确性证明，这意味着就其规范而言，内核在很强的意义上是“没有bug的”。事实上，seL4是世界上第一个在代码级别上有这样证明的操作系统内核[Klein et al.， 2009]。

- seL4 is provably secure

Besides implementation correctness, seL4 comes with further proofs of security enforcement [Klein et al., 2014]. They say that in a correctly congured seL4-based system, the kernel guarantees the classical security properties of condentiality, integrity and availability. More about these proofs in Chapter 3.

[Gerwin Klein, June Andronick, Kevin Elphinstone, Toby Murray, Thomas Sewell, Rafal Kolanski, and Gernot Heiser. Comprehensive formal verication of an OS microkernel. ACM Transactions on Computer Systems, 32(1):2:1–2:70, February 2014.]

除了实现正确性之外，seL4还进一步证明了安全性的加强[Klein et al.， 2014]。他们说，在一个正确配置的基于sel4的系统中，内核保证了机密性、完整性和可用性等经典的安全属性。更多关于这些证明在第三章。

- seL4 improves security with fine-grained access control through capabilities

Capabilities are access tokens which support very fine-grained control over which entity can access a particular resource in a system. They support strong security according to the principle of least privilege (also called principle of least authority, POLA). This is a core design principle of highly secure system, and is impossible to achieve with the way access control happens in mainstream systems such as Linux or Windows.

功能是访问令牌，它支持对哪个实体可以访问系统中的特定资源进行非常细粒度的控制。它们根据最小特权原则(也称为最小权威原则，POLA)支持强大的安全性。这是高度安全系统的核心设计原则，而Linux或Windows等主流系统的访问控制方式是不可能实现的。

seL4 is still the world’s only OS that is both capability-based and formally verified,
and as such has a defensible claim of being the world’s most secure OS. More
about capabilities in Chapter 4.

seL4仍然是世界上唯一的既基于能力又经过正式验证的操作系统，因此有理由声称它是世界上最安全的操作系统。更多关于功能的信息请参见第4章。

- seL4 ensures safety of time-critical systems

seL4 is the world’s only OS kernel (at least in the open literature) that has undergone a complete and sound analysis of its worst-case execution time (WCET) [Blackham et al., 2011, Sewell et al., 2017]. This means, if the kernel is congured appropriately, all kernel operations are bounded in time, and the bound is known. This is a prerequisite for building hard real-time systems, where failure to react to an event within a strictly bounded time period is catastrophic.

[
- Bernard Blackham, Yao Shi, Sudipta Chattopadhyay, Abhik Roychoudhury, and Gernot Heiser. Timing analysis of a protected operating system kernel. In IEEE Real-Time Systems Symposium, pages 339–348, Vienna, Austria, November 2011. IEEE Computer Society. 
- Thomas Sewell, Felix Kam, and Gernot Heiser. High-assurance timing analysis
for a high-assurance real-time OS. Real-Time Systems, 53:812–853, September
2017.
]

seL4是世界上唯一对其最坏情况执行时间(WCET)进行了完整而完善的分析的操作系统内核(至少在开放文献中是这样)[Blackham et al.， 2011, Sewell et al.， 2017]。这意味着，如果内核被适当地配置，那么所有内核操作都是有时间限制的，并且这个界限是已知的。这是构建硬实时系统的先决条件，在硬实时系统中，如果不能在严格限定的时间内对事件做出反应，将是灾难性的。

- seL4 is the world’s most advanced mixed-criticality OS

seL4 provides strong support for mixed criticality real-time systems (MCS), where
the timeliness of critical activities must be ensured even if they co-exist with less
trusted code executing on the same platform. seL4 achieves this with a exible
model that retains good resource utilisation, unlike the more established MCS
OSes that use strict (and inexible) time and space partitioning [Lyons et al., 2018].
More on seL4’s real-time and MCS support in Chapter 5.

seL4为混合临界实时系统(MCS)提供了强大的支持，其中必须确保关键活动的及时性，即使它们与在同一平台上执行的不太可信的代码共存。seL4通过灵活的模型实现了这一点，保持了良好的资源利用率，而不像更成熟的MCS操作系统那样使用严格(且不灵活)的时间和空间划分[Lyons等人，2018]。更多关于seL4的实时和MCS支持在第5章。

- seL4 is the world’s fastest microkernel

Traditionally, systems are either (sort-of) secure, or they are fast. seL4 is unique in that it is both. seL4 is designed to support a wide range of real-world use cases, whether they are security- (or safety-)critical or not, and excellent performance is a requirement. More on seL4’s performance in Chapter 6.

传统上，系统要么是(有点)安全的，要么是快速的。seL4是唯一的，因为它两者都是。seL4被设计为支持广泛的真实世界用例，无论它们是否具有重要的安全性(或安全性)，卓越的性能是一个需求。更多关于seL4的性能，请参见第6章。

## Chapter 3 seL4’s Verification Story

In 2009, seL4 became the world’s first OS kernel with a machine-checked functional correctness proof at the source-code level. This proof was 200,000 lines of proof script at the time, one of the largest ever (we think it was the second largest then). It showed that a functionally correct OS kernel is possible, something that until then had been considered infeasible.

2009年，seL4成为世界上第一个在源代码级别上具有机器检查功能正确性证明的操作系统内核。当时的证明有20万行，是有史以来最大的证明之一(我们认为是当时的第二大证明)。这表明，一个功能正确的OS内核是可能的，而在此之前，这一直被认为是不可行的。

Since then we have extended the scope of the verification to higher level properties, Figure 3.1 shows the chain of proofs, which are explained below. Importantly, we maintained the proof with the ongoing evolution of the kernel: Commits to the mainline kernel source are only allowed if they do not break proofs, otherwise the proofs are updated as well. This proof engineering is also a novelty. Our seL4 proofs constitute by far the largest proof base that is actively maintained. The set of proofs has by now grown to well over a million lines, most of this manually written and then machine checked.

从那时起，我们将验证的范围扩展到更高层次的属性，图3.1展示了证明的链，下面将对此进行解释。重要的是，我们在内核不断演化的过程中维护了证明:只有在不破坏证明的情况下，才允许提交给主线内核源代码，否则证明也会被更新。这种证明工程也是一种新奇的东西。我们自己的证明构成了迄今为止被积极维护的最大的证明库。到目前为止，这组证明已经增长到100多万行，其中大部分是手工编写的，然后由机器检查。

### 3.1 Correctness and security enforcement

#### Functional correctness

The core of seL4’s verification is the functional correctness proof, which says that the C implementation is free of implementation defects. More precisely, there is a formal specification of the kernel’s functionality, expressed in a mathematical language called higher-order logic (HOL). This is represented by the box labelled abstract model in the figure. The functional correctness proof then says that the C implementation is a refinement of the abstract model, meaning the possible behaviours of the C code are a subset of those allowed by the abstract model.

seL4验证的核心是功能正确性证明，即C实现没有实现缺陷。更确切地说，内核功能有一个正式的规范，用一种称为高阶逻辑(HOL)的数学语言表示。这由图中标注为抽象模型的框表示。功能正确性证明说C实现是抽象模型的细化，这意味着C代码可能的行为是抽象模型允许的行为的子集。

This informal description glosses over a lot of detail. Here is some of it in case you wonder.

这种非正式的描述忽略了许多细节。如果你想知道的话，这里有一些。

C is not a formal language; in order to allow reasoning about a C program in the theorem prover (we use Isabelle/HOL), it has to be transformed into mathematical logic (HOL). This is done by a C parser written in Isabelle. The parser defines the semantics of the C program, and gives it meaning in HOL according to this semantics. It is this formalisation which we prove to be a refinement of the mathematical (abstract) model.

C语言不是正式语言;为了允许在定理证明器(我们使用Isabelle/HOL)中对C程序进行推理，它必须被转换成数学逻辑(HOL)。这是由用Isabelle编写的C解析器完成的。解析器定义C程序的语义，并根据此语义在HOL中赋予其意义。我们证明这种形式是对数学(抽象)模型的改进。

Note that C does not have an official mathematical semantics, and parts of the C language are notoriously subtle and not necessarily that well defined. We solve this by restricting our use of C to a well-defined subset of the language, for which we have an unambiguous semantics. However, this does not guarantee that our assumed semantics for that subset is the same as the compiler’s. More on that below.

请注意，C语言没有正式的数学语义，而且C语言的某些部分非常微妙，不一定定义得很好。为了解决这个问题，我们将C的使用限制在该语言的一个定义良好的子集内，对于这个子集我们有明确的语义。但是，这并不能保证我们为该子集假定的语义与编译器的语义相同。更多的细节在下面。

The proof means that everything we want to know about the kernel’s behaviour (other than timing) is expressed by the abstract spec, and the kernel cannot behave in ways that are not allowed by the spec. Among others, this rules out the usual attacks against operating systems, such as stack smashing, null-pointer dereference, any code injection or control-flow highjacking etc.

证明意味着我们想知道的关于内核行为的一切(除了计时)都是通过抽象规范来表达的，并且内核不能以规范所不允许的方式运行，除此之外，这就排除了对操作系统的常见攻击，比如堆栈销毁、空指针解引用、任何代码注入或控制流劫持等。

#### Translation validation

Having a bug-free C implementation of the kernel is great, but still leaves us at the mercy of the C compiler. Those compilers (we use GCC) are themselves large, complex programs that have bugs. So we could have a bug-free kernel that gets compiled into a buggy binary.

拥有一个没有bug的C内核实现是很好的，但是仍然让我们受制于C编译器。这些编译器(我们使用GCC)本身就是有bug的大型复杂程序。我们可以有一个没有bug的内核，它被编译成一个有bug的二进制文件。

In the security-critical space, compiler bugs are not the only problem. A compiler could be outright malicious, containing a Trojan that automatically builds in a back door when compiling the OS. The Trojan can be extended to automatically add itself when compiling the compiler, making it almost impossible to detect, even if the compiler is opensource! Ken Thompson explained this attack in his Turing Award lecture [Thompson, 1984].

在安全关键型领域，编译器错误并不是唯一的问题。编译器可能是完全恶意的，包含一个木马，在编译操作系统时自动构建一个后门。该木马可以扩展到在编译编译器时自动添加自己，使得它几乎不可能被检测到，即使编译器是开源的!Ken Thompson在他的图灵奖演讲中解释了这种攻击[Thompson, 1984]。

To protect against defective or malicious compilers, we additionally verify the executable binary that is produced by the compiler and linker. Specifically, we prove that the binary is a correct translation of the (proved correct) C code, and thus that the binary refines the abstract spec.

为了防止有缺陷或恶意的编译器，我们另外验证编译器和链接器生成的可执行二进制文件。具体地说，我们证明了二进制文件是C代码的正确翻译(证明是正确的)，因此二进制文件改进了抽象规范。

Unlike the verification of the C code, this proof is not done manually but by an automatic tool chain. It consists of several phases, as shown in Figure 3.2. A formal model of the processor’s instruction set architecture (ISA) formalises the binary in the theorem prover; we use an L3 formalisation of the RISC-V ISA, as well as the extensively tested L3 Arm ISA formalisation of Fox and Myreen [2010].

与C代码的验证不同，此验证不是手动完成的，而是通过自动工具链完成的。它由几个阶段组成，如图3.2所示。处理器指令集体系结构(ISA)的形式化模型使定理证明器中的二进制形式化;我们使用了RISC-V ISA的L3正规化，以及Fox和Myreen[2010]广泛测试的L3 Arm ISA正规化。

Then a disassembler, written in the HOL4 theorem prover, translates this low-level representation into a higher-level representation in a graph language that basically represents control flow. This transformation is provably correct.

然后，用HOL4定理证明器编写的反汇编器，将这种低级表示转换为图形语言的高级表示，这种图形语言基本上表示控制流。这种转变被证明是正确的。

The formalised C program is translated into the same graph language, through provably correct transformations in the Isabelle/HOL theorem prover. We then have two programs, in the same representation, which we need to show equivalent. This is a bit tricky, as compilers apply a number of heuristic-driven transformations to optimise the code. We apply a number of such transformations through rewrite rules on the graph-language representation of the C program (still in the theorem prover, and thus provably correct).

通过Isabelle/HOL定理证明器中可证明正确的转换，将形式化的C程序翻译成相同的图语言。然后我们有两个程序，在相同的表示，我们需要显示相等的。这有点棘手，因为编译器会应用许多启发式驱动的转换来优化代码。我们通过重写规则在C程序的图形语言表示中应用大量这样的转换(仍然在定理证明器中，因此被证明是正确的)。

In the end we then have two programs that are quite similar but not the same, and we need to prove that they have the same semantics. In theory this is equivalent to the halting problem and as such unsolvable. In practice, what the compiler does is deterministic enough to make the problem tractable. We do this by throwing the programs, in small chunks, at multiple SMT solvers. If one of these can prove that all the corresponding pieces have the same semantics, then we know that the two programs are equivalent.

最后，我们有两个非常相似但不相同的程序，我们需要证明它们有相同的语义。从理论上讲，这相当于停机问题，因此是不可解决的。在实践中，编译器所做的工作足以使问题变得易于处理。我们通过将程序以小块的方式扔给多个SMT求解器来实现这一点。如果其中一个可以证明所有对应的部分具有相同的语义，那么我们就知道这两个程序是等价的。

Note also that the C program that is proved to refine the abstract spec, and the C program that we prove to be equivalent to the binary, are the same Isabelle/HOL formalisations. This means that our assumptions on C semantics drop out of the assumptions made by the proofs. Altogether, the proofs not only show that the compiler did not introduce bugs, but also that its semantics for the C subset we use are the same as ours.

还要注意的是，被证明完善抽象规范的C程序，以及被证明等价于二进制的C程序，都是相同的Isabelle/HOL形式。这意味着我们对C语义的假设将脱离证明所做的假设。总之，这些证明不仅表明了编译器没有引入bug，而且还表明了它对我们使用的C子集的语义与我们的相同。

#### Security properties

Figure 3.1 also shows proofs between the abstract spec and the high-level security properties confidentiality, integrity and availability (these are commonly dubbed the CIA properties). These state that the abstract spec is actually useful for security: They prove that in a correctly configured system, the kernel will enforce these properties.

图3.1还显示了抽象规范和高级安全属性机密性、完整性和可用性(这些通常被称为CIA属性)之间的证据。这些说明抽象规范实际上对安全性很有用:它们证明在正确配置的系统中，内核将强制执行这些属性。

Specifically, seL4 enforces

- confidentiality: seL4 will not allow an entity to read (or otherwise infer) data without having been explicitly given read access to the data;
- integrity: seL4 will not allow an entity to modify data without having been explicitly given write access to the data;
- availability: seL4 will not allow an entity to prevent another entity’s authorised use of resources.

具体来说,seL4执行
- 机密性:seL4不允许实体在未被明确授予读访问权限的情况下读取(或推断)数据;
- 完整性:seL4不允许实体在未明确授予数据写访问权的情况下修改数据;
- 可用性:seL4不允许一个实体阻止另一个实体对资源的授权使用。

These proofs presently do not capture properties associated with time. Our confidentiality proofs rule out covert storage channels but presently not covert timing channels, which are used by such attacks as Spectre. Preventing timing channels is something we are working on [Heiser et al., 2019]. Similarly, the integrity and availability proofs presently do not cover timeliness, but our new MCS model [Lyons et al., 2018] is designed to cover those aspects (see Section 5.2).

这些证明目前还没有抓住与时间有关的性质。我们的机密证据排除了隐蔽的存储通道，但目前没有隐蔽的定时通道，这些通道被用于幽灵攻击。阻止时机通道是我们正在做的事情[Heiser等人，2019年]。同样，目前的完整性和可用性证明也没有涵盖及时性，但我们的新MCS模型[Lyons et al.， 2018]旨在涵盖这些方面(见第5.2节)。

#### Proof assumptions

All reasoning about correctness is based on assumptions, whether the reasoning is formal, as with seL4, or informal, when someone thinks about why their program might be “correct”. Every program executes in some context, and its correct behaviour inevitably depends on some assumptions about this context.

所有关于正确性的推理都基于假设，无论推理是正式的(如seL4)，还是非正式的(当某人思考为什么他们的程序可能是“正确的”时)。每个程序都在某个上下文中执行，它的正确行为不可避免地依赖于这个上下文中的一些假设。

One of the advantages of machine-checked formal reasoning is that it forces people to make those assumptions explicit. It is not possible to make unstated assumptions, the proofs will just not succeed if they depend on assumptions that are not clearly stated. In that sense, formal reasoning protects against forgetting assumptions, or not being clear about them; that in itself is a significant benefit of verification.

机器检查形式推理的一个优点是，它迫使人们明确地做出这些假设。不可能做出未阐明的假设，如果证明依赖于未阐明的假设，那么证明将不会成功。从这个意义上说，形式推理可以防止忘记假设，或者不清楚它们;这本身就是核查的一个重大好处。

The verification of seL4 makes three assumptions:

- Hardware behaves as expected.

This should be obvious. The kernel is at the mercy of the underlying hardware, and if the hardware is buggy (or worse, has Trojans), then all bets are off, whether you are running verified seL4 or any unverified OS. Verifying hardware is outside the scope of seL4 (and the competency of TS); other people are working on that.

- The spec matches expectations. 

This is a difficult one, because one can never be sure that a formal specification means what we think it should mean. Of course, the same problem exists if there is no formal specification: if the spec is informal or non-existent, then it is obviously impossible to precisely reason about correct behaviour.

这是一个困难的问题，因为人们永远不能确定一个正式的规范意味着我们认为它应该意味着什么。当然，如果没有正式的规范，同样的问题也会存在:如果规范是非正式的或者不存在，那么显然不可能精确地推断出正确的行为。

One can reduce this risk by proving properties about the spec, as we have done with our security proofs, which show that seL4 is able to enforce certain security properties. That then shifts the problem to the specification of those properties. They are much simpler than the kernel spec, reducing the risk of misunderstanding.

我们可以通过证明规范的属性来减少这种风险，就像我们在安全性证明中所做的那样，它表明seL4能够强制执行某些安全性属性。然后问题就转移到这些属性的说明上了。它们比内核规范简单得多，减少了误解的风险。

But in the end, there is always a gap between the world of mathematics and the physical world, and no end of reasoning (formal or informal) can remove this completely. The advantage of formal reasoning is that you know exactly what this gap is.

但最终，在数学世界和物理世界之间总是有差距的，没有任何推理(正式的或非正式的)可以完全消除这一差距。形式推理的好处是你可以确切地知道这个差距是什么。

- The theorem prover is correct. 

This sounds like a serious problem, given that theorem provers are themselves large and complex programs. However, in reality this is the least concerning of the three assumptions. The reason is that the Isabelle/HOL theorem prover has a small core (of a few 10 kSLOC) that checks all proofs against the axioms of the logic. And this core has checked many proofs small and large from a wide field of formal reasoning, so the chance of it containing a correctness critical bug is extremely small.

这听起来像是一个严重的问题，因为定理的证明者本身就是又大又复杂的程序。然而，实际上，这是三个假设中最无关紧要的一个。原因是Isabelle/HOL定理证明器有一个小的核心(有几个10 kSLOC)来检查所有的证明与逻辑的公理。而且这个核心已经从广泛的形式推理领域检查了大大小小的证明，所以它包含一个关键正确性错误的几率非常小。

#### Proof status and coverage

seL4 has been or is being verified for multiple architectures: Arm, x86 and RISC-V. Some of these are more complete than others, but the missing bits are generally worked on or waiting for funding. Please refer to the seL4 project status page for details.

seL4已经或正在为多种架构进行验证:Arm、x86和RISC-V。其中一些比其他的更完整，但缺失的部分通常都在工作或等待资金。详情请参阅seL4项目状态页面。

### 3.2 The CAmkES component framework

CAmkES is a component framework that allows you to reason about a system architecturally, i.e. as a collection of sandboxed components with defined communication channels. Figure 3.3 shows the main abstractions.

CAmkES是一个组件框架，它允许您从体系结构上对系统进行推理，也就是说，它是一个沙箱组件的集合，具有定义好的通信通道。图3.3显示了主要的抽象。

Components are represented as square boxes. They represent programs, code and data, encapsulated by seL4.

组件用方框表示。它们代表由seL4封装的程序、代码和数据。

Interfaces are shown as decorations on the components. They define how a component can be invoked, or can invoke others. An interface is either imported (invoking an interface of another component) or exported (able to be invoked by another component’s imported interface), except for the shared-memory interface, which is symmetric.

接口显示为组件上的装饰。它们定义如何调用组件或调用其他组件。接口要么被导入(调用另一个组件的接口)，要么被导出(能够被另一个组件的导入接口调用)，共享内存接口除外，它是对称的。

Connectors connect like interfaces by linking an importing with an exporting interface. Connectors in CAmkES are always one-to-one, but broadcast or multicast functionality can be implemented on top of this model by building components that copy inputs to multiple outputs.

连接器通过将导入连接到导出接口来进行类似接口的连接。CAmkES中的连接器总是一对一的，但是广播或多播功能可以通过构建将输入复制到多个输出的组件来实现。

The CAmkES system is specified in a formal architecture description language (the CAmkES ADL), which contains a precise description of the components, their interfaces and the connectors that link them up. The CAmkES promise to the system designer is that what is specified in the ADL (and visualised as in Figure 3.3) is a faithful representation of the possible interactions. In particular, it promises that no interactions are possible beyond those shown in the diagram.

CAmkES系统是用一种正式的体系结构描述语言(CAmkES ADL)指定的，它包含了对组件、它们的接口和连接它们的连接器的精确描述。CAmkES对系统设计者的承诺是ADL中指定的内容(如图3.3所示)是可能的交互的忠实表示。特别地，它保证除了图中所示的交互之外，不存在其他交互。

Of course, this promise depends on enforcement by seL4, and the ADL representation must be mapped onto low-level seL4 objects and access rights to them. This is what the CAmkES machinery achieves, and is shown in Figure 3.4.

当然，这个承诺依赖于seL4的实施，而且ADL表示必须映射到低级seL4对象及其访问权限。这就是CAmkES机械所实现的，如图3.4所示。

In the figure, the architecture (i.e. what is described in the ADL) is shown at the top. This is a fairly simple system, consisting of four native components and one component that houses a virtual machine hosting a Linux guest with a couple of networking drivers. The Linux VM is only connected to other components via the crypto component, which ensures that it can only access encrypted links and cannot leak data.

在图中，架构(即在ADL中描述的)显示在顶部。这是一个相当简单的系统，由四个本机组件和一个组件组成，该组件包含一个虚拟机，该虚拟机承载一个具有两个网络驱动程序的Linux客户。Linux虚拟机仅通过crypto组件连接到其他组件，这确保了它只能访问加密的链接而不能泄漏数据。

Even this simple system maps to hundreds if not thousands of seL4 objects, an indication of the complexity reduction provided by the CAmkES component abstraction.

即使这个简单的系统也映射到成百上千个seL4对象，这表明CAmkES组件抽象降低了复杂性。

For the seL4-level description we have another formal language, called CapDL (capability distribution language). The system designer never needs to deal with CapDL, it is a purely internal representation. The CAmkES framework contains a compiler which automatically translates CAmkES ADL into CapDL, indicated by the box arrow pointing left-down. The box in the left of the figure gives a (simplified) representation of the seL4 objects described in CapDL. (It is actually a simplified representation of a much simpler system, basically just the two components at the top of Figure 3.3 and the connector between them.)

对于sel4级别的描述，我们有另一种正式语言，称为CapDL(能力分布语言)。系统设计者永远不需要处理CapDL，它是一个纯粹的内部表示。CAmkES框架包含一个编译器，它可以自动将CAmkES ADL转换为CapDL，由左下方的方框箭头表示。图左侧的框给出了CapDL中描述的seL4对象的(简化)表示。(它实际上是一个简单得多的系统的简化表示，基本上只有图3.3顶部的两个组件和它们之间的连接器。)

The CapDL spec is a precise representation of access rights in the system, and it is what seL4 enforces. Which means that once the system gets into the state described by the CapDL spec, it is guaranteed to behave as described by the CAmkES ADL spec, and therefore the architecture-level description is sufficient for further reasoning about security properties.

CapDL规范是系统中访问权限的精确表示，这也是seL4强制执行的。这意味着一旦系统进入CapDL规范所描述的状态，它就可以保证按照CAmkES ADL规范所描述的那样运行，因此体系结构级别的描述足以进一步解释安全性属性。

So we need assurance that the system boots up into the state described by the CapDL spec. We achieve this with a second automated step: We generate from CapDL the startup code that, as soon as seL4 itself has booted, takes control and generates all the seL4 objects referenced by the spec, including the ones describing active components, and distributes the capabilities (see Chapter 4) that grant access to those objects according to the spec. At the end of the execution of this init code, the system is provably in the state described by the CapDL spec, and thus in the state represented by the ADL spec.

因此，我们需要确保系统启动到CapDL规范所描述的状态。我们通过第二个自动化步骤来实现这一点:我们从CapDL启动代码生成,一旦seL4本身已经启动,需要控制和生成所有seL4对象引用的规范,包括描述活性成分,和分发的功能(见第四章),授权访问这些对象根据规范。最后执行的初始化代码,系统证明地CapDL规范所描述的状态,因此在反诽谤联盟所代表的国家规范。

The third thing that gets generated from the ADL spec is the “glue” code between components. Sending data through a connector requires invocation of seL4 system calls, the exact details of which are hidden by the CAmkES abstraction. The glue code is setting up these system calls. For example, an “RPC” connector abstracts the invocation of a function provided by another component as a regular function call performed by the client component.

从ADL规范中生成的第三个东西是组件之间的“粘合”代码。通过连接器发送数据需要调用seL4系统调用，CAmkES抽象隐藏了这些调用的确切细节。粘合代码将设置这些系统调用。例如，“RPC”连接器将另一个组件提供的函数调用抽象为客户端组件执行的常规函数调用。

Note: At the time of writing, the proofs about CAmkES and CapDL are not yet complete, but completion should not be far off.

注:在本文写作时，CAmkES和CapDL的校样还没有完成，但完成的时间应该不远了。

Note also that none of the verification work mentioned deals with information leakage through timing channels (yet). This is a major unsolved research problem, but we’re at the forefront of solving it.

还要注意的是，上面提到的验证工作都没有处理通过定时通道的信息泄漏。这是一个重大的未解决的研究问题，但我们处于解决它的前沿。


