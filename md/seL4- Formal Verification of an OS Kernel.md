# seL4: Formal Verification of an OS Kernel

## ABSTRACT

Complete formal verification is the only known way to guarantee that a system is free of programming errors.

完整的形式验证是保证系统没有编程错误的唯一已知方法。

We present our experience in performing the formal, machine-checked verification of the seL4 microkernel from an abstract specification down to its C implementation. We assume correctness of compiler, assembly code, and hardware, and we used a unique design approach that fuses formal and operating systems techniques. To our knowledge, this is the first formal proof of functional correctness of a complete, general-purpose operating-system kernel. Functional correctness means here that the implementation always strictly follows our high-level abstract specification of kernel behaviour. This encompasses traditional design and implementation safety properties such as the kernel will never crash, and it will never perform an unsafe operation. It also proves much more: we can predict precisely how the kernel will behave in every possible situation.

我们介绍了从抽象规范到C实现对seL4微核执行正式的、机器检查的验证的经验。我们假设编译器、汇编代码和硬件都是正确的，并且我们使用了一种独特的设计方法，融合了形式和操作系统技术。据我们所知，这是一个完整的、通用操作系统内核的函数正确性的第一个正式证明。函数正确性在这里意味着实现总是严格遵循我们的内核行为的高级抽象规范。这包含了传统的设计和实现安全属性，比如内核永远不会崩溃，它永远不会执行不安全的操作。它还证明了更多:我们可以精确地预测内核在每一种可能的情况下的行为。

seL4, a third-generation microkernel of L4 provenance, comprises 8,700 lines of C code and 600 lines of assembler. Its performance is comparable to other high-performance L4 kernels.

seL4是源自L4的第三代微内核，由8700行C代码和600行汇编程序组成。它的性能可与其他高性能L4内核相媲美。

## 1. INTRODUCTION

The security and reliability of a computer system can only be as good as that of the underlying operating system (OS) kernel. The kernel, defined as the part of the system executing in the most privileged mode of the processor, has unlimited hardware access. Therefore, any fault in the kernel’s implementation has the potential to undermine the correct operation of the rest of the system.

计算机系统的安全性和可靠性只能和底层操作系统内核的安全性和可靠性一样好。内核被定义为在处理器的最特权模式下执行的系统的一部分，它拥有无限的硬件访问权。因此，内核实现中的任何错误都有可能破坏系统其余部分的正确操作。

General wisdom has it that bugs in any sizeable code base are inevitable. As a consequence, when security or reliability is paramount, the usual approach is to reduce the amount of privileged code, in order to minimise the exposure to bugs. This is a primary motivation behind security kernels and separation kernels [38, 54], the MILS approach [4], microkernels [1, 12, 35, 45, 57, 71] and isolation kernels [69], the use of small hypervisors as a minimal trust base [16, 26, 56, 59], as well as systems that require the use of type-safe languages for all code except some “dirty”core [7,23]. Similarly, the Common Criteria [66] at the strictest evaluation level requires the system under evaluation to have a “simple” design.

一般认为，任何规模较大的代码库中的错误都是不可避免的。因此，当安全或可靠性是最重要的，通常的方法是减少特权代码的数量，以尽量减少漏洞暴露。这是安全内核和分离内核[38,54]、MILS方法[4]、微内核[1,12,35,45,57,71]和隔离内核[69]背后的主要动机，使用小型管理程序作为最小信任基础[16,26,56,59]，以及需要对所有代码使用类型安全语言的系统，除了一些“不干净的”核心[7,23]。同样，在最严格的评估级别，通用标准[66]要求被评估的系统具有“简单”的设计。

With truly small kernels it becomes possible to take security and robustness further, to the point where it is possible to guarantee the absence of bugs [22, 36, 56, 64]. This can be achieved by formal, machine-checked verification, providing mathematical proof that the kernel implementation is consistent with its specification and free from programmer-induced implementation defects.

有了真正的小内核，就有可能进一步提高安全性和健壮性，从而可以保证没有bug[22, 36, 56, 64]。这可以通过形式的、机器检查的验证来实现，提供数学证明来证明内核实现与它的规范是一致的，并且没有程序员导致的实现缺陷。

We present seL4, a member of the L4 [46] microkernel family, designed to provide this ultimate degree of assurance of functional correctness by machine-assisted and machinechecked formal proof. We have shown the correctness of a very detailed, low-level design of seL4 and we have formally verified its C implementation. We assume the correctness of the compiler, assembly code, boot code, management of caches, and the hardware; we prove everything else.

我们介绍了seL4, L4[46]微核家族的成员，旨在通过机器辅助和机器检查的形式证明提供功能正确性的最终保证。我们已经展示了非常详细的seL4底层设计的正确性，并且正式验证了它的C实现。我们假设编译器、汇编代码、引导代码、缓存管理和硬件的正确性;我们证明了一切。

Specifically, seL4 achieves the following:

- it is suitable for real-life use, and able to achieve performance that is comparable with the best-performing microkernels;
its behaviour is precisely formally specified at an abstract level;
- its formal design is used to prove desirable properties, including termination and execution safety;
- its implementation is formally proven to satisfy the specification; and
- its access control mechanism is formally proven to provide strong security guarantees.

具体来说，seL4实现了以下几点:

- 适合实际使用，性能可与性能最好的微内核媲美;
- 它的行为在抽象层次上被精确地正式地规定;
- 其正式设计用于证明所需的性能，包括终止和执行安全;
- 其实施被正式证明满足规范要求;和
- 其访问控制机制已正式证明可提供强大的保安保证。

To our knowledge, seL4 is the first-ever general-purpose OS kernel that is fully formally verified for functional correctness. As such, it is a platform of unprecedented trustworthiness, which will allow the construction of highly secure and reliable systems on top.

据我们所知，seL4是有史以来第一个被正式验证功能正确性的通用操作系统内核。因此，它是一个前所未有的可信赖的平台，这将允许在顶部建设高度安全和可靠的系统。

The functional-correctness property we prove for seL4 is much stronger and more precise than what automated techniques like model checking, static analysis or kernel implementations in type-safe languages can achieve. We not only analyse specific aspects of the kernel, such as safe execution, but also provide a full specification and proof for the kernel’s precise behaviour.

我们为seL4证明的函数正确性属性比模型检查、静态分析或用类型安全语言实现的内核实现等自动化技术更强大、更精确。我们不仅分析了内核的具体方面，如安全执行，但也为内核的精确行为提供了一个完整的规范和证明。

We have created a methodology for rapid kernel design and implementation that is a fusion of traditional operating systems and formal methods techniques. We found that our verification focus improved the design and was surprisingly often not in conflict with achieving performance.

我们已经创建了一种用于快速内核设计和实现的方法，它是传统操作系统和正式方法技术的融合。我们发现，我们的验证重点改进了设计，而且令人惊讶的是，它经常与实现性能没有冲突。

In this paper, we present the design of seL4, discuss the methodologies we used, and provide an overview of the approach used in the formal verification from high-level specification to the C implementation. We also discuss the lessons we have learnt from the project, and the implications for similar efforts.

在本文中，我们介绍了seL4的设计，讨论了我们使用的方法，并概述了从高级规范到C实现的正式验证所使用的方法。我们还讨论了我们从该项目中学到的教训，以及对类似工作的启示。

The remainder of this paper is structured as follows. In Sect. 2, we give an overview of seL4, of the design approach, and of the verification approach. In Sect. 3, we describe how to design a kernel for formal verification. In Sect. 4, we describe precisely what we verified, and identify the assumptions we make. In Sect. 5, we describe important lessons we learnt in this project, and in Sect. 6, we contrast our effort with related work.

本文的其余部分结构如下。在第2节中，我们给出了seL4、设计方法和验证方法的概述。在第3节中，我们描述了如何设计一个用于形式验证的内核。在第4节中，我们精确地描述了我们验证的内容，并确定了我们做出的假设。在第5节中，我们描述了我们在这个项目中学到的重要教训，在第6节中，我们对比了我们的努力和相关的工作。

## 2. OVERVIEW

### 2.1 seL4 programming model

This paper is primarily about the formal verification of the seL4 kernel, not its API design. We therefore provide only a brief overview of its main characteristics.

本文主要是关于seL4内核的形式化验证，而不是它的API设计。因此，我们仅对其主要特征作一个简要概述。

seL4 [20], similarly to projects at Johns Hopkins (Coyotos) and Dresden (Nova), is a third-generation microkernel, and is broadly based on L4 [46] and influenced by EROS [58]. It features abstractions for virtual address spaces, threads, inter-process communication (IPC), and, unlike most L4 kernels, capabilities for authorisation. Virtual address spaces have no kernel-defined structure; page faults are propagated via IPC to pager threads, responsible for defining the address space by mapping frames into the virtual space. Exceptions and non-native system calls are also propagated via IPC to support virtualisation. IPC uses synchronous and asynchronous endpoints (port-like destinations without in-kernel buffering) for inter-thread communication, with RPC facilitated via reply capabilities. Capabilities are segregated and stored in capability address spaces composed of capability container objects called CNodes.

