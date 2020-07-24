# Design and Provability of a Statically Configurable Hypervisor

## Abstract

In this thesis we develop a novel, minimalist design for Type I hypervisors and present a fully working prototype for the ARMv7 and ARMv8 architectures. We introduce its key design paradigm, the Principle of Staticity, elaborate on its consequences for the resulting implementation, and discuss scenarios where not all dynamicity can be eliminated.

在本文中，我们开发了一种新颖的I型虚拟机监控程序的极简设计，并给出了一个针对ARMv7和ARMv8架构的完整工作原型。我们介绍了它的关键设计范式，Staticity的原则，详细说明了它对最终实现的后果，并讨论了不能消除所有动态的场景。

We also describe, implement, and evaluate the configuration framework necessary to build this specific kind of hypervisor. Even though the compiled hypervisor images are fully static, we show that the framework is powerful enough to support various system-on-chip target platforms, different address translation regimes, and even completely unrelated architectures. We further demonstrate its versatility by extending the framework to even support embedded processors which only contain a memory protection unit.

我们还描述、实现和评估构建这种特定管理程序所需的配置框架。尽管编译后的虚拟机监控程序映像是完全静态的，但我们展示了该框架足够强大，可以支持各种片上系统目标平台、不同的地址转换机制，甚至完全不相关的架构。我们进一步证明了它的通用性，扩展框架，甚至支持嵌入式处理器，只包含一个内存保护单元。

After performing a selection of benchmarks to substantiate the competitiveness of our implementation, we continue by subjecting our hypervisor to an analysis using a specialized form of symbolic model checking. We succeed in deriving integrity properties for the hypervisor as well as among virtual machines with surprisingly little effort, but show that complexity immediately ramps up once dynamic components like a shadow paging unit are added to our design.

在执行了一组基准测试以证明我们的实现的竞争力之后，我们继续使用一种特殊形式的符号模型检查对我们的虚拟机监控程序进行分析。我们用很少的努力就成功地为hypervisor以及虚拟机派生出了完整性属性，但是我们发现，一旦将动态组件(如影子分页单元)添加到我们的设计中，复杂性就会立即增加。

Finally we demonstrate the extensibility of our design despite our postulated Principle of Staticity. As an optimization example, we introduce the concept of ”lightweight VMs” and present a novel use case for a recent feature of the ARM architecture, thereby reducing the switch latency between virtual machines.

最后，我们证明了设计的可扩展性，尽管我们假设的原则是Staticity。作为一个优化示例，我们引入了“轻量级vm”的概念，并为ARM架构的最新特性提供了一个新的用例，从而减少了虚拟机之间的切换延迟。

We conclude with an overview over the published research efforts based on or heavily inspired by our hypervisor and discuss future research questions.

最后，我们概述了基于我们的虚拟机监控程序或受其启发而发表的研究成果，并讨论了未来的研究问题。

