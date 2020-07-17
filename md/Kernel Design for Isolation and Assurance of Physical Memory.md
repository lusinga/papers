# Kernel Design for Isolation and Assurance of Physical Memory

## ABSTRACT

Embedded systems are evolving into increasingly complex software systems. One approach to managing this software complexity is to divide the system into smaller, tractable components and provide strong isolation guarantees between them. This paper focuses on one aspect of the system’s behaviour that is critical to any such guarantee: management of physical memory resources.

嵌入式系统正在演化为越来越复杂的软件系统。管理这种软件复杂性的一种方法是将系统划分为更小的、可处理的组件，并在这些组件之间提供强大的隔离保证。本文关注于系统行为的一个方面，它对任何此类保证都至关重要:物理内存资源的管理。

We present the design of a kernel that has formally demonstrated the ability to make strong isolation guarantees of physical memory. We also present the macro-level performance characteristics of a kernel implementing the proposed design.

我们介绍了一个内核的设计，它正式证明了能够对物理内存进行强大的隔离保证。我们还给出了实现该设计的内核的宏观性能特征。

## 1. INTRODUCTION

Embedded systems are evolving into increasingly complex software systems. Drivers of this trend include the desire to consolidate functionality previously spread across several platforms onto a single platform, and supporting ever-increasing feature sets. We have moved from single-vendor closed system, to systems incorporating software, sourced from third parties, that possesses varying degrees of assurance. High-end embedded systems now host leagacy operating systems, such as Linux, to further extended the feature set, together with user-installed, untrusted applications. While functional correctness is becoming increasingly difficult and expensive to achieve, it is becoming more important, as safety-critical or mission-critical systems are targetted for consolidation.

嵌入式系统正在演化为越来越复杂的软件系统。这一趋势的驱动因素包括将以前分散在多个平台上的功能整合到单个平台上，以及支持不断增加的特性集。我们已经从单一供应商的封闭系统转移到包含来自第三方的软件的系统，这些软件拥有不同程度的保证。高端嵌入式系统现在宿主leagacy操作系统(如Linux)，以进一步扩展特性集，以及用户安装的、不受信任的应用程序。虽然实现功能正确性变得越来越困难和昂贵，但它变得越来越重要，因为安全关键型或任务关键型系统是用于整合的。

One approach to improving the robustness of embedded systems is to divide the system into components and provide strong isolation guarantees between them — the failure of a component is isolated from the rest of the system. There are many approaches to providing isolation guarantees, such as the classical processes and virtual memory [8], separation kernels [24], isolation kernels [30], virtual machines [29] and microkernels [16], all of which provide varying levels of isolation guarantees at different granularities. The level of isolation that can be achieved depends, to a large degree, on the mechanisms provided by the underlying kernel — be it a microkernel, a virtual machine monitor, or an isolation kernel.

提高嵌入式系统健壮性的一种方法是将系统划分为组件，并在组件之间提供强大的隔离保证——组件的故障与系统的其余部分隔离。有许多方法可以提供隔离保证，比如经典进程和虚拟内存[8]、隔离内核[24]、隔离内核[30]、虚拟机[29]和微内核[16]，所有这些方法都在不同粒度上提供不同级别的隔离保证。可以实现的隔离级别在很大程度上取决于底层内核提供的机制——无论是微内核、虚拟机监视器还是隔离内核。

This paper focuses on one aspect of providing strong isolation guarantees — the management of physical memory on the device. Specifically, the mechanisms used to directly and indirectly control access to physical memory, while providing services to software components on the system.

本文主要关注提供强隔离保证的一个方面——设备上物理内存的管理。具体来说，就是在为系统上的软件组件提供服务时，用于直接或间接控制对物理内存的访问的机制。

The problem is more complex than simply controlling the size of virtual memory, or resident set size of an application. Services such as pages or threads not only require allocation of memory to directly support the service (a frame or thread-control block), service provision also results in the allocation of kernel metadata to implement the service (such as page tables) or provide the bookkeeping required to reclaim the storage on release. Ideally, any kernel mechanism provided to manage physical memory and enforce an particular isolation policy must encompass both physical memory consumed directly by the application, and any physical memory consumed indirectly by the kernel metadata needed to provide services to applications.