与Johns Hopkins (Coyotos)和Dresden (Nova)的项目类似，seL4[20]是第三代微内核，广泛地基于L4[46]并受到EROS[58]的影响。它提供了对虚拟地址空间、线程、进程间通信(IPC)的抽象，以及与大多数L4内核不同的授权功能。虚拟地址空间没有内核定义的结构;页面错误通过IPC传播到分页线程，这些线程负责通过将帧映射到虚拟空间来定义地址空间。异常和非本机系统调用也通过IPC传播以支持虚拟化。IPC使用同步和异步端点(没有内核内缓冲的类端口目的地)进行线程间通信，通过应答功能方便了RPC。功能被分离并存储在由称为CNodes的功能容器对象组成的功能地址空间中。

As in traditional L4 kernels, seL4 device drivers run as user-mode applications that have access to device registers and memory either by mapping the device into the virtual address space, or by controlled access to device ports on x86 hardware. seL4 provides a mechanism to receive notification of interrupts (via IPC) and acknowledge their receipt.

与传统的L4内核一样，seL4设备驱动程序作为用户模式应用程序运行，这些应用程序通过将设备映射到虚拟地址空间或通过对x86硬件上的设备端口的控制访问来访问设备寄存器和内存。seL4提供了一种机制来接收中断的通知(通过IPC)并确认它们的接收。

Memory management in seL4 is explicit: both in-kernel objects and virtual address spaces are protected and managed via capabilities. Physical memory is initially represented by untyped capabilities, which can be subdivided or retyped into kernel objects such as page tables, thread control blocks, CNodes, endpoints, and frames (for mapping in virtual address spaces). The model guarantees all memory allocation in the kernel is explicit and authorised.

seL4中的内存管理是显式的:内核对象和虚拟地址空间都通过功能受到保护和管理。物理内存最初由无类型的功能表示，这些功能可以被细分或重新类型化为内核对象，如页表、线程控制块、cnode、端点和帧(用于在虚拟地址空间中进行映射)。该模型保证内核中的所有内存分配都是显式的并经过授权的。

Initial development of seL4, and all verification work, was done for an ARMv6-based platform, with a subsequent port of the kernel (so far without proof) to x86.

seL4的最初开发和所有验证工作都是为基于armv6的平台完成的，随后将内核(到目前为止还没有证据)移植到x86。

### 2.2 Kernel design process

OS developers tend to take a bottom-up approach to kernel design. High performance is obtained by managing the hardware efficiently, which leads to designs motivated by lowlevel details. In contrast, formal methods practitioners tend toward top-down design, as proof tractability is determined by system complexity. This leads to designs based on simple models with a high degree of abstraction from hardware.

操作系统开发人员倾向于采用自底向上的方法进行内核设计。通过有效地管理硬件来获得高性能，这导致了基于低层次细节的设计。相反，正式方法的实践者倾向于自上而下的设计，因为证明的可处理性是由系统复杂性决定的。这导致设计基于简单的模型，与硬件高度抽象。

As a compromise that blends both views, we adopted an approach [19, 22] based around an intermediate target that is readily accessible by both OS developers and formal methods practitioners. It uses the functional programming language Haskell to provide a programming language for OS developers, while at the same time providing an artefact that can be automatically translated into the theorem proving tool and reasoned about.

作为融合两种观点的折中方案，我们采用了一种基于中间目标的方法[19,22]，OS开发人员和正式方法从业者都可以很容易地访问这个目标。它使用函数式编程语言Haskell为OS开发人员提供一种编程语言，同时提供一种可以自动转换为定理证明工具并进行推理的构件。

Fig. 1 shows our approach in more detail. The square boxes are formal artefacts that have a direct role in the proof. The double arrows represent implementation or proof effort, the single arrows represent design/implementation influence of artefacts on other artefacts. The central artefact is the Haskell prototype of the kernel. The prototype requires the design and implementation of algorithms that manage the low-level hardware details. To execute the Haskell prototype in a near-to-realistic setting, we link it with software (derived from QEMU) that simulates the hardware platform. Normal user-level execution is enabled by the simulator, while traps are passed to the kernel model which computes the result of the trap. The prototype modifies the user-level state of the simulator to appear as if a real kernel had executed in privileged mode.

图1更详细地展示了我们的方法。方框是正式的人工制品，在证明中有直接的作用。双箭头表示实现或验证工作，单箭头表示设计/实现对其他产品的影响。核心人工制品是内核的Haskell原型。原型需要设计和实现管理底层硬件细节的算法。为了在接近真实的设置中执行Haskell原型，我们将它与模拟硬件平台的软件(源自QEMU)链接起来。模拟器支持正常的用户级执行，而陷阱则传递给内核模型，由内核模型计算陷阱的结果。原型修改了模拟器的用户级状态，使之看起来像是在特权模式下运行的真正内核。

This arrangement provides a prototyping environment that enables low-level design evaluation from both the user and kernel perspective, including low-level physical and virtual memory management. It also provides a realistic execution environment that is binary-compatible with the real kernel. For example, we ran a subset of the Iguana embedded OS [37] on the simulator-Haskell combination. The alternative of producing the executable specification directly in the theorem prover would have meant a steep learning curve for the design team and a much less sophisticated tool chain for execution and simulation.

这种安排提供了一个原型环境，允许从用户和内核的角度进行低级设计评估，包括低级物理和虚拟内存管理。它还提供了一个实际的执行环境，它与真正的内核是二进制兼容的。例如，我们在simulator-Haskell组合上运行嵌入的Iguana OS[37]的一个子集。在定理证明器中直接生成可执行规范的选择意味着设计团队需要陡峭的学习曲线，而执行和模拟的工具链则不那么复杂。

We restrict ourselves to a subset of Haskell that can be automatically translated into the language of the theorem prover we use. For instance, we do not make any substantial use of laziness, make only restricted use of type classes, and we prove that all functions terminate. The details of this subset are described elsewhere [19, 41].

我们将自己限制在Haskell的一个子集中，该子集可以自动转换为我们使用的定理证明器的语言。例如，我们没有真正使用惰性，只限制类类型的使用，并且我们证明了所有函数都会终止。这个子集的细节在其他地方描述[19,41]。

While the Haskell prototype is an executable model and implementation of the final design, it is not the final production kernel. We manually re-implement the model in the C programming language for several reasons. Firstly, the Haskell runtime is a significant body of code (much bigger than our kernel) which would be hard to verify for correctness. Secondly, the Haskell runtime relies on garbage collection which is unsuitable for real-time environments. Incidentally, the same arguments apply to other systems based on type-safe languages, such as SPIN [7] and Singularity [23]. Additionally, using C enables optimisation of the low-level implementation for performance. While an automated translation from Haskell to C would have simplified verification, we would have lost most opportunities to micro-optimise the kernel, which is required for adequate microkernel performance.

虽然Haskell原型是一个可执行模型和最终设计的实现，但它不是最终的产品内核。出于几个原因，我们用C编程语言手工重新实现了这个模型。首先，Haskell运行时是一个重要的代码体(比我们的内核大得多)，很难验证其正确性。其次，Haskell运行时依赖于垃圾收集，这并不适合实时环境。顺便说一句，同样的参数也适用于基于类型安全语言的其他系统，如SPIN[7]和Singularity[23]。此外，使用C可以优化底层实现的性能。虽然从Haskell到C的自动转换将简化验证，但我们将失去对内核进行微优化的大部分机会，而微内核的微优化是获得足够性能所必需的。

### 2.3 Formal verification

The technique we use for formal verification is interactive, machine-assisted and machine-checked proof. Specifically, we use the theorem prover Isabelle/HOL [50]. Interactive theorem proving requires human intervention and creativity to construct and guide the proof. However, it has the advantage that it is not constrained to specific properties or finite, feasible state spaces, unlike more automated methods of verification such as static analysis or model checking.

我们用于形式验证的技术是交互式的、机器辅助的和机器检查的证明。特别地，我们使用定理证明者Isabelle/HOL[50]。交互式定理证明需要人的介入和创造性来构造和指导证明。然而，它的优点是不受特定属性或有限可行状态空间的限制，不像静态分析或模型检查等更自动化的验证方法。

The property we are proving is functional correctness in the strongest sense. Formally, we are showing refinement [18]: A refinement proof establishes a correspondence between a high-level (abstract) and a low-level (concrete, or refined) representation of a system.

