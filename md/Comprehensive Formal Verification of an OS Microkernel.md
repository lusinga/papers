# Comprehensive Formal Verification of an OS Microkernel

We present an in-depth coverage of the comprehensive machine-checked formal verification of seL4, a general-purpose operating system microkernel.

我们将深入介绍seL4(一种通用操作系统微内核)的全面机器检查形式验证。

We discuss the kernel design we used to make its verification tractable. We then describe the functional correctness proof of the kernel’s C implementation and we cover further steps that transform this result into a comprehensive formal verification of the kernel: a formally verified IPC fastpath, a proof that the binary code of the kernel correctly implements the C semantics, a proof of correct access-control enforcement, a proof of information-flow noninterference, a sound worst-case execution time analysis of the binary, and an automatic initialiser for user-level systems that connects kernel-level access-control enforcement with reasoning about system behaviour. We summarise these results and show how they integrate to form a coherent overall analysis, backed by machine-checked, end-to-end theorems.

我们讨论了使其验证易于处理的内核设计。然后，我们描述了内核的C实现的功能正确性证明，并介绍了进一步的步骤，将这个结果转换成一个全面的内核的正式验证:一个经过正式验证的IPC快速路径，一个内核的二进制代码正确实现C语义的证明，一个正确的访问控制实施的证明，一个信息流不受干扰的证明，一个完善的二进制执行时间最坏情况分析，以及一个用于用户级系统的自动初始化器，它将内核级访问控制的实施与系统行为的推理联系起来。我们总结了这些结果，并展示了它们是如何结合起来形成一个连贯的整体分析，并以机器检查的端到端定理作为支持。

The seL4 microkernel is currently not just the only general-purpose operating system kernel that is fully formally verified to this degree. It is also the only example of formal proof of this scale that is kept current as the requirements, design and implementation of the system evolve over almost a decade. We report on our experience in maintaining this evolving formally verified code base.

seL4微内核目前并不是唯一经过正式验证的通用操作系统内核。它也是在近十年的时间里，随着系统需求、设计和实现的发展而保持最新的这种规模的正式证明的唯一例子。我们报告了我们在维护这个演进的正式验证代码库方面的经验。

## 1. INTRODUCTION

This article presents a detailed coverage of the comprehensive formal verification of the seL4 microkernel, from its initial functional correctness proof to more recent results, which extend the assurance argument up to higher-level security properties and down to the binary level of its implementation.

本文详细介绍了seL4微内核的全面形式验证，从最初的功能正确性证明到最近的结果，这些验证将保证参数扩展到更高级别的安全属性，并向下扩展到其实现的二进制级别。

The target of our verification, the kernel, is the most critical part of a system, which is our motivation for starting system verification with this component. The customary definition of a kernel is the software that executes in the privileged mode of the hardware, meaning that there can be no protection from faults occurring in the kernel, and every single bug can potentially cause arbitrary damage. Microkernels are motivated by the desire to minimise the exposure to bugs by reducing the amount of privileged code [Brinch Hansen 1970; Wulf et al. 1974; Accetta et al. 1986; Liedtke 1993; Shapiro et al. 1996; Hohmuth et al. 2004]. This is also the primary motivation behind separation kernels [Rushby 1981; Information Assurance Directorate 2007], the MILS approach [Alves-Foss et al. 2006], isolation kernels [Whitaker et al. 2002], the use of small hypervisors as aminimal trust base [Garfinkel et al. 2003; Singaravelu et al. 2006; Seshadri et al. 2007; Criswell et al. 2007], as well as systems that require the use of type-safe languages for all code except some “dirty” core [Bershad et al. 1995; Fahndrich et al. 2006].

我们验证的目标，即内核，是系统中最关键的部分，这是我们使用该组件启动系统验证的动机。内核的习惯定义是在硬件的特权模式下执行的软件，这意味着无法防止内核中发生的错误，而且每个错误都可能造成任意的损害。微内核的动机是希望通过减少特权代码的数量来尽量减少bug的暴露[Brinch Hansen 1970;伍尔夫等，1974;1986年;利特克1993;夏皮罗等，1996;Hohmuth等人，2004]。这也是分离内核[Rushby 1981;信息保障理事会2007]，MILS方法[Alves-Foss等人2006]，隔离内核[Whitaker等人2002]，使用小型虚拟机监控程序作为最小信任基[Garfinkel等人2003;Singaravelu等，2006;Seshadri等，2007;Criswell等人2007]，以及要求对除某些“脏”核心之外的所有代码使用类型安全语言的系统[Bershad等人1995;Fahndrich等，2006]。

With truly small kernels it becomes possible to take security and robustness further, to the point where it is possible to guarantee the absence of bugs [Tuch et al. 2005; Hohmuth and Tews 2005; Seshadri et al. 2007; Elphinstone et al. 2007] and to establish the presence of high-level security properties [Klein et al. 2011; Sewell et al. 2011; Murray et al. 2013; Sewell et al. 2013]. This can be achieved by formal,machine-checked verification, providing mathematical proof that firstly the kernel implementation is consistent with its specification and free from programmer-induced implementation defects, and secondly that the specification satisfies desirable high-level properties that carry through to the code and binary level.