这个问题比简单地控制虚拟内存的大小或应用程序的常驻集的大小要复杂得多。服务,如页面或线程不仅需要分配的内存直接支持服务(一个框架或线程控制块),服务条款也导致内核配置元数据来实现服务(如页表)或提供所需的簿记回收存储在释放。理想情况下，为管理物理内存和实施特定隔离策略而提供的任何内核机制都必须包含由应用程序直接使用的物理内存和由为应用程序提供服务所需的内核元数据间接使用的任何物理内存。

We can summarise our approach to this problem as: (a) eliminating the need for all implicit allocation of metadata from kernel, and (b) the promotion of all kernel metadata (including the provision for book keeping) into first-class, explictly-allocated objects. This approach reduces the problem of enforcing a physical memory isolation policy over the components, to that of enforcing isolation over the authority to explicit kernel object creation and object-authority distribution between components, which is achieved by a capability based, decidable access control model.

我们可以将我们解决这个问题的方法总结为:(a)消除所有来自内核的隐式元数据分配的需要，(b)将所有内核元数据(包括簿记的供应)提升为一级的、明确分配的对象。这种方法减少了对组件实施物理内存隔离策略的问题，减少了对显式内核对象创建和组件间对象权限分配实施隔离的问题，这是通过基于功能的可判定访问控制模型实现的。

Kernel services and their implementation complexity have a direct consequence on the ability to successfully apply our approach. Thus our overall system design is that of a microkernel-based system, where the microkernel aims to provide a minimal, efficient, flexible kernel with strong guarantees on the sufficiency and the implementation correctness of the mechanisms required for achieving true isolation. Moreover, in order to realise the proposed model, the system must be equipped with a hardware MMU. Thus the target of our work is mid- to high-end devices of the embedded systems spectrum.

内核服务及其实现的复杂性直接影响到成功应用我们的方法的能力。因此，我们的总体系统设计是基于微内核的系统，其中微内核旨在提供最小的、高效的、灵活的内核，并对实现真正隔离所需的机制的充分性和实现正确性提供强有力的保证。此外，为了实现所提出的模型，系统必须配备一个硬件MMU。因此，我们的工作目标是嵌入式系统的中高端设备。

In the remainder of the paper, we discuss the requirements and issues surrounding the management of physical memory in the context of enforcing isolation boundaries, that consequently motivated our approach (Section 2). In Section 3, we introduce the memory management model and a summary of its performance is given in Section 4. Finally, Section 5 discusses related work, and conclusions and future work are in Section 6.

在本文的其余部分,我们将讨论需求和问题管理物理内存的上下文中执行隔离边界,动机,因此我们的方法(第二节)。在第三节中,我们介绍了内存管理模型和总结它在第四节的表现。最后，第5节讨论相关工作，第6节是结论和未来的工作。

## 2. REQUIREMENTS

Memory allocation to support kernel services and associated metadata can have a direct or indirect effect on the spatial and temporal isolation between components, and the efficiency and assurance of the overall system. In the following sections we examine these issues and requirements of memory allocation mechanisms in each of these areas.

为支持内核服务和相关元数据而分配的内存可能对组件之间的空间和时间隔离以及整个系统的效率和保证产生直接或间接的影响。在下面的部分中，我们将研究这些问题以及每个领域中的内存分配机制的需求。

### 2.1 Spatial Isolation

Physical memory is a limited and exhaustible resource. Any limited resource requires precise managment to avoid one task’s requests for authorised services from directly or indirectly denying services to another task.

物理内存是一种有限的、可耗尽的资源。任何有限的资源都需要精确的管理，以避免一个任务对授权服务的请求直接或间接拒绝另一个任务的服务。

