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

- Marie Farrell, Matt Luckcuck, and Michael Fisher. 2018. Robotics and integrated formal methods: Necessity meets opportunity. In Proceedings of the 14th International Conference on Integrated Formal Methods (iFM), Lecture Notes in Computer Science (LNCS), vol. LNCS 11023. Springer, 161–171.

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

- Charles Antony Richard Hoare, Jayadev Misra, Gary T. Leavens, and Natarajan Shankar. 2009. The verified software initiative. Comput. Surveys 41, 4 (2009), 1–8. DOI:https://doi.org/10.1145/1592434.1592439

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

- A. Avizienis, J.-C. Laprie, B. Randell, and C. Landwehr. 2004. Basic concepts and taxonomy of dependable and secure computing. IEEE Transactions on Dependable and Secure Computing 1, 1 (2004), 11–33. DOI:https://doi.org/10.1109/TDSC. 2004.2

通过可靠的系统工程，我们指的是控制系统和嵌入式软件开发中的错误避免和错误检测活动 (例如，根据 V 模型)。Avizienis 等人 [2004] 设计了一个全面的术语和对各种故障、错误和故障的评估和处理的概述。对于关键系统，此类活动预计是明确的 (e.g.,可追溯的，有记录的)，采用最佳实践 (e.g.,设计模式)，并由合理合格的人员 (e.g.,训练有素，经验丰富的工程师或程序员)。

The need for dependability often arises from the embedding of software into a cyber-physical context (i.e., an electronic execution platform, a physical process to be controlled, and other systems or human users to interact with). Dependability assurance (DA), or assurance for short, encompasses the usually cross-disciplinary task of providing evidence for an assurance case (e.g., safety, security, reliability) for a system in a specific operational context [Kelly 1999].

- Tim P. Kelly. 1999. Arguing Safety – A Systematic Approach to Safety Case Management. Ph.D. dissertation. University of York, Dept. of Computer Science.

对可靠性的需求通常来自将软件嵌入到网络物理环境中 (i。e.,电子执行平台、要控制的物理过程以及要与之交互的其他系统或人类用户)。可靠性保证 (DA)，简称保证，通常包含为保证案例提供证据的跨学科任务 (e.g.,安全性，安全性，可靠性) 对于特定操作环境中的系统 [Kelly 1999]。

By formal methods, we refer to the use of formal (i.e., mathematically precise and unambiguous) modelling languages to describe system elements, such as software, hardware, and the environment, and the subjection of models written in these languages to analysis [Jones 2003; MacKenzie 2001], the results of which are targeted at assurance [Clarke and Wing 1996; RTCA 2011]. FMs always require the use of both formal syntax and formal semantics (i.e., the mapping of syntax into a mathematical structure). Semantics that allow the verification of refinement or conformance across different FMs is said to be unifying [Hoare and He 1998; van Glabbeek 2001]. iFMs allow the coordinated application of several potentially heterogeneous FMs, supported by interrelated layers of formal semantics [Börger et al. 2008; Grieskamp et al. 2000].

- Cliff B. Jones. 2003. The early search for tractable ways of reasoning about programs. IEEE Annals of the History of Computing 25, 2 (4 2003), 26–49. DOI:https://doi.org/10.1109/mahc.2003.1203057
- Donald A. MacKenzie. 2001. Mechanizing Proof: Computing, Risk, and Trust. The MIT Press. DOI:https://doi.org/10.7551/mitpress/4529.001.0001
- Edmund M. Clarke and Jeannette M. Wing. 1996. Formal methods: State of the art and future directions. Comput. Surveys 28, 4 (1996), 626–643. DOI:https://doi.org/10.1145/242223.242257
- RTCA. 2012. Formal Methods Supplement to DO-178C and DO-278A. Technical Report DO-333. Radio Technical Commission for Aeronautics (RTCA).
- Charles Antony Richard Hoare and Jifeng He. 1998. Unifying Theories of Programming. Pearson College Div.
- Robert J. van Glabbeek. 2001. Handbook of Process Algebra. Elsevier, Chapter 1. “The Linear Time - Branching Time Spectrum I: The Semantics of Concrete, Sequential Processes”, 3–99.
- Egon Börger, Michael Butler, Jonathan P. Bowen, and Paul Boca (Eds.). 2008. Abstract State Machines, B and Z. Lecture Notes in Computer Science, vol. 5238. Springer Berlin. DOI:https://doi.org/10.1007/978-3-540-87603-8
- Wolfgang Grieskamp, Thomas Santen, and Bill Stoddart (Eds.). 2000. Proceedings of the 2nd International Conference on Integrated Formal Methods, Lecture Notes in Computer Science, (LNCS), vol. 1945. Springer Berlin. DOI:https://doi.org/10.1007/3-540-40911-4

通过形式化方法，我们指的是使用形式化 (i.e.,数学上精确和明确的) 建模语言来描述系统元素，如软件、硬件和环境，以及用这些语言编写的模型对分析的服从 [Jones 2003; macKenzie 2001],其结果针对保证 [Clarke and Wing 1996; RTCA 2011]。FMs 总是需要使用形式语法和形式语义 (即，将语法映射到数学结构中)。允许验证不同 FMs 的细化或一致性的语义据说是统一的 [Hoare 和 He 1998; van glambeek 2001]。IFMs 允许几个潜在的异构 FMs 的协调应用，由形式语义的相互关联层支持 [Börger et al. 2008; Grieskamp et al. 2000]。

FMs stand in contrast to informal methods, which employ artefacts without a formal syntax or semantics, such as natural language descriptions and requirements. In the gap between informal methods and FMs, there is also a variety of semi-formal methods, including languages like the Unified Modelling Language (UML) and the Systems Modelling Language (SysML), whose syntax and semantics have frequently been subject of formalisation in research [e.g., Breu et al. 1997; Giese and Heldal 2004; Posse and Dingel 2016; Wieringa and Dubois 1994].

- Ernesto Posse and Jürgen Dingel. 2016. An executable formal semantics for UML-RT. Software & Systems Modeling 15, 1 (2016), 179–217. DOI:https://doi.org/10.1007/s10270-014-0399-z
- Ruth Breu, Ursula Hinkel, Christoph Hofmann, Cornel Klein, Barbara Paech, Bernhard Rumpe, and Veronika Thurner. 1997. Towards a formalization of the unified modeling language. In Proceedings of ECOOP’97 – Object-Oriented Programming. Springer Berlin, 344–366. DOI:https://doi.org/10.1007/bfb0053386
- M. Giese and R. Heldal. 2004. From informal to formal specifications in UML. The Unified Modelling Language (2004), 197–211. DOI:https://doi.org/10.1007/978-3-540-30187-5_15
- Roel Wieringa and Ericq Dubois. 1994. Integrating semi-formal and formal software specification techniques. Information Systems 19, 4 (1994), 33–54. DOI:https://doi.org/10.1016/s0306-4379(98)00007-6

FMs 与非正式方法形成对比，非正式方法采用没有正式语法或语义的人工制品，如自然语言描述和要求。在非正式方法与 FMs 的鸿沟中，也有多种半正式方法，包括统一建模语言 (UML) 和系统建模语言 (SysML),其语法和语义经常成为研究中的形式化主题 [e.g.,breu 等人 1997;Giese 和 Heldal 2004; Posse 和 Dingel 2016; Wieringa 和 Dubois 1994]。

FM-based tools assist in the modelling and reasoning based on an FM. Model-based development (MBD) and model-driven engineering (MDE) served many opportunities for FM-based tools to be applied in dependable systems practice [Bicarregui et al. 2009; Woodcock et al. 2009].

- J. C. Bicarregui, John S. Fitzgerald, Peter Gorm Larsen, and Jim Woodcock. 2009. Industrial practice in formal methods: A review. In FM 2009: Formal Methods, Ana Cavalcanti and Dennis R. Dams (Eds.). Springer Berlin, Berlin, 810–813.
- Jim Woodcock, Peter Gorm Larsen, Juan Bicarregui, and John Fitzgerald. 2009. Formal methods: Practice and experience. Comput. Surveys 41, 4, Article 19 (2009), 19:1–19:36 pages. DOI:https://doi.org/10.1145/1592434.1592436

基于 FM 的工具有助于基于 FM 的建模和推理。基于模型的开发 (MBD) 和模型驱动工程 (MDE) 为基于 FM 的工具应用于可靠的系统实践提供了许多机会 [Bicarregui 等人。2009; Woodcock 等人。2009]。

We speak of applied or practical FMs to signify successful applications of FMs in a practical context, for example, to develop embedded control software deployed in a commercial product marketed by an industrial company. We consider the use of FMs in research projects still as FM research. Empirical FM research investigates practical FMs, for example, using surveys, case studies, or controlled field experiments [Goues et al. 2018]. We speak of FM transfer if FM research is transferred into practice with the aim to effectively apply and practice FMs. We consider FM transfer, as discussed below, as crucial for empirical FM research and progress of iFM research.

- C. L. Goues, C. Jaspan, I. Ozkaya, M. Shaw, and K. T. Stolee. 2018. Bridging the gap: From research to practical advice. IEEE Software 35, 5 (2018), 50–57. DOI:https://doi.org/10.1109/MS.2018.3571235

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

- Axel van Lamsweerde. 2000. Formal specification: A roadmap. In Proceedings of the Conference on the Future of Software Engineering (ICSE’00). ACM, New York, 147–159. DOI:https://doi.org/10.1145/336512.336546

Van Lamsweerde [2000] 在需求工程中观察到越来越多的 FM 成功案例。评估几个 FM 范式，他概述了弱点 (e.g.,语言的隔离，糟糕的指导) 要得到补偿，并面临有效使用 FM 的挑战，特别是它们集成到多范例规范语言中。

Aiming at the improvement of software dependability, Jackson et al. [2007]made several key observations of recent dependability practice (e.g., lack of evidence for method effectiveness) leading to a general proposal with broad implications: rigorous dependability cases with explicit claims, the support of reuse and evolution, and the selective use of FMs. Additionally, these authors provide a number of recommendations to tool vendors and organisations in education and research.

- Daniel Jackson, Lynette I. Millett, and Martyn Thomas. 2007. Software for Dependable Systems: Sufficient Evidence? National Academies Press.

针对软件可靠性的改进，Jackson 等人 [2007] 对最近的可靠性实践 (e.g.,缺乏方法有效性的证据) 导致一个具有广泛含义的一般性建议: 具有明确要求的严格可靠性案例，重用和进化的支持，以及 FMs 的选择性使用。此外，这些作者还为教育和研究领域的工具供应商和组织提供了一些建议。

Also in the mid-2000s, Hinchey et al. [2008] spotted a decline of internet software dependability in the context of an increased level of concurrency in such systems. Their observation was backed by an earlier comparative software/hardware dependability discussion by Gray and Brewer [2001]. Hinchey et al. highlighted achievements in FM automation enabling an increased use of lightweight FMs in “software engineers’ usual development environments”. Furthermore, they stressed the ability to use several FMs in a combined manner to verify distributed (embedded) systems, avoid errors, and hence stop the decline of software dependability.

- Mike Hinchey, Michael Jackson, Patrick Cousot, Byron Cook, Jonathan P. Bowen, and Tiziana Margaria. 2008. Software engineering and formal methods. Commun. ACM 51, 9 (2008), 54–59. DOI:https://doi.org/10.1145/1378727.1378742
- J. Gray and E. Brewer. 2001. Dependability in the Internet era. In Proceedings of the High Dependability Computing Consortium Conference (2001-05-07).

同样在 21世纪00年代中期中期，Hinchey 等人 [2008] 发现在这样的系统中增加的并发水平的背景下互联网软件可靠性的下降。他们的观察得到了格雷和布鲁尔早期软件/硬件可靠性比较讨论的支持 [2001]。Hinchey 等人强调了 FM 自动化方面的成就，使得轻量级 FMs 在 “软件工程师通常的开发环境” 中的使用得以增加。此外，他们强调了以组合方式使用几个 FMs 来验证分布式 (嵌入式) 系统的能力，避免错误，从而阻止软件可靠性的下降。

Hoare et al. [2009] issued a manifesto for a “Verified Software Initiative”. Based on a consensus of strengths, weaknesses, opportunities, and threats in the software engineering community, they proposed a long-term international “research program towards the construction of error-free software systems”. This initiative aims to achieve its agenda through 
- (1) new theoretical insights into software development, 
- (2) creation of novel automated FM tools, and
- (3) a collection of experiments and benchmarks. In particular, the initiative is driven by a number of “grand challenges” [Woodcock 2006]—difficult practical verification problems that can guide future research. The experiments have broad scope, and include a smart cash card [Stepney et al. 2000] (the Mondex card), a secure entry system (Tokeneer), and a cardiac pacemaker.

- Jim Woodcock. 2006. First steps in the verified software grand challenge. IEEE Computer 39, 10 (10 2006).
- S. Stepney, D. Cooper, and Jim Woodcock. 2000. An Electronic Purse: Specification, Refinement, and Proof. Technical monograph PRG-126. Oxford University Computing Laboratory.

Hoare 等人 [2009] 发布了一个 “验证软件倡议” 的宣言。基于对软件工程界的优势、劣势、机会和威胁的共识，他们提出了一个长期的国际 “无错软件系统建设研究计划”。该倡议旨在通过 (1) 对软件开发的新理论见解，(2) 创建新型自动化正式方法工具，以及 (3) 实验和基准的集合来实现其议程。特别是，该倡议是由一系列 “重大挑战” [Woodcock 2006] 驱动的-困难的实际验证问题，可以指导未来的研究。实验范围广泛，包括智能现金卡 [Stepney et al. 2000] (Mondex 卡) 、安全进入系统 (令牌) 和心脏起搏器。

Outlining an agenda for FM transfer, Jhala et al. [2012] raised the need for improved benchmarks, metrics, and infrastructure for experimental evaluation, the need for revised teaching and training curricula [Perlis 1969], and the need for research communities interested in engaging with practitioners and working on ways to scale FMs up to large systems and to increase the usability of FMs. The authors specified several applications with great opportunities for FM transfer.

- Ranjit Jhala, Rupak Majumdar, Rajeev Alur, Anupam Datta, Daniel Jackson, Shriram Krishnamurthi, John Regehr, Natarajan Shankar, and Cesare Tinelli. 2012. Formal Methods: Future Directions & Transition To Practice. Workshop Report. National Science Foundation. http://goto.ucsd.edu/rjhala/NSFWorkshop/
- Alan J. Perlis. 1969. Identifying and developing curricula in software engineering. In Proceedings of the May 14-16, 1969, Spring Joint Computer Conference (AFIPS’69 (Spring)). ACM, New York, 540–541. DOI:https://doi.org/10.1145/1476793.1476877

Jhala概述形式化转换的议程[2012]提出了需要改进的标准,指标、实验评价和基础设施,需要修订的教学和培训课程(玻璃市1969年),和需要研究社区感兴趣与从业者和致力于规模FMs大型系统,提高FMs的可用性。作者详细介绍了几种具有良好调频传输前景的应用。

Applied researchers and practitioners interviewed by Schaffer and Voas [2016] convey an optimistic picture of FM adoption in practice, highlighting the potentials to improve IT security, particularly in cyber-physical systems. Chong et al. [2016] share the view that FMs are the most promising approach towards acceptably dependable and secure systems. The challenges they list for the security domain are similar to the challengeswe perceive in RAS assurance: FM integration, sound abstraction techniques, compositional guarantees, and evidence for sustainable transfer.

- Kim Schaffer and Jeffrey Voas. 2016. What happened to formal methods for security? Computer 49, 8 (2016), 70–79. DOI:https://doi.org/10.1109/mc.2016.228
- Stephen Chong, Joshua Guttman, Anupam Datta, Andrew Myers, Benjamin Pierce, Patrick Schaumont, Tim Sherwood, and Nickolai Zeldovich. 2016. Report on the NSF Workshop on Formal Methods for Security. Technical Report. National Science Foundation.

Schaffer和Voas[2016]采访的应用研究人员和实践者对FM在实践中的应用前景表示乐观，并强调了改进IT安全的潜力，特别是在网络物理系统中。Chong等人[2016]认为，形式化方法是实现可接受的可靠和安全系统的最有前途的方法。他们列出的安全领域的挑战与我们在RAS保证中看到的挑战类似:FM集成、可靠的抽象技术、组合保证和可持续转移的证据。

With their survey of FMs for RAS verification, Luckcuck et al. [2018] identified difficulties of applying FMs in the robotics domain and summarised research results and their limitations. They conclude (i) that formalisation remains the most critical and most difficult task, (ii) that the surveyed approaches do not provide “sufficient evidence for public trust and certification”, and (iii) that iFMs would be highly desirable if the current lack of translations between the most relevant of the surveyed techniques (e.g., model checking) could be overcome. We complement their observations with a further analysis of the lack of unification of FMs and of the missing empirical evidence for the effectiveness of FMs and iFMs. Additionally, we provide a research road map.