我们要证明的性质是最强意义上的函数正确性。形式上，我们展示了精化[18]:精化证明在系统的高级(抽象)和低级(具体的，或精化的)表示之间建立了对应关系。

The correspondence established by the refinement proof ensures that all Hoare logic properties of the abstract model also hold for the refined model. This means that if a security property is proved in Hoare logic about the abstract model (not all security properties can be), refinement guarantees that the same property holds for the kernel source code. In this paper, we concentrate on the general functional correctness property. We have also modelled and proved the security of seL4’s access-control system in Isabelle/HOL on a high level. This is described elsewhere [11,21], and we have not yet connected it to the proof presented here.

通过精化证明所建立的对应关系，保证了精化模型的所有Hoare逻辑属性都能保持。这意味着如果一个安全属性在关于抽象模型的Hoare逻辑中被证明(不是所有的安全属性都可以被证明)，那么细化可以保证内核源代码中包含相同的属性。在本文中，我们主要讨论一般函数的正确性性质。我们还在Isabelle/HOL中对seL4的访问控制系统进行了高层次的建模和验证。这在别处有描述[11,21]，我们还没有把它和这里提出的证明联系起来。

Fig. 2 shows the specification layers used in the verification of seL4; they are related by formal proof. Sect. 4 explains the proof and each of these layers in detail; here we give a short summary.

图2显示了用于验证seL4的规范层;它们通过形式证明相联系。第4节详细解释了证据和每一层;这里我们做一个简短的总结。

The top-most layer in the picture is the abstract specification: an operational model that is the main, complete specification of system behaviour. The abstract level contains enough detail to specify the outer interface of the kernel, e.g., how system-call arguments are encoded in binary form, and it describes in abstract logical terms the effect of each system call or what happens when an interrupt or VM fault occurs. It does not describe in detail how these effects are implemented in the kernel.

图中的最顶层是抽象规范:一个操作模型，它是系统行为的主要的、完整的规范。抽象层包含足够的细节来指定内核的外部接口，例如，系统调用参数是如何以二进制形式编码的，并且它以抽象的逻辑术语描述每个系统调用的效果，或者当中断或VM故障发生时发生了什么。它没有详细描述这些效果是如何在内核中实现的。

The next layer down in Fig. 2 is the executable specification generated from Haskell into the theorem prover. The translation is not correctness-critical because we seek assurance about the generated definitions and ultimately C, not the Haskell source, which only serves as an intermediate prototype. The executable specification contains all data structure and implementation details we expect the final C implementation to have.

图2的下一层是由Haskell生成到定理证明器的可执行规范。这个转换不是正确的，因为我们要确保生成的定义和最终的C，而不是Haskell源代码，它只是充当中间原型。可执行规范包含了我们期望最终C实现拥有的所有数据结构和实现细节。

Finally, the bottom layer in this verification effort is the high-performance C implementation of seL4. For programs to be formally verified they must have formally-defined semantics. One of the achievements of this project is a very exact and faithful formal semantics for a large subset of the C programming language [62]. Even with a formal semantics of C in the logic of the theorem prover, we still have to read and translate the specific program into the prover. This is discussed in Sect. 4.3.

最后，验证工作的底层是seL4的高性能C实现。要正式验证程序，它们必须有正式定义的语义。这个项目的成果之一是为C编程语言的一个大子集提供了一个非常精确和忠实的形式语义[62]。即使有了C语言在逻辑上的形式化语义的定理证明器，我们仍然要把特定的程序读到证明器中并翻译出来。这在第4.3节中进行了讨论。

Verification can never be absolute; it always must make fundamental assumptions. In our work we stop at the sourcecode level, which implies that we assume at least the compiler and the hardware to be correct.

核查永远不可能是绝对的;它总是必须做出基本的假设。在我们的工作中，我们停留在源代码级别，这意味着我们至少假设编译器和硬件是正确的。

An often-raised concern is the question of proof correctness. More than 30 years of research in theorem proving has addressed this issue, and we can now achieve a degree of trustworthiness of formal, machine-checked proof that far surpasses the confidence levels we rely on in engineering or mathematics for our daily survival. We use two specific techniques: firstly, we work foundationally from first principles; mathematics, semantics, and Hoare logic are not axiomatised, but defined and proved. Secondly, the Isabelle theorem prover we are using can produce external proof representations that can be independently checked by a small, simple proof checker.

一个经常引起关注的问题是证明的正确性。超过30年的定理证明研究已经解决了这个问题，我们现在可以实现形式的、机器检查的证明的可信度，这远远超过了我们日常生存所依赖的工程或数学的置信水平。我们使用两种特定的技术:首先，我们从基本原理出发进行基础性工作;数学、语义学和霍尔逻辑不是公理化的，而是定义和证明的。其次，我们使用的伊莎贝尔定理证明器可以产生外部证明表示，这些外部证明表示可以由一个小的、简单的证明检查器独立检查。

## 3. KERNEL DESIGN FOR VERIFICATION

The main body of the correctness proof can be thought of as showing Hoare triples on program statements and on functions in each of the specification levels. The proof in our refinement and Hoare logic framework decomposes along function boundaries. Each unit of proof has a set of preconditions that need to hold prior to execution, a statement or sequence of statements in a function that modify the system state, and the post-conditions that must hold afterwards. The degree of difficulty in showing that pre- and post-conditions hold is directly related to the complexity of the statement, the state the statement can modify, and the complexity of the properties the pre- and post-conditions express. Around 80% of the properties we show relate to preserving invariants.

正确性证明的主体可以被认为是在每个规范级别的程序语句和函数上显示Hoare三元组。在我们的精炼和Hoare逻辑框架中的证明是沿着函数边界分解的。每个证明单元都有一组在执行前需要保存的先决条件，函数中修改系统状态的语句或语句序列，以及必须在执行后保存的后置条件。前置条件和后置条件的难易程度与语句的复杂性、语句可以修改的状态以及前置条件和后置条件所表达的属性的复杂性直接相关。我们所展示的大约80%的属性与保持不变量有关。

To make verification of the kernel feasible, its design should minimise the complexity of these components. Ideally, the kernel code (and associated proofs) would consist of simple statements that rely on explicit local state, with simple invariants. These smaller elements could then be composed abstractly into larger elements that avoid exposing underlying local elements. Unfortunately, OS kernels are not usually structured like this, and generally feature highly inter-dependent subsystems [10].

- [10] I. T. Bowman, R. C. Holt, and N. V. Brewster. Linux as a case study: its extracted software architecture. In ICSE ’99: Proc. 21st Int. Conf. on Software Engineering, pages 555–563. ACM, 1999.

为了使内核验证可行，其设计应该将这些组件的复杂性降到最低。理想情况下，内核代码(和相关的证明)将由一些简单的语句组成，这些语句依赖于显式的局部状态，并带有简单的不变量。这些较小的元素可以抽象地组合成更大的元素，以避免暴露底层的局部元素。不幸的是，OS内核通常不是这样结构的，通常以高度相互依赖的子系统[10]为特征。

As a consequence of our design goal of suitability for reallife use, our kernel design attempts to minimise the proof complexity without compromising performance. In this light we will now examine typical properties of kernels and discuss their effect on verification, including presenting specific features of our kernel design.

由于我们的设计目标是适合现实生活使用，我们的内核设计试图在不影响性能的情况下最小化证明的复杂性。在这种情况下，我们现在将研究内核的典型属性并讨论它们对验证的影响，包括展示内核设计的特定特性。

### 3.1 Global variables and side effect

Programming with global variables and with side effects is common in operating systems kernels and our verification technique has no problem dealing with them. However, implicit state updates and complex use of the same global for different purposes can make verification harder than necessary.

带有全局变量和副作用的编程在操作系统内核中很常见，我们的验证技术处理它们没有问题。然而，隐式状态更新和为不同的目的而复杂地使用相同的全局变量会使验证变得比必要的更困难。

Global variables usually require stating and proving invariant properties. For example, if global scheduler queues are implemented as doubly-linked lists, the corresponding invariant might state that all back links in the list point to the appropriate nodes and that all elements point to thread control blocks. Invariants are expensive because they need to be proved not only locally for the functions that directly manipulate the scheduler queue, but for the whole kernel— we have to show that no other pointer manipulation in the kernel accidentally destroys the list or its properties. This proof can be easy or hard, depending on how modularly the global variable is used.

全局变量通常需要声明和证明不变属性。例如，如果全局调度器队列被实现为双链表，相应的不变式可能声明列表中的所有反向链接指向适当的节点，而所有元素指向线程控制块。不变量代价高昂，因为它不仅需要对直接操作调度器队列的函数进行局部证明，而且需要对整个内核进行证明——我们必须证明内核中的其他指针操作不会意外地破坏列表或其属性。这个证明可以简单也可以困难，这取决于全局变量的模块化使用方式。