Simple per-task quota-based schemes or static preallocation would suffice in a statically structured system. However, any dynamic variation in system structure or resource requirements leads to under-utilisation, due to the overly conservative commitment of resources required to ensure all authorised service requests are satisfied during peak demand. Significantly higher efficiency can be achieved if memory can be safely reassigned to where it can be utilised [29]. Memory allocation mechanisms must support dynamic allocation and safe re-assignment of memory in a controlled manner.

在静态结构的系统中，简单的基于任务配额的方案或静态预分配就足够了。然而，系统结构或资源需求的任何动态变化都会导致利用率不足，这是因为在需求高峰时，为了确保所有授权服务请求得到满足，对资源的承诺过于保守。如果内存可以被安全地重新分配到它可以被使用的地方[29]，则可以实现显著的更高效率。内存分配机制必须支持动态分配和安全的重新分配内存的控制方式。

### 2.2 Temporal Isolation

Temporal isolation is an important issue in the context of realtime embedded systems. By temporal isolation we mean the predictability of execution times of a task, irrespective of other system activities. The main issue that arises with memory allocation in this context is the predictability of execution of kernel operations. Memory allocation affects predictability when the kernel’s physical memory is treated as a cache to avoid kernel memory starvation. Several operating systems use the kernel’s physical memory as a cache of data structures stored at user-level or on disk [3, 25], and thus can always evict cache content to service new requests. While, such a strategy avoids physical-memory based denial-of-service attacks, it can not guarantee the temporal isolation — authorised request from one task might evict an entry from another and thereby cause unexpected system call delays for the latter task. The kernel’s physical memory management scheme must be capable of providing predictable access times to data structures associated with those tasks that require temporal predictability.

- [3] E. Alkassar, N. Schirmer, and A. Starostin. Formal pervasive verification of a paging mechanism. In C. R. Ramakrishnan and J. Rehof, editors, Tools and Alg. for the Construction and Analysis of Systems (TACAS), volume 4963 of LNCS, pages 109–123. Springer, 2008.
- [25] B. Ford, M. Hibler, J. Lepreau, R. McGrath, and P. Tullmann. Interface and execution models in the Fluke kernel. In 3rd OSDI. USENIX, Feb 1999.

时间隔离是实时嵌入式系统中的一个重要问题。通过时间隔离，我们指的是任务执行时间的可预测性，而与其他系统活动无关。在这种上下文中，内存分配产生的主要问题是内核操作执行的可预测性。当内核的物理内存被当作缓存来避免内核内存短缺时，内存分配会影响可预测性。一些操作系统使用内核的物理内存作为存储在用户级或磁盘上的数据结构的缓存[3,25]，因此总是可以删除缓存内容以服务于新的请求。尽管这样的策略避免了基于物理内存的拒绝服务攻击，但它不能保证时间隔离——来自一个任务的授权请求可能会从另一个任务逐出一个条目，从而导致后一个任务意外的系统调用延迟。内核的物理内存管理方案必须能够为与需要时间可预测性的任务相关的数据结构提供可预测的访问时间。

CPU cache colouring techniques for improving predictable cache behaviour are also dependent on control of the memory allocation of the data structures requiring colouring [17], including the kernel’s internal data structures.

- [17] J. Liedtke, H. Härtig, and M. Hohmuth. OS-controlled cache predictability for real-time systems. In Proceedings of the Third IEEE Real-Time Technology and Applications Symposium (RTAS ’97), pages 213–227, Montreal, Canada, Jun 1997. IEEE.

CPU缓存着色技术用于改善可预测的缓存行为也依赖于对需要着色[17]的数据结构的内存分配的控制，包括内核的内部数据结构。

The structure of bookkeeping in a kernel managing allocation also affects predictability of interrupt or event latencies. Traversal of lists or trees can result in varying or unreasonably long executing times for kernel operations traversing the list. Ideally, all system calls would complete in constant time, or at least be preemptable to minimise interrupt latency.

### 2.3 No Single Policy

