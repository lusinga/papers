# Multi-core Devices for Safety-critical Systems: A Survey

Multi-core devices are envisioned to support the development of next-generation safety-critical systems, enabling the on-chip integration of functions of different criticality. This integration provides multiple systemlevel potential benefits such as cost, size, power, and weight reduction. However, safety certification becomes a challenge and several fundamental safety technical requirements must be addressed, such as temporal and spatial independence, reliability, and diagnostic coverage. This survey provides a categorization and overview at different device abstraction levels (nanoscale, component, and device) of selected key research contributions that support the compliance with these fundamental safety requirements.

多核设备的设想是支持下一代安全关键系统的发展，使不同临界状态的功能在芯片上集成。这种集成提供了多个系统级的潜在好处，如成本、尺寸、功率和重量减轻。然而，安全认证成为一项挑战，必须满足一些基本的安全技术要求，如时间和空间独立性、可靠性和诊断覆盖率。本调查提供了一个分类和概述，在不同的设备抽象级别(纳米级、组件和设备)，选择的关键研究贡献，以支持这些基本安全要求的依从性。

## 1 INTRODUCTION

Over the past decades, embedded systems have enabled tremendous improvements with respect to functionality, dependability, and performance in many application domains such as transportation and industrial control systems. In these domains, the embedded systems often play a crucial role in guaranteeing the overall safety of the system. These systems are referred to as safetycritical systems as their failure can derive into catastrophic consequences such as the loss of lives or severe environmental damage [18] (e.g., automotive cruise control [10], railway signalling [9], wind turbine integrity protection [159], pacemaker [146]). With the aim of reducing the risk of causing such fatalities, safety-critical systems must follow strict certification processes according to domain-specific safety standards. This process usually involves high development efforts and costs. As a general rule, the higher the safety integrity level, the higher the cost of safety certification [12, 158].

在过去的几十年里，嵌入式系统已经在许多应用领域，如运输和工业控制系统的功能性、可靠性和性能方面实现了巨大的改进。在这些领域中，嵌入式系统在保证系统的整体安全方面往往起着至关重要的作用。这些系统被称为安全关键系统，因为它们的故障可能导致灾难性的后果，如生命损失或严重的环境破坏[18](例如，汽车巡航控制[10]，铁路信号[9]，风力涡轮机完整性保护[159]，起搏器[146])。为了降低造成此类死亡的风险，关键安全系统必须按照特定领域的安全标准遵循严格的认证流程。这个过程通常涉及到很高的开发努力和成本。一般来说，安全完整性水平越高，安全认证成本越高[12,158]。

In addition, with the increasing digitization trend, where an increasing number of functionalities are implemented by software, the embedded systems often include functions with different safety criticalities that must also co-exist with non-critical software, conforming mixed-criticality systems. In the past, the mixed-criticality architecture has generally followed a federated architecture approach in which each major functionality is deployed on a dedicated computing node. The growing demand for additional functionality, leads to an increase in the number of computing nodes, wires and connectors. As a consequence, this leads to an increase in the overall cost, complexity, Size, Weight, and Power (SWaP) that in several cases limits the future scalability of this approach [12, 17, 39, 145, 158, 159]. For example, in the automotive domain, premium cars have more than 20 million lines of code deployed in around 100 computing nodes [35, 39] and electronic components added value ranges between 40% for traditional vehicles up to 75% for electric vehicles [126]. The current automotive evolution targets the development of intelligent Advanced Driver-Assistance Systems (ADAS) and autonomous driving solutions that will further increase the number of functionalities to be integrated [39].

此外，随着数字化趋势的增加，越来越多的功能是由软件实现的，嵌入式系统通常包括具有不同安全临界的功能，这些功能也必须与非临界软件共存，符合混合临界系统。在过去，混合临界性体系结构通常遵循一种联邦体系结构方法，其中每个主要功能都部署在一个专用的计算节点上。对附加功能的需求不断增长，导致计算节点、电线和连接器的数量增加。因此，这会导致整体成本、复杂性、大小、重量和功率(SWaP)的增加，在某些情况下限制了这种方法的未来可伸缩性[12,17,39,145,158,159]。例如，在汽车领域，高档汽车在大约100个计算节点上部署了超过2000万行代码[35,39]，电子元件的附加值在传统汽车的40%到电动汽车的75%之间[126]。目前的汽车发展的目标是开发智能高级驾驶辅助系统(ADAS)和自动驾驶解决方案，这将进一步增加功能的数量，以集成[39]。