A hypothetical, problematic example would be a complex, overloaded page-table data structure that can represent translation, validity of memory regions, copy-on-write, zeroon- demand memory, and the location of data in swap space, combined with a relationship to the frame table. This would create a large, complex invariant for each of the involved data structures, and each of the involved operations would have to preserve all of it.

一个假设的、有问题的示例是一个复杂的、重载的页表数据结构，它可以表示转换、内存区域的有效性、写时复制、零随需应变内存以及交换空间中数据的位置，以及与框架表的关系。这将为每个涉及的数据结构创建一个庞大而复杂的不变量，而每个涉及的操作将不得不保存所有的不变量。

The treatment of globals becomes especially difficult if invariants are temporarily violated. For example, adding a new node to a doubly-linked list temporarily violates invariants that the list is well formed. Larger execution blocks of unrelated code, as in preemption or interrupts, should be avoided during that violation. We address these issues by limiting preemption points, and by deriving the code from Haskell, thus making side effects explicit and bringing them to the attention of the design team.

如果暂时违反不变量，全局变量的处理就变得特别困难。例如，向双链表添加新节点暂时违反了该列表是良构的不变量。不相关代码的更大的执行块，如抢占或中断，应该避免在这种违反期间。我们通过限制优先购买点和从Haskell派生代码来解决这些问题，从而使副作用显式并引起设计团队的注意。

### 3.2 Kernel memory management

The seL4 kernel uses a model of memory allocation that exports control of the in-kernel allocation to appropriately authorised applications [20]. While this model is mostly motivated by the need for precise guarantees of memory consumption, it also benefits verification. The model pushes the policy for allocation outside of the kernel, which means we only need to prove that the mechanism works, not that the user-level policy makes sense. Obviously, moving it into userland does not change the fact that the memory-allocation module is part of the trusted computing base. It does mean, however, that such a module can be verified separately, and can rely on verified kernel properties.

- [20] D. Elkaduwe, P. Derrin, and K. Elphinstone. Kernel design for isolation and assurance of physical memory. In 1st IIES, pages 35–40. ACM SIGOPS, Apr 2008.

seL4内核使用内存分配模型，该模型将内核内分配的控制导出到经过适当授权的应用程序[20]。虽然这个模型主要是为了精确保证内存消耗，但它也有利于验证。模型将分配策略推到内核之外，这意味着我们只需要证明该机制有效，而不需要证明用户级策略有意义。显然，将内存分配模块转移到用户空间并不会改变内存分配模块是可信计算基础的一部分这一事实。但是，这确实意味着这样的模块可以单独验证，并且可以依赖已验证的内核属性。

The correctness of the allocation algorithm involves checks that new objects are wholly contained within an untyped (free) memory region and that they do not overlap with any other objects allocated from the region. Our memory allocation model keeps track of capability derivations in a treelike structure, whose nodes are the capabilities themselves.

分配算法的正确性包括检查新对象是否完全包含在一个无类型(空闲)内存区域中，以及它们是否与从该区域分配的任何其他对象重叠。我们的内存分配模型在树形结构中跟踪能力的派生，其节点就是能力本身。

Before re-using a block of memory, all references to this memory must be invalidated. This involves either finding all outstanding capabilities to the object, or returning the object to the memory pool only when the last capability is deleted. Our kernel uses both approaches.

在重用内存块之前，对该内存的所有引用必须无效。这包括找到对象的所有突出功能，或者只有在最后一个功能被删除时才将对象返回到内存池。我们的内核使用了这两种方法。

In the first approach, the capability derivation tree is used to find and invalidate all capabilities referring to a memory region. In the second approach, the capability derivation tree is used to ensure, with a check that is local in scope, that there are no system-wide dangling references. This is possible because all other kernel objects have further invariants on their own internal references that relate back to the existence of capabilities in this derivation tree.

在第一种方法中，功能派生树用于查找引用一个内存区域的所有功能并使其无效。在第二种方法中，通过本地范围内的检查，使用功能派生树来确保没有系统范围内的悬浮引用。这是可能的，因为所有其他内核对象在它们自己的内部引用上有进一步的不变量，这些不变量与这个派生树中存在的功能相关。

### 3.3 Concurrency and non-determinism

Concurrency is the execution of computation in parallel (in the case of multiple hardware processors), or by nondeterministic interleaving via a concurrency abstraction like threads. Proofs about concurrent programs are hard, much harder than proofs about sequential programs.

并发是并行执行计算(在多个硬件处理器的情况下)，或者通过线程之类的并发抽象通过不确定性交错执行。并行程序的证明是困难的，比顺序程序的证明困难得多。

While we have some ideas on how to construct verifiable systems on multiprocessors, they are outside the scope of this paper and left for future work. In this paper we focus on uniprocessor support where the degree of interleaving of execution and non-determinism can be controlled. However, even on a uniprocessor there is some remaining concurrency resulting from asynchronous I/O devices. seL4 avoids much of the complications resulting from I/O by running device drivers at user level, but it must still address interrupts.

虽然我们对如何在多处理器上构建可验证的系统有一些想法，但它们不在本文的范围内，留给未来的工作。在这篇文章中，我们关注于单处理器的支持，其中执行交错的程度和非确定性可以被控制。然而，即使在单处理器上，也存在一些由异步I/O设备产生的剩余并发。seL4通过在用户级运行设备驱动程序，避免了I/O带来的很多并发症，但它仍然必须处理中断。

Consider the small code fragment A; X; B, where A must establish the state that X relies on, X must establish the state B relies on, and so on. Concurrency issues in the verification of this code arise from yielding, interrupts and exceptions.

考虑小代码片段A;X;B, A必须建立X所依赖的状态，X必须建立B所依赖的状态，以此类推。此代码验证中的并发问题源于yielding、中断和异常。

Yielding at X results in the potential execution of any reachable activity in the system. This implies A must establish the preconditions required for all reachable activities, and all reachable activities on return must establish the preconditions of B. Yielding increases complexity significantly and makes verification harder. Preemption is a non-deterministically optional yield. Blocking kernel primitives, such as in lock acquisition and waiting on condition variables, are also a form of non-deterministic yield.

在X处让步会导致系统中任何可达活动的潜在执行。这意味着A必须为所有可达到的活动建立前提条件，而所有可达到的活动在返回时必须建立b的前提条件。优先购买权是一种非确定性的可选收益率。阻塞内核原语，例如锁定获取和等待条件变量，也是一种非确定性的生成形式。

By design, we side-step addressing the verification complexity of yield by using an event-based kernel execution model, with a single kernel stack, and a mostly atomic application programming interface [25].

通过设计，我们使用基于事件的内核执行模型，使用单个内核堆栈和主要是原子化的应用程序编程接口[25]，从而避免了对yield验证复杂性的处理。

Interrupt complexity has two forms: non-deterministic execution of the interrupt handlers, and interrupt handling resulting in preemption (as a result of timer ticks). Theoretically, this complexity can be avoided by disabling interrupts during kernel execution. However, this would be at the expense of large or unbounded interrupt latency, which we consider unacceptable.

中断复杂性有两种形式:中断处理程序的非确定性执行和导致抢占的中断处理(作为计时器计时的结果)。理论上，这种复杂性可以通过在内核执行期间禁用中断来避免。但是，这将以巨大的或无限制的中断延迟为代价，我们认为这是不可接受的。

Instead, we run the kernel with interrupts mostly disabled, except for a small number of carefully-placed interrupt points. If, in the above code fragment, X is the interrupt point, A must establish the state that all interrupt handlers rely on, and all reachable interrupt handlers must establish or preserve the properties B relies on.

相反，我们在运行内核时禁用了中断，除了少数精心放置的中断点。在上面的代码片段中，如果X是中断点，则A必须建立所有中断处理程序所依赖的状态，而所有可到达的中断处理程序必须建立或保存B所依赖的属性。

We simplify the problem further by implementing interrupt points via polling, rather than temporary enabling of interrupts. On detection of a pending interrupt, we explicitly return through the function call stack to the kernel/user boundary. At the boundary we leave a (potentially modified) event stored in the saved user-level registers. The interrupt becomes a new kernel event (prefixed to the pending usertriggered event). After the in-kernel component of interrupt handling, the interrupted event is restarted. This effectively re-tries the (modified) operation, including re-establishing all the preconditions for execution. In this way we avoid the need for any interrupt-point specific post-conditions for interrupt handlers, but still achieve Fluke-like partial preemptability [25].

- [25] B. Ford, M. Hibler, J. Lepreau, R. McGrath, and P. Tullmann. Interface and execution models in the Fluke kernel. In 3rd OSDI. USENIX, Feb 1999.