Modern embedded devices are multipurpose appliances; they are composed of applications with diverse resource requirements. At one extreme, the system must support applications with stringent temporal requirements. For kernel memory allocation, this implies a guaranteed allocation of physical memory (including metadata) to those applications, that cannot be interfered with by any activities of the rest of the system. In the other extreme, system is expected to support best-effort applications where physical memory management is dynamic, on-demand and uncritical. These application specific requirements must be considered in making memory management decisions, so that we can maximise the use of the limited memory resource.

现代嵌入式设备是多用途设备;它们由具有不同资源需求的应用程序组成。在一个极端，系统必须支持具有严格时间需求的应用程序。对于内核内存分配，这意味着向这些应用程序保证物理内存(包括元数据)的分配，而系统其他部分的任何活动都不能干扰这些应用程序。在另一个极端，系统应该支持那些物理内存管理是动态的、按需的和不关键的应用程序。在进行内存管理决策时，必须考虑这些特定于应用程序的需求，以便最大限度地利用有限的内存资源。

A realistic example of the latter extreme is in efficiently supporting a para-virtualised legacy operating system [1] on the microkernel. Ideally, we want to isolate the critical components of the system from the legacy OS (and its applications), while facilitating the legacy OS to make its own memory management decisions — the legacy OS is in the best position to determine the allocation of page tables, pages, frames, and thread control blocks for its clients. This scenario is just a specialised case of the more general argument that application self-management of resources can lead to more efficient use of those resources [6, 10].

- [1] P. Barham, B. Dragovic, K. Fraser, S. Hand, T. Harris, A. Ho, R. Neugebauer, I. Pratt, and A. Warfield. Xen and the art of virtualization. In 19th SOSP, pages 164–177, Bolton Landing, NY, USA, Oct 2003.
- [6] D. R. Engler, S. K. Gupta, and M. F. Kaashoek. AVM: Application-level virtual memory. In 5th HotOS, pages 72–77, May 1995.
- [10] S. M. Hand. Self-paging in the Nemesis operating system. In 3rd OSDI, pages 73–86, New Orleans, LA, USA, Feb 1999. USENIX.

后一种极端的一个现实的例子是在微内核上有效地支持准虚拟化的遗留操作系统[1]。理想情况下,我们想要隔离系统的关键部件从遗留系统(及其应用程序),而促进遗留系统使其自己的内存管理决策——遗留系统是在最好的位置来确定分配的页表,页面框架,为其客户和线程控制块。这个场景只是更普遍的观点的一个特殊案例，即应用程序对资源的自我管理可以导致更有效地使用这些资源[6,10]。

### 2.4 Assurance

The ability to reason about the behaviour of components is central to any system attempting to make strong isolation guarantees; one must be capable of deciding whether or not isolation truly holds for a given system configuration and in particular assure that it cannot be violated in any future state. We believe that such assurance can only be provided within a formal framework—a formal model that captures the operational semantics of the kernel together with a mathematical proof of its ability to enforce isolation. In our context, this means (a) the model employed to control the allocation of physical memory must be amenable to formal modelling, and (b) the formal model must be decidable.

判断组件行为的能力对于任何试图做出强隔离保证的系统来说都是至关重要的;必须能够判断隔离是否真正适用于给定的系统配置，特别是确保在任何未来状态下都不会违反隔离。我们相信这样的保证只能在一个正式的框架中提供——一个正式的模型，它捕获内核的操作语义，以及对其强制隔离能力的数学证明。在我们的语境中，这意味着(a)用于控制物理内存分配的模型必须服从于形式模型，以及(b)形式模型必须是可决定的。

Moreover, to make such an analysis truly trustworthy, we need to verify the kernel implementation against the formal model used for the analysis. Verification here means a formal refinement proof from the model down to kernel code. Our desire (and efforts [5]) to formally verify the embedded kernel introduces another requirement to our design. A verified kernel, implies a relatively static kernel, in the sense of its design. This is motivated by the desire to avoid invalidating any successful verification proofs of the kernel. Ideally, the kernel code, including the memory management model should be fixed. Changing the lowest-level of behaviour; such as that of the heap, requires a significant redo of the proofs. However, given the diversity of embedded applications we expect no single memory allocation policy to suffice. This leads to the conclusion that a verified embedded kernel must minimise the allocation policy in the kernel, and maximise the control higher-level software over manging physical memory within the kernel.