有了真正的小内核，就有可能进一步提高安全性和健壮性，达到可以保证没有bug的程度[Tuch et al. 2005;Hohmuth和Tews 2005;Seshadri等，2007;Elphinstone等人[2007]和建立高级别安全属性的存在[Klein等人，2011;Sewell等人2011;Murray et al. 2013;Sewell等人，2013年]。machine-checked验证,这可以通过正式提供数学证明,首先内核实现符合其规范和自由从programmer-induced实现缺陷,其次,规范满足理想的高级属性,完成代码和二进制级别。

The seL4 microkernel is a member of the L4 microkernel family [Liedtke 1996], designed for providing provably strong security mechanisms while retaining the high performance that is customary in the L4 family and considered essential for real-world use. The radical size reduction in microkernels comes with a price in complexity. It results in a high degree of interdependency between different parts of the kernel, as indicated in Figure 1. Despite this increased complexity, our work shows that with modern techniques and careful design, an operating system (OS)microkernel is entirely within the realm of full formal verification—not just for functional correctness, but also for a full range of further formal analysis.

seL4微内核是L4微内核家族[Liedtke 1996]的一员，其设计目的是提供可证明的强大安全机制，同时保留L4家族中常见的高性能，并被认为是在实际使用中必不可少的。微内核的大规模缩减是以复杂性为代价的。它导致内核的不同部分之间高度的相互依赖，如图1所示。尽管复杂性增加了，但我们的工作表明，通过现代技术和精心设计，操作系统(OS)微内核完全可以进行完全的形式验证——不仅可以进行功能正确性验证，还可以进行更全面的形式分析。

Most of the key results summarised here have appeared previously in separate publications [Klein et al. 2009b; Sewell et al. 2011; Blackham et al. 2011; Murray et al. 2013; Sewell et al. 2013; Boyton et al. 2013]. The main contribution of this article is to give an overall picture and an in-depth coverage of the entire, comprehensive verification of seL4. We tie the individual results together, and extensively analyse our experience in sustained long-term verification.

这里总结的大多数关键结果以前都曾在单独的出版物中出现过[Klein et al. 2009b;Sewell等人2011;Blackham et al. 2011;Murray et al. 2013;Sewell等，2013年;Boyton等人，2013年]。本文的主要贡献是对seL4的整体情况和全面验证的深入报道。我们将个别结果结合在一起，并广泛分析我们在持续长期核查方面的经验。

The central piece of this work is still the initial functional correctness verification of seL4 [Klein et al. 2009b] in the theorem prover Isabelle/HOL [Nipkow et al. 2002]. This property is stronger and more precise than what automated techniques such as model checking, static analysis or kernel implementations in type-safe languages can achieve. It not only analyses specific aspects of the kernel, such as safe execution, but also provides a full specification and proof for the kernel’s precise behaviour down to its C implementation on the ARM platform.

这项工作的核心仍然是seL4 [Klein et al. 2009b]在定理证明者Isabelle/HOL [Nipkow et al. 2002]中的初始函数正确性验证。此属性比模型检查、静态分析或类型安全语言中的内核实现等自动化技术更强、更精确。它不仅分析了内核的特定方面，如安全执行，而且还提供了完整的规范和证据，证明了内核在ARM平台上的C实现的精确行为。

The following additional results, summarised here, extend this proof in the strongest sense. They directly compose with the formal functional correctness statement and yield end-to-end theorems about the C source code semantics in the theorem prover Isabelle/HOL:
- a functional correctness proof for a high-performance, hand-optimised inter-process communication (IPC) fastpath;
- a proof of correct access-control enforcement, in particular integrity and authority confinement [Sewell et al. 2011];
- a proof of information-flow noninterference, which shows that seL4 can be configured as a separation kernel to provide strong isolation [Murray et al. 2013];
- a proof of user-level system initialisation that connects to the access-control model of the two security proofs above [Boyton et al. 2013].

下面总结的附加结果，在最强烈的意义上扩展了这个证明。它们直接与形式的函数正确性声明组成，并在定理证明者Isabelle/HOL中产生关于C源代码语义的端到端定理:
- 高性能、手工优化的进程间通信(IPC)快速通道的功能正确性证明;
- 正确的访问控制实施证明，特别是完整性和权限限制[Sewell等，2011];
- 信息流不干扰的证明，这表明seL4可以配置为一个分离内核，提供强隔离[Murray et al. 2013];
- 连接到上述两个安全证明的访问控制模型的用户级系统初始化的证明[Boyton et al. 2013]。

The last three of these proofs reduce the gap between requirements as understood by humans, and the formal functional specification: they more directly and obviously describe properties we want this kernel to have. As we will discuss in the experience section, these additional proofs connect to appropriately abstract functional specification layers of seL4, and can thus be conducted with significantly less effort than the initial functional correctness proof.

最后三种证明减少了人类所理解的需求与正式的功能规范之间的差距:它们更直接、更明显地描述了我们希望这个内核具有的特性。正如我们将在经验部分中讨论的，这些额外的证明连接到seL4的适当抽象功能规范层，因此可以比初始的功能正确性证明简单得多。

Two further results on seL4 strengthen the assurance argument we can give on the binary level of the kernel:
- an automatic proof of refinement between the semantics of the kernel binary after compilation/linking and the C source code semantics used in the functional correctness proof [Sewell et al. 2013];
- an automatic static analysis of the seL4 binary to provide a sound worst-case execution time (WCET) profile of all system calls [Blackham et al. 2011].

seL4的另外两个结果加强了我们可以在内核的二进制级别上给出的保证参数:
- 编译/链接后的内核二进制代码的语义和功能正确性证明中使用的C源代码语义之间的细化的自动证明[Sewell et al. 2013];
- seL4二进制文件的自动静态分析，提供所有系统调用的最坏情况执行时间(WCET)概要[Blackham et al. 2011]。

To achieve a higher degree of automation, both of these analyses leave the logical framework of Isabelle/HOL. While the WCET analysis is a separate tool with its own highly detailed hardware model, the binary correctness verification stays logically compatible, using the Cambridge ARM semantics [Fox 2003; Fox and Myreen 2010] in the HOL4 prover [Slind and Norrish 2008], connecting directly to the Isabelle/HOL C semantics of the functional correctness proof. Since it hands off proof obligations to automatic SMT solvers such as Z3 [de Moura and Bjørner 2008], the resulting theorem is strictly speaking not an end-to-end theorem in Isabelle/HOL. Even so, the result is still a refinement statement that composes logically with the other functional correctness results.

为了达到更高的自动化程度，这两种分析都离开了Isabelle/HOL的逻辑框架。虽然WCET分析是一个单独的工具，它有自己非常详细的硬件模型，但是二进制正确性验证在逻辑上保持兼容，使用的是Cambridge ARM语义[Fox 2003;Fox和Myreen 2010]在HOL4 prover [Slind和Norrish 2008]中，直接连接到Isabelle/HOL C语义的函数正确性证明。因为它的手证明义务自动解决SMT等Z3·德·莫拉和Bjørner 2008,结果在伊莎贝尔定理严格来说不是一个端到端的定理/假日。即便如此，结果仍然是与其他函数正确性结果进行逻辑组合的细化语句。

With this, the functional correctness and security proofs do not need to assume the correctness of compiler and linker any more. We still assume correctness of handwritten assembly code, boot code, management of caches, and the hardware; we prove everything else. Information-flow noninterference comes with its own set of additional hardware assumptions that we detail in Section 5.4.

这样，函数正确性和安全性证明就不再需要假定编译器和链接器的正确性。我们仍然假设手写汇编代码、引导代码、缓存管理和硬件的正确性;我们证明了所有的东西。信息流不干涉有它自己的一套附加硬件假设，我们将在第5.4节详细介绍。

This verification makes seL4 the only kernel analysed to the degree of assurance and detail presented here, and still, to our knowledge, the only general-purpose OS kernel that is fully formally verified for functional correctness with machine-checked end-to-end theorems. It is also the only evolving formally-verified code base of the order of 10 000 lines of code and we report on maintaining it for almost a decade together with its now 480 000 lines of Isabelle proofs and specifications.

这种验证使seL4成为惟一一个在本文给出的保证和详细程度上进行分析的内核，而且据我们所知，它仍然是惟一一个通过机器端到端检验定理正式验证功能正确性的通用操作系统内核。它也是唯一进化的1万行代码的正式验证代码库，我们报告了近10年来的维护，以及现在的48万行Isabelle证明和规范。

The remainder of this article is structured as follows. In Section 2, we give an overview of seL4, of the design approach, and of the verification approach. In Section 3, we describe how to design a kernel for formal verification. Section 4 summarises the extended functional correctness verification and identifies the assumptions we make there. Section 5 does the same for the security theorems, and Section 6 discusses how to build trustworthy systems on top of seL4, including worst-case execution time and correct user-level initialisation of systems. In Section 7, we describe lessons we learnt in this work and in maintaining a code base with increasing large-scale formal proofs over an extended period of time.

本文其余部分的结构如下。在第2节中，我们将概述seL4、设计方法和验证方法。在第3节中，我们将描述如何设计用于正式验证的内核。第4节总结了扩展的函数正确性验证，并确定了我们在这里所做的假设。第5节对安全定理做了同样的事情，第6节讨论如何在seL4之上构建值得信任的系统，包括最坏情况下的执行时间和正确的用户级系统初始化。在第7节中，我们描述了我们在这项工作中以及在维护代码库时学到的教训，这些代码库在很长一段时间内增加了大规模的正式证明。
