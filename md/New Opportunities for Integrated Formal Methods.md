# New Opportunities for Integrated Formal Methods

Formal methods have provided approaches for investigating software engineering fundamentals and also have high potential to improve current practices in dependability assurance. In this article, we summarise known strengths and weaknesses of formal methods. From the perspective of the assurance of robots and autonomous systems (RAS), we highlight new opportunities for integrated formal methods and identify threats to the adoption of such methods. Based on these opportunities and threats, we develop an agenda for fundamental and empirical research on integrated formal methods and for successful transfer of validated research to RAS assurance. Furthermore, we outline our expectations on useful outcomes of such an agenda.

正式方法为调查软件工程基础提供了方法，也有很高的潜力来改善当前的可靠性保证实践。在本文中，我们总结了已知的形式化方法的优点和缺点。从机器人和自主系统 (RAS) 的保证的角度来看，我们强调了集成的正式方法的新机会，并确定了采用此类方法的威胁。基于这些机会和威胁，我们制定了一个议程，对整合的正式方法进行基础和实证研究，并将经过验证的研究成功转移到 RAS 保证。此外，我们概述了我们对这一议程的有益成果的期望。

## 1 INTRODUCTION

A plethora of difficulties in software practice and momentous software faults have been continuously delivering reasons to believe that a significantly more rigorous discipline of software engineering is needed [Jones 2003]. Researchers such as Neumann [2018] have collected plenty of anecdotal evidence on software-related risks substantiating this belief.

- Cliff B. Jones. 2003. The early search for tractable ways of reasoning about programs. IEEE Annals of the History of Computing
25, 2 (4 2003), 26–49. DOI:https://doi.org/10.1109/mahc.2003.1203057

- Peter G. Neumann. 2018. Risks to the public. ACM SIGSOFT Software Engineering Notes 43, 2 (2018), 8–11. DOI:https://doi.
org/10.1145/3203094.3203102

软件实践中过多的困难和重大的软件故障一直在不断地提供理由相信需要一个更加严格的软件工程学科 [琼斯 2003]。研究人员如 Neumann [2018] 已经收集了大量关于软件相关风险的轶事证据，证实了这一信念。

In dependable systems engineering, researchers have turned this belief into one of their working hypotheses and contributed formalisms, techniques, and tools to increase the rigour in engineering workflows [Jones 2003]. Examples of activities where formalisms have been brought to bear include requirements engineering [e.g., Gunter et al. 2000], architecture design and verification, testdriven development, program synthesis, and testing, to name a few. Formal method (FMs) have been an active research area for decades, serving as powerful tools for theoretical researchers. As a paradigm to be adopted by practitioners they have also been investigated by applied researchers. Applications of FMs have shown their strengths but also theirweaknesses [Jones 2003;MacKenzie 2001].

- C. A. Gunter, E. L. Gunter, M. Jackson, and P. Zave. 2000. A reference model for requirements and specifications. IEEE
Software 17, 3 (2000), 37–43. DOI:https://doi.org/10.1109/52.896248
- Donald A. MacKenzie. 2001. Mechanizing Proof: Computing, Risk, and Trust. The MIT Press. DOI:https://doi.org/10.7551/mitpress/4529.001.0001

在可靠的系统工程中，研究人员已经将这种信念转化为他们的工作假设之一，并贡献了形式、技术和工具来增加工程工作流程的严谨性 [Jones 2003]。形式主义产生的活动的例子包括需求工程 [e.g.,gunter 等人。2000]，架构设计和验证，测试驱动开发，程序合成和测试，仅举几例。形式方法 (FMs) 几十年来一直是一个活跃的研究领域，是理论研究者的有力工具。作为实践者采用的范式，应用研究人员也对它们进行了调查。FMs 的应用显示了它们的优点，但也显示了它们的缺点 [Jones 2003;MacKenzie 2001]。

Based on observations contradicting the mentioned difficulties, Hoare [1996] came to ask “How did software get so reliable without proof?” He, and later MacKenzie [2001], suggested that continuously improved design of programming languages and compilers, defensive programming, inspection, and testing must have effectively prevented many dangerous practices. MacKenzie coined the “Hoare paradox” stating that although proof was seldom used, software had shown to be surprisingly fit for purpose. However, faced with software of increasing complexity (e.g., RAS), MacKenzie pondered how long Hoare’s question will remain valid.

- Charles Antony Richard Hoare. 1996. How did software get so reliable without proof? In FME’96: Industrial Benefit and
Advances in Formal Methods. Springer Berlin Heidelberg, 1–17. DOI:https://doi.org/10.1007/3-540-60973-3_77


基于与上述困难相矛盾的观察，霍尔 [1996] 来问 “软件是如何在没有证据的情况下变得如此可靠的？” 他，以及后来的 MacKenzie [2001]，建议不断改进的设计编程语言和编译器，防御性编程，检查和测试必须有效地阻止了许多危险的实践。麦肯齐创造了 “霍尔悖论”，指出尽管证据很少被使用，但软件已经显示出令人惊讶地适合目的。然而，面对日益复杂的软件 (例如 RAS)，麦肯齐思考霍尔的问题将持续多久。

Indeed, recently we can observe a plethora of difficulties with robots and autonomous systems [Koopman and Wagner 2017; Neumann 2018]. Such systems are set to be more broadly deployed in society, thereby increasing their level of safety criticality [Guiochet et al. 2017] and requiring a stringent regulatory regime. A successful method for regulatory acceptance is provided by structured assurance cases, which provide comprehensible and indefeasible safety arguments supported by evidence [Habli et al. 2010; Hawkins et al. 2015; Kelly 1999]. However, such assurance cases—whether or not compliant with standards like IEC 615081 and DO-178C2—can be laborious to create, complicated to maintain and evolve, and must be rigorously checked by the evaluation process to ensure that all obligations are met and confidence in the arguments is achieved [Greenwell et al. 2006; Rushby 2013]. Nevertheless, these are problems that FMs are designed to overcome.

- Philip Koopman and Michael Wagner. 2017. Autonomous vehicle safety: An interdisciplinary challenge. IEEE Intelligent
Transportation Systems Magazine 9, 1 (1 2017), 90–96. 

- Jeremie Guiochet, M. Machin, andH.Waeselynck. 2017. Safety-critical advanced robots: A survey. Robotics and Autonomous
Systems 94 (2017).

- Ibrahim Habli, I. Ibarra, R. Rivett, and T. Kelly. 2010. Model-based assurance for justifying automotive functional safety. In
Proceedings of the SAE World Congress.

- Richard Hawkins, I. Habli, D. Kolovos, R. Paige, and T. Kelly. 2015.Weaving an assurance case from design: A model-based
approach. In Proceedings of the 16th IEEE International Conference on High Assurance Systems Engineering (HASE). IEEE.

- Tim P. Kelly. 1999. Arguing Safety – A Systematic Approach to Safety Case Management. Ph.D. dissertation. University of
York, Dept. of Computer Science.

- William S. Greenwell, John C. Knight, C. Michael Holloway, and Jacob J. Pease. 2006. A taxonomy of fallacies in system
safety arguments. In Proceedings of the 24th International System Safety Conference (2006).

- John Rushby. 2013. Logic and epistemology in safety cases. In Lecture Notes in Computer Science, Vol. 8153. Springer Berlin,
1–7. DOI:https://doi.org/10.1007/978-3-642-40793-2_1