此外，要使这样的分析真正可信，我们需要验证内核实现与用于分析的形式化模型。这里的验证意味着从模型到内核代码的正式的细化证明。正式验证嵌入式内核的愿望(以及努力[5])为我们的设计引入了另一个需求。验证过的内核，就其设计而言，意味着一个相对静态的内核。这样做的动机是希望避免使任何成功的内核验证证明无效。理想情况下，应该修复内核代码，包括内存管理模型。改变最低层次的行为;例如堆的验证，需要对验证进行大量重做。然而，考虑到嵌入式应用程序的多样性，我们不希望单一的内存分配策略能够满足需求。由此得出的结论是，经过验证的嵌入式内核必须将内核中的分配策略最小化，并将更高级别软件对内核中物理内存的控制最大化。

## 3. SEL4MEMORY ALLOCATION MODEL

The seL4 project proposes a novel kernel memory management model to meet the requirements identified in Section 2. The design of our model is inspired by early hardware-based capability machines (such as CAP [22]), where capabilities control access to physical memory; the KeyKOS and EROS systems [11, 25], with their controls on dissemination of capabilities; and the L4 microkernel [16], where the semantics of virtual memory objects are implemented outside of the kernel.

seL4项目提出了一种新的内核内存管理模型，以满足第2节中确定的需求。我们模型的设计受到早期基于硬件的功能机器(如CAP[22])的启发，功能控制对物理内存的访问;KeyKOS和EROS系统[11,25]，它们控制着能力的传播;以及L4微内核[16]，其中虚拟内存对象的语义在内核之外实现。

We start with an overview of the model together with a rationale of our design choices (see Section 3.1), which is followed by a discussion of issues and solutions related to realising the model (see Section 3.2).

我们首先概述模型以及设计选择的基本原理(见3.1节)，然后讨论与实现模型相关的问题和解决方案(见3.2节)。

### 3.1 Overview

In our model, all metadata are promoted into first-class kernel objects, that are allocated upon explicit user requests. We explain how users request allocation of these objects later. Each kernel object implements a particular abstraction, and supports one or more operations related to that abstraction. For instance, abstraction of threads is provided by the TCB object, and thread-related system calls are the operations supported by the TCB object.

在我们的模型中，所有元数据都被提升为一级内核对象，根据显式的用户请求进行分配。稍后我们将解释用户如何请求分配这些对象。每个内核对象实现一个特定的抽象，并支持与该抽象相关的一个或多个操作。例如，线程的抽象是由TCB对象提供的，与线程相关的系统调用是TCB对象支持的操作。

Authority over objects are conferred via tamper-proof (partitioned) capabilities [4]. Authorised users obtain kernel services by invoking capabilities.

通过防篡改(分区)功能[4]授予对对象的权限。授权用户通过调用功能获得内核服务。

Other than these explicitly-allocated kernel objects, the kernel allocates no metadata — there are no implicit allocations within the kernel. We achieved this by carefully avoiding, where possible, mechanisms that require in-kernel bookkeeping; and by including the essential bookkeeping data in the explicitly allocated kernel objects themselves.

除了这些明确分配的内核对象之外，内核不分配元数据——内核中不存在隐式分配。我们尽可能小心地避免了需要内核记账的机制，从而实现了这一点;通过在显式分配的内核对象本身中包含基本的簿记数据。

There are two aspects that are worth mentioning here. Firstly, explicit allocation provides the required flexibility—it is the users that instruct the kernel to allocate a particular object type. Secondly, the approach reduces the problem of memory allocation to kernel-object allocation. Thus, for achieving isolation, we simply require precise control of kernel-object allocation.

这里有两个方面值得一提。首先，显式分配提供了所需的灵活性——是用户指示内核分配特定的对象类型。其次，该方法减少了内核对象分配内存的问题。因此，为了实现隔离，我们只需精确地控制内核对象的分配。