- Matt Luckcuck, Marie Farrell, Louise Dennis, Claire Dixon, and Michael Fisher. 2018. Formal specification and verification of autonomous robotic systems: A survey. ArXiv e-prints (2018). arxiv:cs.FL/1807.00048

Luckcuck等[2018]通过对FMs进行RAS验证的调查，发现了形式化方法在机器人领域应用的困难，总结了研究结果及其局限性。他们的结论是
(i)正规化仍然是最关键和最困难的任务,
(ii)调查方法不提供“足够的证据对公众信任和认证”,和
(3),ifm将高度可取,如果当前之间缺乏翻译最相关的调查技术(例如,模型检测)可能被克服。我们通过进一步分析FMs缺乏统一性以及缺少FMs和iFMs有效性的经验证据来补充他们的观察。此外，我们还提供了一份研究路线图。

# 3 STRENGTHS AND WEAKNESSES OF FORMAL METHODS FOR ASSURANCE

Following the guidelines for strengths, weaknesses, opportunities, and threats (SWOT) analysis by Piercy and Giles [1989], we provide an overview of strengths and weaknesses of FMs, regarding
- reputation, proof culture, education, training, and use (Section 3.1), 
- transfer efforts (Section 3.2), 
- evidence of effectiveness (Section 3.3), 
- expressivity (Section 3.4), and 
- integration and coordination (Section 3.5).

- Nigel Piercy and William Giles. 1989. Making SWOT analysis work. Marketing Intelligence & Planning 7, 5/6 (1989), 5–7. DOI:https://doi.org/10.1108/eum0000000001042

根据Piercy和Giles[1989]的《优势、劣势、机会和威胁(SWOT)分析指南》，我们提供了FMs的优势和劣势的概述
- 声誉、证明文化、教育、培训和使用(第3.1节)，
- 转移努力(第3.2节)，
- 有效性的证据(第3.3条)，
- 表达性(第3.4节)，和
- 一体化和协调(第3.5节)。

### 3.1 Reputation, Proof Culture, Education, Training, and Use

In the guest editor’s introduction of the “50 Years of Software Engineering” IEEE Software special theme issue [Erdogmus et al. 2018], the question “Are formal methods essential, or even useful, or are they just an intellectual exercise that gets in the way of building real-world systems?” invited us to deliberate on this topic and summarise its highlights. Applied researchers have raised the issue of limited effectiveness and productivity of FMs, particularly in large practical systems with changing requirements [Glass 2002; Parnas 2010]. FMs are known to be difficult to apply in practice, and bad communication between theorists and practitioners sustains the issue that FMs are taught but rarely applied [Glass 2002]. In contrast, they are considered to have significant potential to cope with the toughest recent engineering problems: certifiable RAS assurance [Farrell et al. 2018].

- Hakan Erdogmus, Nenad Medvidovic, and Frances Paulisch. 2018. 50 years of software engineering. IEEE Software 35, 5 (2018), 20–24. DOI:https://doi.org/10.1109/ms.2018.3571240
- Robert L. Glass. 2002. Facts and Fallacies of Software Engineering. Pearson Education (US).
- David Lorge Parnas. 2010. Really rethinking ‘formal methods’. IEEE Computer 43, 1 (2010), 28–34. DOI:https://doi.org/10.1109/mc.2010.22
- Marie Farrell, Matt Luckcuck, and Michael Fisher. 2018. Robotics and integrated formal methods: Necessity meets opportunity. In Proceedings of the 14th International Conference on Integrated Formal Methods (iFM), Lecture Notes in Computer Science (LNCS), vol. LNCS 11023. Springer, 161–171.

在“软件工程50年”IEEE软件专题[Erdogmus et al. 2018]的嘉宾编辑介绍中，问题是“正式方法是必需的，甚至是有用的，还是它们只是妨碍构建真实世界系统的智力练习?”“邀请我们对这个主题进行讨论并总结其亮点。应用研究人员提出了FMs的有效性和生产率有限的问题，特别是在需求不断变化的大型实际系统中[Glass 2002;帕尔曼党注册2010]。众所周知，FMs很难在实践中应用，理论家和实践者之间的沟通不畅导致了FMs被教授却很少被应用的问题[Glass 2002]。相比之下，它们被认为有巨大的潜力来应对最近最棘手的工程问题:可认证的RAS保证(Farrell等，2018年)。

Studying the sociology of proof, MacKenzie [2001] identified three sources of knowledge about a system’s dependability: induction (i.e., from observation), authority (e.g., expert opinion), and deduction (i.e., inference from models) which is possibly the most powerful. Since the beginning of software engineering there has been a debate on the style of deductive reasoning about programs and on the usefulness of FMs. De Millo et al. [1979] argued that proof is a social process. Long and difficult-to-read computer-produced verification evidence cannot be subject to such a process and is not genuine proof. Dijkstra [1978] countered, albeit not as a supporter of mechanisation, to change from a personal trust-based culture of proof to the formalisation of proof steps. Fetzer [1988] doubted that verification based on a model of the program can yield any knowledge about the dependability of an implementation of that program. According to Naur [1994], it is not the degree of formalisation making a proof convincing but the way the argument is organised. MacKenzie tried to arbitrate this debate between rigorous proof in ordinary mathematics and formal mechanised proof. He suggested that proof assistants have the potential to use formal methods [Jones 2003] to the maximum benefit. Daylight [2013] concluded from a discussion with Tony Hoare that formalist and empiricist perspectives, while still causing controversies between research and practice, complement each other in a fruitful way.

- Donald A. MacKenzie. 2001. Mechanizing Proof: Computing, Risk, and Trust. The MIT Press. DOI:https://doi.org/10.7551/mitpress/4529.001.0001
- Richard A. De Millo, Richard J. Lipton, and Alan J. Perlis. 1979. Social processes and proofs of theorems and programs. Commun. ACM 22, 5 (5 1979), 271–280. DOI:https://doi.org/10.1145/359104.359106
- Edsger W. Dijkstra. 1978. On a political pamphlet from the middle ages. ACM SIGSOFT Software Engineering Notes 3, 2 (4 1978), 14–16. DOI:https://doi.org/10.1145/1005888.1005890
- James H. Fetzer. 1988. Program verification: The very idea. Commun. ACM 31, 9 (8 1988), 1048–1063. DOI:https://doi.org/10.1145/48529.48530
- Peter Naur. 1994. Proof versus formalization. BIT 34, 1 (3 1994), 148–164. DOI:https://doi.org/10.1007/bf01935023
- Cliff B. Jones. 2003. The early search for tractable ways of reasoning about programs. IEEE Annals of the History of Computing 25, 2 (4 2003), 26–49. DOI:https://doi.org/10.1109/mahc.2003.1203057
- Edgar G. Daylight. 2013. From mathematical logic to programming-language semantics: A discussion with Tony Hoare. Journal of Logic and Computation 25, 4 (2 2013), 1091–1110. DOI:https://doi.org/10.1093/logcom/exs071