我们通过轮询而不是临时启用中断来实现中断点，从而进一步简化了这个问题。在检测到一个挂起的中断时，我们通过函数调用堆栈显式地返回到内核/用户边界。在边界处，我们将一个(可能已修改的)事件存储在已保存的用户级寄存器中。中断成为一个新的内核事件(以挂起的用户触发事件为前缀)。在中断处理的内核组件之后，中断的事件被重新启动。这有效地重试了(修改的)操作，包括重新建立执行的所有先决条件。通过这种方式，我们避免了对中断处理程序使用任何特定于中断点的后置条件，但仍然实现了类似意外的部分可抢占性[25]。

The use of interrupt points creates a trade-off, controlled by the kernel designer, between proof complexity and interrupt processing latency. Almost all of seL4’s operations have short and bounded latency, and can execute without any interrupt points at all. The exception is object destruction, whose cleanup operations are inherently unbounded, and are, of course, critical to kernel integrity.

中断点的使用会在证明复杂性和中断处理延迟之间产生一种权衡，这种权衡由内核设计者控制。几乎seL4的所有操作都有短而有界的延迟，并且完全可以在没有任何中断点的情况下执行。对象销毁是一个例外，它的清理操作本质上是无限制的，当然，这对内核完整性至关重要。

We make these operations preemptable by storing the state of progress of destruction in the last capability referencing the object being destroyed; we refer to this as a zombie capability. This guarantees that the correctness of a restarted destroy is not dependent on user-accessible registers. Another advantage of this approach is that if another user thread attempts to destroy the zombie, it will simply continue where the first thread was preempted (a form of priority inheritance), instead of making the new thread dependent (blocked) on the completion of another.

我们通过在最后一个引用被销毁对象的能力中存储销毁进程的状态，使这些操作具有可抢占性;我们将其称为僵尸功能。这保证了重新启动的销毁的正确性不依赖于用户可访问的寄存器。这种方法的另一个优点是，如果另一个用户线程试图销毁zombie，它将简单地继续第一个线程被抢占(一种优先级继承形式)，而不是使新线程依赖于(阻塞)另一个线程的完成。

Exceptions are similar to interrupts in their effect, but are synchronous in that they result directly from the code being executed and cannot be deferred. In the seL4 kernel we avoid exceptions completely and much of that avoidance is guaranteed as a side-effect of verification. Special care is required only for memory faults.

异常在效果上与中断类似，但它们是同步的，因为它们直接由正在执行的代码产生，不能被延迟。在seL4内核中，我们完全避免了异常，并且保证这种避免是验证的副作用。仅对内存错误需要特别注意。

We avoid having to deal with virtual-memory exceptions in kernel code by mapping a fixed region of the virtual address space to physical memory, independent of whether it is actively used or not. The region contains all the memory the kernel can potentially use for its own internal data structures, and is guaranteed to never produce a fault. We prove that this region appears in every virtual address space.

通过将虚拟地址空间的一个固定区域映射到物理内存，我们避免了必须在内核代码中处理虚拟内存异常，而与它是否被积极使用无关。该区域包含内核可能用于其内部数据结构的所有内存，并且保证不会产生错误。我们证明了该区域出现在每个虚拟地址空间中。

Arguments passed to the kernel from user level are either transferred in registers or limited to preregistered physical frames accessed through the kernel region.

从用户级传递到内核的参数要么在寄存器中传输，要么限制为通过内核区域访问的预先注册的物理帧。

### 3.4 I/O

As described earlier we avoid most of the complexity of I/O by moving device drivers into protected user-mode components. When processing an interrupt event, our interrupt delivery mechanism determines the interrupt source, masks further interrupts from that specific source, notifies the registered user-level handler (device driver) of the interrupt, and unmasks the interrupt when the handler acknowledges the interrupt.

如前所述，我们通过将设备驱动程序移动到受保护的用户模式组件中来避免I/O的大部分复杂性。当处理一个中断事件时，我们的中断传递机制确定中断源，屏蔽来自那个特定源的进一步中断，通知已注册的用户级处理程序(设备驱动程序)中断，并在处理程序确认中断时解禁中断。

We coarsely model the hardware interrupt controller of the ARM platform to include interrupt support in the proof. The model includes existence of the controller, masking of interrupts, and that interrupts only occur if unmasked. This is sufficient to include interrupt controller access, and basic behaviour in the proof, without modelling correctness of the interrupt controller management in detail. The proof is set up such that it is easy to include more detail in the hardware model should it become necessary later to prove additional properties.

我们对ARM平台的硬件中断控制器进行了粗略的建模，以包含中断支持。该模型包括控制器的存在性、中断的屏蔽性以及中断仅在未屏蔽时才会发生。这足以包括中断控制器访问，和基本行为的证明，没有建模的正确性的中断控制器管理的细节。如果以后需要证明其他属性，那么可以很容易地在硬件模型中包含更多细节。

Our kernel contains a single device driver, the timer driver, which generates timer ticks for the scheduler. This is set up in the initialisation phase of the kernel as an automatically reloaded source of regular interrupts. It is not modified or accessed during the execution of the kernel. We did not need to model the timer explicitly in the proof, we just prove that system behaviour on each tick event is correct.

我们的内核包含一个设备驱动程序，即计时器驱动程序，它为调度程序生成计时器刻度。这是在内核的初始化阶段设置的，作为一个自动重新加载的常规中断源。它不会在内核执行期间被修改或访问。我们不需要在proof中显式地建模计时器，我们只需要证明系统在每个tick事件上的行为是正确的。

### 3.5 Observations

The requirements of verification force the designers to think of the simplest and cleanest way of achieving their goals. We found repeatedly that this leads to overall better design, which tends to reduce the likelihood of bugs.

验证的需求迫使设计人员考虑实现其目标的最简单和最干净的方法。我们反复发现，这将带来更好的总体设计，从而降低出现错误的可能性。

In a number of cases there were significant other benefits. This is particularly true for the design decisions aimed at simplifying concurrency-related verification issues. Nonpreemptable execution (except for a few interrupt-points) has traditionally been used in L4 kernels to maximise averagecase performance. Recent L4 kernels aimed at embedded use [32] have adopted an event-based design to reduce the kernel’s memory footprint (due to the use of a single kernel stack rather than per-thread stacks).

在许多情况下，还有其他显著的好处。对于旨在简化与并发相关的验证问题的设计决策来说，尤其如此。传统上，L4内核使用不可抢占的执行(除了少数中断点)来最大化平均用例性能。最近针对嵌入式使用[32]的L4内核采用了基于事件的设计来减少内核的内存占用(由于使用了单个内核堆栈而不是每个线程堆栈)。

## 4. seL4 VERIFICATION

This section describes each of the specification layers as well as the proof in more detail.

本节将更详细地描述每个规范层以及证明。

### 4.1 Abstract specification

The abstract level describes what the system does without saying how it is done. For all user-visible kernel operations it describes the functional behaviour that is expected from the system. All implementations that refine this specification will be binary compatible.

抽象层描述系统做什么，而不说明如何做。对于所有用户可见的内核操作，它描述了系统预期的功能行为。改进该规范的所有实现都是二进制兼容的。

We precisely describe argument formats, encodings and error reporting, so for instance some of the C-level size restrictions become visible on this level. In order to express these, we rarely make use of infinite types like natural numbers. Instead, we use finite machine words, such as 32-bit integers. We model memory and typed pointers explicitly. Otherwise, the data structures used in this abstract specification are high-level — essentially sets, lists, trees, functions and records. We make use of non-determinism in order to leave implementation choices to lower levels: If there are multiple correct results for an operation, this abstract layer would return all of them and make clear that there is a choice. The implementation is free to pick any one of them.

我们精确地描述参数格式、编码和错误报告，因此，例如，在这个级别上可以看到一些c级的大小限制。为了表达这些，我们很少使用无限类型，比如自然数。相反，我们使用有限的机器字，比如32位整数。我们显式地建模内存和类型指针。此外，在这个抽象规范中使用的数据结构是高级的——本质上是集、列表、树、函数和记录。我们利用非确定性将实现选择留给更低的层次:如果一个操作有多个正确的结果，这个抽象层将返回所有结果，并表明存在选择。实现可以自由选择其中任何一个。

An example of this is scheduling. No scheduling policy is defined at the abstract level. Instead, the scheduler is modelled as a function picking any runnable thread that is active in the system or the idle thread. The Isabelle/HOL code for this is shown in Fig. 3. The function all_active_tcbs returns the abstract set of all runnable threads in the system. Its implementation (not shown) is an abstract logical predicate over the whole system. The select statement picks any element of the set. The OR makes a non-deterministic choice between the first block and switch_to_idle_thread. The executable specification makes this choice more specific.