Capability-based systems and their formal properties are well studied in literature [2, 20, 26, 27]. When coupled with a suitable authority transfer scheme; such as the take-grant [18], these systems yield a decidable access control model. Decidable here means that future access rights a task may obtain can be decided by analysing the initial system state. Moreover, with the take-grant model, enforcing some invariants on the initial distribution of capabilities guarantees isolation of authority.

文献[2,20,26,27]对基于能力的系统及其形式性质进行了深入研究。配合适当的权力转移计划;例如获取-授予[18]，这些系统产生一个可决定的访问控制模型。此处的Decidable意思是任务可能获得的未来访问权限可以通过分析初始系统状态来决定。此外，通过获取-授予模型，在功能的初始分布上强制执行一些不变量，可以确保权限的隔离。

While the primary concern of take-grant (and the like) is controlling access to user-level resources, its properties match to our requirements for control of kernel-object allocation. seL4 leverages this similarity by extending its take-grant-like capability protection model to control the allocation of kernel objects.

虽然获取-授予(和类似的东西)的主要关注点是控制对用户级资源的访问，但它的属性符合我们控制内核对象分配的需求。seL4通过扩展它的take-grant功能保护模型来控制内核对象的分配，从而利用了这种相似性。

While the primary concern of take-grant (and the like) is controlling access to user-level resources, its properties match to our requirements for control of kernel-object allocation. seL4 leverages this similarity by extending its take-grant-like capability protection model to control the allocation of kernel objects.

虽然获取-授予(和类似的东西)的主要关注点是控制对用户级资源的访问，但它的属性符合我们控制内核对象分配的需求。seL4通过扩展它的take-grant功能保护模型来控制内核对象的分配，从而利用了这种相似性。

In seL4, allocation of kernel objects is performed by the retype method, implemented by a special object type called untyped memory (UM) — an abstraction of a power-of-two sized, size-aligned region of physical memory. Possession of an UM capability provides sufficient authority to allocate kernel objects in the corresponding region — by invoking a UM capability, a user-level application can request that the kernel refines that region into other kernel objects (including smaller UM objects).

在seL4中，内核对象的分配是由retype方法执行的，它是由一种称为无类型内存(UM)的特殊对象类型实现的——对物理内存中大小为2、大小一致的区域的抽象。拥有UM能力提供了在相应区域分配内核对象的足够权限——通过调用UM能力，用户级应用程序可以请求内核将该区域细化为其他内核对象(包括较小的UM对象)。

As such, the set of UM capabilities possessed by an application defines the precise amount of physical memory that can be directly or indirectly consumed by the application. Moreover, the explicit allocation scheme provides user-level applications the freedom to make policy decisions on how to use the UM capabilities in its possession. We introduce the mechanisms used to restrict the number of UM capabilities an application possesses later in Section 3.2.3.

因此，应用程序所拥有的UM功能集定义了应用程序可以直接或间接使用的物理内存的精确数量。此外，显式分配方案为用户级应用程序提供了关于如何使用UM功能的决策自由。我们将在3.2.3节稍后介绍用于限制应用程序拥有的UM功能数量的机制。

### 3.2 The seL4 Kernel

In this section, we provide a detailed description of our memory management approach as applied to the seL4 kernel. 3.2.1 Memory Allocation At boot time, seL4 preallocates all the memory required for the kernel itself, including the code, data, and stack sections (seL4 is a single kernel-stack operating system). The remainder of the memory is given to the first task in the form of capabilities to untyped memory (UM), and some additional capabilities to kernel objects that were required to bootstrap the task.

在本节中，我们将详细描述应用于seL4内核的内存管理方法。在引导时，seL4预先分配内核本身所需的所有内存，包括代码、数据和堆栈部分(seL4是一个单一的内核堆栈操作系统)。剩余的内存以无类型内存(UM)能力的形式提供给第一个任务，并向内核对象提供一些额外的能力，这些能力是引导该任务所需的。