One possible solution is the shift toward an integrated architectural approach [12, 17, 39, 145, 158, 159], where functions of different safety criticality are integrated in a reduced number of centralized computing nodes and processing devices. In this approach, safety certification becomes a challenge, because the integration of mixed-criticality functions requires justifying sufficient independence of implementation and sufficiently low probability of dependent failure between functions [12, 89, 93, 128, 158]. Moreover, this approach requires an increase in the overall computational performance of devices, which could potentially be achieved by means of multi-core devices and mono-core devices with higher frequency. The usage of mono-core devices with higher frequency is considered not competitive in several domains due to increased sensibility to Electromagnetic Interference (EMI) [78], low reliability of thermal dissipation fans [39, 159] and cooling systems volume and weight [170]. However, Commercial Off-The-Shelf (COTS) multi-core devices are becoming dominant in silicon manufacturer roadmaps [17, 28, 41, 80, 91, 126, 198] and provide a cross-domain potential solution, e.g., automotive [10, 96, 128], avionics [17, 99], railway [9, 25], industrial control [3, 158], medical applications [146].

一种可能的解决方案是转向一体化的架构方法[12,17,39,145,158,159]，其中不同安全临界度的功能集成在减少数量的集中计算节点和处理设备中。在这种方法中，安全认证成为一项挑战，因为混合临界功能的集成需要证明实现的充分独立性和功能之间的依赖失效的充分低概率[12,89,93,128,158]。此外，这种方法需要提高设备的整体计算性能，这可以通过使用更高频率的多核设备和单核设备来实现。由于对电磁干扰(EMI)的敏感性增强[78]、散热风扇的低可靠性[39,159]和冷却系统体积和重量[170]，使用频率更高的单核器件被认为在一些领域没有竞争力。然而，商用成品(COTS)多核设备在硅制造商路线图[17,28,41,80,91,126,198]中占据主导地位，并提供跨领域的潜在解决方案，如汽车[10,96,128]、航空电子[17,99]、铁路[9,25]、工业控制[3,158]、医疗应用[146]。

In this scenario, safety-critical system developers of multi-core device-based mixed-criticality systems, need to comply with two sometimes conflicting and contradictory constraints. On the one hand, conservative functional safety standards based on the best safety industrial practices of the last decades, with none or limited consideration of multi-core devices (see advances in ISO 26262- 11 [93]). On the other hand, a rapidly evolving and highly innovative semiconductor industry that produces multi-core devices with shrinking technologies and higher integration of cores [94].

在这种情况下，基于多核设备的混合临界系统的安全临界系统开发人员，需要遵守两个有时相互冲突的约束条件。一方面，保守的功能性安全标准基于过去几十年最好的安全工业实践，没有或有限地考虑多核设备(参见ISO 26262- 11[93]的进步)。另一方面，快速发展和高度创新的半导体行业，生产的多核器件采用缩小技术和更高集成度的核心[94]。

This publication is a survey on multi-core devices for safety-critical systems. The bulk of the research in multi-core devices for dependable systems focuses on specific challenges such as device and component architectures, reliability, time predictability, and safety certification strategies [12, 146]. This survey however, aims to provide a categorization and end-to-end view of safety implications on multi-core devices, starting from nanoscale level and up to device level, by bringing together and summarizing the most relevant research contributions that support the compliance with fundamental safety technical requirements. This survey is aimed at researchers, with the aim of offering a panoramic view (survey of surveys) of the set of fundamental technical challenges that need to be addressed at different abstraction levels. This survey is also aimed at multi-core device and safety system developers. It provides an updated categorized research state-of-the-art, which provides a starting point to go through the (complex) design decision process required in the development of safety-critical systems, as required by diligence and liability requirements when the applicable safety standard has none or limited consideration of multi-core devices [56].