这方面的一个例子是调度。没有在抽象级别定义调度策略。相反，调度程序被建模为一个函数，它选择系统中活动的任何可运行线程或空闲线程。Isabelle/HOL代码为此显示在图3中。函数all_active_tcbs返回系统中所有可运行线程的抽象集。它的实现(未显示)是对整个系统的抽象逻辑谓词。select语句选择集合中的任何元素。OR在第一个块和switch_to_idle_thread之间进行非确定性选择。可执行规范使这种选择更加具体。

### 4.2 Executable specification

The purpose of the executable specification is to fill in the details left open at the abstract level and to specify how the kernel works (as opposed to what it does). While trying to avoid the messy specifics of how data structures and code are optimised in C, we reflect the fundamental restrictions in size and code structure that we expect from the hardware and the C implementation. For instance, we take care not to use more than 64 bits to represent capabilities, exploiting for instance known alignment of pointers. We do not specify in which way this limited information is laid out in C.

可执行规范的目的是填充在抽象层开放的细节，并指定内核如何工作(而不是它做什么)。在试图避免在C中优化数据结构和代码的混乱细节的同时，我们反映了我们期望从硬件和C实现在大小和代码结构方面的基本限制。例如，我们注意不要使用超过64位的数据来表示功能，例如利用已知的指针对齐。我们没有具体说明在C语言中如何列出这些有限的信息。

The executable specification is deterministic; the only nondeterminism left is that of the underlying machine. All data structures are now explicit data types, records and lists with straightforward, efficient implementations in C. For example the capability derivation tree of seL4, modelled as a tree on the abstract level, is now modelled as a doubly linked list with limited level information. It is manipulated explicitly with pointer-update operations.

可执行的规范是确定的;剩下的唯一不确定性是底层机器的不确定性。所有的数据结构现在都是显式的数据类型、记录和列表，在c语言中有直接、有效的实现。例如，seL4的功能派生树，在抽象层上被建模为树，现在被建模为具有有限层信息的双重链表。通过指针更新操作显式地操作它。

Fig. 4 shows part of the scheduler specification at this level. The additional complexity becomes apparent in the chooseThread function that is no longer merely a simple predicate, but rather an explicit search backed by data structures for priority queues. The specification fixes the behaviour of the scheduler to a simple priority-based round-robin algorithm. It mentions that threads have time slices and it clarifies when the idle thread will be scheduled. Note that priority queues duplicate information that is already available (in the form of thread states), in order to make it available efficiently. They make it easy to find a runnable thread of high priority. The optimisation will require us to prove that the duplicated information is consistent.

图4显示了该级别的部分调度器规范。在chooseThread函数中，额外的复杂性变得很明显，它不再只是一个简单的谓词，而是由数据结构支持的优先级队列的显式搜索。该规范将调度器的行为修复为一种简单的基于优先级的循环算法。它提到了线程有时间片，并阐明了空闲线程何时被调度。请注意，优先级队列重复了已经可用的信息(以线程状态的形式)，以便使其有效可用。它们可以很容易地找到高优先级的可运行线程。优化需要我们证明重复的信息是一致的。

We have proved that the executable specification correctly implements the abstract specification. Because of its extreme level of detail, this proof alone already provides stronger design assurance than has been shown for any other generalpurpose OS kernel.

我们证明了可执行规范正确地实现了抽象规范。由于其极端的详细程度，仅此证明就提供了比任何其他通用操作系统内核更强的设计保证。

### 4.3 C implementation

The most detailed layer in our verification is the C implementation. The translation from C into Isabelle is correctness-critical and we take great care to model the semantics of our C subset precisely and foundationally. Precisely means that we treat C semantics, types, and memory model as the standard prescribes, for instance with architecture-dependent word size, padding of structs, typeunsafe casting of pointers, and arithmetic on addresses. As kernel programmers do, we make assumptions about the compiler (GCC) that go beyond the standard, and about the architecture used (ARMv6). These are explicit in the model, and we can therefore detect violations. Foundationally means that we do not just axiomatise the behaviour of C on a high level, but we derive it from first principles as far as possible. For example, in our model of C, memory is a primitive function from addresses to bytes without type information or restrictions. On top of that, we specify how types like unsigned int are encoded, how structures are laid out, and how implicit and explicit type casts behave. We managed to lift this low-level memory model to a highlevel calculus that allows efficient, abstract reasoning on the type-safe fragment of the kernel [62, 63, 65]. We generate proof obligations assuring the safety of each pointer access and write. They state that the pointer in question must be non-null and of the correct alignment. They are typically easy to discharge. We generate similar obligations for all restrictions the C99 standard demands.

在我们的验证中最详细的一层是C实现。从C到Isabelle的转换是正确的，我们非常注意精确和基础地建模我们的C子集的语义。精确地意味着我们将C语义、类型和内存模型作为标准规定，例如与体系结构相关的字大小、结构的填充、类型不安全的指针转换和地址的算术。正如内核程序员所做的那样，我们对超越标准的编译器(GCC)以及所使用的架构(ARMv6)进行了假设。这些在模型中是显式的，因此我们可以检测违规。在基础上意味着我们不仅要在高层次上证明C的行为，而且要尽可能从基本原理中推导出它。例如，在我们的C模型中，memory是一个从地址到字节的原始函数，没有类型信息或限制。在此基础上，我们指定像无符号int这样的类型如何编码、结构如何布局以及隐式和显式类型强制转换的行为方式。我们设法将这个低级的内存模型提升到一个高级的演算，允许对内核的类型安全片段进行有效的、抽象的推理[62,63,65]。我们生成保证每个指针访问和写入的安全性的证明义务。它们指出，有问题的指针必须是非空的，并且对齐方式正确。他们通常很容易出院。我们为C99标准要求的所有限制产生类似的义务。

We treat a very large, pragmatic subset of C99 in the verification. It is a compromise between verification convenience and the hoops the kernel programmers were willing to jump through in writing their source. The following paragraphs describe what is not in this subset.

我们在验证中处理一个非常大的、实用的C99子集。这是验证便利和内核程序员在编写源代码时愿意跳过的障碍之间的妥协。下面的段落描述了不属于这个子集的内容。

We do not allow the address-of operator & on local variables, because, for better automation, we make the assumption that local variables are separate from the heap. This could be violated if their address was available to pass on. It is the most far-reaching restriction we implement, because it is common to use local variable references for return parameters of large types that we do not want to pass on the stack. We achieved compliance with this requirement by avoiding reference parameters as much as possible, and where they were needed, used pointers to global variables (which are not restricted).

我们不允许在局部变量上使用address-of操作符&，因为为了更好地实现自动化，我们假设局部变量与堆是分开的。如果他们的地址是可传递的，这可能会被违反。这是我们实现的影响最深远的限制，因为对于不希望传递到堆栈上的大型类型的返回参数，通常使用局部变量引用。我们尽可能地避免引用参数，并在需要引用参数的地方使用指向全局变量(不受限制)的指针，从而满足了这一要求。

One feature of C that is problematic for verification (and programmers) is the unspecified order of evaluation in expressions with side effects. To deal with this feature soundly, we limit how side effects can occur in expressions. If more than one function call occurs within an expression or the expression otherwise accesses global state, a proof obligation is generated to show that these functions are side-effect free. This proof obligation is discharged automatically.

对于验证(和程序员)来说，C的一个有问题的特性是表达式中未指定的求值顺序，并带有副作用。为了更好地处理这个特性，我们限制了表达式中可能出现的副作用。如果一个表达式中发生多个函数调用，或者该表达式访问全局状态，则生成一个证明义务，以表明这些函数没有副作用。此举证义务自动解除。

We do not allow function calls through function pointers. (We do allow handing the address of a function to assembler code, e.g. for installing exception vector tables.) We also do not allow goto statements, or switch statements with fallthrough cases. We support C99 compound literals, making it convenient to return structs from functions, and reducing the need for reference parameters. We do not allow compound literals to be lvalues. Some of these restrictions could be lifted easily, but the features were not required in seL4.

我们不允许通过函数指针调用函数。(我们允许将一个函数的地址传递给汇编代码，例如用于安装异常向量表。)我们还不允许使用goto语句，也不允许使用fall through案例切换语句。我们支持C99复合文字，这使得从函数返回struct很方便，并且减少了对引用参数的需要。我们不允许复合文字是左值。其中一些限制可以很容易地取消，但是seL4并不需要这些特性。