A capability to UM can be used to create new kernel objects that are smaller power-of-two sized, size-aligned objects via the retype method, which returns a capability to the new object. These capabilities to the new objects are called children, and the original UM capability the parent. Retype enforces the following invariants:

1. the child objects must be wholely contained within the original parent UM, and
2. the child objects must be distinct and non-overlapping.

通过retype方法，UM的功能可用于创建新的内核对象，这些对象是更小的幂次大小、大小对齐的对象，该方法将功能返回给新对象。新对象的这些功能称为子对象，而原始UM功能称为父对象。Retype强制执行以下不变量:

1. 子对象必须完全包含在原始父对象UM和中
2. 子对象必须是不同的和不重叠的。

The child objects may be UM (which may themselves eventually become parents of their children), or they may be kernel objects of other types that implement kernel services. The details of the other types depend on the services provided by the kernel, and can be ignored for the purposes of our discussion.

子对象可能是UM(它们自己可能最终成为它们子对象的父对象)，或者它们可能是实现内核服务的其他类型的内核对象。其他类型的细节依赖于内核提供的服务，在我们的讨论中可以忽略。

The user-level application (manager) that creates an object via
retype receives full authority over the object. It can then delegate
all or part of the authority it possesses over the object to one or
more of its clients. This is done by granting each client a capability
to the kernel object, thereby allowing the client to obtain kernel
services by invoking the object.

All the physical memory required to implement the object is preallocated
within the object at the time of its creation, and does not
exceed the size of the UM it was refined from. This eliminates
the need for the kernel to dynamically allocate memory to satisfy
requests from user-level tasks.

For obvious security reasons, kernel data must be protected from
user access. The seL4 kernel prevents such access by using two
mechanisms. First, the above allocation policy guarantees that
typed objects never overlap. Second, the kernel ensures that each
physical frame mapped by the MMU at a user-accessible address
corresponds to a typed frame object; frame objects contain no kernel
data, so direct user access to kernel data is not possible.


## 6. CONCLUSIONS AND FUTUREWORK

In this paper, we have presented a kernel design that is capable of providing strong guarantees on the physical memory consumption of an application. We achieve this by extending the access control mechanism of the kernel to include all physical memory that an application can directly or indirectly consume — including kernel’s metadata. The kernel itself is mostly free of memory management policy—it does not require the kernel to make any decisions about how, where or when to allocate kernel memory. Instead, user-level applications create, manage, recycle and destroy kernel objects via the secure access control scheme. Thereby provide strong allocation guarantees and the flexibility required to manage the scarce memory resource of an embedded device.

在本文中，我们提出了一种内核设计，它能够对应用程序的物理内存消耗提供强有力的保证。我们通过扩展内核的访问控制机制来实现这一点，包括应用程序可以直接或间接使用的所有物理内存——包括内核的元数据。内核本身基本上不受内存管理策略的影响——它不需要内核决定如何、在何处或何时分配内核内存。相反，用户级应用程序通过安全访问控制方案创建、管理、回收和销毁内核对象。从而提供强大的分配保证和管理嵌入式设备的稀缺内存资源所需的灵活性。

We have evaluated the design in two aspects; its formal properties and its performance. To evaluate the formal properties, we developed a machine-checked, abstract specification of the kernel model in Isabelle/HOL and formally proved that the design is sufficient to achieve physical memory isolation. We have characterised the performance of the design by using it as a platform for hosting a para-virtualised Linux kernel, which shows significant performance gains for benchmarks that require allocation of physical memory. At present, we are working on improving the performance numbers and making a more concrete comparison with existing systems.

我们从两个方面对设计进行了评估;它的形式性质和性能。为了评估形式属性，我们在Isabelle/HOL中开发了内核模型的机器检查抽象规范，并正式证明了该设计足以实现物理内存隔离。我们通过使用它作为托管准虚拟化Linux内核的平台来描述该设计的性能，这对于需要分配物理内存的基准测试来说显示了显著的性能提升。目前，我们正在努力改进性能数字，并与现有系统进行更具体的比较。