事实上，最近我们可以观察到机器人和自主系统的诸多困难 [Koopman 和 Wagner 2017; Neumann 2018]。这样的系统被设定为在社会中更广泛地部署，从而提高其安全临界水平 [Guiochet 等人 2017] 并要求严格的监管制度。结构化担保案例提供了一个成功的监管接受方法，它提供了可理解和不可行的安全论据，并得到证据的支持 [Habli et al. 2010; Hawkins et al. 2015; Kelly 1999]。然而，这样的保证案例 -- 无论是否符合 IEC 615081 和 DO-178C2-can 标准，创建起来都很费力，维护和发展起来也很复杂,并且必须经过评估过程的严格检查，以确保所有义务都得到履行，并且对论点的信心得以实现 [Greenwell 等人 2006; 拉什比 2013]。然而，这些是 FMs 旨在克服的问题。

In spite of the weaknesses of current FMs, and encouraged by their strengths, we believe that their coordinated use within established processes can reduce critical deficits observable in dependable systems engineering. Farrell et al. [2018] state that “there is currently no general framework integrating formal methods for robotic systems”. The authors highlight the use of what are called integrated formal method (iFMs) in the construction of assurance cases and the production of evidence as a key opportunity to meet current RAS challenges. Particularly, computer-assisted assurance techniques [Wei et al. 2019], supported by evidence provided by iFMs, can greatly increase confidence in the sufficiency of assurance cases, and also aid in their maintenance and evolution through automation. Moreover, the use of modern FM-based tools to support holistic simulation, prototyping, and verification activities, at each stage of system, hardware, and software development, can lead to systems that are demonstrably safe, secure, and trustworthy.

- Marie Farrell, Matt Luckcuck, and Michael Fisher. 2018. Robotics and integrated formal methods: Necessity meets opportunity.
In Proceedings of the 14th International Conference on Integrated Formal Methods (iFM), Lecture Notes in Computer
Science (LNCS), vol. LNCS 11023. Springer, 161–171.

- Ran Wei, Tim Kelly, X. Dai, S. Zhao, and Richard Hawkins. 2019. Model based system assurance using the structured
assurance case metamodel. Journal of Software and Systems 154 (8 2019), 211–233.

尽管目前的形式化方法存在弱点，并受到其优势的鼓舞，我们相信，在已建立的过程中，它们的协调使用可以减少在可靠的系统工程中观察到的临界赤字。Farrell 等人 [2018] 指出，“目前没有集成机器人系统正式方法的通用框架”。作者强调了在构建保证案例和产生证据时使用所谓的集成形式方法 (iFMs)，这是应对当前 RAS 挑战的一个关键机会。特别是，计算机辅助保证技术 [Wei et al. 2019]，由 iFMs 提供的证据支持，可以大大增加对保证案例充分性的信心，也有助于通过自动化维护和演变。此外，在系统、硬件和软件开发的每个阶段，使用基于现代调频的工具来支持整体模拟、原型制作和验证活动,可以导致明显安全、可靠和值得信赖的系统。

### 1.1 Contribution

We investigate the potentials for the wider adoption of integrated formal methods in dependable systems engineering, taking robots and autonomous systems as a recent opportunity to foster research in such methods and to support their successful transfer and application in practice.

我们调查了在可靠的系统工程中更广泛采用集成正式方法的潜力,以机器人和自主系统为最近的机会来促进这种方法的研究，并支持它们在实践中的成功转移和应用。

We analyse the strengths, weaknesses, opportunities, and threats of using formal methods in the assurance of dependable systems such as RAS. For this analysis, we summarise experience reports on formal method transfer and application.

我们分析了在可靠系统 (如 RAS) 的保证中使用正式方法的优势、劣势、机会和威胁。对于这个分析，我们总结了关于正式方法转移和应用的经验报告。

Assuming that (a) integration enhances formal methods and (b) the assurance of robots and autonomous systems is a branch of dependable systems engineering, Figure 1 shows how we derive an agenda for fundamental and applied iFM research.

假设 (a) 集成增强了正式方法，(b) 机器人和自主系统的保证是可靠系统工程的一个分支,图 1 显示了我们如何得出基础和应用 iFM 研究的议程。

From the strengths, we see in recent research, and from the opportunities in current RAS assurance, we argue why this domain is a key opportunity for iFMs. Particularly, we indicate how such methods can meet typical assurance challenges that RASs are increasingly facing.

从优势，我们在最近的研究中看到，从当前 RAS 保证的机会，我们争论为什么这个领域是 iFMs 的一个关键机会。特别地，我们指出这样的方法如何能够满足 RASs 日益面临的典型保证挑战。

From theweaknesses,we observe in recent research, and fromthe threats formal method research transfer is exposed to, we derive directions of foundational and empirical research to be taken to transfer iFMs into the assurance of robots, autonomous systems, and other applications, and to use these methods to their maximum benefit.

从弱点，我们在最近的研究中观察到，从威胁正式方法研究转移暴露,我们得出了基础和经验研究的方向，将 iFMs 转移到机器人、自主系统和其他应用的保证中，并使用这些方法来获得最大利益。

Our analysis (1) elaborates on the analysis and conclusions of Hoare et al. [2009], (2) extends their suggestions with regard to formal method experimentation and empirical evidence of effectiveness focusing on collaboration between formal method researchers and practitioners, and (3) develops a research and research transfer road map, placing emphasis on RASs.

我们的分析 (1) 阐述了 Hoare 等人的分析和结论 [2009]，(2) 扩展他们关于正式方法实验和注重正式方法研究人员和实践者之间合作的有效性的经验证据的建议，以及 (3) 开发研究和研究转移路线图,强调 RASs。

### 1.2 Overview

We provide some background including terminology (Section 2.1) and related work (Section 2.2) in the following. Then, we carry through an analysis of strengths, weaknesses (Section 3), opportunities (Section 4), and threats (Section 5) of iFMs for RAS assurance. Based on this analysis, we formulate our hypotheses (Section 6), pose research questions based on these hypotheses, derive a research agenda, and specify some outcomes we expect from this agenda (Section 7).

我们提供了一些背景知识，包括以下术语 (第 2.1 节) 和相关工作 (第 2.2 节)。然后，我们对 RAS 保证的 iFMs 的优势、劣势 (第 3 节) 、机会 (第 4 节) 和威胁 (第 5 节) 进行分析。基于这个分析，我们制定我们的假设 (第 6 节)，提出基于这些假设的研究问题，得出研究议程,并具体说明我们对本议程的一些预期结果 (第 7 节)。

## 2 BACKGROUND

This section introduces the core terminology used throughout this article, and provides a discussion of other surveys of the FM state of the art and a summary of similar positions and agendas.

本节介绍了本文中使用的核心术语，并讨论了形式化方法最先进的其他调查，以及类似立场和议程的总结。

### 2.1 Terminology

For sake of clarity among readers of different provenance, we restrict the meaning of some terms we use in the following and introduce convenient abbreviations.

为了让不同出处的读者清楚，我们限制了下面一些术语的含义，并引入了方便的缩写。

We view robots and autonomous systems as both dependable systems and highly automated machines capable of achieving a variety of complex tasks in support of humans.We can consider such systems by looking at four layers: the plant or process composed of the operational environment and the machine; the machine itself; the machine’s controller, and the software embedded into this controller. Based on these layers, we treat “embedded system” and “embedded software” as synonyms. Machine, controller, and software can all be distributed.

我们认为机器人和自主系统是可靠的系统和高度自动化的机器，能够实现各种复杂的任务来支持人类。我们可以通过观察四层来考虑这样的系统: 由操作环境和机器组成的工厂或过程; 机器本身; 机器的控制器,和嵌入到这个控制器的软件。基于这些层次，我们将 “嵌入式系统” 和 “嵌入式软件” 视为同义词。机器、控制器和软件都可以分发。

By dependable systems engineering, we refer to error-avoidance and error-detection activities in control system and embedded software development (e.g., according to the V-model). Avizienis et al. [2004] devised a comprehensive terminology and an overview of the assessment and handling of a variety of faults, errors, and failures. For critical systems, such activities are expected to be explicit (e.g., traceable, documented), to employ best practices (e.g., design patterns), and to be driven by reasonably qualified personnel (e.g., well-trained and experienced engineers or programmers).