We did not use unions directly in seL4 and therefore do not support them in the verification (although that would be possible). Since the C implementation was derived from a functional program, all unions in seL4 are tagged, and many structs are packed bitfields. Like other kernel implementors, we do not trust GCC to compile and optimise bitfields predictably for kernel code. Instead, we wrote a small tool that takes a specification and generates C code with the necessary shifting and masking for such bitfields. The tool helps us to easily map structures to page table entries or other hardware-defined memory layouts. The generated code can be inlined and, after compilation on ARM, the result is more compact and faster than GCC’s native bitfields. The tool not only generates the C code, it also automatically generates Isabelle/HOL specifications and proofs of correctness [13].

我们在seL4中没有直接使用union，因此在验证中不支持它们(尽管这是可能的)。因为C实现是从一个函数程序派生出来的，所以seL4中的所有union都被标记，而且许多struct都被打包成位字段。像其他内核实现者一样，我们不相信GCC可以为内核代码编译和优化位字段。相反，我们编写了一个小工具，它接受一个规范并生成C代码，并对这些位域进行必要的移动和屏蔽。该工具帮助我们轻松地将结构映射到页表条目或其他硬件定义的内存布局。生成的代码可以内联，并且在ARM上编译后，结果比GCC的本机位字段更紧凑、更快。该工具不仅生成C代码，还自动生成Isabelle/HOL规范和[13]正确性证明。

Fig. 5 shows part of the implementation of the scheduling functionality described in the previous sections. It is standard C99 code with pointers, arrays and structs. The thread_state functions used in Fig. 5 are examples of generated bitfield accessors.

图5显示了前几节中描述的调度功能的部分实现。它是带有指针、数组和结构体的标准C99代码。图5中使用的thread_state函数是生成位域访问器的例子。

### 4.4 Machine model

Programming in C is not sufficient for implementing a kernel. There are places where the programmer has to go outside the semantics of C to manipulate hardware directly. In the easiest case, this is achieved by writing to memorymapped device registers, as for instance with a timer chip; in other cases one has to drop down to assembly to implement the required behaviour, as for instance with TLB flushes.

用C语言编程不足以实现内核。有些地方，程序员必须跳出C的语义来直接操作硬件。在最简单的情况下，这是通过写入到内存映射设备寄存器来实现的，例如与计时器芯片;在其他情况下，必须使用程序集来实现所需的行为，例如使用TLB刷新。

Presently, we do not model the effects of certain direct hardware instructions because they are too far below the abstraction layer of C. Of these, cache and TLB flushes are relevant for the correctness of the code, and we rely on traditional testing for these limited number of cases. Higher assurance can be obtained by adding more detail to the machine model—we have phrased the machine interface such that future proofs about the TLB and cache can be added with minimal changes. Additionally, required behaviour can be guaranteed by targeted assertions (e.g., that page-table updates always flush the TLB), which would result in further proof obligations.

目前，我们并不对某些直接硬件指令的影响进行建模，因为它们远低于c的抽象层。在这些指令中，缓存和TLB刷新与代码的正确性有关，对于这些有限数量的情况，我们依赖于传统的测试。通过向机器模型中添加更多细节，可以获得更高的保证——我们已经对机器接口进行了修饰，这样将来关于TLB和缓存的证明就可以添加到最小的更改中。此外，所需的行为可以由目标断言来保证(例如，页表更新总是刷新TLB)，这将导致进一步的证明义务。

The basis of this formal model of the machine is the internal state of the relevant devices, collected in one record machine_state. For devices that we model more closely, such as the interrupt controller, the relevant part in machine_state contains details such as which interrupts are currently masked. For the parts that we do not model, such as the TLB, we leave the corresponding type unspecified, so it can be replaced with more details later.

此机器形式化模型的基础是相关设备的内部状态，收集在一个记录machine_state中。对于我们建模更紧密的设备，比如中断控制器，machine_state中的相关部分包含了一些细节，比如当前哪些中断被屏蔽了。对于我们没有建模的部分，比如TLB，我们未指定相应的类型，以便以后可以用更多的细节替换它。

Fig. 6 shows our machine interface. The functions are all of type X machine_m which restricts any side effects to the machine_state component of the system. Most of the functions return nothing (type unit), but change the state of a device. In the abstract and executable specification, these functions are implemented with maximal non-determinism. This means that in the extreme case they may arbitrarily change their part of the machine state. Even for devices that we model, we are careful to leave as much behaviour as possible non-deterministic. The less behaviour we prescribe, the less assumptions the model makes about the hardware.

图6为我们的机器界面。这些函数都是X machine_m类型，它将任何副作用限制在系统的machine_state组件上。大多数函数不返回任何东西(类型单元)，但是改变设备的状态。在抽象和可执行的规范中，这些函数的实现具有极大的非确定性。这意味着，在极端情况下，它们可以任意更改机器的部分状态。即使是对我们建模的设备，我们也会小心地让尽可能多的行为不具有确定性。我们规定的行为越少，模型对硬件的假设就越少。

In the seL4 implementation, the functions in Fig. 6 are implemented in C where possible, and otherwise in assembly; we must check (but we do not prove) that the implementations match the assumptions we make in the levels above. An example is the function getIFSR, which on ARM returns the instruction fault status register after a page fault. For this function, which is basically a single assembly instruction, we only assume that it does not change the memory state of the machine, which is easy to check.

在seL4实现中，图6中的功能尽可能用C实现，否则用汇编实现;我们必须检查(但我们不证明)实现是否符合我们在上面的级别所做的假设。一个例子是getIFSR函数，它在ARM上在出现页面错误后返回指令错误状态寄存器。这个函数基本上是一条组装指令，我们只假设它不会改变机器的内存状态，这很容易检查。

### 4.5 The proof

This section describes the main theorem we have shown and how its proof was constructed. As mentioned, the main property we are interested in is functional correctness, which we prove by showing formal refinement. We have formalised this property for general state machines in Isabelle/HOL, and we instantiate each of the specifications in the previous sections into this statemachine framework.

本节描述我们所展示的主要定理以及它的证明是如何建立的。如前所述，我们感兴趣的主要性质是函数正确性，我们通过展示形式的细化来证明它。我们已经在Isabelle/HOL中为通用状态机形式化了这个属性，并且在前面的部分中我们将每个规范实例化到这个状态机框架中。



## 5. EXPERIENCE AND LESSONS LEARNT

### 5.1 Performance

### 5.2 Verification effort

### 5.3 The cost of change

## 6. RELATEDWORK

We briefly summarise the literature on OS verification.
Klein [40] provides a comprehensive overview.

- [40] G. Klein. Operating system verification — an overview. S¯adhan¯a, 34(1):27–69, Feb 2009.

我们简要总结了关于操作系统验证的文献。Klein[40]提供了一个全面的概述。

The first serious attempts to verify an OS kernel were in the late 1970s UCLA Secure Unix [67] and the Provably Secure Operating System (PSOS) [24]. Our approach mirrors the UCLA effort in using refinement and defining functional correctness as the main property to prove. The UCLA project managed to finish 90 % of their specification and 20 % of their proofs in 5 py. They concluded that invariant reasoning dominated the proof effort, which we found confirmed in our project.

对操作系统内核的第一次真正的验证是在20世纪70年代后期的UCLA Secure Unix[67]和可证实的安全操作系统(PSOS)[24]。我们的方法反映了UCLA在使用细化和定义函数正确性作为主要证明性质方面的努力。UCLA项目设法在5 py中完成了90%的规范和20%的证明。他们得出结论，不变量推理主导了证明的努力，我们发现在我们的项目中得到了证实。

PSOS was mainly focussed on formal kernel design and never completed any substantial implementation proofs. Its design methodology was later used for the Kernelized Secure Operating System (KSOS) [52] by Ford Aerospace. The Secure Ada Target (SAT) [30] and the Logical Coprocessor Kernel (LOCK) [55] are also inspired by the PSOS design and methodology.

PSOS主要专注于正式的内核设计，从未完成任何实质性的实现证明。它的设计方法后来被福特航空航天公司用于内核化安全操作系统(KSOS)[52]。安全Ada目标(SAT)[30]和逻辑协处理器内核(LOCK)[55]也受到PSOS设计和方法的启发。

In the 1970s, machine support for theorem proving was rudimentary. Basic language concepts like pointers still posed large problems. The UCLA effort reports that the simplifications required to make verification feasible made the kernel an order of magnitude slower [67]. We have demonstrated that with modern tools and techniques, this is no longer the case.

在20世纪70年代，对定理证明的机器支持还很初级。像指针这样的基本语言概念仍然存在很大的问题。加州大学洛杉矶分校的研究报告说，使验证可行所需要的简化使内核速度慢了一个数量级[67]。我们已经证明，随着现代工具和技术的发展，情况已不再如此。

The first real, completed implementation proofs, although
for a highly idealised OS kernel are reported for KIT, consisting of 320 lines of artificial, but realistic assembly instructions [8].