本出版物是关于安全关键系统的多核设备的调查。可靠系统的多核设备的大部分研究集中在特定的挑战上，如设备和组件体系结构、可靠性、时间可预测性和安全认证策略[12,146]。然而，这项调查的目的是提供一个分类和端到端对多核设备的安全影响的观点，从纳米级到设备级，通过汇集和总结支持基本安全技术要求的最相关的研究贡献。这个调查是针对研究人员的，目的是提供一个全景(调查的调查)的一组基本技术挑战，需要解决在不同的抽象级别。本调查还针对多核设备和安全系统开发商。它提供了一个更新分类研究最先进的,它提供了一个起点通过所需的(复杂)的设计决策过程发展的安全至上的系统中,根据需要通过勤奋和责任要求时适用的安全标准没有或有限的考虑多核设备[56]。

The reminder of the survey is organized as follows. Section 2 summarizes basic concepts and terminology. Section 3 analyzes, categorizes and summarizes the state-of-the-art with selected key research contributions that support the compliance with fundamental safety technical requirements at different device abstraction levels (nanoscale, component, and device). Then, Section 4 summarizes avionics and space domain-specific safety technical considerations, and summarizes relevant example case-studies from several domains. Finally, Section 5 provides links to related research topics, overall conclusion, and future research directions.

调查提示如下。第2节总结了基本概念和术语。第三部分分析、分类和总结了在不同设备抽象级别(纳米级、组件和设备)上支持基本安全技术要求的最先进的关键研究贡献。第四部分总结了航空电子和空间领域的安全技术考虑，并总结了几个领域的相关案例研究。最后，第5节提供了相关研究主题、总体结论和未来研究方向的链接。

## 2 BASIC CONCEPTS AND TERMINOLOGY

This survey uses the basic concepts, taxonomy and terminology defined by Avižienis et al. [18] for dependable and secure computing. In addition to this, this survey integrates various research fields with specific detailed concepts and terminology described by referencedmajor survey publications of each specific research field (e.g., nanoscale multi-core device dependability [146]).

这个调查中使用的基本概念、分类和术语定义为Avižienis et al。[18]可靠和安全的计算。此外，本调查将各个研究领域与每个特定研究领域的主要调查出版物(如纳米级多核器件可靠性[146])所描述的具体详细概念和术语相结合。

The term multi-core device, or device for short, is used within this survey to refer to multi-core processors, System-on-a-Chip (SoC), Multi-Processor System-on-a-Chip (MPSoCs), FPGA with soft-cores and combinations of the previous. And the term computing node is used within this survey to refer to domain-specific terms such as Electronic Control Unit (ECU) for automotive and Line Replaceable Unit (LRU) for avionics.

术语多核设备，或简称设备，在本调查中用于指多核处理器、系统片上(SoC)、多处理器系统片上(MPSoCs)、软核的FPGA和前面的组合。在本调查中，术语计算节点指的是特定领域的术语，如用于汽车的电子控制单元(ECU)和用于航空电子设备的线路可替换单元(LRU)。

### 2.1 Safety Certification and Standards

Safety is defined as the “absence of catastrophic consequences on the user(s) and the environment” [18]. In several cases, safety-critical systems must be approved by an independent certification agency as part of a certification process. IEC 61508 [89] is a generic international safety standard considered a reference for other domain-specific standards such as automotive (ISO 26262 [93]) and railway (EN 5012X [55]). For further details see the survey [176] that describes safety (and security) qualification and certification state-of-the-art and challenges for automotive, railway, space, and avionics domains. Taking into consideration the variability of domain-specific terms and requirements, this survey uses IEC 61508 as the reference safety standard. Section 4 provides a simplified survey of solutions specific for other additional domains such as avionics and space.

安全定义为“对用户和环境没有灾难性的后果”。在某些情况下，作为认证过程的一部分，安全关键系统必须得到独立认证机构的批准。IEC 61508[89]是一个通用的国际安全标准，被认为是其他领域特定标准的参考，如汽车(ISO 26262[93])和铁路(EN 5012X[55])。欲了解更多细节，请参阅调查[176]，该调查描述了汽车、铁路、空间和航空电子领域的安全(和安全)资格认证和最先进的挑战。考虑到特定领域术语和要求的可变性，本次调查采用IEC 61508作为参考安全标准。第4节提供了针对其他附加领域(如航空电子和空间)的解决方案的简化概览。