通过可靠的系统工程，我们指的是控制系统和嵌入式软件开发中的错误避免和错误检测活动 (例如，根据 V 模型)。Avizienis 等人 [2004] 设计了一个全面的术语和对各种故障、错误和故障的评估和处理的概述。对于关键系统，此类活动预计是明确的 (e.g.,可追溯的，有记录的)，采用最佳实践 (e.g.,设计模式)，并由合理合格的人员 (e.g.,训练有素，经验丰富的工程师或程序员)。

The need for dependability often arises from the embedding of software into a cyber-physical context (i.e., an electronic execution platform, a physical process to be controlled, and other systems or human users to interact with). Dependability assurance (DA), or assurance for short, encompasses the usually cross-disciplinary task of providing evidence for an assurance case (e.g., safety, security, reliability) for a system in a specific operational context [Kelly 1999].

对可靠性的需求通常来自将软件嵌入到网络物理环境中 (i。e.,电子执行平台、要控制的物理过程以及要与之交互的其他系统或人类用户)。可靠性保证 (DA)，简称保证，通常包含为保证案例提供证据的跨学科任务 (e.g.,安全性，安全性，可靠性) 对于特定操作环境中的系统 [Kelly 1999]。

By formal methods, we refer to the use of formal (i.e., mathematically precise and unambiguous) modelling languages to describe system elements, such as software, hardware, and the environment, and the subjection of models written in these languages to analysis [Jones 2003; MacKenzie 2001], the results of which are targeted at assurance [Clarke and Wing 1996; RTCA 2011]. FMs always require the use of both formal syntax and formal semantics (i.e., the mapping of syntax into a mathematical structure). Semantics that allow the verification of refinement or conformance across different FMs is said to be unifying [Hoare and He 1998; van Glabbeek 2001]. iFMs allow the coordinated application of several potentially heterogeneous FMs, supported by interrelated layers of formal semantics [Börger et al. 2008; Grieskamp et al. 2000].

通过形式化方法，我们指的是使用形式化 (i.e.,数学上精确和明确的) 建模语言来描述系统元素，如软件、硬件和环境，以及用这些语言编写的模型对分析的服从 [Jones 2003; macKenzie 2001],其结果针对保证 [Clarke and Wing 1996; RTCA 2011]。FMs 总是需要使用形式语法和形式语义 (即，将语法映射到数学结构中)。允许验证不同 FMs 的细化或一致性的语义据说是统一的 [Hoare 和 He 1998; van glambeek 2001]。IFMs 允许几个潜在的异构 FMs 的协调应用，由形式语义的相互关联层支持 [b ö rger et al. 2008; Grieskamp et al. 2000]。

FMs stand in contrast to informal methods, which employ artefacts without a formal syntax or semantics, such as natural language descriptions and requirements. In the gap between informal methods and FMs, there is also a variety of semi-formal methods, including languages like the Unified Modelling Language (UML) and the Systems Modelling Language (SysML), whose syntax and semantics have frequently been subject of formalisation in research [e.g., Breu et al. 1997; Giese and Heldal 2004; Posse and Dingel 2016; Wieringa and Dubois 1994].

FMs 与非正式方法形成对比，非正式方法采用没有正式语法或语义的人工制品，如自然语言描述和要求。在非正式方法与 FMs 的鸿沟中，也有多种半正式方法，包括统一建模语言 (UML) 和系统建模语言 (SysML),其语法和语义经常成为研究中的形式化主题 [e.g.,breu 等人 1997;Giese 和 Heldal 2004; Posse 和 Dingel 2016; Wieringa 和 Dubois 1994]。

FM-based tools assist in the modelling and reasoning based on an FM. Model-based development (MBD) and model-driven engineering (MDE) served many opportunities for FM-based tools to be applied in dependable systems practice [Bicarregui et al. 2009; Woodcock et al. 2009].

基于 FM 的工具有助于基于 FM 的建模和推理。基于模型的开发 (MBD) 和模型驱动工程 (MDE) 为基于 FM 的工具应用于可靠的系统实践提供了许多机会 [Bicarregui 等人。2009; Woodcock 等人。2009]。

We speak of applied or practical FMs to signify successful applications of FMs in a practical context, for example, to develop embedded control software deployed in a commercial product marketed by an industrial company. We consider the use of FMs in research projects still as FM research. Empirical FM research investigates practical FMs, for example, using surveys, case studies, or controlled field experiments [Goues et al. 2018]. We speak of FM transfer if FM research is transferred into practice with the aim to effectively apply and practice FMs. We consider FM transfer, as discussed below, as crucial for empirical FM research and progress of iFM research.

我们谈论应用或实际的 FMs 来表示 FMs 在实际环境中的成功应用，例如，开发在工业公司销售的商业产品中部署的嵌入式控制软件。我们认为在研究项目中使用 FMs 仍然是 FM 研究。实证 FM 研究调查实用 FMs，例如，使用调查，案例研究或对照现场实验 [Goues et al. 2018]。我们谈到 FM 转移，如果 FM 研究转移到实践中，目的是有效地应用和实践 FMs。如下所述，我们认为 FM 转移对于经验 FM 研究和 iFM 研究的进展至关重要。

### 2.2 RelatedWork

Many researchers have suggested that FMs will, in one way or another, play a key role in mastering the difficulties discussed below and in achieving the desired guarantees (e.g., dependability, security, performance) of future critical systems [e.g., Jones 2003; MacKenzie 2001].

- Cliff B. Jones. 2003. The early search for tractable ways of reasoning about programs. IEEE Annals of the History of Computing
25, 2 (4 2003), 26–49. DOI:https://doi.org/10.1109/mahc.2003.1203057
- Donald A. MacKenzie. 2001. Mechanizing Proof: Computing, Risk, and Trust. The MIT Press. DOI:https://doi.org/10.7551/mitpress/4529.001.0001

许多研究人员认为，FMs 将以这样或那样的方式，在掌握下面讨论的困难和实现期望的保证方面发挥关键作用 (e.g.,可靠性、安全性、性能) 的未来关键系统 [e.g.,琼斯 2003; 麦肯齐 2001]。

Expecting an increased use of FMs to solve practical challenges in the mid-1990s, Clarke and Wing [1996] suggested FM integration, tool development, and continuous specialist training to foster successful FM transfer to practice.

- Edmund M. Clarke and Jeannette M. Wing. 1996. Formal methods: State of the art and future directions. Comput. Surveys 28, 4 (1996), 626–643. DOI:https://doi.org/10.1145/242223.242257

克拉克和荣 [1996] 期望在 20世纪90年代中期中期更多地使用 FMs 来解决实际挑战，建议 FM 整合、工具开发和持续的专业培训来促进成功的 FM 转移到实践中。

van Lamsweerde [2000] observed a growing number of FM success stories in requirements engineering. Evaluating several FM paradigms, he outlined weaknesses (e.g., isolation of languages, poor guidance) to be compensated and challenges to be met towards effective FM use, particularly their integration into multi-paradigm specification languages.

Van Lamsweerde [2000] 在需求工程中观察到越来越多的 FM 成功案例。评估几个 FM 范式，他概述了弱点 (e.g.,语言的隔离，糟糕的指导) 要得到补偿，并面临有效使用 FM 的挑战，特别是它们集成到多范例规范语言中。

Aiming at the improvement of software dependability, Jackson et al. [2007]made several key observations of recent dependability practice (e.g., lack of evidence for method effectiveness) leading to a general proposal with broad implications: rigorous dependability cases with explicit claims, the support of reuse and evolution, and the selective use of FMs. Additionally, these authors provide a number of recommendations to tool vendors and organisations in education and research.