第一个真正的，完成的实现证明，尽管一个高度理想化的操作系统内核是报告KIT，由320行人工，但现实的组装指令[8]。

Bevier and Smith later produced a formalisation of the Mach microkernel [9] without implementations proofs. Other formal modelling and proofs for OS kernels that did not proceed to the implementation level include the EROS kernel [57], the high-level analysis of SELinux [5, 29] based on FLASK [60], and the MASK [48] project which was geared towards information-flow properties.

Bevier和Smith后来在没有实现证明的情况下，生成了Mach微内核[9]的形式化版本。其他未进入实现层的OS内核的形式化建模和证明包括:EROS内核[57]、基于FLASK[60]的SELinux高级分析[5,29]和面向信息流特性的MASK[48]项目。

The VFiasco project [36] and later the Robin project [61] attempted to verify C++ kernel implementations. They managed to create a precise model of a large, relevant part of C++, but did not verify substantial parts of a kernel.

VFiasco项目[36]和后来的Robin项目[61]试图验证c++内核实现。他们成功地创建了c++的一个大的、相关的部分的精确模型，但是没有验证内核的主要部分。

Heitmeyer et al [33] report on the verification and Common Criteria certification of a “software-based embedded device” featuring a small (3,000 LOC) separation kernel. They show data separation only, not functional correctness. Although they seem to at least model the implementation level, they did not conduct a machine-checked proof directly on the C-code.

Heitmeyer等[33]报告了“基于软件的嵌入式设备”的验证和通用标准认证，该设备具有一个小型(3000 LOC)分离内核。它们只显示了数据分离，而不是功能正确性。尽管它们似乎至少在实现级别建模，但它们没有直接在c -代码上执行机器检查证明。

Hardin et al [31] formally verified information-flow properties of the AAMP7 microprocessor [53], which implements the functionality of a static separation kernel in hardware. The functionality provided is less complex than a general purpose microkernel—the processor does not support online reconfiguration of separation domains. The proof goes down to a low-level design that is in close correspondence to the micro code. This correspondence is not proven formally, but by manual inspection.

Hardin等[31]正式验证了AAMP7微处理器[53]的信息流特性，实现了硬件上静态分离内核的功能。提供的功能没有通用微内核复杂——处理器不支持分离域的在线重新配置。这个证明可以追溯到与微代码紧密对应的底层设计。这种通信没有被正式证明，但通过人工检查。

A similar property was recently shown for Green Hills’ Integrity kernel [28] during a Common Criteria EAL6+ certification [27]. The Separation Kernel Protection Profile [38] of Common Criteria shows data separation only. It is a weaker property than full functional correctness.

在通用标准EAL6+认证[27]期间，Green Hills的完整性内核[28]最近也显示了类似的特性。Common Criteria的分离内核保护配置文件[38]只显示数据分离。这是一个比完全函数正确性更弱的性质。

A closely related contemporary project is Verisoft [2], which is attempting to verify not only the OS, but a whole software stack from verified hardware up to verified application programs. This includes a formally verified, non-optimising compiler for their own Pascal-like implementation language. Even if not all proofs are completed yet, the project has successfully demonstrated that such a verification stack for full functional correctness can be achieved. They have also shown that verification of assembly-level code is feasible. However, Verisoft accepts two orders of magnitude slow-down for their highly-simplified VAMOS kernel (e.g. only single-level page tables) and that their verified hardware platform VAMP is not widely deployed. We deal with real C and standard tool chains on ARMv6, and have aimed for a commercially deployable, realistic microkernel.

与之密切相关的当代项目是Verisoft[2]，它不仅试图验证操作系统，还试图验证从验证硬件到验证应用程序的整个软件堆栈。这包括一个正式验证的、非优化的编译器，用于他们自己的类似pascal的实现语言。即使不是所有的证明都完成了，这个项目已经成功地证明了这样一个验证堆栈可以实现完全功能的正确性。他们还证明了程序集级代码的验证是可行的。然而，Verisoft的高度简化的VAMOS内核接受两个数量级的慢化(例如，只有单级页表)，并且他们的验证硬件平台VAMP没有被广泛部署。我们在ARMv6上处理真实的C语言和标准的工具链，目标是实现商业上可部署的、真实的微内核。

Other formal techniques for increasing the trustworthiness of operating systems include static analysis, model checking and shape analysis. Static analysis can in the best case only show the absence of certain classes of defects such as buffer overruns. Model checking in the OS space includes SLAM [6] and BLAST [34]. They can show specific safety properties of C programs automatically, such as correct API usage in device drivers. The terminator tool [15] increases reliability of device drivers by attempting to prove termination automatically. Full functional correctness of a realistic microkernel is still beyond the scope of these automatic techniques.

其他提高操作系统可靠性的正式技术包括静态分析、模型检查和形状分析。静态分析在最好的情况下只能显示某些类型缺陷(如缓冲区溢出)的缺失。OS空间中的模型检查包括SLAM[6]和BLAST[34]。它们可以自动显示C程序的特定安全属性，比如在设备驱动程序中正确使用API。终止器工具[15]通过尝试自动证明终止，提高了设备驱动程序的可靠性。真实的微内核的完全功能正确性仍然超出了这些自动技术的范围。

Implementations of kernels in type-safe languages such as SPIN [7] and Singularity [23] offer increased reliability, but they have to rely on traditional “dirty” code to implement their language runtime, which tends to be substantially bigger than the complete seL4 kernel. While type safety is a good property to have, it is not very strong. The kernel may still misbehave or attempt, for instance, a null pointer access. Instead of randomly crashing, it will report a controlled exception. In our proof, we show a variant of type safety for the seL4 code. Even though the kernel deliberately breaks the C type system, it only does so in a safe way. Additionally, we prove much more: that there will never be any such null pointer accesses, that the kernel will never crash and that the code always behaves in strict accordance with the abstract specification.

使用类型安全的语言(如SPIN[7]和Singularity[23])实现内核提供了更高的可靠性，但它们必须依赖传统的“脏”代码来实现它们的语言运行时，而这些运行时往往比整个seL4内核大得多。虽然类型安全是一个很好的属性，但它不是很强大。内核可能仍然会有错误行为或尝试，例如空指针访问。它将报告一个受控异常，而不是随机崩溃。在我们的证明中，我们展示了seL4代码的类型安全的一个变体。即使内核故意破坏C类型系统，它也只是以一种安全的方式这样做。此外，我们还证明了更多:永远不会有任何这样的空指针访问，内核永远不会崩溃，代码始终严格按照抽象规范运行。

## 7. CONCLUSIONS

We have presented our experience in formally verifying seL4. We have shown that full, rigorous, formal verification is practically achievable for OS microkernels with very reasonable effort compared to traditional development methods.

我们已经介绍了我们在正式验证seL4方面的经验。我们已经证明，与传统的开发方法相比，对于OS微内核来说，通过非常合理的努力，完全、严格、正式的验证实际上是可以实现的。

Although we have not invested significant effort into optimisation, we have shown that optimisations are possible and that performance does not need to be sacrificed for verification. The seL4 kernel is practical, usable, and directly deployable, running on ARMv6 and x86.

尽管我们没有在优化上投入太多精力，但是我们已经证明了优化是可能的，并且不需要为了验证而牺牲性能。seL4内核很实用，可以直接部署，运行在ARMv6和x86上。

Collateral benefits of the verification include our rapid prototyping methodology for kernel design. We observed a confluence of design principles from the formal methods and the OS side, leading to design decisions such as an event-based kernel that is mostly non-preemptable and uses interrupt polling. These decisions made the kernel design simpler and easier to verify without sacrificing performance. Evidence suggests that taking the detour via a Haskell prototype increased our productivity even without considering verification.

验证的附带好处包括我们的内核设计的快速原型方法。我们观察到正式方法和操作系统端的设计原则的融合，导致设计决策，比如基于事件的内核，它主要是非可抢占的，并使用中断轮询。这些决策使得内核设计更简单，更容易验证，而不会牺牲性能。有证据表明，通过Haskell原型可以提高我们的生产力，甚至不考虑验证。

Future work in this project includes verification of the assembly parts of the kernel, a multi-core version of the kernel, as well as application verification. The latter now becomes much more meaningful than previously possible: application proofs can rely on the abstract, formal kernel specification that seL4 is proven to implement.

这个项目的未来工作包括验证内核的组装部分，内核的多核版本，以及应用程序验证。后者现在变得比以前可能的更有意义:应用程序证明可以依赖于被证明可以实现seL4的抽象的、形式化的内核规范。

Compared to the state of the art in software certification, the ultimate degree of trustworthiness we have achieved is redefining the standard of highest assurance.

与软件认证的艺术状态相比，我们所达到的最终可信赖程度是重新定义最高保证的标准。