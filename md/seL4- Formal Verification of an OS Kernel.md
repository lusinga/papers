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

The correctness of the allocation algorithm involves checks
that new objects are wholly contained within an untyped
(free) memory region and that they do not overlap with
any other objects allocated from the region. Our memory
allocation model keeps track of capability derivations in a treelike
structure, whose nodes are the capabilities themselves.

Before re-using a block of memory, all references to this
memory must be invalidated. This involves either finding
all outstanding capabilities to the object, or returning the
object to the memory pool only when the last capability is
deleted. Our kernel uses both approaches.

In the first approach, the capability derivation tree is used
to find and invalidate all capabilities referring to a memory
region. In the second approach, the capability derivation tree
is used to ensure, with a check that is local in scope, that
there are no system-wide dangling references. This is possible
because all other kernel objects have further invariants on
their own internal references that relate back to the existence
of capabilities in this derivation tree.

### 3.3 Concurrency and non-determinism

### 3.4 I/O

### 3.5 Observations

## 4. seL4 VERIFICATION

### 4.1 Abstract specification

### 4.2 Executable specification

### 4.3 C implementation

### 4.4 Machine model

### 4.5 The proof

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