针对软件可靠性的改进，Jackson 等人 [2007] 对最近的可靠性实践 (e.g.,缺乏方法有效性的证据) 导致一个具有广泛含义的一般性建议: 具有明确要求的严格可靠性案例，重用和进化的支持，以及 FMs 的选择性使用。此外，这些作者还为教育和研究领域的工具供应商和组织提供了一些建议。

Also in the mid-2000s, Hinchey et al. [2008] spotted a decline of internet software dependability in the context of an increased level of concurrency in such systems. Their observation was backed by an earlier comparative software/hardware dependability discussion by Gray and Brewer [2001]. Hinchey et al. highlighted achievements in FM automation enabling an increased use of lightweight FMs in “software engineers’ usual development environments”. Furthermore, they stressed the ability to use several FMs in a combined manner to verify distributed (embedded) systems, avoid errors, and hence stop the decline of software dependability.

同样在 21世纪00年代中期中期，Hinchey 等人 [2008] 发现在这样的系统中增加的并发水平的背景下互联网软件可靠性的下降。他们的观察得到了格雷和布鲁尔早期软件/硬件可靠性比较讨论的支持 [2001]。Hinchey 等人强调了 FM 自动化方面的成就，使得轻量级 FMs 在 “软件工程师通常的开发环境” 中的使用得以增加。此外，他们强调了以组合方式使用几个 FMs 来验证分布式 (嵌入式) 系统的能力，避免错误，从而阻止软件可靠性的下降。

Hoare et al. [2009] issued a manifesto for a “Verified Software Initiative”. Based on a consensus of strengths, weaknesses, opportunities, and threats in the software engineering community, they proposed a long-term international “research program towards the construction of error-free software systems”. This initiative aims to achieve its agenda through (1) new theoretical insights into software development, (2) creation of novel automated FM tools, and (3) a collection of experiments and benchmarks. In particular, the initiative is driven by a number of “grand challenges” [Woodcock 2006]—difficult practical verification problems that can guide future research. The experiments have broad scope, and include a smart cash card [Stepney et al. 2000] (the Mondex card), a secure entry system (Tokeneer), and a cardiac pacemaker.

Hoare 等人 [2009] 发布了一个 “验证软件倡议” 的宣言。基于对软件工程界的优势、劣势、机会和威胁的共识，他们提出了一个长期的国际 “无错软件系统建设研究计划”。该倡议旨在通过 (1) 对软件开发的新理论见解，(2) 创建新型自动化正式方法工具，以及 (3) 实验和基准的集合来实现其议程。特别是，该倡议是由一系列 “重大挑战” [Woodcock 2006] 驱动的-困难的实际验证问题，可以指导未来的研究。实验范围广泛，包括智能现金卡 [Stepney et al. 2000] (Mondex 卡) 、安全进入系统 (令牌) 和心脏起搏器。

Outlining an agenda for FM transfer, Jhala et al. [2012] raised the need for improved benchmarks, metrics, and infrastructure for experimental evaluation, the need for revised teaching and training curricula [Perlis 1969], and the need for research communities interested in engaging with practitioners and working on ways to scale FMs up to large systems and to increase the usability of FMs. The authors specified several applications with great opportunities for FM transfer.

Jhala概述调频传输的议程,等。[2012]提出了需要改进的标准,指标、实验评价和基础设施,需要修订的教学和培训课程(玻璃市1969年),和需要研究社区感兴趣与从业者和致力于规模FMs大型系统,提高FMs的可用性。作者详细介绍了几种具有良好调频传输前景的应用。

Applied researchers and practitioners interviewed by Schaffer and Voas [2016] convey an optimistic picture of FM adoption in practice, highlighting the potentials to improve IT security, particularly in cyber-physical systems. Chong et al. [2016] share the view that FMs are the most promising approach towards acceptably dependable and secure systems. The challenges they list for the security domain are similar to the challengeswe perceive in RAS assurance: FM integration, sound abstraction techniques, compositional guarantees, and evidence for sustainable transfer.

Schaffer和Voas[2016]采访的应用研究人员和实践者对FM在实践中的应用前景表示乐观，并强调了改进IT安全的潜力，特别是在网络物理系统中。Chong等人[2016]认为，FMs是实现可接受的可靠和安全系统的最有前途的方法。他们列出的安全领域的挑战与我们在RAS保证中看到的挑战类似:FM集成、可靠的抽象技术、组合保证和可持续转移的证据。

With their survey of FMs for RAS verification, Luckcuck et al. [2018] identified difficulties of applying FMs in the robotics domain and summarised research results and their limitations. They conclude (i) that formalisation remains the most critical and most difficult task, (ii) that the surveyed approaches do not provide “sufficient evidence for public trust and certification”, and (iii) that iFMs would be highly desirable if the current lack of translations between the most relevant of the surveyed techniques (e.g., model checking) could be overcome. We complement their observations with a further analysis of the lack of unification of FMs and of the missing empirical evidence for the effectiveness of FMs and iFMs. Additionally, we provide a research road map.

Luckcuck等[2018]通过对FMs进行RAS验证的调查，发现了形式化方法在机器人领域应用的困难，总结了研究结果及其局限性。他们的结论是(i)正规化仍然是最关键和最困难的任务,(ii)调查方法不提供“足够的证据对公众信任和认证”,和(3),ifm将高度可取,如果当前之间缺乏翻译最相关的调查技术(例如,模型检测)可能被克服。我们通过进一步分析FMs缺乏统一性以及缺少FMs和iFMs有效性的经验证据来补充他们的观察。此外，我们还提供了一份研究路线图。

# 3 STRENGTHS AND WEAKNESSES OF FORMAL METHODS FOR ASSURANCE

Following the guidelines for strengths, weaknesses, opportunities, and threats (SWOT) analysis by Piercy and Giles [1989], we provide an overview of strengths and weaknesses of FMs, regarding
- reputation, proof culture, education, training, and use (Section 3.1), 
- transfer efforts (Section 3.2), 
- evidence of effectiveness (Section 3.3), 
- expressivity (Section 3.4), and 
- integration and coordination (Section 3.5).

根据Piercy和Giles[1989]的《优势、劣势、机会和威胁(SWOT)分析指南》，我们提供了FMs的优势和劣势的概述
- 声誉、证明文化、教育、培训和使用(第3.1节)，
- 转移努力(第3.2节)，
- 有效性的证据(第3.3条)，
- 表达性(第3.4节)，和
- 一体化和协调(第3.5节)。

### 3.1 Reputation, Proof Culture, Education, Training, and Use

In the guest editor’s introduction of the “50 Years of Software Engineering” IEEE Software special theme issue [Erdogmus et al. 2018], the question “Are formal methods essential, or even useful, or are they just an intellectual exercise that gets in the way of building real-world systems?” invited us to deliberate on this topic and summarise its highlights. Applied researchers have raised the issue of limited effectiveness and productivity of FMs, particularly in large practical systems with changing requirements [Glass 2002; Parnas 2010]. FMs are known to be difficult to apply in practice, and bad communication between theorists and practitioners sustains the issue that FMs are taught but rarely applied [Glass 2002]. In contrast, they are considered to have significant potential to cope with the toughest recent engineering problems: certifiable RAS assurance [Farrell et al. 2018].

在“软件工程50年”IEEE软件专题[Erdogmus et al. 2018]的嘉宾编辑介绍中，问题是“正式方法是必需的，甚至是有用的，还是它们只是妨碍构建真实世界系统的智力练习?”“邀请我们对这个主题进行讨论并总结其亮点。应用研究人员提出了FMs的有效性和生产率有限的问题，特别是在需求不断变化的大型实际系统中[Glass 2002;帕尔曼党注册2010]。众所周知，FMs很难在实践中应用，理论家和实践者之间的沟通不畅导致了FMs被教授却很少被应用的问题[Glass 2002]。相比之下，它们被认为有巨大的潜力来应对最近最棘手的工程问题:可认证的RAS保证(Farrell等，2018年)。