MacKenzie[2001]研究了证明的社会学，确定了关于系统可靠性的三个知识来源:归纳法(即。、权威(如专家意见)和演绎(如专家意见)。这可能是最强大的。自从软件工程开始以来，关于程序演绎推理的风格和FMs的有效性一直存在争议。De Millo等人[1979]认为，证据是一个社会过程。长而难读的计算机生成的验证证据不能经过这样的过程，也不是真正的证据。Dijkstra[1978]反对，尽管不是机械化的支持者，从个人信任为基础的证明文化到证明步骤的形式化。Fetzer[1988]怀疑基于程序模型的验证能否产生关于程序实现可靠性的任何知识。根据Naur[1994]的研究，证明的说服力不是正式化的程度，而是论证的组织方式。MacKenzie试图在普通数学中的严格证明和正式的机械化证明之间进行仲裁。他认为，证据助理有潜力使用正式的方法[琼斯2003]的最大利益。Daylight[2013]通过与Tony Hoare的讨论得出结论，虽然形式主义和经验主义的观点仍然会在研究和实践之间引起争议，但是它们是相辅相成的。

Nevertheless, FMs have shown to be well suited to substantially improve modelling precision, requirements clarity, and verification confidence. FM applications in requirements engineering such as the “Software Cost Reduction” tool set [Heitmeyer et al. 1995] even carry the hypothesis of FM cost-effectiveness in its name. By the 1990s, FM researchers had already started to examine FM usefulness with the aim to respond to critical observations of practitioners [Barroca and McDermid 1992; Bowen and Hinchey 1995; Hall 1990; Knight et al. 1997; Littlewood et al. 1998]. Some of these efforts culminated in empirical studies [Pfleeger and Hatton 1997; Sobel and Clarkson 2002] suggesting high error detection effectiveness, though with some controversy also caused by employed research designs [Berry and Tichy 2003; Sobel and Clarkson 2003].

- Constance Heitmeyer, A. Bull, C. Gasarch, and B. Labaw. 1995. SCR: A toolset for specifying and analyzing requirements. In Proceedings of the 10th Annual Conference on Computer Assurance, Systems Integrity, Software Safety, and Process Security - COMPASS’95 (1995). IEEE. DOI:https://doi.org/10.1109/cmpass.1995.521891
- Leonor M. Barroca and John A. McDermid. 1992. Formal methods: Use and relevance for the development of safety-critical systems. Comp. J. 35, 6 (1992), 579-599. DOI:https://doi.org/10.1093/comjnl/35.6.579
- Jonathan Bowen and Michael G. Hinchey. 1995. Seven more myths of formal methods. IEEE Software 12, 4 (1995), 34–41. DOI:https://doi.org/10.1109/52.391826
- Anthony Hall. 1990. Seven myths of formal methods. IEEE Software 7, 5 (1990), 11–19. DOI:https://doi.org/10.1109/52.57887
- John C. Knight, Colleen L. DeJong, Matthew S. Gibble, and Luís G. Nakano. 1997. Why are formal methods not used more widely? In Proceedings of the 4th NASA Formal Methods Workshop. 1–12.
- Bev Littlewood, I. Bainbridge, and R. E. Bloomfield. 1998. The use of computers in safety-critical applications. http:// openaccess.city.ac.uk/1955/
- Shari L. Pfleeger and Les Hatton. 1997. Investigating the influence of formal methods. Computer 30, 2 (1997), 33–43. DOI:https://doi.org/10.1109/2.566148
- Ann E. K. Sobel and M. R. Clarkson. 2002. Formal methods application: An empirical tale of software development. IEEE Transactions on Software Engineering 28, 3 (2002), 308–320. DOI:https://doi.org/10.1109/32.991322
- Dan M. Berry and Walter F. Tichy. 2003. Comments on “Formal methods application: An empirical tale of software development”.
IEEE Transactions on Software Engineering 29, 6 (2003), 567–571. DOI:https://doi.org/10.1109/tse.2003.1205183
- Ann E. Kelley Sobel and M. R. Clarkson. 2003. Response to comments on ”Formal methods application: An empirical tale of software development”. IEEE Transactions on Software Engineering 29, 6 (2003), 572–575. DOI:https://doi.org/10.1109/tse.2003.1205184

尽管如此，FMs已被证明非常适合大幅度地提高建模精度、需求明确性和验证信心。FM在需求工程中的应用，如“软件成本降低”工具集[Heitmeyer et al. 1995]，甚至在其名称中包含FM成本效益的假设。到20世纪90年代，FM的研究人员已经开始检查FM的有效性，目的是响应实践者的批判性观察[Barroca和McDermid 1992;Bowen和Hinchey, 1995;大厅1990;奈特等人，1997;Littlewood等人，1998]。其中一些努力在实证研究中达到顶峰[Pfleeger和Hatton 1997;Sobel和Clarkson 2002]表明了很高的错误检测效率，尽管也有一些争议是由采用的研究设计引起的[Berry和Tichy 2003;索贝尔和克拉克森2003年。

Jones and Bonsignour [2011, Sec. 3.2, Tab. 3.2] observe that the combination of formal inspection, static analysis, and formal testing has been the best approach to defect prevention with up to 99% of accumulated defect removal efficiency. FMs can be seen as a rigorous and systematic form of this approach, though less often applied. In Appendix A, we make a brief excursion to the relationship between FMs and formal inspection and try to roughly estimate the population size of FM users.

- Capers Jones and Olivier Bonsignour. 2011. The Economics of Software Quality. Addison-Wesley Professional.

Jones和Bonsignour[2011，第3.2节，表3.2]指出，将正式的检查、静态分析和正式的测试相结合是预防缺陷的最佳方法，其累积缺陷清除效率最高可达99%。FMs可以被看作是这种方法的一种严格的和系统的形式，尽管很少被应用。在附录A中，我们简要介绍了形式化方法和形式化检查之间的关系，并尝试粗略估计形式化方法用户的总体大小。

From two larger surveys, one in the early 1990s [Austin and Parkin 1993] and another one in the late 2000s [Bicarregui et al. 2009;Woodcock et al. 2009], we obtain a more comprehensive picture of the typical advantages of FM use and barriers to FM adoption as seen by practitioners and practical FM researchers. In two recent surveys [Gleirscher and Marmsoler 2018; Gleirscher and Nyokabi 2018], we made two, not necessarily surprising but empirically supported, observations underpinning the main findings of the former studies: many practitioners view FMs as promising instruments with high potential, and would use these instruments to their maximum benefit, whether directly or through FM-based tools. However, the beneficial use of FMs is still hindered by severe obstacles (e.g., FMs are considered hard to learn, difficult to integrate in existing processes, too expensive, prone to invalid abstractions, and difficult to maintain).

- Stephen Austin and Graeme Parkin. 1993. Formal Methods: A Survey. Technical Report. National Physical Laboratory, Teddington,Middlesex, UK.
- J. C. Bicarregui, John S. Fitzgerald, Peter Gorm Larsen, and Jim Woodcock. 2009. Industrial practice in formal methods: A review. In FM 2009: Formal Methods, Ana Cavalcanti and Dennis R. Dams (Eds.). Springer Berlin, Berlin, 810–813.
- Jim Woodcock, Peter Gorm Larsen, Juan Bicarregui, and John Fitzgerald. 2009. Formal methods: Practice and experience. Comput. Surveys 41, 4, Article 19 (2009), 19:1–19:36 pages. DOI:https://doi.org/10.1145/1592434.1592436
- Mario Gleirscher and Diego Marmsoler. 2018. Formal Methods in Dependable Systems Engineering: A Survey of Professionals from Europe and North America.Working paper. Department of Computer Science, University of York. arxiv:cs.SE/1812.08815 https://eprints.whiterose.ac.uk/149642/
- Mario Gleirscher and Anne Nyokabi. 2018. System Safety Practice: An Interrogation of Practitioners about Their Activities, Challenges, and Views with a Focus on the European Region.Working paper. Department of Computer Science, University of York. arxiv:cs.SE/1812.08452 https://eprints.whiterose.ac.uk/149641/

从两个大的调查,一个在1990年代早期(1993年奥斯汀和帕金),另一个在2000年代末(Bicarregui et al . 2009;丘鹬et al . 2009],我们获得一个更全面的图片FM调频使用和障碍的典型优点收养所看到的从业者和实际调频人员。最近的两项调查[Gleirscher和Marmsoler 2018;Gleirscher和Nyokabi 2018]，我们做了两个，不一定是令人惊讶的，但有经验支持的观察，支持了之前研究的主要发现:许多从业者认为FMs是有前途的工具，具有很高的潜力，并将使用这些工具，无论是直接或通过基于FMs的工具，最大限度地获益。然而，FMs的有益使用仍然受到严重障碍的阻碍(例如，FMs被认为难于学习、难以集成到现有流程中、太昂贵、容易产生无效的抽象和难于维护)。

Strength 1. 

FMs can improve RAS modelling, the specification of RAS requirements, and the automation of RAS verification, fostering the early detection of systematic errors in RAS designs. Many assurance practitioners perceive FM usefulness as positive.

FMs可以改进RAS建模、RAS需求说明和RAS验证的自动化，促进RAS设计中系统错误的早期检测。许多保险从业人员认为FM的有用性是积极的。

Weakness 1.

FMs have shown to be difficult to learn and apply. Many assurance practitioners perceive the ease of use of FMs as negative. Moreover, research has been ineffectively communicated in FM teaching and training.

形式化方法已被证明很难学习和应用。许多保险从业人员认为形式化方法的易用性是负面的。此外，在FM教学和培训中，研究交流的效果不佳。

### 3.2 Transfer Efforts

FMs can be effective in two ways, ab-initio (i.e., before implementation) and post-facto (i.e., after implementation). The ab-initio use of FMs aims at reducing late error costs through, for example, formal prototyping, step-wise refinement, formal test-driven development, crafting module assertions prior to programming, or formal contract-based development. Once an initial formalisation (e.g., invariants) is available, it is argued for families of similar systems that, from the second or third FM application onward, the benefit of having the formalisation outperforms the cumulative effort to maintain the formalisation up to an order of magnitude [Jackson et al. 2007; Miller 1998; Miller et al. 1999]. This argument also addresses agile settings inasmuch as iterations or increments refer to similar systems. The post-facto use of FMs can occur through knowledge extraction from existing artefacts and using automated tools such as, for example, formal or model-based post-facto testing tools or post-facto use of code assertion checkers [Kästner et al. 2010; Leino 2017]. Overall, the second way of utilising FMs is known to be more compatible with everyday software practice.

- Daniel Jackson, Lynette I. Millett, and Martyn Thomas. 2007. Software for Dependable Systems: Sufficient Evidence? National Academies Press.
- Steven P. Miller. 1998. The industrial use of formal methods: Was Darwin right? In Proceedings of the 2nd IEEE Workshop on Industrial Strength Formal Specification Techniques (1998). IEEE Comput. Soc. DOI:https://doi.org/10.1109/wift.1998.766300
- Steven P. Miller, David A. Greve, Matthew M. Wilding, and Mandayam Srivas. 1999. Formal Verification of the AAMP-FV Microcode. Technical Report NASA/CR-1999-208992. NASA. https://ntrs.nasa.gov/search.jsp?R=19990018441
- Daniel Kästner, Stephan Wilhelm, Stefana Nenova, Patrick Cousot, Radhia Cousot, Jérôme Feret, Laurent Mauborgne, Antoine Miné, and Xavier Rival. 2010. Astrée: Proving the absence of runtime errors. In Proceedings of Embedded Real Time Software and Systems (ERTS2) (2010), Vol. 9.
- K. Rustan M. Leino. 2017. Accessible software verification with Dafny. IEEE Software 34, 6 (2017), 94–97. DOI:https://doi.org/10.1109/ms.2017.4121212

FMs有两种有效的方式，从头开始(也就是从头开始)。、实施前和实施后(即后,实现)。FMs的从头到尾的使用旨在减少后期的错误成本，例如，通过正式的原型设计、逐步细化、正式的测试驱动的开发、在编程之前制作模块断言，或者正式的基于契约的开发。一旦一个初始的形式化(例如，不变量)是可用的，对于类似系统的家族来说，从第二个或第三个FM应用程序开始，拥有形式化的好处超过了将形式化维持到数量级的累积努力[Jackson et al. 2007;米勒1998年;Miller等人，1999]。这个论点也涉及到敏捷设置，因为迭代或增量指的是类似的系统。FMs的事后使用可以通过从现有人工制品中提取知识和使用自动化工具(例如，正式的或基于模型的事后测试工具或事后使用代码断言检查器)来实现[Kastner et al. 2010;Leino 2017]。总的来说，使用FMs的第二种方法被认为与日常软件实践更兼容。

Achievements collected by Aichernig and Maibaum [2003], Boulanger [2012], and Gnesi and Margaria [2013] show that many researchers have been working towards successful FM transfer. Moreover, researchers experienced in particular FMs draw positive conclusions from FM applications, especially in scaling FMs through adequate tool support for continuous reasoning in agile software development [Miller et al. 2010; O’Hearn 2018]. Other researchers report about progress in theorem proving of system software of industrial size [e.g., Klein et al. 2018] and about FM-based tools for practical use [e.g., Bicarregui et al. 2009; Kästner et al. 2010; Peleska and Huang 2016]. MBD and MDE have a history of wrapping FMs into software tools to make access to formalisms easier and to help automating tedious tasks via domain-specific language (DSLs) and visual notations.

- Bernhard K. Aichernig and Tom Maibaum (Eds.). 2003. Formal Methods at the Crossroads. From Panacea to Foundational Support. Springer Berlin Heidelberg.
- Jean-Louis Boulanger. 2012. Industrial Use of Formal Methods: Formal Verification. Wiley-ISTE.
- Stefania Gnesi and Tiziana Margaria. 2013. Formal Methods for Industrial Critical Systems: A Survey of Applications. Wiley-IEEE Press.
- Steven P. Miller, Michael W. Whalen, and Darren D. Cofer. 2010. Software model checking takes off. Commun. ACM 53, 2 (2010), 58–64. DOI:https://doi.org/10.1145/1646353.1646372
- Peter W. O’Hearn. 2018. Continuous reasoning. In Proceedings of the 33rd Annual ACM/IEEE Symposium on Logic in Computer Science - LICS’18. ACM Press. DOI:https://doi.org/10.1145/3209108.3209109
- Ihor Kuz, Toby Murray, and Gernot Heiser. 2018. Formally verified software in the real world. Commun. ACM 61, 10 (2018), 68–77. DOI:https://doi.org/10.1145/3230627
- J. C. Bicarregui, John S. Fitzgerald, Peter Gorm Larsen, and Jim Woodcock. 2009. Industrial practice in formal methods: A
review. In FM 2009: Formal Methods, Ana Cavalcanti and Dennis R. Dams (Eds.). Springer Berlin, Berlin, 810–813.
- Daniel Kästner, Stephan Wilhelm, Stefana Nenova, Patrick Cousot, Radhia Cousot, Jérôme Feret, Laurent Mauborgne, Antoine Miné, and Xavier Rival. 2010. Astrée: Proving the absence of runtime errors. In Proceedings of Embedded Real Time Software and Systems (ERTS2) (2010), Vol. 9.
- Jan Peleska and Wen-Ling Huang. 2016. Industrial-strength model-based testing of safety-critical systems. In FM 2016: Formal Methods. Springer International Publishing, 3–22. DOI:https://doi.org/10.1007/978-3-319-48989-6_1

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

- M. D. Harrison, P. Masci, and J. C. Campos. 2018. Formal modelling as a component of user centred design. In Proc. Software Technologies: Applications and Foundations (STAF) (LNCS), Vol. 11176. Springer, 274–289.
- R. Oliveira, S. Dupuy-Chessa, and G. Calvary. 2015. Plasticity of user interfaces: Formal verification of consistency. In Proceedings of ACM Symposium on Engineering Interactive Computing Systems (EICS). ACM, 260–265. DOI:https://doi.org/10.1145/2774225.2775078
- B.Weyers, J. Bowen, A. Dix, and P. Palanque (Eds.). 2017. The Handbook of Formal Methods for Human-Computer Interaction. Springer. DOI:https://doi.org/10.1007/978-3-319-51838-1
- Jonathan Bowen and S. Reeves. 2017. Generating obligations, assertions and tests from UI models. In Proceedings of the ACM Symposium on Engineering Interactive Computing Systems (EICS), Vol. 1. ACM. DOI:https://doi.org/10.1145/3095807
- P. Masci, P. Curzon, M. D. Harrison, A. Ayoub, I. Lee, and H. Thimbleby. 2013. Verification of interactive software for
medical devices: PCA infusion pumps and FDA regulation as an example. In Proc. ACM Symposium on Engineering
Interactive Computing Systems (EICS). ACM, 81–90. DOI:https://doi.org/10.1145/2494603.2480302
- M. D. Harrison, L. Freitas, M. Drinnan, J. C. Campos, P. Masci, C. Maria, and M. Whitaker. 2019. Formal techniques in the safety analysis of software components of a new dialysis machine. Science of Computer Programming 175 (2 2019), 17–34. DOI:https://doi.org/10.1016/j.scico.2019.02.003

最近，在使用 FMs 来保证与人机交互 (HCI) 相关的安全要求方面有了一些发展，并且越来越多的文献 [Harrison 等人。2018; Oliveira 等人。2015; Weyers 等人。2017]，特别是与商业医疗器械认证 [Bowen 和 Reeves 2017;Masci 等人。2013]。在那里，正式的方法已被证明在促进监管接受方面是成功的，例如，美国食品和药物管理局 (FDA) 机构。Masci 等人 [2013] 将病人控制输液泵的用户界面的 FDA 监管安全要求正式化，并使用 PVS 证明助手进行验证。类似地，Bowen 和 Reeves [2017] 使用 Z 符号正式建模输液泵接口，然后使用生成的规范作为 oracle 生成测试用例。Harrison 等人 [2019] 对一种新的新生儿透析机进行了风险分析，形式化了安全要求，并使用这些要求来分析和验证源代码。这些作者的共识是，FMs 可以在部署之前大大减少安全关键 HCI 中的缺陷。这些技术通过更大的自动化越来越实际适用。此外，这些结果很可能在其他领域有潜在的应用，如 RAS，只要它们能够被转移并扩展到这种更大的复杂性。

Strength 2. There exist many transfer re-entry points from a range of insightful FM case studies in industrial and academic labs. FMs were demonstrated to be a useful basis formany static analysis and MDE tools.

Weakness 2. The number of practical (comparative) case studies using (ab-initio) FMs or iFMs, particularly on RASs, is still too low to draw useful and firm conclusions on FM effectiveness.

强度2。在工业和学术实验室中，存在着许多有见地的FM案例研究的转学再入点。FMs被证明是许多静态分析和MDE工具的有用基础。

缺点2。使用FMs或iFMs的实际(比较)案例研究(ab-initio)的数量，特别是在RASs上，仍然太少，无法得出有关FM有效性的有用和可靠的结论。

### 3.3 Evidence of Effectiveness

Whether used as ab-initio or post-facto tools, strong evidence for the efficacy of FMs in practice is still scarce [e.g., Pfleeger and Hatton 1997] and more anecdotal [e.g. Aichernig and Maibaum 2003; Boulanger 2012; Gnesi andMargaria 2013; Schaffer and Voas 2016], rarely drawn from comparative studies [e.g., Pfleeger and Hatton 1997; Sobel and Clarkson 2002], often primarily conducted in research labs [e.g., Chudnov et al. 2018; Galloway et al. 1998], or not recent enough to reflect latest achievements in verification tool research [e.g., Cataño and Huisman 2002]. We observe that a large fraction of empirical evidence for FM effectiveness can be classified as level 6 or 7 according to Goues et al. [2018, Tab. 2], that is, too weak to draw effective conclusions.

- Shari L. Pfleeger and Les Hatton. 1997. Investigating the influence of formal methods. Computer 30, 2 (1997), 33–43. DOI:https://doi.org/10.1109/2.566148

无论是用作从头开始还是事后工具，形式化方法在实践中的有效性的有力证据仍然稀缺 [e.g.,pfleeger 和 Hatton 1997] 和更多轶事 [e.g.Aicreneg 和 Maibaum 2003; Boulanger 2012; Gnesi 和 margaria 2013; Schaffer 和 Voas 2016]，很少来自比较研究 [e.g.,Pfleeger 和 Hatton 1997; Sobel 和 Clarkson 2002]，通常主要在研究实验室进行 [e.g.,chudnov 等人。2018; 加洛韦等人。1998]，或最近不足以反映验证工具研究的最新成果 [e.g.,卡塔尼奥和惠斯曼 2002]。我们观察到，根据 Goues 等人 [2018，表 1，FM 有效性的大部分经验证据可以归类为 6 级或 7 级。2]，即太弱，无法得出有效结论。

Jackson et al. [2007, p. 39] aswell as Jones and Bonsignour [2011, Sec. 4.4, p. 220], two researchers from the software engineering measurement community, support this observation. Jones and Bonsignour state that “there is very little empirical data on several topics that need to be well understood if proofs of correctness are to become useful tools for professional software development as opposed to academic experiments”. Moreover, the controversies about proof culture summarised in Section 3.1 contain little data to resolve practitioners’ doubts.

Jackson 等人 [2007，2011 页] 以及 Jones 和 Bonsignour [，Sec.4.4，p. 220]，软件工程测量社区的两位研究人员支持这一观察。Jones 和 Bonsignour 指出，“如果正确性证明要成为专业软件开发的有用工具，而不是学术实验，需要很好地理解的几个主题的经验数据很少”。此外，3.1 节中总结的关于证明文化的争议几乎没有数据来解决从业者的疑虑。

Graydon [2015] observed this lack of evidence of FM effectiveness in assurance argumentation. More generally, Rae et al. [2010] noticed insufficiently evaluated safety research. About 86% of works lack guidance to reproduce results, hence forming a barrier to the advancement of safety practice. Although their study is limited to one conference series, it indicates deficiencies in the evaluation of DA research. Overall, it is important to understand that the mentioned lack of evidence and successful transfer produces great opportunities for further empirical and theoretical FM research.

Graydon [2015] 观察到在保证论证中缺乏 FM 有效性的证据。更一般地说，Rae 等人 [2010] 注意到安全性研究评价不充分。大约 86% 的工作缺乏重现结果的指导，从而形成了安全实践前进的障碍。虽然他们的研究仅限于一个会议系列，但它表明了 DA 研究评估的不足。总的来说，重要的是要理解提到的缺乏证据和成功的转移为进一步的实证和理论 FM 研究产生了巨大的机会。

Strength 3. For (comparative) studies of FM effectiveness, there are several research designs and benchmark examples available from the scientific literature. In Appendix A, we assess the effort and feasibility of corresponding qualitative and quantitative studies.

Weakness 3. FMs have been suffering from fragile effectiveness and productivity in dependability engineering in general. There is a lack of convincing evidence of FM effectiveness, particularly, of ab-initio FMs. RAS engineering and assurance are likely to be affected by these weaknesses.

强度3。对于FM有效性的(比较)研究，可以从科学文献中找到一些研究设计和基准示例。在附录A中，我们评估了相应的定性和定量研究的努力和可行性。

缺点3。在可靠性工程中，柔性制造系统的效率和生产率普遍较低。对于FM的有效性，特别是ab-initio FMs，缺乏令人信服的证据。RAS工程和保证可能会受到这些弱点的影响。

### 3.4 Expressivity

An often quoted weakness of MBD, particularly when applied to RASs, is the “reality gap” [Brooks 1992; Jakobi et al. 1995] that can exist between a naively constructed model and its corresponding real-world artefact. According to Brooks [1992], over-reliance on simulation to test behaviour using naive and insufficiently validated models can lead to effort being applied to solving problems that do not exist in the real world. Worse, programs for robotic controllers developed in a model-based setting may fail when executed on real-world hardware, because “it is very hard to simulate the actual dynamics of the real-world” [Brooks 1992]. This problem is not only true of simulation, but any form of model-based analysis, including reasoning in FMs [Fetzer 1988].

MBD 的一个经常被引用的弱点，特别是当应用于 RASs 时，是 “现实差距” [Brooks 1992; Jakobi 等人。1995] 可以存在于一个天真构建的模型和它对应的真实世界的人工制品之间。根据 Brooks [1992] 的说法，过度依赖模拟来测试使用天真和未经充分验证的模型的行为会导致努力应用于解决现实世界中不存在的问题。更糟糕的是，在基于模型的设置中开发的机器人控制器程序在真实硬件上执行时可能会失败,因为 “很难模拟真实世界的实际动态” [Brooks 1992]。这个问题不仅是真正的模拟，但任何形式的基于模型的分析，包括在 FMs 推理 [Fetzer 1988]。

The fundamental problem here is that it is impossible to model the behaviour of any physical entity precisely [Lee and Sirjani 2018], unless we replicate the original. Moreover, as models become more detailed, their utility decreases and they can become just as difficult to comprehend and analyse as their real-world counterparts, an observation highlighted by the famous paradox of Bonini [1962]. Nevertheless, as statistician George Box said “all models are wrong but some are useful” [Box and Draper 1986]: we must evaluate a model not upon how “correct” it is, or how much detail it contains, but on how informative it is. According to Lee and Sirjani [2018], the antidote is not to abandon the use of models, but to recognise their inherent limitations and strengths, and apply them intelligently to reasoning about a specific problem. This means selecting appropriate modelling paradigms that enable specification of behaviour at a sufficiently detailed level of abstraction, and using the resulting models to guide the engineering process.

这里的根本问题是，除非我们复制原始实体，否则不可能精确地模拟任何物理实体的行为(Lee和Sirjani, 2018年)。此外，随着模型变得更加详细，它们的效用会下降，它们可能变得与现实世界中的模型一样难以理解和分析，这是著名的博尼尼悖论(paradox of Bonini, 1962)所强调的观察结果。然而，正如统计学家乔治•博克斯(George Box)所说，“所有模型都是错误的，但有些模型是有用的”(Box和Draper 1986):我们必须评估一个模型，不是看它有多“正确”，或者它包含多少细节，而是看它提供了多少信息。Lee和Sirjani[2018]认为，解决方法不是放弃使用模型，而是认识到模型固有的局限性和优势，并将其聪明地应用于对特定问题的推理。这意味着选择适当的建模范例，使行为规范在足够详细的抽象级别上成为可能，并使用结果模型来指导工程过程。

Strength 4. FMs allow and foster the use of specific abstractions to specifically inform engineers of RAS properties critical for their assurance.

Weakness 4. The effectiveness of formal models is fragile and can be significantly reduced because of uncontrollable gaps between models and their implementations.

强度4。FMs允许并促进特定抽象的使用，以明确地告知工程师RAS属性对于他们的保证至关重要。

弱点4。正式模型的有效性是脆弱的，并且由于模型和它们的实现之间的不可控的差距而显著降低。

### 3.5 Integration and Coordination

Modelling notations usually employ a particular paradigm to abstract the behaviour of the realworld. For example, the state-based paradigm, employed by FMs like Z [Spivey 1989], B [Abrial 1996], and refinement calculus [Back and von Wright 1989; Morgan 1996], considers how the internal state of a system evolves, whilst the event-driven paradigm, employed in process calculi like CSP [Hoare 1985], CCS [Milner 1989], and π-calculus [Milner 1999], considers how behaviour may be influenced by external interactions. Consequently, individual formal methods are usually limited to considering only certain aspects or views of a system’s behaviour [Broy and Slotosch 1998; Paige 1997], which can limit their effectiveness when used in isolation. Many researchers have therefore sought to overcome this weakness by FM integration [Broy and Slotosch 1998; Clarke and Wing 1996; Galloway and Stoddart 1997b; Paige 1997].

建模符号通常采用一种特定的范式来抽象现实世界的行为。例如，FMs采用的基于状态的范式，如Z [Spivey 1989]、B [Abrial 1996]和求精演算[Back and von Wright 1989;摩根1996],考虑系统的内部状态的发展,而事件驱动的范例,采用CSP过程结石像霍尔[1985],CCS米尔纳[1989],和π-calculus米尔纳[1999],认为行为是如何受外部交互作用的影响。因此，个别的形式方法通常仅限于考虑系统行为的某些方面或观点[Broy and Slotosch 1998;，这可能限制其有效性时，在隔离使用。因此，许多研究人员试图通过FM集成来克服这一弱点[Broy和Slotosch 1998;Clarke和Wing 1996;加洛韦和斯托达特1997b;佩奇1997]。

The 1990s saw a large number of works on semantic unification and method integration [Galloway and Stoddart 1997b; Paige 1997]. Theoretical foundations were provided by Hehner, in his seminal work on semantic unification using the “programs-as-predicates” approach [Hehner 1984, 1990] and comparative semantics [Hehner and Malton 1988]. At the same time, refinement calculi were developed [Back and vonWright 1989; Morgan 1996;Morris 1987] thatwould underlie the work on linking heterogeneous notations through abstraction. Also, Woodcock and Morgan [1990] explored the integration of state- and event-based modelling using weakest preconditions, and several other works on this topic followed [Evans 1994; Galloway and Stoddart 1997a; Roscoe et al. 1994]. Hoare proposed a unified theory of programming [Hoare 1994] that links together the three semantic styles: denotational, operational, and algebraic. These developments culminated in Hoare and He’s Unifying Theories of Programming (UTP) [Hoare and He 1998], a general framework for integration of semantically heterogeneous notations by application of Hehner and Hoare’s approach [Hehner and Hoare 1983] to the formalisation of a catalogue of computational paradigms, with links between them formalised using Galois connections. This framework enables a definitive solution to the integration of states and events, along with other computational paradigms, in the Circus language family [Butterfield and Gancarski 2009; Oliveira et al. 2009; Wei et al. 2013].

20世纪90年代出现了大量关于语义统一和方法集成的著作[Galloway和Stoddart 1997b;佩奇1997]。理论基础由Hehner提供，在他的开创性工作中，语义统一使用“程序作为谓词”的方法[Hehner 1984, 1990]和比较语义学[Hehner和Malton 1988]。与此同时，精制结石被开发出来[回到和vonWright 1989年;这将是通过抽象来连接异类表示法的工作的基础。此外，Woodcock和Morgan[1990]利用最弱的前提条件探索了基于状态和事件的建模的集成，随后又有几本关于这个主题的著作[Evans, 1994;加洛韦和斯托达特1997a;Roscoe等人，1994]。Hoare提出了一种统一的编程理论[Hoare 1994]，它将三种语义风格联系在一起:外延式、运算式和代数式。这些发展最终导致霍尔和他统一理论的编程(UTP)(霍尔和他1998),一般的集成框架的语义异构符号应用Hehner和霍尔的方法[Hehner和霍尔1983]的正规化的计算范例,它们之间有联系正式使用伽罗瓦连接。这个框架为马戏团语言家族中状态和事件的集成以及其他计算范例提供了一个确定的解决方案[Butterfield和Gancarski 2009;Oliveira等，2009;Wei等人，2013]。

Another result of these developments was a number of seminal works on FM integration [Broy and Slotosch 1998; Galloway and Stoddart 1997b; Paige 1997]. Paige, inspired by work on systematic method integration [Kronlöf 1993], defined a generic “meta-method” that aimed at integration of several formal and semi-formal methods using notational translations with a common predicative semantic foundation, which builds on Hehner’s work [Hehner 1990]. Meanwhile, Galloway and Stoddart [1997b], building on their previous work [Galloway and Stoddart 1997a], likewise proposed the creation of hybrid FMs with amulti-paradigm approach. Moreover, Broy and Slotosch [1998] proposed that FMs should be integrated into the V-Model of development [Höhn and Höppner 2008] with common semantic foundations to link the various artefacts across development steps.

这些发展的另一个结果是FM整合的一些开创性的工作[Broy和Slotosch 1998;加洛韦和斯托达特1997b;佩奇1997]。Paige受系统方法集成的启发[Kronlof 1993]，定义了一种通用的“元方法”，其目的是在Hehner [Hehner 1990]的基础上，使用具有共同谓词语义基础的符号翻译将几种正式和半正式方法集成在一起。与此同时，Galloway和Stoddart [1997b]在他们之前的研究[Galloway和Stoddart 1997a]的基础上，也提出了用多范式方法创建混合FMs的设想。此外，Broy和Slotosch[1998]提出FMs应该集成到V-Model of development [Hohn和Hoppner 2008]中，使用共同的语义基础将不同的人工制品跨开发步骤连接起来。

These diverse efforts eventually led to the founding of the iFM conference series in 1999 [Araki et al. 1999], with the aim of developing theoretical foundations for “combining behavioural and state-based formalisms”. For the second iteration of the iFM conference [Grieskamp et al. 2000], the scope broadened to consider all the different aspects of FM integration, including semantic integration, traceability, tool integration, and refinement. A few years later, a conference series was also established for UTP [Dunne and Stoddart 2006], with the aim of continuing to develop unifying semantics for diverse notations within the UTP framework.

这些不同的努力最终导致了1999年iFM系列会议的成立[荒木等人1999]，其目的是为“将基于行为和基于状态的形式主义相结合”奠定理论基础。对于iFM会议的第二次迭代[Grieskamp等人，2000]，范围扩大到考虑FM集成的所有不同方面，包括语义集成、可跟踪性、工具集成和细化。几年后，还为UTP [Dunne和Stoddart 2006]建立了一个会议系列，目的是继续在UTP框架内为不同的表示法开发统一的语义。

However, there is as yet no agreed and general methodology for integrating FMs that could be applied to RASs [Farrell et al. 2018]. Overall, integration is of particular pertinence to RASs, since such systems are multi-layered and possess a high degree of semantic heterogeneity. As Farrell et al. found, they “can be variously categorised as embedded, cyber-physical, real-time, hybrid, adaptive and even autonomous systems, with a typical robotic system being likely to contain all of these aspects”. When we consider RASs, we must consider advanced computational paradigms like real-time, hybrid computation with differential equations, probability, and rigid body dynamics. This implies the use of several different modelling languages and paradigms to describe the different aspects, and therefore a variety of analysis techniques to assure properties of the overall system. Assurance of autonomous systems will certainly therefore require iFMs [Farrell et al. 2018]. Figure 1 on page 3 summarises this relationship.

然而，目前还没有公认的和通用的集成FMs的方法可以应用于RASs [Farrell et al. 2018]。总体而言，由于RASs系统是多层次的，并且具有高度的语义异构性，因此集成对RASs具有特殊的针对性。正如Farrell等人所发现的，它们“可以分为嵌入式、网络物理、实时、混合、自适应甚至自主系统，典型的机器人系统可能包含所有这些方面”。当我们考虑RASs时，我们必须考虑先进的计算范式，如实时、混合计算微分方程、概率和刚体动力学。这意味着使用几种不同的建模语言和范例来描述不同的方面，从而使用各种分析技术来确保整个系统的属性。因此，自主系统的保证必然需要iFMs [Farrell等，2018]。第3页的图1总结了这种关系。

Strength 5. iFMs raise the potential of integration and coordination of several FMs to consistently reason about RAS properties implemented by a combination of various technologies such as software, electronic hardware, and mechanical hardware.

iFMs提高了几个FMs的集成和协调的潜力，从而一致地推断由软件、电子硬件和机械硬件等各种技术组合实现的RAS属性。

Weakness 5. There is currently no agreed framework for the integration of FMs that would effectively address the needs in the RAS domain or similar domains.

目前还没有商定的FMs一体化框架来有效地解决RAS领域或类似领域的需求。

## 4 OPPORTUNITIES FOR INTEGRATED FORMAL METHODS

This section continues with the environmental part of our SWOT analysis. Several key opportunities for the transfer of iFMs arise from ongoing assurance challenges, particularly in RAS assurance and from looking at what other disciplines do to cope with similar challenges. In the following, we describe opportunities stemming from 
- the desire for early removal of severe errors (Section 4.1); 
- the desire to learn from accidents and their root causes (Section 4.2); 
- the desire of assurance to form a mature discipline (Section 4.3); and 
- the desire for adequate and dependable norms (Section 4.4). 

本节继续我们的SWOT分析的环境部分。iFMs的转移的几个关键机会来自于持续的保证挑战，特别是在RAS保证中，并且来自于观察其他规程如何处理类似的挑战。在下面，我们描述的机会来自
- 希望早日消除严重错误(第4.1节);
- 希望从事故及其根源中吸取教训(第4.2节);
- 形成成熟学科的保证愿望(第4.3条);和
- 希望有适当和可靠的准则(第4.4条)。

### 4.1 The Desire for Early Removal of Severe Errors

Summarising major challenges in automotive systems engineering in 2006, Broy [2006, p. 39] indicated that modelling languages used in practice were often not formalised and the desired benefits could not be achieved from semi-formal languages. Moreover, software engineering was not well integrated with core control and mechanical engineering processes. Domain engineers would produce software/hardware sub-systems and mechanical sub-assemblies in undesirable isolation. Broy referred to a lack of iFMs for overall architecture verification.

总结2006年汽车系统工程的主要挑战，Broy [2006, p. 39]指出，在实践中使用的建模语言往往没有形式化，并且不能从半形式化语言中获得预期的好处。此外，软件工程并没有很好地与核心控制和机械工程过程相结合。领域工程师将在不希望的隔离状态下生产软件/硬件子系统和机械部件。Broy提到缺乏用于整体架构验证的iFMs。

Has the situation changed since then? In model-centric development in embedded software practice (e.g., based on UML or SysML), drawbacks can be significant if methods and tools are not well integrated or trained personnel are missing [Liebel et al. 2016]. Likely, Broy’s criticism remains in contemporary automatic vehicle engineering and assurance practice. In fact, he has a recent, clearly negative, but not pessimistic answer to this question [Broy 2018]. Moreover, this view is shared by the Autonomy Assurance International Programme’s discussion of assurance barriers,8 that is, current challenges in RAS assurance. These barriers (e.g., validation, verification, risk acceptance, simulation, human-robot interaction) could be addressed by formal engineering models and calculations based on such models to be used as evidence in assurance cases.

从那以后情况有变化吗?在嵌入式软件实践的以模型为中心的开发中(例如，基于UML或SysML)，如果方法和工具没有很好地集成或缺少经过培训的人员，缺陷可能会很严重[Liebel等人，2016]。布洛伊的批评可能还停留在当代自动车辆工程和保险实践中。事实上，对于这个问题(Broy 2018)，他最近给出了一个明显负面但并不悲观的答案。此外，自治保证国际计划对保证障碍的讨论(即RAS保证的当前挑战)也同意这一观点。这些障碍(例如，验证、验证、风险接受、仿真、人机交互)可以通过正式的工程模型和基于这些模型的计算来解决，并在保证案例中用作证据。

Model-based assurance [Habli et al. 2010; Hawkins et al. 2015] uses system models to structure assurance cases and represents another opportunity for formal methods in (through-life) assurance. Assurance arguments that are purely informal can be difficult to evaluate, and may be subject to argumentation fallacies [Greenwell et al. 2006]. Consequently, there have been a number of efforts to formalise and mechanise assurance cases, both at the argumentation level [Denney and Pai 2018; Rushby 2014] and the evidence level [Cruanes et al. 2013]. More recently, in MDE, the Structured Assurance Case Meta-model (SACM)9 is a standardised meta-model that supports both structured argumentation and integration of evidence from diverse system models [Wei et al. 2019]. SACM unifies several existing argumentation notations, and also provides support for artefact traceability and terminology. It could, in the future, serve as a crucial component for iFMs in assurance.

基于模型的保证[Habli et al. 2010;Hawkins等人[2015]使用系统模型来构建保证案例，并代表了(终身)保证中正式方法的另一个机会。纯粹非正式的保证论证可能很难评估，而且可能成为论证谬误[Greenwell et al. 2006]。因此，在论证层面(Denney和Pai 2018;Rushby 2014]和证据水平[Cruanes et al. 2013]。最近，在MDE中，结构化保证案例元模型(Structured Assurance Case Meta-model, SACM)9是一个标准化的元模型，它支持结构化论证和来自不同系统模型的证据集成[Wei et al. 2019]。SACM统一了几种现有的论证符号，还提供了对人工制品可跟踪性和术语的支持。将来，它可能成为iFMs的一个重要组成部分。

Leading voices from applied software engineering research periodically mention the role of FMs as a key technology to master upcoming challenges in assuring critical software systems [Moore et al. 2016]. A round table about the adoption of FMs in IT security [Schaffer and Voas 2016] positively evaluated their overall suitability, the combination of FMs with testing, and the achievements in FM automation. The panellists noticed limitations of FMs in short-time-tomarket projects and in detecting unknown vulnerabilities as well as shortcomings in FM training and adoption in practice.

来自应用软件工程研究的主要声音定期提到FMs作为一种关键技术的角色，以掌握在确保关键软件系统方面即将面临的挑战[Moore等人，2016]。一个关于在IT安全中采用FMs的圆桌会议[Schaffer和Voas 2016]对FMs的总体适用性、FMs与测试的结合以及FM自动化的成果进行了积极的评估。小组成员注意到FMs在短期市场项目和检测未知漏洞方面的局限性，以及FM培训和实践采用方面的缺陷。

However, even for mission-critical systems, high costs from late defect removal and long defect repair cycles [Jones and Bonsignour 2011], as well as dangerous and fatal10 incidents indicate that assurance in some areas is still driven by practices failing to assist engineers in overcoming their challenges. Moreover, Neumann, an observer of a multitude of computing risks, stated that “the needs for better safety, reliability, security, privacy, and system integrity that I highlighted 24 years ago in my book, Computer-Related Risks, are still with us in one form or another today” [Feitelson 2019; Hoffmann 2018; Neumann 1995].

然而，即使对于关键任务系统，后期的缺陷清除和较长的缺陷修复周期带来的高成本[Jones和Bonsignour 2011]，以及危险和致命的事件表明，在某些领域的保证仍然是由未能帮助工程师克服挑战的实践驱动的。此外，Neumann，一个对大量计算风险的观察者，指出“24年前我在我的书《计算机相关风险》中强调的对更好的安全性、可靠性、安全性、隐私和系统完整性的需求，今天仍然以这样或那样的形式与我们同在”[Feitelson 2019;霍夫曼2018;诺依曼1995年)。

For example, artificial intelligence software—particularly machine learning (ML) components— has been developed at a high pace and used in many non-critical applications. Recently, ML components are increasingly deployed in critical domains. For verification and error removal, such software has to be transparent and explainable. Preferring verifiable algorithms to heuristics, Parnas [2017] recalled the corresponding engineering principle: “We cannot trust a device unless we know how it works”. One way to follow this principle and establish transparency is to reverse engineer (i.e., to decode) the functionality of an ML component even if this is not possible in general [Ben-David et al. 2019]. FMs can help extract knowledge and reverse engineer abstractions of ML systems to explain their behaviour.We might then ask to which extent the reverse engineered and verified functionality serves as a substitute for the original ML component.

例如，人工智能软件——尤其是机器学习(ML)组件——已经得到了高速发展，并在许多非关键应用中得到了应用。最近，ML组件越来越多地部署在关键领域。为了验证和消除错误，这样的软件必须是透明和可解释的。Parnas[2017]更喜欢可验证的算法，而不是启发式，回顾了相应的工程原理:“我们不能相信一个设备，除非我们知道它是如何工作的”。遵循这一原则并建立透明度的一种方法是逆向工程(即逆向工程)。(用于解码)ML组件的功能，即使这在一般情况下是不可能的[Ben-David et al. 2019]。FMs可以帮助提取知识并对ML系统的抽象进行逆向工程以解释它们的行为。然后我们可能会问，反向工程和验证功能在多大程度上可以替代原始ML组件。

These anecdotes make it reasonable to question current assurance practice. Seen through the eyes of assurance, they suggest that we might again be facing a dependable software engineering crisis similar to the one from the late 1960s [Broy 2018; Randell 2018].

这些趣闻使我们有理由对当前的保险实践提出质疑。从确信的角度来看，它们表明我们可能再次面临一场可靠的软件工程危机，类似于上世纪60年代末的那场危机(Broy 2018;Randell 2018]。

Opportunity 1. 
We as method researchers could learn from this crisis and improve the way that FMs can be effectively coordinated to support early error removal in practical engineering processes. We as practitioners could learn from this crisis and improve the way that we correctly engineer and certify highly automated systems such as robots and autonomous systems.

作为方法研究人员，我们可以从这次危机中吸取教训，改进FMs的有效协调方式，以支持实际工程过程中的早期错误消除。作为实践者，我们可以从这场危机中吸取教训，改进我们正确设计和认证高度自动化系统(如机器人和自主系统)的方式。

### 4.2 The Desire to Learn From Accidents and Their Root Causes

In the title of Section 4.1, the word “severe” refers to the negative consequences potentially caused by errors we want to remove using iFMs. The more severe the potential consequences of an error, the more critical is its early removal. The usefulness of iFMs thus positively correlates with their support in the removal of critical errors. However, the estimation of severity often also requires the careful study of past field incidents [Holloway and Johnson 2008].

在第4.1节的标题中，“严重”一词指的是我们希望使用iFMs消除的错误可能造成的负面后果。一个错误的潜在后果越严重，越需要尽早排除它。因此，iFMs的有用性与它们在消除关键错误方面的支持是正相关的。然而，对严重程度的估计通常也需要仔细研究过去的现场事故[Holloway和Johnson 2008]。

We speak of field incidents to refer to significant operational events in the field (i.e., the environment in which a technical system is operated) which are undesired because of their safety risks and their severe harmful consequences. Field incidents range from minor incidents to major accidents. It is important to separate the observed effect, the field incident, from its causes or, more precisely, from the causal chains of events leading to the observed effect. Hence, this analysis depends on the considered system perimeter [e.g., Avizienis et al. 2004]. Depending on the possibilities of observation and the depth pursued in a root cause analysis (RCA), a conclusion on a possible cause can result in any combination of, for example, overall system failure, human error, adverse environmental condition, design fault, hardware fault, software fault, or specification error.

我们所说的野战事件是指在野战中发生的重大作战事件。由于其安全风险和严重的有害后果而不受欢迎的。现场事故从小到大。重要的是要把观察到的结果，即现场事件，同其原因分开，或者更确切地说，同导致观察到的结果的事件的因果链分开。因此，这种分析依赖于考虑的系统周长。， Avizienis et al. 2004]。根据观察的可能性和根本原因分析(RCA)所追求的深度，对可能原因的结论可以导致任何组合，例如，整个系统故障、人为错误、不利的环境条件、设计故障、硬件故障、软件故障或规格错误。

There are many databases about field incidents: some are comprehensive and include RCA, others are less detailed, and some are confidential, depending on the regulations in the corresponding application domain or industry sector. Based on such databases, accident research, insurance, and consumer institutions occasionally provide brief root cause statistics along with accident statistics.

关于现场事故的数据库有很多:一些是全面的，包括RCA，另一些不太详细，还有一些是机密的，这取决于相应应用领域或行业部门的法规。基于这样的数据库，事故研究、保险和消费者机构偶尔会提供简要的根本原因统计数据和事故统计数据。

Accident statistics allow certain predictions of the safety of systems and their operation, for example, whether risk has been and will be acceptably low. Such statistics are also used in estimations of the amount of field testing necessary to sufficiently reduce risk [Kalra and Paddock 2016].

事故统计允许对系统及其操作的安全性进行某些预测，例如，风险是否已经并且将会低到可以接受的程度。这些统计数据也被用于评估充分降低风险所需的实地测试数量[Kalra和Paddock 2016]。

For example, it is well acknowledged that inadequately designed and implemented user interfaces are a significant contributory factor in computer-related accidents [Koopman and Wagner 2017; Kun et al. 2016; MacKenzie 1994]. But how and how fast have we arrived at this conclusion and how can we prevent future such incidents? Without proper empirical investigation of accident causes, such statistics are of little use in decisions on measures for accident prevention [Hopkins 2004], particularly on improvements of engineering processes, methods (e.g., iFMs), and technologies (e.g., iFM tools) used to build these systems. For this, we require more in-depth, possibly formal, RCAs and statistics that relate error removal by iFMs and incident root causes. To this extent, RCA is a great opportunity for the investigation of iFM effectiveness.

例如，众所周知，设计和实现不充分的用户界面是导致计算机相关事故的一个重要因素[Koopman和Wagner 2017;Kun等人，2016;麦肯齐1994]。但是，我们是如何、以多快的速度得出这一结论的?我们如何防止今后再次发生这类事件?如果没有对事故原因进行适当的实证调查，这些统计数据在事故预防措施的决策中几乎没有用处[Hopkins 2004]，特别是在用于构建这些系统的工程过程、方法(例如，iFMs)和技术(例如，iFM工具)的改进方面。为此，我们需要更深入、更正式的RCAs和统计数据，这些数据与iFMs的错误清除和事件根源相关。从这个意义上说，RCA是研究iFM有效性的一个很好的机会。

MacKenzie [1994] reported about deficiencies of information gathering (e.g., RCA) for databases on computer-related accidents back in the early 1990s. He noted that independent, well-known, but confidential databases might reduce under-reporting and were thus believed to improve safety culture. Reporting has not much improved as observed by Jackson et al. [2007, p. 39]. To understand the current RCA situation,we studied a sample of 377 reports from open field incident databases (in aviation, automotive, rail, energy, and others) finding the following [Gleirscher and Nyokabi 2018]: 
- (1) RCAs in these reports were of poor quality, either because they were not going deep enough, economically or technically infeasible, or inaccessible to us. 
- (2) Particularly, root causes (e.g., software faults, specification errors)were rarely documented in a way that useful information about the technologies used (e.g., software) or consequences in the development process could be retrieved from the reports. 
- (3) Reports in some sectors (e.g., aerospace, rail, power plants, process industry) contain more in-depth RCAs than others (e.g., automotive) because of different regulations. 
- (4) Some sectors operate official databases (e.g., NHTSA14 and NTSB15 in the US transportation sector) and others do not (e.g., German road transportation sector). 
- (5) Our findings suggest that even in domains with regulated RCA, reports in open databases tend to be less informative than reports in closed databases. 
- (6) The reports from the automotive industry exhibited a relatively small fraction of technology-related errors (e.g., software-related errors).

MacKenzie[1994]报告了1990年代早期计算机相关事故数据库信息收集(例如RCA)的缺陷。他指出，独立、知名但保密的数据库可能会减少漏报，因此被认为可以改善安全文化。Jackson等人[2007,p. 39]观察到报告并没有太大改善。为了了解当前RCA的情况，我们研究了来自开放领域事故数据库(航空、汽车、铁路、能源和其他领域)的377份报告样本，发现了以下内容[Gleirscher和Nyokabi 2018]:
-( 1)这些报告中的rca质量较差，原因可能是它们不够深入，在经济或技术上不可行，或者我们无法获得。
- (2)特别是，根本原因(如软件故障、规范错误)很少以一种能够从报告中检索到关于开发过程中使用的技术(如软件)或结果的有用信息的方式记录下来。
- (3)由于法规不同，某些行业(如航空航天、铁路、电厂、流程工业)的报告比其他行业(如汽车行业)的报告包含更深入的rca。
- (4)有些部门经营官方数据库(如美国运输部门的NHTSA14和NTSB15)，有些部门不经营官方数据库(如德国公路运输部门)。
- (5)我们的研究结果表明，即使在RCA规范的领域，开放数据库中的报告也往往不如封闭数据库中的报告提供的信息丰富。
- (6)来自汽车行业的报告显示了相对较小的技术相关错误(例如，与软件相关的错误)。

To validate our study and to better understand the context of our findings, we interviewed eight16 safety experts [Gleirscher and Nyokabi 2018]. One finding was that, because of an unclear separation of technologies and a lack of explicit architectural knowledge, a desirable classification of root causes is sometimes infeasible. Hence, accident analysts often close their reports with a level of detail too low to draw helpful conclusions. Additionally, one expert stated that the hidden number of software-related or software-caused field incidents in dependable systems practice is likely much larger than the known number. This matches our intuition but we are missing clear evidence.

为了验证我们的研究并更好地理解我们的发现，我们采访了8位安全专家(Gleirscher和Nyokabi 2018)。一个发现是，由于技术的不清晰分离和缺乏明确的架构知识，对根本原因进行理想的分类有时是不可行的。因此，事故分析人员在结束报告时，往往过于详细，难以得出有益的结论。此外，一名专家指出，在可靠的系统实践中，与软件相关或由软件引起的现场事故的隐藏数量可能远远大于已知的数量。这符合我们的直觉，但我们缺少明确的证据。

Ladkin [2013a], a researcher involved in the further development of IEC 61508, demands regulations to mandate the use of systematic RCAs. In support of his view, we believe that rigorous in-depth RCAs based on iFMs can be helpful to gain clarity about actual root causes. Again, beyond this undesirable form of late error removal, RCA data is essential for the measurement of the effectiveness of error removal techniques, particularly iFMs.

Ladkin [2013a]是一位参与IEC 61508进一步发展的研究人员，他要求制定法规强制要求使用系统的RCAs。为了支持他的观点，我们认为，基于iFMs的严格深入的RCAs有助于弄清实际的根本原因。此外，除了这种不受欢迎的后期错误消除形式之外，RCA数据对于测量错误消除技术的有效性是必不可少的，特别是对于iFMs。

The “Toyota unintended acceleration” incident exemplifies the difficulty of drawing conclusions without using powerful RCA techniques: a first RCA concluded that floor mats and sticky throttle pedals caused a fatal car mishap. A second RCA carried out by NASA experts and based on testing and automated static analysis of the control system (i.e., software and hardware) was not conclusive. A third RCA based on code reviews—we could not find out which level of formal inspection was used—detected defects in the control software and safety architecture, demonstrated to be likely the causes of the accident [Koopman 2014].

“丰田汽车意外加速”事件表明，在不使用强大的RCA技术的情况下，很难得出结论:RCA的第一个结论是，地垫和卡住的油门踏板导致了一场致命的汽车事故。第二个RCA由NASA专家进行，基于控制系统的测试和自动静态分析。，软件和硬件)不是决定性的。第三个基于代码审查的RCA——我们无法找出使用了哪一级的正式检查——检测到控制软件和安全架构中的缺陷，这些缺陷被证明可能是事故的原因[Koopman 2014]。

Opportunity 2. 
We could invest in integrated formal methods for RCA based on standardised data recording (e.g., aircraft black boxes), especially important for RASs where human operators cannot practically perform incident response. Based on lessons from formal RCA, we could further invest in the employment of integrated formal methods in RAS assurance and certification to prevent field incidents, major product recalls, and overly lengthy root cause investigations.

我们可以投资于基于标准化数据记录(例如，飞机黑匣子)的RCA的集成正式方法，这对RASs尤其重要，因为在RASs中，人工操作员无法实际执行事件响应。根据正式RCA的经验教训，我们可以进一步投资于在RAS保证和认证中使用集成的正式方法，以防止现场事故、重大产品召回和过于冗长的根本原因调查。

### 4.3 The Desire of Assurance to Form a Mature Discipline

In his Turing Award acceptance speech in 1981, Tony Hoare reviewed type safety precautions in programming languages and concluded: “In any respectable branch of engineering, failure to observe such elementary precautions would have long been against the law” [Hoare 1981].

在1981年的图林奖获奖感言中，Tony Hoare回顾了编程语言中的类型安全预防措施，并总结道:“在任何受人尊敬的工程分支中，不遵守这些基本的预防措施将是违反法律的”[Hoare 1981]。

Inspired by this comparison, it can be helpful to look at other engineering disciplines such as civil, mechanical, or electrical engineering to identify transfer opportunities for iFMs. There, engineers use FMs in many of their critical tasks. However, nowadays these methods are often hidden behind powerful software tools usable by qualified professional engineers. Although type systems, run-time bounds checking, and other variants of assertion checking have been frequently used in dependable systems practice, the overall level of FM adoption is still comparatively low.

受到这个比较的启发，它可以帮助查看其他工程学科，如土木、机械或电气工程，以确定iFMs的转移机会。在那里，工程师们在许多关键任务中使用FMs。然而，现在这些方法常常隐藏在由合格的专业工程师使用的强大软件工具背后。尽管在可靠的系统实践中经常使用类型系统、运行时界限检查和断言检查的其他变体，但是FM的总体采用水平仍然相对较低。

For example, even in less critical mechanical engineering domains, vocationally trained engineers use computer-aided engineering, design, and manufacturing software. Whether for designing machine parts for serial production (i.e., specification) or for calculations (e.g., dimensioning, force or material flow simulations) for these parts and their assembly (i.e., for prototype verification), these engineers use tools based on canonical mathematical models.

例如，即使在不太重要的机械工程领域，受过职业培训的工程师也会使用计算机辅助工程、设计和制造软件。是否设计用于批量生产的机器零件(即，或用于计算(例如，尺寸、力或物料流动模拟)这些部件及其装配(例如，尺寸、力或物料流动模拟)。这些工程师使用基于标准数学模型的工具。

Nowadays, drawings from computer-aidedmechanical design carry at least two types of semantics: one declarative based on calculus for dimensioning (1), and one procedural for the synthesis of Computer-Numerical-Control programs for production machines processing materials to realise the drawings (2). Note that the unifying base of these two semantics is geometry, a well-studied mathematical discipline. Although higher levels of complexity demand more sophisticated analytical expertise, typically from engineers with several years of work experience, many tasks can be accomplished by less-trained engineers using the corresponding tools.

如今,从计算机辅助机械设计图纸携带至少两种语义类型:一个声明基于微积分的尺寸(1),和一个程序对计算机数控程序的合成生产机器加工材料实现图纸(2)。请注意,这两个语义的统一的基础是几何,研究数学学科。虽然更高层次的复杂性需要更复杂的分析专业知识，通常来自具有多年工作经验的工程师，但是许多任务可以由缺乏训练的工程师使用相应的工具来完成。

Whereas in computer-aided mechanical design both semantics seem to be used to a similar extent, in DA we observe that analogous semantics are rarely used even if tools are available, and less often we see (1) and (2) being consistently used. Low adoption might result from the semantics for dimensioning and production automation being usually less abstract than the semantics for verification (1) and synthesis (2) of computer programs. Accordingly, Parnas suggests a shift from correctness proof to property calculation to develop practical formal methods [Parnas 2010, p. 33].

然而，在计算机辅助机械设计中，这两种语义似乎被使用到相似的程度，在DA中，我们观察到，即使有工具可用，也很少使用类似的语义，而且很少看到(1)和(2)被一致地使用。低采用可能是由于尺寸标注和生产自动化的语义通常不如计算机程序的验证(1)和合成(2)的语义抽象。因此，Parnas建议从正确性证明转向属性计算，以开发实用的形式化方法[Parnas 2010, p. 33]。

Patterns have had a long history in many disciplines. In mechanical engineering, patterns are better known as machine elements and are particularly useful in high-reliability applications. Machine elements (and standardised forms thereof) have a stabilising impact on the outcome of an engineering project. The process of element selection and composition can take tremendous advantage not only from the reuse of proven design knowledge but also from the reuse of complex calculations (e.g., from gear transmissions, injection moulding tools, skeleton framings). Moreover, modern tools typically foster the use of element libraries and parametric design. Importantly, because the properties of such elements are in many cases well known, calculations for assemblies (i.e., compositional verification) get relatively easy. However, the higher the required precision of these calculations, the more expensive is their computation.

模式在许多学科中都有悠久的历史。在机械工程中，模式更多地被称为机器元素，在高可靠性应用程序中特别有用。机械元件(及其标准化形式)对工程项目的结果具有稳定的影响。元素选择和组合的过程不仅可以从重复使用已证实的设计知识中获得巨大的好处，而且可以从重复使用复杂的计算中获得巨大的好处(例如，从齿轮传动、注塑工具、骨架骨架)。此外，现代工具通常鼓励使用元素库和参数化设计。重要的是，因为这些元素的属性在很多情况下都是众所周知的，所以对程序集的计算(即，成分验证)变得相对容易。然而，这些计算所需的精度越高，它们的计算成本就越高。

These observations are in line with what we know from collaborations in robotics, like mechatronics, a discipline where many engineering domains have to play together well: FMs are heavily used for the analysis of robot controllers and for various kinds of simulations and tests [Lozano- Perez and Wesley 1979; Meng and Kak 1993].

这些观察结果与我们从机器人领域的合作中了解到的情况一致，比如机电一体化，这是一门许多工程领域必须很好地合作的学科:FMs被大量用于机器人控制器的分析以及各种模拟和测试[Lozano- Perez和Wesley, 1979;孟和Kak 1993]。

Digital circuit engineering is a domain where FMs such as model checking have been successfully applied decades ago. However, systematic hardware errors, such as Spectre and Meltdown, and the unavailability of temporal specifications of optimised operations (e.g., branch-prediction and speculative execution) discontinue the verifiability of recent computer architectures. This lack of verifiability of the assumptions (e.g., partitioning, information flow) about the execution platform complicates the verifiability of the software (e.g., an operating system) running on such a platform.

数字电路工程是一个领域，FMs，如模型检查已经成功地应用了几十年。然而，系统硬件错误，如Spectre和Meltdown，以及优化操作的临时规范(例如，分支预测和投机执行)的不可用，使最近计算机架构的可验证性中断。关于执行平台的假设(例如，分区、信息流)缺乏可验证性，这使得在这样的平台上运行的软件(例如，操作系统)的可验证性复杂化。

Opportunity 3. Dependability assurance has not yet successfully adopted iFMs as a vital part of their key methodologies. If FMs seem relatively well established in other disciplines, we might also be able to successfully transfer iFMs to RAS assurance and assurance in other domains. Beyond software design patterns, we could benefit from best practices in formal specification and development manifested in repositories of FM patterns. Moreover, we could aim at the further unification of established FMs to provide common formal semantics for various domains.

可靠性保证尚未成功地将iFMs作为其关键方法的重要组成部分。如果FMs在其他学科中建立得相对良好，那么我们也可以成功地将iFMs转移到RAS担保和其他领域的担保。除了软件设计模式之外，我们还可以受益于FM模式存储库中正式规范和开发中的最佳实践。此外，我们还可以进一步统一已建立的FMs，为各种领域提供通用的形式语义。

### 4.4 The Desire for Adequate and Dependable Norms

Dependable systems practice usually includes the transition from what is called the system specification to an artefact called system design. Typically, software and hardware specifications are then derived from these two artefacts before delving into detailed technology choice and development. Jackson et al. [2007, p. 48] observe that safety culture in such a framework is more important than strict standards, but adequate standards and certification regimes can establish and strengthen the safety culture desirable in dependable systems practice.

可靠的系统实践通常包括从所谓的系统规范到人为的系统设计的转变。通常，在深入研究详细的技术选择和开发之前，软件和硬件规范是从这两种人工制品中派生出来的。Jackson等人[2007,p. 48]观察到，在这种框架下的安全文化比严格的标准更重要，但是适当的标准和认证制度可以建立和加强可靠的系统实践中所需要的安全文化。

A striking finding in one of our recent discussions of dependable systems standards (e.g., IEC 61508, ISO 26262,20 DO-178C) is that normative parts for specification (i.e., requirements engineering), for specification validation (i.e., avoiding and handling requirements errors), and for hazard and risk analysis (particularly in early process stages) seem to be below the state of the art [Feitelson 2019; Gleirscher and Nyokabi 2018], despite several observations that significant portions [e.g., 44%, Health and Safety Executive 2003] of the causes of safety-critical softwarerelated incidents fall into the category of specification errors [Knight 2002, 2014].

我们最近在讨论可靠的系统标准(如IEC 61508, ISO 26262,20 DO-178C)时，一个惊人的发现是，规范的规范部分(即，用于规范验证(例如，需求工程)。，避免和处理需求错误)，以及危害和风险分析(特别是在早期过程阶段)似乎低于目前的水平[Feitelson 2019;Gleirscher和Nyokabi[2018]，尽管有几项观察表明， 44%，健康与安全执行局2003]的安全关键软件相关事件的原因属于规格错误的范畴[Knight 2002, 2014]。

If one identifies an error in the software specification, do causes of this error originate from an erroneous system specification? How are system, software, and hardware specifications (formally) related? Control software typically governs the behaviour of a whole machine. Hence, core components of a software specification might, modulo an input/output relation [Parnas and Madley 1995], very well govern the narrative of the overall system specification. Inspired by the issue of specification validity as highlighted in Jones [2003], do these standards provide guidance for checking the validity and consistency of core parts of system, software, and hardware specifications?

如果一个人识别出软件规范中的一个错误，那么这个错误的原因是源于一个错误的系统规范吗?系统、软件和硬件规范(形式上)如何相关?控制软件通常控制整个机器的行为。因此，软件规范的核心组件可能通过模块化输入/输出关系[Parnas和Madley 1995]，很好地控制了整个系统规范的叙述。受到Jones[2003]强调的规范有效性问题的启发，这些标准是否为检查系统、软件和硬件规范的核心部分的有效性和一致性提供了指导?

The literature provides plenty of evidence of undesired impacts of specification errors dating back as early as the investigations of Lutz [1993] and Endres [1975]. As reported by MacKenzie [1994], the 92% of computer-related field incidents caused by human-computer interaction also illustrate the gap between specifications and capabilities of humans to interact with automation. Despite these older figures, we are talking of one of the most critical parts of standards. Practitioners could expect to receive strong guidance from these parts. Moreover, the requirements to show conformance to these parts should not be vacuous.

文献提供了大量的证据，证明早在Lutz[1993]和Endres[1975]的研究中，就存在规格错误的不良影响。正如MacKenzie[1994]所报道的，由人机交互引起的92%的与计算机相关的现场事故也说明了人类与自动化交互的规范和能力之间的差距。尽管这些数据比较老，但我们讨论的是标准中最关键的部分之一。实践者可以期望从这些部分得到强有力的指导。此外，显示这些部分一致性的需求不应该是空洞的。

Many standards define specific sets of requirements (i.e., for error removal and fault-tolerance) depending on the level of risk a system (or any part of it) might cause. The higher this level, the more demanding these requirements. Examples of demanding requirements include safety integrity level (SIL) 3-4 (IEC 61508), automotive safety integrity level (ASIL) C-D (ISO 26262), systematic capability 3-4 (IEC 61508), design assurance level (DAL) A-B (DO-178C). Even for the highest such levels the mentioned standards only “highly recommend” but not mandate the use of FMs.

许多标准定义了特定的需求集(例如，根据系统(或系统的任何部分)可能导致的风险级别而定。这个级别越高，这些要求就越高。严格要求的例子包括安全完整性等级(SIL) 3-4 (IEC 61508)，汽车安全完整性等级(ASIL) C-D (ISO 26262)，系统能力3-4 (IEC 61508)，设计保证等级(DAL) A-B (DO-178C)。即使对于这样的最高水平，上述标准也只是“强烈推荐”，而不是强制要求使用FMs。

Guidelines for embedded software development such as MISRA:1994 [Motor Industry Research Association 1994] recommend FMs for SIL 4, although MISRA:2004 no longer includes such information and instead refers back21 to MISRA:1994. As already mentioned, ISO 26262, as the overriding standard, does not go beyond high recommendation of FMs for ASIL D. Koopman [2014] that, in the US, car manufacturers are not required to follow MISRA and that there are no other software certification requirements. This currently also applies to autonomous road vehicles.

嵌入式软件开发的指导方针，如MISRA:1994[汽车工业研究协会1994]，建议将FMs用于SIL 4，尽管MISRA:2004不再包含此类信息，而是返回到MISRA:1994。如前所述，ISO 26262作为压倒一切的标准，并没有超出ASIL D. Koopman[2014]对FMs的高推荐，在美国，汽车制造商不需要遵循MISRA，也没有其他的软件认证要求。这目前也适用于自动驾驶道路车辆。

In an interesting anecdote, Ladkin reported on his lack of success in introducing systematic hazard (and risk) analysis methodology into normative parts of this standard [Ladkin 2013a]. Moreover, he mentioned22 unsuccessful attempts to strengthen the role of FMs in IEC 61508 and on the “broken standardisation” in assurance practice. In reaction to that, he proposed the use of evidently independent peer reviews to “dampen committee-capture by big-company bully players”.

在一个有趣的轶事中，Ladkin报告了他在将系统风险(和风险)分析方法引入本标准的规范部分方面缺乏成功[Ladkin 2013a]。此外，他还提到了在iec61508中加强FMs作用的22次失败的尝试，以及在保证实践中“不规范”的做法。作为对此的回应，他提议使用明显独立的同行评审来“抑制大公司恃强凌弱者的承诺”。

Additionally, Knight [2014] observed: “There is an expectation by the community that standards will embody the best available technology and that their presentation will allow determination of conformance to be fairly straightforward. A criticism that is seldom heard is that some standards are, in fact, technically flawed and poorly presented.” He exemplifies his critique by several issues with IEC 61508 and RTCA DO-178B and suggests to make the meaning of “conformance [or compliance] with a standard” more rigorous. Particularly, he encourages to replace indirect (i.e., process-related) evidence (e.g., documentation of specification activities) in assurance cases by direct (i.e., artefact-related) evidence (e.g., unsuccessful checks for presence of certain specification faults, successful checks for absence of implementation errors).23 With the observation in software quality control that “there is little evidence that conformance to process standards guarantees good products”, Kitchenham and Pfleeger [1996] delivered a reasonable basis for Knight’s suggestions.

此外，Knight[2014]观察到:“社区期望标准将包含最好的可用技术，并且它们的表示将使一致性的确定相当简单。一个很少听到的批评是，有些标准实际上存在技术缺陷，而且没有得到很好的体现。他举例说明了他对IEC 61508和RTCA DO-178B的几个问题的批评，并建议使“与标准的一致性[或遵从性]”的含义更加严格。特别是，他鼓励取代间接(即。过程相关的)证据(例如，规范活动的文档)在保证案例中由直接的(例如，，与工件相关的)证据(例如，不成功的检查是否存在某些规格错误，成功的检查是否存在实现错误)。在软件质量控制的观察中，“几乎没有证据表明过程标准的一致性保证了好的产品”，Kitchenham和Pfleeger[1996]为Knight的建议提供了一个合理的基础。

Regarding the integration of dependability approaches and FMs, Bowen and Stavridou [1993] had stated in 1993 that they “do not know how to combine formal methods assurance with metrics collected from other techniques such as fault-tolerance”. Is this still an issue? From a practical viewpoint, standards such as, for example, IEC 61508, ISO 26262, and DO-178C, provide recommendations about techniques for the reduction of both random hardware failures (e.g., by fault-tolerance techniques) and systematic hardware and software failures (e.g., by FMs, static analysis, and testing). If iFMs can support the combined application of the recommended techniques and achieve an improvement in practice, then we should really strive to demonstrate this.

关于可靠性方法和FMs的集成，Bowen和Stavridou[1993]在1993年表示，他们“不知道如何将正式方法保证与从其他技术(如容错)收集的度量结合起来”。这还是个问题吗?从实用的角度来看，诸如IEC 61508、ISO 26262和DO-178C之类的标准提供了关于减少随机硬件故障(例如，通过容错技术)和系统硬件和软件故障(例如，通过FMs、静态分析和测试)的技术建议。如果iFMs能够支持推荐技术的联合应用并在实践中实现改进，那么我们就应该努力证明这一点。

We believe that critical fractions of strong direct evidence can be delivered through the use of FMs. In support of Feitelson’s argument [Feitelson 2019], we see a great opportunity for an assessment of how the corresponding guidelines in these standards can be extended and aligned with recent results in FM research.

我们相信，重要的直接证据可以通过使用FMs来传递。为了支持Feitelson的观点[Feitelson 2019]，我们看到了一个评估这些标准中相应的指导方针如何扩展并与FM研究的最新结果相一致的大好机会。

Opportunity 4. We as researchers and practitioners could support the creation of adequate state-of-the-art regulations with improved guidance on specification construction and validation. That way, we could foster well-certified high-risk software in a time where dangerous autonomous machines are about to get widely deployed in our society.

作为研究人员和从业人员，我们可以通过改进规范构建和验证方面的指导来支持创建足够的最新法规。通过这种方式，我们可以在危险的自动机器即将广泛应用于我们的社会之际，培育经过良好认证的高风险软件。

## 5 THREATS TO THE ADOPTION OF INTEGRATED FORMAL METHODS

This section closes the environmental part of our SWOT analysis by identifying threats to FM transfer as well as challenges that arise from alternative or competing approaches taking the opportunities mentioned in Section 4. We also outline remedies to these threats.

本节结束了我们的SWOT分析的环境部分，通过识别FM转移的威胁，以及来自替代或竞争的方法所带来的挑战，利用第4节中提到的机会。我们还概述了应对这些威胁的措施。

The development of effective iFMs and their successful transfer into practice can be impeded
by
• a lack of agreement on a sound semantic base for domain-specific and cross-domain FM
integration (Sections 5.1 and 5.2),
• missing support for widely used and established tools (Section 5.2),
• a lack of interest in practical problems on the side of FM researchers (Section 5.3),
• a lack of incentives for FM researchers to engage with current practice and for software
practitioners to engage with recent theoretical results (Section 5.3),
• a bad reputation among practitioners and applied researchers (Section 5.3),
• proofs that are faulty or do not scale (Section 5.4),
• the quest for soundness overriding the quest for usefulness (Section 5.5).
We discuss these threats and barriers in more detail in the following:

有效的综合管理措施的发展及其成功地转化为实践可能受到阻碍

通过

- 缺乏对领域特定和跨域FM集成的良好语义基础的共识(第5.1和5.2节)，
- 缺少对广泛使用和已建立的工具的支持(第5.2节)，
- FM研究者对实际问题缺乏兴趣(5.3节)，
- FM研究人员缺乏参与当前实践的动机，软件从业者缺乏参与最近理论结果的动机(第5.3节)，
- 从业者和应用研究人员之间的坏名声(第5.3节)，
- 有缺陷或不缩放的证明(第5.4节)，
- 对可靠性的追求压倒了对有用性的追求(第5.5节)。
我们将在以下详细讨论这些威胁和障碍:

### 5.1 Difficulties and Misconceptions of Unification

According to Broy [2006], the successes and failures of semi-formal languages (e.g., UML, SysML) suggest that FMs, once wrapped in FM-based tools, get exposed to the quest for a unified syntax, one main objective of the UML movement in the 1990s. Rather than a unified syntax, it is more desirable to have a unified semantics and several well-defined mappings to domain-specific syntax wherever convenient (Section 3.5). This approach is occasionally taken up by DSLs in MDE (Section 3.2). Harel and Rumpe [2004] argued that one cannot achieve proper integration of methods and notations without a unifying semantics. This argument carries over to the problem of tool integration as already discussed in Section 3.5 and revisited below. Particularly, the following challenges apply to FMs when used in MDE: 
- (1) the maintenance of a single source of information serving in the (automated) derivation of downstream artefacts (e.g., proof results, code via synthesis) [Mohagheghi et al. 2012], 
- (2) a clear mapping between the DSL presented to the engineer (using intuitive notation) and the DSL semantics serving as the basis of formal verification, 
- (3) the embedding of a lean domain-specific formalism into a common data model [Broy et al. 2010] suitable for access and manipulation by engineers through their various tools [Gleirscher et al. 2007].

根据Broy[2006]的研究，半正式语言(如UML、SysML)的成功和失败表明，FMs一旦被基于fm的工具所包裹，就会暴露出对统一语法的追求，而这正是20世纪90年代UML运动的主要目标之一。与其使用统一的语法，不如使用统一的语义和几个定义良好的映射来方便地映射到特定于域的语法(第3.5节)。在MDE中，dsl偶尔采用这种方法(第3.2节)。Harel和Rumpe[2004]认为，如果没有统一的语义，就无法实现方法和表示法的适当集成。这一观点也适用于工具集成的问题，正如在3.5节中已经讨论过并在下面重新讨论过的。特别是，以下挑战适用于FMs时，在MDE使用:
- (1)维护单一的信息来源(自动化)派生的服务下游制品(例如,结果证明,代码通过合成)[Mohagheghi et al . 2012],
- (2)一个清晰的DSL提交给工程师之间的映射(使用直观的符号)和DSL语义作为形式验证的基础上,
- (3)将精益领域特定的形式主义嵌入到通用数据模型中[Broy等人2010]，该模型适合于工程师通过各种工具进行访问和操作[Gleirscher等人2007]。

These challenges are complicated by irreducible unidirectionalities in automated transformations (e.g., model-to-code) limiting the desirable round-trip engineering [Stevens 2018] (i.e., the change between views of the same data).

这些挑战由于自动化转换(例如，模型到代码)中不可减少的单向性而变得复杂，从而限制了理想的双向工程[Stevens 2018](例如相同数据视图之间的更改)。

We discussed SACM [Wei et al. 2019] as an assurance DSL in Section 4.1. Likewise, architecture description languages (e.g., the Architecture Analysis & Design Language [Feiler et al. 2004], EASTADL [Debruyne et al. 2004]) are DSLs for overall embedded system design. DSLs can be seen as one shortcut to the still ongoing efforts of arriving at a reduced version or a variant of UML where a semantics can be defined for the whole language [e.g., Posse and Dingel 2016].

我们在第4.1节中讨论了SACM [Wei等人，2019年]作为一种保证DSL。同样，架构描述语言(例如，架构分析和设计语言[Feiler等，2004]，EASTADL [Debruyne等，2004])是用于整体嵌入式系统设计的dsl。dsl可以被看作是一种捷径，它可以帮助人们不断努力，最终得到一个简化的版本或者UML的变体，在这个版本中可以为整个语言定义一个语义。， Posse和Dingel 2016]。

At a higher level of abstraction, so-called architecture frameworks (cf. ISO 42010, e.g., the Department of Defense Architecture Framework) and artefact and traceability models [e.g., Ramesh and Jarke 2001; Whitehead 2007] have been proposed, aiming at the standardisation of specific parts of the systems and software engineering life-cycle and of the documentation and data models used there. These frameworks and models are similar to the models used in product data/life-cycle management in fields like mechanical or civil engineering.

在更高的抽象级别上，所谓的体系结构框架(如ISO 42010，国防部体系结构框架)和人工制品和可跟踪性模型(如，拉梅什和贾克2001;Whitehead 2007]已经被提出，旨在对系统和软件工程生命周期的特定部分以及其中使用的文档和数据模型进行标准化。这些框架和模型类似于在机械或土木工程等领域的产品数据/生命周期管理中使用的模型。

To our best knowledge, no cross-disciplinary semantic unification has been undertaken yet (see Section 3.5), serving as a basis for dependable systems engineering. Although many of these approaches have not been developed with the aim of formalisation and the unification of semantics, we believe that this effort has to be made when developing powerful iFMs.

据我们所知，还没有进行任何跨学科的语义统一(参见3.5节)，这是可靠的系统工程的基础。虽然这些方法中有许多都不是为了形式化和语义的统一而开发的，但是我们认为，在开发强大的iFMs时必须做出这种努力。

Threat 1. Themain threat is the lack of agreement on a sound semantic base for domain-specific and cross-domain iFMs.

主要的威胁是缺乏对领域特定和跨领域iFMs的良好语义基础的一致。

Remedy 1. To reduce this threat, FM integration and refinement-based software engineering could be better aligned with artefact models [e.g., Mendez-Fernandez et al. 2010; Whitehead 2007]. This alignmentmay foster the unification of formal semantics to strengthen traceability among the artefacts and to aid in a variety of change impact analysis tasks in the engineering process [e.g., Broy 2017; van Lamsweerde 2009].

为了减少这种威胁，FM集成和基于细化的软件工程可以更好地与人工模型相结合[例如，FM集成和基于细化的软件工程]。， Mendez-Fernandez et al. 2010;怀特黑德2007]。这种对齐可以促进形式语义的统一，以加强人工制品之间的可追溯性，并在工程过程中帮助进行各种变化影响分析任务[例如。,布罗2017;范Lamsweerde 2009]。

### 5.2 Reluctant Integration Culture and Legacy Processes

Tool integration is about the integration of engineering information technology, for example, tools for requirements specification, computer-aided software engineering, and computer-aided mechanical design. Among the wide variety of solutions to capture and track model data, the majority deals with linking or merging data models [Gleirscher et al. 2007] in one or another shallow way (e.g., software repositories, data exchange formats, product/engineering/application data or life-cycle management systems).

工具集成是关于工程信息技术的集成，例如，需求规格说明的工具、计算机辅助软件工程和计算机辅助机械设计。在捕获和跟踪模型数据的各种解决方案中，大多数以一种或另一种浅层方式(例如，软件存储库、数据交换格式、产品/工程/应用程序数据或生命周期管理系统)处理连接或合并数据模型[Gleirscher et al. 2007]。

Some tools with sustainable support are heavyweight, making it difficult to agree on lean model semantics, while others are proprietary, accompanied with interest in hiding model semantics. The surveys of Liebel et al. [2016, pp. 102,104], Mohagheghi et al. [2012, p. 104], and Akdur et al. [2018] confirm that method and model integration have not yet been solved in MBD, MDE, and dependable systems practice. Moreover, frequent proposals by researchers [e.g., Breu et al. 1997; Giese and Heldal 2004; Posse and Dingel 2016] to formalise fragments or variants of UML and SysML have not yet received wide attention by practitioners and standardisation authorities.

一些具有可持续支持的工具是重量级的，因此很难在精益模型语义上达成一致，而另一些则是私有的，并伴随着对隐藏模型语义的兴趣。Liebel等人[2016,pp. 102,104]、Mohagheghi等人[2012,p. 104]和Akdur等人[2018]的调查证实，在MBD、MDE和可靠的系统实践中，方法和模型集成尚未得到解决。此外，研究人员经常提出建议。， Breu等，1997;Giese和Heldal 2004;将UML和SysML的片段或变体正式化，还没有得到从业者和标准化权威的广泛关注。

DSL-based integrated development environments (e.g., using Xtext and Sirius) get close to what is suitable for FM-based tools. Such tools rely on a trusted representation of the formal semantics integrating the model data. For successful iFM transfer to assurance practice, tools need to be built on a lean and open central system model [e.g., Aravantinos et al. 2015; Huber et al. 1996].

基于dsl的集成开发环境(例如，使用Xtext和Sirius)非常适合基于fm的工具。这些工具依赖于集成模型数据的形式语义的可信表示。为了成功地将iFM转移到保证实践中，工具需要建立在一个精益和开放的中央系统模型上[例如。， Aravantinos et al. 2015;Huber等人，1996]。

An even greater barrier than loosely integrated tools are legacy language and modelling paradigms, an established tool and method market carried by legacy stakeholders and, possibly, a neglected continuous improvement of FM education and training.

与松散集成的工具相比，更大的障碍是遗留语言和建模范例、遗留涉众所持有的已建立的工具和方法市场，以及可能忽略的FM教育和培训的持续改进。

Threat 2. The main threat is discontinuous and disintegrated FM education, transfer, and tool development.

Remedy 2. To reduce this threat, continuous adaptation and improvement of education through teaching, of transfer through training, application, and feedback, and of tool development through regulated interface standards are necessary.

威胁2。主要的威胁是教育、迁移和工具开发的不连续和不整合。

方法2。为了减少这种威胁，有必要通过教学、通过培训、应用和反馈进行转移、通过规范的接口标准进行工具开发，从而不断适应和改进教育。

### 5.3 Reluctant Transfer Culture and Exaggerated Scepticism

Finally, the vision of introducing iFMs into assurance practice might be hindered by a lack of FM researchers able or willing to engage with industrial assurance practice, as diagnosed byWoodcock et al. [2009]. It is certainly hardwork to collect sufficient evidence for FM effectiveness in assurance practice because of intellectual property rights and other legal issues but also because of a lack of awareness among FM researchers [Woodcock et al. 2009]. However, for credible method comparison experiments, Jones and Bonsignour [2011] recommended a sample of 20 similar projects split into two groups, 10 projects without treatment (i.e., not using FMs) and 10 projects with treatment (i.e., using FMs) to establish strong evidence (i.e., evidence of level 5 or above [Goues et al. 2018]).

最后，将iFMs引入保险实践的愿景可能会受到FM研究人员缺乏能力或意愿参与工业保险实践的阻碍，正如woodcock等人[2009]所诊断的那样。由于知识产权和其他法律问题，以及FM研究人员缺乏认识，收集足够的证据来证明FM在保证实践中的有效性当然是一项艰巨的工作[Woodcock et al. 2009]。然而，为了进行可信的方法比较实验，Jones和Bonsignour[2011]推荐了20个类似的项目作为样本，并将其分为两组，其中10个项目没有经过处理(即没有经过处理)。，不使用FMs)和10个项目与治疗(即。，以确立有力的证据(即， 5级或以上的证据[Goues等，2018])。

Exaggerated scepticism on the side of practitioners and applied researchers that has piled up over the years might be the most important barrier to cross. Early failures to meet high expectations on FMs and FM transfer might have led to what can be called an “FM Winter”. However, we think crossing a few other barriers first might make it easier to cope with scepticism in the assurance community and initiate an “FM Spring”, at least in assurance practice. The recent successes with FMs for certification of commercial medical devices [Harrison et al. 2019; Masci et al. 2013], and the associated burgeoning field of FMs for HCI [Weyers et al. 2017], are examples that could help to overcome this scepticism. Moreover, there is potential for transfer of these results to RAS engineering, where the need for safety-assured HCI is also paramount [Koopman and Wagner 2017; Kun et al. 2016].

从业人员和应用研究人员多年来积累的过分怀疑态度，可能是最需要跨越的障碍。早期未能满足FMs和FM传输的高期望可能会导致所谓的“FM冬季”。然而，我们认为，首先跨越一些其他的障碍可能会使我们更容易应对来自于保证社区的怀疑，并至少在保证实践中启动一个“FM Spring”。FMs最近在商用医疗器械认证方面取得的成功[Harrison等，2019年;Masci等人2013年的研究，以及与之相关的HCI FMs新兴领域[Weyers等人2017年的研究]，都是有助于克服这种怀疑的例子。此外，这些结果有可能转移到RAS工程，在那里对安全保证的HCI的需求也是最重要的[Koopman和Wagner 2017;Kun等人，2016]。

Threat 3. The main threat is the reluctance of FM researchers to regularly engage in transfer efforts combined with an exaggerated scepticism of practitioners and with other mechanisms (e.g. lack of funding, poorly focused research evaluation, intellectual property rights) preventing both sides from engaging in an effective bidirectional transfer.

Remedy 3. To reduce this threat, building awareness among researchers as well as stronger incentives (e.g., regulation) to provide continuous transfer funding for FM research (e.g., not relying on short-term projects like Ph.D theses) is needed. A good start on the academic side could then be a specific standardised repository of FM case studies.

威胁3。主要的威胁是，FM研究人员不愿定期参与转移努力，加上对从业者的过分怀疑，以及其他机制(例如缺乏资金、缺乏重点的研究评估、知识产权)阻止双方进行有效的双向转移。

补救3。为了减少这种威胁，需要在研究人员中建立意识，并采取更有力的激励措施(例如，监管)，为FM研究提供持续的转移资金(例如，不依赖于博士论文等短期项目)。学术方面的一个良好开端可以是FM案例研究的一个特定的标准化存储库。

### 5.4 Too Many Errors in Proofs and Failure to Scale

From the perspective of measurement, Jones and Bonsignour [2011, Sec. 4.1] stated that “proofs of correctness sound useful, but [i] errors in the proofs themselves seem to be common failings not covered by the literature. Further, large applications may have thousands of provable algorithms, and [ii] the time required to prove them all might take many years”. For [i], the authors opposed 7% of erroneous bug repairs to up to 100% of erroneous proofs, though stating that the latter was based on an anecdote and there had been little data around. Jones and Bonsignour elaborated an example for [ii]: Assuming one provable algorithm per 5 function points24 and on average 4 proofs per day, Microsoft Windows 7 (160,000 function points) would have about 32,000 provable algorithms, taking a qualified software engineer about 36 calendar years. They highlighted that typically only around 5% of the personnel are trained to do this work, assuming that algorithms and requirements are stable during proof time.

Jones和Bonsignour[2011，第4.1节]从度量的角度指出，“正确性的证明听起来很有用，但是[i]证明本身的错误似乎是文献中没有涵盖的常见错误。此外，大型应用程序可能有数千种可证明的算法，[ii]证明所有这些算法所需的时间可能需要多年”。对于[i]，作者反对7%的错误错误修复到100%的错误证明，尽管后者是基于一个奇闻轶事，几乎没有数据。Jones和Bonsignour为[ii]详细阐述了一个示例:假设每5个功能点有一个可证明算法，平均每天有4个证明，那么Microsoft Windows 7(16万个功能点)将有大约32,000个可证明算法，而一个合格的软件工程师大约需要36个日历年。他们强调，假设算法和需求在证明期间是稳定的，通常只有5%的人员接受过这方面的培训。

Jones and Bonsignour’s argument is based on the view of practical program verification as a largely manual activity. However, research in reasoning about programs has devised promising approaches to proof automation (e.g., bounded model checkers, interactive proof assistants). Assessing such approaches, MacKenzie [2001] compared rigorous but manual formal proof with mechanised or automated proof. He could not find a case where mechanised proof threw doubt upon an established mathematical theorem. MacKenzie observed that this underpins the robustness argument of De Millo et al. [1979] for proofs as manual social processes rather than lengthy mechanical derivations (cf. Section 3.3). However, opposing De Millo et al.’s view, Jones [2003, p. 38] concluded that structuring of knowledge about computing ideas is the actual issue, and not the avoidance of mechanisation. Furthermore, Jones [2003, p. 39] mitigated Fetzer’s issue of the reality gap in program verification [Fetzer 1988] by referring to researchers’ continuous efforts in verifying hardware, programming languages, and compilers in addition to individual programs. Finally, in support of Jones and Bonsignour’s concerns, Jones [2003, p. 38] highlighted the validity of specifications, manual and automated proofs rely on, as a serious issue indeed having received too little attention from researchers (cf. Section 4.4).

Jones和Bonsignour的论点基于这样一种观点，即实际的程序验证主要是手工活动。然而，关于程序推理的研究已经为证明自动化设计出了有前途的方法(例如，有界模型检验器、交互式证明助手)。在评估这些方法时，MacKenzie[2001]将严格但手工的形式证明与机械化或自动化的证明进行了比较。他找不到一个机械证明使人们对已建立的数学定理产生怀疑的例子。MacKenzie观察到，这支持了De Millo等人[1979]的稳健性论证，即证明是手工的社会过程，而不是冗长的机械推导(参见第3.3节)。然而，反对De Millo等人的观点，Jones [2003, p. 38]得出结论，关于计算思想的知识结构是实际问题，而不是避免机械化。此外，Jones [2003, p. 39]通过参考研究人员在验证除个别程序之外的硬件、编程语言和编译器方面的持续努力，减轻了Fetzer在程序验证方面的现实差距问题[Fetzer 1988]。最后，为了支持Jones和Bonsignour的观点，Jones [2003, p. 38]强调了规范、手动和自动证明所依赖的有效性，这确实是一个严重的问题，但却很少得到研究人员的关注(参见第4.4节)。

Threat 4. The main threat is the lack of qualified personnel to cope with the required amount and type of proof.

Remedy 4. We believe one angle of attacking this threat is the use of proof assistants wellintegrated with common environments for requirements specification and software development combined with continuous research and education in the corresponding methods and tools.

威胁4。主要的威胁是缺乏合格的人员来处理所需的证据数量和类型。

补救措施4。我们认为，攻击这种威胁的一个角度是使用proof assistant，它与需求规范和软件开发的常见环境很好地集成在一起，并结合对相应方法和工具的不断研究和教育。

### 5.5 Failure to Derive Useful Tools

Being loosely related to erroneous proofs, the information overload through false-positive findings of errors is a well-known problem in static program analysis. Semi-formal pattern checkers,25 such as PMD and FindBugs, are exposed to this threat [Gleirscher et al. 2014]. Additionally, FM-based verification tools, such as Terminator and ESC/Java [Flanagan et al. 2002], can be unable to correctly report all potential problems, because they are bounded and therefore unsound. While such tools can be helpful, confronting developers with many irrelevant findings (i.e., false positives) or a high risk of critical misses (i.e., false negatives) can lead to decreased use of FM-based tools.

由于与错误证明的松散关系，通过错误的假阳性结果导致的信息过载是静态程序分析中一个众所周知的问题。半正式的模式检查器，如PMD和FindBugs等25个，面临这种威胁[Gleirscher et al. 2014]。此外，基于fm的验证工具，如Terminator和ESC/Java [Flanagan et al. 2002]，可能无法正确报告所有潜在问题，因为它们是有界的，因此是不可靠的。虽然这些工具可能很有帮助，但是让开发人员面对许多不相关的发现(例如，，误报)或严重漏报的高风险(即(如假阴性)会导致减少使用基于模糊语言的工具。

igure 2 relates the two information retrieval metrics, precision and recall, with two adequacy criteria of proof calculi, soundness and completeness. Precision denotes the ratio of actual (solid circle) to correct (true positive findings in the hashed area) findings, while recall measures the ratio of correct to all theoretically correct (dotted circle) findings. The ideal value for both metrics is 1, meaning that there are only true negatives and true positives. Completeness, although unachievable for richer theories, would correspond to recall and soundness would correspond to a precision of 1.

图2将两个信息检索指标precision和recall与两个证明算法的充分性标准:健全性和完全性联系起来。Precision表示实际(实心圆)结果与正确(散列区域内的真实阳性结果)结果之比，recall表示正确与所有理论正确(虚线圆)结果之比。这两个指标的理想值都是1，这意味着只有真阴性和真阳性。完整度，尽管对于更丰富的理论来说是无法达到的，但它将对应于回忆，而可靠性将对应于1的精度。

On the one hand, the usefulness of the calculi underlying FMs is directly proportional only to their completeness and (traditionally) expires with a precision of less than 1. In other words, we usually try to avoid calculi that allow the derivation of false theorems. On the other hand, semiformal pattern checkers have shown to have a wide, sometimes unacceptable, range of precision and recall of their findings. The usefulness of practical FM-based tools might lie somewhere in the middle between classical calculi and bug finding tools with poor precision/recall values.

一方面，FMs下的算法的有效性只与其完整性成正比，并且(传统上)失效的精度小于1。换句话说，我们通常尽量避免使用可以推导出错误定理的计算器。另一方面，半正式模式检查器显示出了广泛的，有时是不可接受的，范围的精度和回忆他们的发现。实用的基于fm的工具的有用性可能介于经典的微积分和精度/回忆值较差的bug查找工具之间。

Threat 5. Themain threat is the struggle of academia and tool vendors to provide adequate tools, suited to adapt to novel scientific insights, to be integrated with other tools, and to be maintained in a flexible and independent manner.

Remedy 5. To reduce this threat, an improvement of education and strong incentives can play an important role here (cf. Sections 5.2 and 5.3).

威胁5。主要的威胁是学术界和工具供应商努力提供适当的工具，以适应新的科学见解，与其他工具集成，并以灵活和独立的方式进行维护。

补救措施5。为了减少这种威胁，教育的改善和强有力的奖励措施可以在这方面发挥重要作用(参看第5.2和5.3节)。

## 6 A VISION OF INTEGRATED FORMAL METHODS FOR ASSURANCE

The following discussion applies to many domains of dependability assurance. However, the complexity of robots and autonomous systems forms a key opportunity for the progress of iFM research and for its successful transfer. Accordingly, Table 1 summarises the discussion in Section 3 to 5 with an interpretation into RAS assurance practice. Based on the strengths and opportunities described in Sections 3 and 4, we formulate our vision in terms of working hypotheses:

下面的讨论适用于可靠性保证的许多领域。然而，机器人和自主系统的复杂性为iFM研究的进展及其成功转移提供了关键的机遇。因此，表1总结了第3至5节的讨论，并对RAS保证实践进行了解释。基于第3节和第4节所描述的优势和机会，我们根据工作假设来制定我们的愿景:

- (H1) From Section 4.1: Software tools for the construction of arguments and production of evidence using iFMs can meet the challenge of assuring RAS safe. Computer-assisted assurance cases supported by heterogeneous formal models will increase confidence in their sufficiency, and also aid in maintenance and evolution through modularisation of arguments and evidence.
- (H2) From Sections 4.1 and 4.3: iFMs, in particular modern verification tools, will enable automation of the evidence gathering process, and highlight potential problems when an assurance case changes or when an incident occurs.
- (H3) From Sections 4.1 and 4.3: There is no stable path to assured autonomy without the use of iFMs. Acceptable safety will be much more likely achieved with iFMs than without them.
- (H4) From Section 3.5: The success of iFMs depends on the ability to integrate a variety of FMs for different aspects of RAS (e.g., HCI, safety-security interaction, missing human fallback, environment/world modelling, uncertain prediction/behaviour), which is not currently possible. 
- (H5) From Sections 3.4 and 3.5: Sophisticated techniques for model integration and synchronisation are necessary to support MDE with iFMs. This way, iFMs will make it easier to express consistent RAS models covering all relevant aspects, make their modelling assumptions explicit, and improve future assurance practices.
- (H6) From Sections 3.1 to 3.3 and 5.3: iFMs can be beneficial in the short term. However, an important engineering principle is to be conservative [Perlis 1969] and, therefore, not to change procedures unless there is compelling evidence that iFMs are effective. Such evidence can be delivered through empirical research [e.g., Jeffery et al. 2015; Pfleeger and Hatton 1997; Sobel and Clarkson 2002; Woodcock et al. 2009] and collaboration of academia and industry. That evidence is required to re-evaluate research and foster research progress and transfer. 
- (H7) From Section 4.2: The demonstration of cost effectiveness in addition to technical effectiveness of new iFMs is necessary to justify further research. 
- (H8) From Section 4.4: Norms are a lever of public interest in dependability [Feitelson 2019]. Current norms seem to deviate from the state of the art and may fail to guarantee product certification procedures that satisfy the public interest.

- (H1)第4.1节:使用iFMs构建论点和产生证据的软件工具可以满足保证RAS安全的挑战。由异构形式模型支持的计算机辅助保证案例将增加对其充分性的信心，并通过参数和证据的模块化来帮助维护和改进。

- (H2)从第4.1和4.3节:iFMs，特别是现代的验证工具，将启用证据收集过程的自动化，并突出潜在的问题时，保证案件的变化或事故发生。

- (H3)从第4.1和4.3节:没有使用iFMs就没有稳定的路径来确保自治。使用iFMs比不使用它们更有可能达到可接受的安全性。

- (H4)来自第3.5节:iFMs的成功依赖于为RAS的不同方面(例如，HCI、安全-安全交互、缺失的人类备份、环境/世界建模、不确定的预测/行为)集成各种FMs的能力，这在目前是不可能的。

- (H5)来自第3.4和3.5节:模型集成和同步的复杂技术是支持MDE与iFMs的必要条件。通过这种方式，iFMs将使表达覆盖所有相关方面的一致的RAS模型变得更容易，使它们的建模假设更明确，并改进未来的保证实践。

- (H6)从第3.1至3.3和5.3节:iFMs在短期内是有益的。然而，一个重要的工程原则是保守[Perlis 1969]，因此，除非有令人信服的证据表明iFMs是有效的，否则不改变程序。这些证据可以通过实证研究来提供。， Jeffery et al. 2015;Pfleeger和Hatton 1997;2002年;Woodcock等人2009]和学术界与产业界的合作。需要这些证据来重新评估研究并促进研究进展和转移。

- (H7)第4.2节:除了新的综合管理服务系统的技术效率外，还必须证明其成本效益，以证明进一步研究的必要性。

- (H8)来自第4.4条:规范是可靠性的公共利益杠杆[Feitelson 2019]。目前的标准似乎偏离了目前的技术水平，可能无法保证满足公众利益的产品认证程序。

Figure 3 assigns these hypotheses to the relationships between foundational and transferdirected iFM research by example of the RAS domain. Overall, we believe that iFMs have great potential and can improve assurance but practitioners do not use them accordingly.

图3以RAS域为例，将这些假设分配给基础研究和转移导向iFM研究之间的关系。总的来说，我们相信iFMs有很大的潜力，可以改善保险，但是从业者没有相应地使用它们。

Opportunity 5. We could take and enhance credible measures to convince assurance practitioners of our results and effectively transfer these results. For this to happen, we have to answer further research questions.

机遇5。我们可以采取和提高可靠的措施有效地说服保证从业者的我们的结果和转移这些结果。要做到这一点，我们必须回答进一步的研究问题。

## 7 EMPIRICAL, APPLIED, AND FOUNDATIONAL RESEARCH

Based on the aforementioned working hypotheses, we state several objectives for foundational and transfer-directed iFM research, formulate research questions, and show our expectations on desirable outcomes of such research.

基于上述工作假设，我们为基础研究和转移导向的iFM研究提出了几个目标，提出了研究问题，并展示了我们对此类研究理想结果的期望。

### 7.1 Research Objectives and Tasks

To validate and transfer our research results, we need to
• evaluate how assurance case construction and management for certifiable RASs can be improved
by iFMs [Gleirscher et al. 2019].
• debunk or justify arguments against the use of FMs or FM-based tools in RAS assurance.
• foster a culture of successful FM research transfer to industries performing RAS assurance.

Taking an iFM foundational point of view, we need
• foundational research on the integration and unification of FMs to tackle the complexity of
RASs [Farrell et al. 2018].
• a unified semantic foundation for the plethora of notations in RAS assurance, to enable
method and tool integration. There are a number of promising research directions still being
investigated [Hoare and He 1998; Rosu and Serbanuta 2010].

Taking an evidence-based point of view, as already highlighted in 1993 by Bowen and Stavridou
[1993], we need to
• understand the difference between the state of assurance practice and assurance research.
• understand in which ways current RAS assurance practices fail, and suggest effective approaches
from assurance research. In this way, we can be sure that assurance practice is
equipped with state-of-the-art assurance technology for defence against potential liability
claims, and that assurance practitioners do not fail in fulfilling their obligations.
• understand how results from assurance research can be validated to be sure that research
follows promising directions with high potential of success in assurance practice.

Based on that, we need to
• set concrete directions for empirical FM research in RAS assurance.
• train FM researchers in applying empirical research designs in their work on rigorous assurance
cases. Woodcock et al. [2009] corroborated this objective by saying that “formal
methods champions need to be aware of the need to measure costs”.
• avoid biases as found in various branches of scientific research. For example, in the social
and biomedical sciences, researchers identified such biases through meta-analyses and
suggested measures for bias avoidance [e.g., Fanelli et al. 2017; Franco et al. 2014].
• increase the level of evidence of FM research to level 2 according to the hierarchy in Goues
et al. [2018, Tab. 2].
• avoid knowledge gaps about whether (a) RAS practice is keeping up with the state of the
assurance art, and (b) whether recent academic or industrial research is going in the right
direction. In this way, we can be sure that we do our best to inform and serve the society.

Using appropriate research designs, we need to
• invite the RAS industry to enhance their efforts in engaging with recent iFM research.
• foster goal-oriented interaction (a) between assurance practitioners and researchers and
(b) between FM researchers and assurance researchers. In this way, we can be sure to do
everything to keep researchers up to date with respect to practical demands.
• join the FM research and applied assurance research communities (Figure 1 on page 3), both
vital for the progress and transfer of assurance research into RAS assurance practice. This
way,we can be sure to foster necessary knowledge transfer between these two communities.
• summarise achievements in practical applications of iFMs for constructing assurance cases.
• suggest improvements of curricula for RAS assurance.
• guide the RAS industry in process improvement, training, and tool support.
• guide vendors of FM-based assurance tools to assess and improve their tools and services.

### 7.2 Some Research Questions Addressing These Objectives

The research questions below are relevant for FMs in general. We consider these questions as
crucial to be answered for RAS assurance to address the aforementioned objectives:

(Q1) What is the true extent of computer-related accidents up to 2019 [MacKenzie 1994]?
What would these figures mean for the RAS domain?
(Q2) Does the use of formalism detect severe errors to a larger extent than without the use
of formalism [Pfleeger and Hatton 1997; Sobel and Clarkson 2002]?
(Q3) Does the use of formalism detect severe errors earlier than without using formalism?
(Q4) Why would such errors be a compelling argument for the use of FMs?
(Q5) Apart from error avoidance and removal, which other benefits of iFMs in practice are
evident and can be utilised for method tradeoffs?
(Q6) Which criteria play a central role in measuring iFM effectiveness? Particularly, how
can FM-based tools be used at scale?
(Q7) Howwould Commercial-off-the-Shelf and System-Element-out-of-Context verification
by iFMs pay off?
(Q8) Which hurdles need to be overcome to use iFMs in practice to the maximum benefit?
(Q9) How do we know when these hurdles are actually overcome?
(Q10) How can FMs (from different disciplines) be used together (iFMs, unification)?
(Q11) How can FMs be used to assure systems (e.g., computer vision in road vehicles) involving
artificial intelligence (AI) techniques like machine learning and deep neural
networks?
(Q12) How can FMs be integrated into assurance cases to support certification against international
safety and security standards?
(Q13) How can we best combine formal and informal methods? For example, how can we
deal with the issue of the validity of specifications that proofs rely on Jones [2003].
(Q14) How can we best present formal requirements, evidence, and artefacts in an assurance
case?
(Q15) How can empirical research help in successfully demonstrating the capabilities of iFMs
for rigorous and certifiable autonomy assurance?

This list of research questions can easily be extended by further more detailed empirical questions
from the settings discussed in Jones and Bonsignour [2011, Sec. 4.4].

### 7.3 Some Envisaged Research Outcomes

Our vision of rigorous RAS assurance implies foundational iFM research to result in
• novel semantic frameworks unifying best practice methods, models, and formalisms established
in RAS.
• new concepts for iFM-based development environments.
• new computational theories to support formal modelling and verification of RAS.
• evaluations of
—assurance tools, languages, frameworks, or platforms used in practice regarding their
support of iFMs;
—the integration of iFMs into modelling and programming techniques, assurance methods,
and assurance processes;
—languages for linking informal requirements with evidence from iFMs;
—(automated) abstraction techniques used in assurance and certification.
• opinions, positions, and visions on FM integration and unification for rigorous practical
assurance.

Our vision of rigorous RAS assurance implies applied and empirical iFM research to result in
• comparisons of
—projects applying iFMs in assurance practice with similar practical projects applying noniFM
approaches.
—iFM-based (embedded software) assurance with assurance approaches in traditional engineering
disciplines.
• checklists, metrics, and benchmarks (for and beyond tool performance) for
—the evaluation and comparison of iFM-based assurance approaches (e.g., confidence
level).
—relating error removal and incident root cause data (e.g., efficiency and effectiveness in
removal of severe errors or in avoidance of severe accidents, cf. MacKenzie [1994]).
—usability and maturity assessment of iFMs (e.g., abstraction effort, proof complexity, assurance
case complexity, productivity).
—the evaluation of FM budget cases (cf. Darbari [2018] in electronic hardware development).
• experiences in or surveys (e.g., systematic mappings and reviews of assurance case research,
interview studies with assurance practitioners, cf. Section A) of
—iFM transfers and applications (e.g., case studies in assurance and certification projects).
—challenges, limitations/barriers, and benefits of iFMs in assurance and certification
projects.
• research designs (e.g., for controlled field experiments) for the practical validation of iFMs
in assurance and certification projects.
• opinions, positions, and visions on future research, education, and training in the use of
iFMs in assurance and certification.

## 8 SUMMARY

Along the lines of Hoare et al. [2009], we analysed strengths, weaknesses, opportunities, and threats to determine the potential of integrated formal methods to improve the practice of dependability assurance. Emphasising robots and autonomous systems as an area in the spotlight of dependability assurance, we express our expectations of research progress and transfer. From these expectations, we derived a research and research transfer agenda with the objective of (i) enhancing the foundations of integrated formal methods, (ii) collecting evidence on the effectiveness of integrated formal methods in practice, (iii) successfully transferring integrated formal methods into the assurance practice, with a short-term focus on robots and autonomous systems, and (iv) fostering research progress, education, and training from the results of this transfer effort.

- Charles Antony Richard Hoare, Jayadev Misra, Gary T. Leavens, and Natarajan Shankar. 2009. The verified software initiative. Comput. Surveys 41, 4 (2009), 1–8. DOI:https://doi.org/10.1145/1592434.1592439

按照Hoare等人[2009]的思路，我们分析了优势、劣势、机会和威胁，以确定集成形式方法改进可靠性保证实践的潜力。我们强调机器人和自主系统是可靠性保障的重点领域，并表达了我们对研究进展和转移的期望。从这些期望,我们得到一个研究和研究议程的目标转移
- (i)提高综合形式方法的基础,
- (ii)收集证据的有效性综合正式的方法在实践中,
- (iii)成功地集成形式方法转移到保证实践,短期关注机器人和自治系统,及
- (iv)培养研究进展,教育和训练的结果转移工作。