In IEC 61508, “Safety Integrity Level (SIL) is a discrete level corresponding to a range of safety integrity values where 4 is the highest and 1 is the lowest” [159]. Themost stringent safety systems such as railway signaling systems (SIL4) are implemented with programmable electronics with a probability of dangerous failure in the range of 10−9 hours of operation (≈ 114.155 years). This requires stringent technical solutions to reduce to such low level the probability of catastrophe due to systematic faults (e.g., method to reduce the probability of human, process and tool errors) and random faults (e.g., safety mechanism with a given Diagnostic Coverage (DC)).

在IEC 61508中，“安全完整性级别(SIL)是一个离散级别，对应于一个安全完整性值范围，其中4是最高的，1是最低的”[159]。最严格的安全系统，如铁路信号系统(SIL4)采用可编程电子设备，在运行10 - 9小时内(≈114.155年)发生危险故障的概率。这需要严格的技术解决方案，以将由系统故障(例如，减少人为、过程和工具错误概率的方法)和随机故障(例如，具有给定诊断范围(DC)的安全机制)造成的灾难概率降低到如此低的水平。

A safety function is either fail-safe, if a safe state can be reached either by the safety function or by the diagnosis reaction (e.g., wind turbine stop [159], train stop [9]), or fail-operational if the system must guarantee full or degraded operation of the given function even in the presence of a failure (e.g., automotive autonomous driving system, avionics flight control system).

安全功能是自动防故障装置,如果可以达到一个安全的状态通过安全函数或诊断反应(例如,风力发电机停止[159],火车停止[9]),或操作失败如果系统必须保证给定函数的完整或退化的操作,即使在失败的存在(如汽车自主驾驶系统、航空电子飞行控制系统)。

### 2.2 Fundamental Safety Technical Requirements

The development of safety-critical systems that must comply with safety standards such as IEC 61508, requires an appropriate safety methodology to mitigate systematic errors and compliance with at least two fundamental safety technical requirements (reliability and diagnostic coverage (DC)). The development of safety-critical systems based on multi-core devices extends the previous with spatial and temporal independence requirements [89, 93] (see IEC 61508-3 Annex F). Thus, compliance with at least these four fundamental safety technical requirements is required.

安全关键系统的开发必须符合安全标准(如IEC 61508)，需要适当的安全方法来减少系统错误，并符合至少两个基本的安全技术要求(可靠性和诊断覆盖率(DC))。基于多核设备的安全关键系统的发展以空间和时间独立性要求扩展了以前的系统[89,93](见IEC 61508-3附件F)。因此，至少需要符合这四项基本安全技术要求。

- The reliability of a device at time t is the probability that the device will operate correctly during the period of time [0, t ]. Reliability is measured as the failure rate of the device (λ) and expressed in Failures In Time (FIT), the number of failures expected in 109 hours of operation. For a given error, the effect can either be safe (not dangerous) or dangerous with respect to the associated safety function. Thus, as described in Equation (1), device failure rate is made up of the addition of components safe failure rates (λs ) and dangerous failure rates (λd ). The reliability of a device can be improved by techniques such as fault-tolerance.

### 2.3 Reliability Degradation Threats

### 2.4 Semiconductor Industry Trend

## 3 MULTI-CORE DEVICE ARCHITECTURES

### 3.1 Summary

### 3.2 Nanoscale Level

### 3.3 Component Level

### 3.4 Device Level

## 4 DOMAIN-SPECIFIC APPROACHES

### 4.1 Avionics Domain

### 4.2 Space Domain

### 4.3 Example Case Studies

## 5 CONCLUSION AND FUTURE RESEARCH DIRECTIONS

This section describes links to other related research topics, overall conclusion and future research directions.

### 5.1 Links to Other Research Topics

### 5.2 Conclusions and Future Research Directions