Studying the sociology of proof, MacKenzie [2001] identified three sources of knowledge about a system’s dependability: induction (i.e., from observation), authority (e.g., expert opinion), and deduction (i.e., inference from models) which is possibly the most powerful. Since the beginning of software engineering there has been a debate on the style of deductive reasoning about programs and on the usefulness of FMs. De Millo et al. [1979] argued that proof is a social process. Long and difficult-to-read computer-produced verification evidence cannot be subject to such a process and is not genuine proof. Dijkstra [1978] countered, albeit not as a supporter of mechanisation, to change from a personal trust-based culture of proof to the formalisation of proof steps. Fetzer [1988] doubted that verification based on a model of the program can yield any knowledge about the dependability of an implementation of that program. According to Naur [1994], it is not the degree of formalisation making a proof convincing but the way the argument is organised. MacKenzie tried to arbitrate this debate between rigorous proof in ordinary mathematics and formal mechanised proof. He suggested that proof assistants have the potential to use formal methods [Jones 2003] to the maximum benefit. Daylight [2013] concluded from a discussion with Tony Hoare that formalist and empiricist perspectives, while still causing controversies between research and practice, complement each other in a fruitful way.

MacKenzie[2001]研究了证明的社会学，确定了关于系统可靠性的三个知识来源:归纳法(即。、权威(如专家意见)和演绎(如专家意见)。这可能是最强大的。自从软件工程开始以来，关于程序演绎推理的风格和FMs的有效性一直存在争议。De Millo等人[1979]认为，证据是一个社会过程。长而难读的计算机生成的验证证据不能经过这样的过程，也不是真正的证据。Dijkstra[1978]反对，尽管不是机械化的支持者，从个人信任为基础的证明文化到证明步骤的形式化。Fetzer[1988]怀疑基于程序模型的验证能否产生关于程序实现可靠性的任何知识。根据Naur[1994]的研究，证明的说服力不是正式化的程度，而是论证的组织方式。MacKenzie试图在普通数学中的严格证明和正式的机械化证明之间进行仲裁。他认为，证据助理有潜力使用正式的方法[琼斯2003]的最大利益。Daylight[2013]通过与Tony Hoare的讨论得出结论，虽然形式主义和经验主义的观点仍然会在研究和实践之间引起争议，但是它们是相辅相成的。

Nevertheless, FMs have shown to be well suited to substantially improve modelling precision, requirements clarity, and verification confidence. FM applications in requirements engineering such as the “Software Cost Reduction” tool set [Heitmeyer et al. 1995] even carry the hypothesis of FM cost-effectiveness in its name. By the 1990s, FM researchers had already started to examine FM usefulness with the aim to respond to critical observations of practitioners [Barroca and McDermid 1992; Bowen and Hinchey 1995; Hall 1990; Knight et al. 1997; Littlewood et al. 1998]. Some of these efforts culminated in empirical studies [Pfleeger and Hatton 1997; Sobel and Clarkson 2002] suggesting high error detection effectiveness, though with some controversy also caused by employed research designs [Berry and Tichy 2003; Sobel and Clarkson 2003].

尽管如此，FMs已被证明非常适合大幅度地提高建模精度、需求明确性和验证信心。FM在需求工程中的应用，如“软件成本降低”工具集[Heitmeyer et al. 1995]，甚至在其名称中包含FM成本效益的假设。到20世纪90年代，FM的研究人员已经开始检查FM的有效性，目的是响应实践者的批判性观察[Barroca和McDermid 1992;Bowen和Hinchey, 1995;大厅1990;奈特等人，1997;Littlewood等人，1998]。其中一些努力在实证研究中达到顶峰[Pfleeger和Hatton 1997;Sobel和Clarkson 2002]表明了很高的错误检测效率，尽管也有一些争议是由采用的研究设计引起的[Berry和Tichy 2003;索贝尔和克拉克森2003年。

Jones and Bonsignour [2011, Sec. 3.2, Tab. 3.2] observe that the combination of formal7 inspection, static analysis, and formal testing has been the best approach to defect prevention with up to 99% of accumulated defect removal efficiency. FMs can be seen as a rigorous and systematic form of this approach, though less often applied. In Appendix A, we make a brief excursion to the relationship between FMs and formal inspection and try to roughly estimate the population size of FM users.

Jones和Bonsignour[2011，第3.2节，表3.2]指出，将正式的检查、静态分析和正式的测试相结合是预防缺陷的最佳方法，其累积缺陷清除效率最高可达99%。FMs可以被看作是这种方法的一种严格的和系统的形式，尽管很少被应用。在附录A中，我们简要介绍了FMs和正式检查之间的关系，并尝试粗略估计FM用户的总体大小。

From two larger surveys, one in the early 1990s [Austin and Parkin 1993] and another one in the late 2000s [Bicarregui et al. 2009;Woodcock et al. 2009], we obtain a more comprehensive picture of the typical advantages of FM use and barriers to FM adoption as seen by practitioners and practical FM researchers. In two recent surveys [Gleirscher and Marmsoler 2018; Gleirscher and Nyokabi 2018], we made two, not necessarily surprising but empirically supported, observations underpinning the main findings of the former studies: many practitioners view FMs as promising instruments with high potential, and would use these instruments to their maximum benefit, whether directly or through FM-based tools. However, the beneficial use of FMs is still hindered by severe obstacles (e.g., FMs are considered hard to learn, difficult to integrate in existing processes, too expensive, prone to invalid abstractions, and difficult to maintain).

从两个大的调查,一个在1990年代早期(1993年奥斯汀和帕金),另一个在2000年代末(Bicarregui et al . 2009;丘鹬et al . 2009],我们获得一个更全面的图片FM调频使用和障碍的典型优点收养所看到的从业者和实际调频人员。最近的两项调查[Gleirscher和Marmsoler 2018;Gleirscher和Nyokabi 2018]，我们做了两个，不一定是令人惊讶的，但有经验支持的观察，支持了之前研究的主要发现:许多从业者认为FMs是有前途的工具，具有很高的潜力，并将使用这些工具，无论是直接或通过基于FMs的工具，最大限度地获益。然而，FMs的有益使用仍然受到严重障碍的阻碍(例如，FMs被认为难于学习、难以集成到现有流程中、太昂贵、容易产生无效的抽象和难于维护)。

Strength 1. 

FMs can improve RAS modelling, the specification of RAS requirements, and the automation of RAS verification, fostering the early detection of systematic errors in RAS designs. Many assurance practitioners perceive FM usefulness as positive.

FMs可以改进RAS建模、RAS需求说明和RAS验证的自动化，促进RAS设计中系统错误的早期检测。许多保险从业人员认为FM的有用性是积极的。

Weakness 1. 

FMs have shown to be difficult to learn and apply. Many assurance practitioners perceive the ease of use of FMs as negative. Moreover, research has been ineffectively communicated in FM teaching and training.

FMs已被证明很难学习和应用。许多保险从业人员认为FMs的易用性是负面的。此外，在FM教学和培训中，研究交流的效果不佳。

### 3.2 Transfer Efforts

FMs can be effective in two ways, ab-initio (i.e., before implementation) and post-facto (i.e., after implementation). The ab-initio use of FMs aims at reducing late error costs through, for example, formal prototyping, step-wise refinement, formal test-driven development, crafting module assertions prior to programming, or formal contract-based development. Once an initial formalisation (e.g., invariants) is available, it is argued for families of similar systems that, from the second or third FM application onward, the benefit of having the formalisation outperforms the cumulative effort to maintain the formalisation up to an order of magnitude [Jackson et al. 2007; Miller 1998; Miller et al. 1999]. This argument also addresses agile settings inasmuch as iterations or increments refer to similar systems. The post-facto use of FMs can occur through knowledge extraction from existing artefacts and using automated tools such as, for example, formal or model-based post-facto testing tools or post-facto use of code assertion checkers [Kästner et al. 2010; Leino 2017]. Overall, the second way of utilising FMs is known to be more compatible with everyday software practice.

FMs有两种有效的方式，从头开始(也就是从头开始)。、实施前和实施后(即后,实现)。FMs的从头到尾的使用旨在减少后期的错误成本，例如，通过正式的原型设计、逐步细化、正式的测试驱动的开发、在编程之前制作模块断言，或者正式的基于契约的开发。一旦一个初始的形式化(例如，不变量)是可用的，对于类似系统的家族来说，从第二个或第三个FM应用程序开始，拥有形式化的好处超过了将形式化维持到数量级的累积努力[Jackson et al. 2007;米勒1998年;Miller等人，1999]。这个论点也涉及到敏捷设置，因为迭代或增量指的是类似的系统。FMs的事后使用可以通过从现有人工制品中提取知识和使用自动化工具(例如，正式的或基于模型的事后测试工具或事后使用代码断言检查器)来实现[Kastner et al. 2010;Leino 2017]。总的来说，使用FMs的第二种方法被认为与日常软件实践更兼容。

Achievements collected by Aichernig and Maibaum [2003], Boulanger [2012], and Gnesi and Margaria [2013] show that many researchers have been working towards successful FM transfer. Moreover, researchers experienced in particular FMs draw positive conclusions from FM applications, especially in scaling FMs through adequate tool support for continuous reasoning in agile software development [Miller et al. 2010; O’Hearn 2018]. Other researchers report about progress in theorem proving of system software of industrial size [e.g., Klein et al. 2018] and about FM-based tools for practical use [e.g., Bicarregui et al. 2009; Kästner et al. 2010; Peleska and Huang 2016]. MBD and MDE have a history of wrapping FMs into software tools to make access to formalisms easier and to help automating tedious tasks via domain-specific language (DSLs) and visual notations.

Aichernig和Maibaum[2003]、Boulanger[2012]、Gnesi和Margaria[2013]等人的研究成果表明，许多研究人员一直致力于FM的成功转移。此外，有经验的FMs研究人员从FM应用程序中得出了积极的结论，特别是通过在敏捷软件开发中为连续推理提供足够的工具支持来扩展FMs [Miller et al. 2010;奥赫恩2018]。其他研究者报告了工业规模的系统软件的定理证明的进展。， Klein等人[2018]，以及关于基于fm的实用工具[例如， Bicarregui等，2009;Kastner等人2010;Peleska和Huang 2016]。MBD和MDE有将FMs封装到软件工具中的历史，以便更容易地访问形式主义，并通过领域特定语言(dsl)和可视化表示法帮助自动化繁琐的任务。

Static (program) analysis is another branch where FM-based tools have been successfully practised [e.g., Kästner et al. 2010]. However, few static analysis tools are based on FMs and many of these tools are exposed to reduced effectiveness because of high false-positive rates, particularly if settings are not perfectly adjusted to the corresponding project [Gleirscher et al. 2014].

- Daniel Kästner, Stephan Wilhelm, Stefana Nenova, Patrick Cousot, Radhia Cousot, Jérôme Feret, Laurent Mauborgne, Antoine Miné, and Xavier Rival. 2010. Astrée: Proving the absence of runtime errors. In Proceedings of Embedded Real Time Software and Systems (ERTS2) (2010), Vol. 9.
- Mario Gleirscher, Dmitriy Golubitskiy, Maximilian Irlbeck, and StefanWagner. 2014. Introduction of static quality analysis in small and medium-sized software enterprises: Experiences from technology transfer. Software Quality Journal 22, 3 (2014), 499–542. DOI:https://doi.org/10.1007/s11219-013-9217-z

静态(程序)分析是另一个已经成功实践了基于fm的工具的分支。， Kastner et al. 2010]。然而，很少有静态分析工具是基于FMs的，而且由于高假阳性率，许多这些工具的有效性降低，特别是如果设置没有完全调整到相应的项目[Gleirscher et al. 2014]。

Furthermore, the concolic testing technique [Godefroid et al. 2005], a post-facto FM, has seen multiple successes in industry [Godefroid et al. 2012; Kim et al. 2011]. It exercises all possible execution paths of a program through systematic permutation of a sequence of branch conditions inferred by an instrumented concrete execution. It uses these symbolic execution paths and Satisfiability Modulo Theory (SMT) solving to obtain a series of inputs that exercise the full range of program paths. It does not depend on a predefined model of the program, but effectively infers one based on the branch conditions. It can therefore readily be used on existing program developments, and has notably been used by Samsung for verification of their flash storage platform software [Kim et al. 2011]. Indeed, it is a belief of the authors of this latter work that post-facto methods provide greater opportunities for adoption of FMs in industry.

- P. Godefroid, N. Klarlund, and K. Sen. 2005. DART: Directed automated random testing. In Programming Language Design and Implementation (PLDI) (ACM SIGPLAN Notices), Vol. 40. ACM, 213–223.
- P. Godefroid, M. Y. Levin, and D. Molnar. 2012. SAGE: Whitebox fuzzing for security testing. Commun. ACM 55, 3 (2012).
- M. Kim, Y. Kim, and Y. Choi. 2011. Concolic testing of the multi-sector read operation for flash storage platform software. Formal Aspects of Computing 24 (2011), 355–374.

此外，concolic测试技术[Godefroid et al. 2005]是一种后事实FM，在行业中获得了多次成功[Godefroid et al. 2012;Kim等人，2011]。它通过由插装的具体执行推断出的分支条件序列的系统排列来执行程序的所有可能的执行路径。它使用这些符号执行路径和可满足性模理论(SMT)求解来获得一系列的输入，这些输入可以执行整个程序路径。它不依赖于程序的预定义模型，而是根据分支条件有效地推断出一个模型。因此，它可以很容易地用于现有的程序开发，特别是已被三星用于验证其闪存平台软件[Kim等人，2011]。事实上，后一项工作的作者认为，事后方法为工业上采用FMs提供了更大的机会。

Recently, there have been several developments in the use of FMs to assure safety requirements related to human-computer interaction (HCI), and a growing body of literature [Harrison et al. 2018; Oliveira et al. 2015; Weyers et al. 2017], particularly relating to certification of commercial medical devices [Bowen and Reeves 2017; Masci et al. 2013]. There, formal methods have been shown to be successful in facilitating regulatory acceptance, for example , with the Food and Drug Administration (FDA) agency in the United States. Masci et al. [2013] formalised the FDA regulatory safety requirements for the user interface of a patient-controlled infusion pump, and used the PVS proof assistant to verify it. Bowen and Reeves [2017], similarly, formally modelled an infusion pump interface, using the Z notation, and then used the resulting specification as an oracle to generate test cases. Harrison et al. [2019] performed risk analysis for a new neonatal dialysis machine, formalised safety requirements, and use these requirements to analyse and verify the source code. The consensus of these authors is that FMs can greatly reduce the defects in safety critical HCI prior to deployment. The techniques are increasingly practically applicable through greater automation. Moreover, it is likely that these results have potential applications in other domains, such as RAS, provided they can be transferred and scale to this greater complexity.

最近，在使用 FMs 来保证与人机交互 (HCI) 相关的安全要求方面有了一些发展，并且越来越多的文献 [Harrison 等人。2018; Oliveira 等人。2015; Weyers 等人。2017]，特别是与商业医疗器械认证 [Bowen 和 Reeves 2017;Masci 等人。2013]。在那里，正式的方法已被证明在促进监管接受方面是成功的，例如，美国食品和药物管理局 (FDA) 机构。Masci 等人 [2013] 将病人控制输液泵的用户界面的 FDA 监管安全要求正式化，并使用 PVS 证明助手进行验证。类似地，Bowen 和 Reeves [2017] 使用 Z 符号正式建模输液泵接口，然后使用生成的规范作为 oracle 生成测试用例。Harrison 等人 [2019] 对一种新的新生儿透析机进行了风险分析，形式化了安全要求，并使用这些要求来分析和验证源代码。这些作者的共识是，FMs 可以在部署之前大大减少安全关键 HCI 中的缺陷。这些技术通过更大的自动化越来越实际适用。此外，这些结果很可能在其他领域有潜在的应用，如 RAS，只要它们能够被转移并扩展到这种更大的复杂性。

Strength 2. There exist many transfer re-entry points from a range of insightful FM case studies in industrial and academic labs. FMs were demonstrated to be a useful basis formany static analysis and MDE tools.

Weakness 2. The number of practical (comparative) case studies using (ab-initio) FMs or iFMs, particularly on RASs, is still too low to draw useful and firm conclusions on FM effectiveness.

### 3.3 Evidence of Effectiveness

Whether used as ab-initio or post-facto tools, strong evidence for the efficacy of FMs in practice is still scarce [e.g., Pfleeger and Hatton 1997] and more anecdotal [e.g. Aichernig and Maibaum 2003; Boulanger 2012; Gnesi andMargaria 2013; Schaffer and Voas 2016], rarely drawn from comparative studies [e.g., Pfleeger and Hatton 1997; Sobel and Clarkson 2002], often primarily conducted in research labs [e.g., Chudnov et al. 2018; Galloway et al. 1998], or not recent enough to reflect latest achievements in verification tool research [e.g., Cataño and Huisman 2002]. We observe that a large fraction of empirical evidence for FM effectiveness can be classified as level 6 or 7 according to Goues et al. [2018, Tab. 2], that is, too weak to draw effective conclusions.

无论是用作从头开始还是事后工具，FMs 在实践中的有效性的有力证据仍然稀缺 [e.g.,pfleeger 和 Hatton 1997] 和更多轶事 [e.g.Aicreneg 和 Maibaum 2003; Boulanger 2012; Gnesi 和 margaria 2013; Schaffer 和 Voas 2016]，很少来自比较研究 [e.g.,Pfleeger 和 Hatton 1997; Sobel 和 Clarkson 2002]，通常主要在研究实验室进行 [e.g.,chudnov 等人。2018; 加洛韦等人。1998]，或最近不足以反映验证工具研究的最新成果 [e.g.,卡塔尼奥和惠斯曼 2002]。我们观察到，根据 Goues 等人 [2018，表 1，FM 有效性的大部分经验证据可以归类为 6 级或 7 级。2]，即太弱，无法得出有效结论。

Jackson et al. [2007, p. 39] aswell as Jones and Bonsignour [2011, Sec. 4.4, p. 220], two researchers from the software engineering measurement community, support this observation. Jones and Bonsignour state that “there is very little empirical data on several topics that need to be well understood if proofs of correctness are to become useful tools for professional software development as opposed to academic experiments”. Moreover, the controversies about proof culture summarised in Section 3.1 contain little data to resolve practitioners’ doubts.

Jackson 等人 [2007，2011 页] 以及 Jones 和 Bonsignour [，Sec.4.4，p. 220]，软件工程测量社区的两位研究人员支持这一观察。Jones 和 Bonsignour 指出，“如果正确性证明要成为专业软件开发的有用工具，而不是学术实验，需要很好地理解的几个主题的经验数据很少”。此外，3.1 节中总结的关于证明文化的争议几乎没有数据来解决从业者的疑虑。

Graydon [2015] observed this lack of evidence of FM effectiveness in assurance argumentation. More generally, Rae et al. [2010] noticed insufficiently evaluated safety research. About 86% of works lack guidance to reproduce results, hence forming a barrier to the advancement of safety practice. Although their study is limited to one conference series, it indicates deficiencies in the evaluation of DA research. Overall, it is important to understand that the mentioned lack of evidence and successful transfer produces great opportunities for further empirical and theoretical FM research.

Graydon [2015] 观察到在保证论证中缺乏 FM 有效性的证据。更一般地说，Rae 等人 [2010] 注意到安全性研究评价不充分。大约 86% 的工作缺乏重现结果的指导，从而形成了安全实践前进的障碍。虽然他们的研究仅限于一个会议系列，但它表明了 DA 研究评估的不足。总的来说，重要的是要理解提到的缺乏证据和成功的转移为进一步的实证和理论 FM 研究产生了巨大的机会。

Strength 3. For (comparative) studies of FM effectiveness, there are several research designs and benchmark examples available from the scientific literature. In Appendix A, we assess the effort and feasibility of corresponding qualitative and quantitative studies.

Weakness 3. FMs have been suffering from fragile effectiveness and productivity in dependability engineering in general. There is a lack of convincing evidence of FM effectiveness, particularly, of ab-initio FMs. RAS engineering and assurance are likely to be affected by these weaknesses.

### 3.4 Expressivity

An often quoted weakness of MBD, particularly when applied to RASs, is the “reality gap” [Brooks 1992; Jakobi et al. 1995] that can exist between a naively constructed model and its corresponding real-world artefact. According to Brooks [1992], over-reliance on simulation to test behaviour using naive and insufficiently validated models can lead to effort being applied to solving problems that do not exist in the real world. Worse, programs for robotic controllers developed in a model-based setting may fail when executed on real-world hardware, because “it is very hard to simulate the actual dynamics of the real-world” [Brooks 1992]. This problem is not only true of simulation, but any form of model-based analysis, including reasoning in FMs [Fetzer 1988].

MBD 的一个经常被引用的弱点，特别是当应用于 RASs 时，是 “现实差距” [Brooks 1992; Jakobi 等人。1995] 可以存在于一个天真构建的模型和它对应的真实世界的人工制品之间。根据 Brooks [1992] 的说法，过度依赖模拟来测试使用天真和未经充分验证的模型的行为会导致努力应用于解决现实世界中不存在的问题。更糟糕的是，在基于模型的设置中开发的机器人控制器程序在真实硬件上执行时可能会失败,因为 “很难模拟真实世界的实际动态” [Brooks 1992]。这个问题不仅是真正的模拟，但任何形式的基于模型的分析，包括在 FMs 推理 [Fetzer 1988]。

The fundamental problem here is that it is impossible to model the behaviour of any physical entity precisely [Lee and Sirjani 2018], unless we replicate the original. Moreover, as models become more detailed, their utility decreases and they can become just as difficult to comprehend and analyse as their real-world counterparts, an observation highlighted by the famous paradox of Bonini [1962]. Nevertheless, as statistician George Box said “all models are wrong but some are useful” [Box and Draper 1986]: we must evaluate a model not upon how “correct” it is, or how much detail it contains, but on how informative it is. According to Lee and Sirjani [2018], the antidote is not to abandon the use of models, but to recognise their inherent limitations and strengths, and apply them intelligently to reasoning about a specific problem. This means selecting appropriate modelling paradigms that enable specification of behaviour at a sufficiently detailed level of abstraction, and using the resulting models to guide the engineering process.

这里的根本问题是，除非我们复制原始实体，否则不可能精确地模拟任何物理实体的行为(Lee和Sirjani, 2018年)。此外，随着模型变得更加详细，它们的效用会下降，它们可能变得与现实世界中的模型一样难以理解和分析，这是著名的博尼尼悖论(paradox of Bonini, 1962)所强调的观察结果。然而，正如统计学家乔治•博克斯(George Box)所说，“所有模型都是错误的，但有些模型是有用的”(Box和Draper 1986):我们必须评估一个模型，不是看它有多“正确”，或者它包含多少细节，而是看它提供了多少信息。Lee和Sirjani[2018]认为，解决方法不是放弃使用模型，而是认识到模型固有的局限性和优势，并将其聪明地应用于对特定问题的推理。这意味着选择适当的建模范例，使行为规范在足够详细的抽象级别上成为可能，并使用结果模型来指导工程过程。

Strength 4. FMs allow and foster the use of specific abstractions to specifically inform engineers of RAS properties critical for their assurance.

Weakness 4. The effectiveness of formal models is fragile and can be significantly reduced because of uncontrollable gaps between models and their implementations.

### 3.5 Integration and Coordination

Modelling notations usually employ a particular paradigm to abstract the behaviour of the realworld. For example, the state-based paradigm, employed by FMs like Z [Spivey 1989], B [Abrial 1996], and refinement calculus [Back and von Wright 1989; Morgan 1996], considers how the internal state of a system evolves, whilst the event-driven paradigm, employed in process calculi like CSP [Hoare 1985], CCS [Milner 1989], and π-calculus [Milner 1999], considers how behaviour may be influenced by external interactions. Consequently, individual formal methods are usually limited to considering only certain aspects or views of a system’s behaviour [Broy and Slotosch 1998; Paige 1997], which can limit their effectiveness when used in isolation. Many researchers have therefore sought to overcome this weakness by FM integration [Broy and Slotosch 1998; Clarke and Wing 1996; Galloway and Stoddart 1997b; Paige 1997].

建模符号通常采用一种特定的范式来抽象现实世界的行为。例如，FMs采用的基于状态的范式，如Z [Spivey 1989]、B [Abrial 1996]和求精演算[Back and von Wright 1989;摩根1996],考虑系统的内部状态的发展,而事件驱动的范例,采用CSP过程结石像霍尔[1985],CCS米尔纳[1989],和π-calculus米尔纳[1999],认为行为是如何受外部交互作用的影响。因此，个别的形式方法通常仅限于考虑系统行为的某些方面或观点[Broy and Slotosch 1998;，这可能限制其有效性时，在隔离使用。因此，许多研究人员试图通过FM集成来克服这一弱点[Broy和Slotosch 1998;Clarke和Wing 1996;加洛韦和斯托达特1997b;佩奇1997]。

The 1990s saw a large number of works on semantic unification and method integration [Galloway and Stoddart 1997b; Paige 1997]. Theoretical foundations were provided by Hehner, in his seminal work on semantic unification using the “programs-as-predicates” approach [Hehner 1984, 1990] and comparative semantics [Hehner and Malton 1988]. At the same time, refinement calculi were developed [Back and vonWright 1989; Morgan 1996;Morris 1987] thatwould underlie the work on linking heterogeneous notations through abstraction. Also, Woodcock and Morgan [1990] explored the integration of state- and event-based modelling using weakest preconditions, and several other works on this topic followed [Evans 1994; Galloway and Stoddart 1997a; Roscoe et al. 1994]. Hoare proposed a unified theory of programming [Hoare 1994] that links together the three semantic styles: denotational, operational, and algebraic. These developments culminated in Hoare and He’s Unifying Theories of Programming (UTP) [Hoare and He 1998], a general framework for integration of semantically heterogeneous notations by application of Hehner and Hoare’s approach [Hehner and Hoare 1983] to the formalisation of a catalogue of computational paradigms, with links between them formalised using Galois connections. This framework enables a definitive solution to the integration of states and events, along with other computational paradigms, in the Circus language family [Butterfield and Gancarski 2009; Oliveira et al. 2009; Wei et al. 2013].

20世纪90年代出现了大量关于语义统一和方法集成的著作[Galloway和Stoddart 1997b;佩奇1997]。理论基础由Hehner提供，在他的开创性工作中，语义统一使用“程序作为谓词”的方法[Hehner 1984, 1990]和比较语义学[Hehner和Malton 1988]。与此同时，精制结石被开发出来[回到和vonWright 1989年;这将是通过抽象来连接异类表示法的工作的基础。此外，Woodcock和Morgan[1990]利用最弱的前提条件探索了基于状态和事件的建模的集成，随后又有几本关于这个主题的著作[Evans, 1994;加洛韦和斯托达特1997a;Roscoe等人，1994]。Hoare提出了一种统一的编程理论[Hoare 1994]，它将三种语义风格联系在一起:外延式、运算式和代数式。这些发展最终导致霍尔和他统一理论的编程(UTP)(霍尔和他1998),一般的集成框架的语义异构符号应用Hehner和霍尔的方法[Hehner和霍尔1983]的正规化的计算范例,它们之间有联系正式使用伽罗瓦连接。这个框架为马戏团语言家族中状态和事件的集成以及其他计算范例提供了一个确定的解决方案[Butterfield和Gancarski 2009;Oliveira等，2009;Wei等人，2013]。

Another result of these developments was a number of seminal works on FM integration [Broy and Slotosch 1998; Galloway and Stoddart 1997b; Paige 1997]. Paige, inspired by work on systematic method integration [Kronlöf 1993], defined a generic “meta-method” that aimed at integration of several formal and semi-formal methods using notational translations with a common predicative semantic foundation, which builds on Hehner’s work [Hehner 1990]. Meanwhile, Galloway and Stoddart [1997b], building on their previous work [Galloway and Stoddart 1997a], likewise proposed the creation of hybrid FMs with amulti-paradigm approach. Moreover, Broy and Slotosch [1998] proposed that FMs should be integrated into the V-Model of development [Höhn and Höppner 2008] with common semantic foundations to link the various artefacts across development steps.

这些发展的另一个结果是FM整合的一些开创性的工作[Broy和Slotosch 1998;加洛韦和斯托达特1997b;佩奇1997]。Paige受系统方法集成的启发[Kronlof 1993]，定义了一种通用的“元方法”，其目的是在Hehner [Hehner 1990]的基础上，使用具有共同谓词语义基础的符号翻译将几种正式和半正式方法集成在一起。与此同时，Galloway和Stoddart [1997b]在他们之前的研究[Galloway和Stoddart 1997a]的基础上，也提出了用多范式方法创建混合FMs的设想。此外，Broy和Slotosch[1998]提出FMs应该集成到V-Model of development [Hohn和Hoppner 2008]中，使用共同的语义基础将不同的人工制品跨开发步骤连接起来。

## 8 SUMMARY

Along the lines of Hoare et al. [2009], we analysed strengths, weaknesses, opportunities, and threats to determine the potential of integrated formal methods to improve the practice of dependability assurance. Emphasising robots and autonomous systems as an area in the spotlight of dependability assurance, we express our expectations of research progress and transfer. From these expectations, we derived a research and research transfer agenda with the objective of (i) enhancing the foundations of integrated formal methods, (ii) collecting evidence on the effectiveness of integrated formal methods in practice, (iii) successfully transferring integrated formal methods into the assurance practice, with a short-term focus on robots and autonomous systems, and (iv) fostering research progress, education, and training from the results of this transfer effort.

- Charles Antony Richard Hoare, Jayadev Misra, Gary T. Leavens, and Natarajan Shankar. 2009. The verified software initiative. Comput. Surveys 41, 4 (2009), 1–8. DOI:https://doi.org/10.1145/1592434.1592439

按照Hoare等人[2009]的思路，我们分析了优势、劣势、机会和威胁，以确定集成形式方法改进可靠性保证实践的潜力。我们强调机器人和自主系统是可靠性保障的重点领域，并表达了我们对研究进展和转移的期望。从这些期望,我们得到一个研究和研究议程的目标转移(i)提高综合形式方法的基础,(ii)收集证据的有效性综合正式的方法在实践中,(3)成功地集成形式方法转移到保证实践,短期关注机器人和自治系统,及(iv)培养研究进展,教育和训练的结果转移工作。
