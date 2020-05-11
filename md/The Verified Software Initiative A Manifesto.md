# The Verified Software Initiative: A Manifesto

## 1. INTRODUCTION

We propose an ambitious and long-term research program toward the construction of error-free software systems. Our manifesto represents a consensus position that has emerged from a series of national and international meetings, workshops, and conferences held from 2004 to 2007. The research project, the Verified Software Initiative, will attempt to construct over the next fifteen years: (1) a comprehensive theory of programming that covers the features needed to build practical and reliable programs, (2) a coherent toolset that automates the theory and scales up to the analysis of industrialstrength software, and (3) a collection of realistic verified programs that could replace unverified programs in current service and continue to evolve in a verified state.

我们提出了一个雄心勃勃的和长期的研究计划，以建设无错误的软件系统。我们的宣言代表了在2004年至2007年举行的一系列国家和国际会议、讲习班和会议上形成的共识立场。研究项目、软件验证计划,在未来15年将尝试构建:(1)全面的编程理论覆盖特性需要构建实用和可靠的计划,(2)一个连贯的工具集,自动化理论和尺度工业强度的分析软件,和(3)现实验证程序的集合,可以取代当前服务和未经证实的项目继续发展处于验证状态。

This document summarizes the background of the initiative, its scientific goals, and the principles that underlie a worldwide collaboration to achieve them. We include an assessment of its strengths, weaknesses, threats and opportunities. A companion document will summarize a range of work packages, including developments in theory, tools, and experiments.

本文件概述了该倡议的背景、其科学目标以及为实现这些目标而开展全球合作的原则。我们将评估它的优势、劣势、威胁和机遇。一个配套的文档将总结一系列的工作包，包括理论、工具和实验的发展。

## 2. EXECUTIVE SUMMARY

The scope of software in the modern world is unprecedented. Software is now woven into artifacts such as telephones, cars, and planes; it governs the infrastructure for systems in communication, air traffic control, and banking. Faulty software contributes to the unreliability of the products in which it is embedded and is a source of significant recurring costs and delivery delays. It exposes systems to malware attacks and raises the risk of catastrophic failure in critical applications.

软件在现代世界的范围是前所未有的。软件现在被编织成人工制品，如电话、汽车和飞机;它管理通信、空中交通管制和银行系统的基础设施。有缺陷的软件会导致其嵌入的产品的不可靠性，并造成重大的经常性成本和交付延迟。它使系统暴露于恶意软件攻击，并增加了关键应用程序发生灾难性故障的风险。

## 3. MANIFESTO

### 3.1. Background

### 3.2. Vision

### 3.3. Cost/Benefit

### 3.4. Scientific Method and Ideals

### 3.5. The scale of collaboration

### 3.6. Industrial participation

## 4. ASSESSMENT

The ideal of verification of software correctness dates back at least forty years. Before embarking now on a large-scale project to solve the problem, it is essential to understand and explain past failures and make a convincing case for future success.

软件正确性验证的理想至少可以追溯到40年前。在着手解决这个问题的大型项目之前，有必要了解并解释过去的失败，并为未来的成功提出令人信服的理由。

### 4.1. Strengths

Verification is not another well-meaning panacea for software unreliability. It is the foundational discipline on which programming is based. Programs have properties that can typically be stated in the form of constraints on the inputs and conditions satisfied by the observable outputs. Verification makes these properties explicit, and rigorously determines whether the program has the expected property. The properties can then be used to construct further arguments involving larger programs and the relationship between a program and its external environment.

验证并不是软件不可靠的另一种善意的灵丹妙药。它是编程的基础学科。程序的属性通常可以用输入约束的形式来表示，并且满足可观察输出的条件。验证使这些属性显式，并严格地确定程序是否具有预期的属性。然后，可以使用这些属性构造涉及更大程序的进一步参数，以及程序与其外部环境之间的关系。

Verification technology is already widely exploited in advanced engineering industries—transport, aerospace, electronics, communications, defense, and national security. Specialized software verification tools have been developed in these areas, and are available from a number of startup companies, together with consultation and collaboration in their use. Other useful tools, such as program analyzers, are available for rapid detection of programming errors at the earliest possible stage.

核查技术已经广泛应用于先进的工程工业——运输、航空航天、电子、通信、国防和国家安全。在这些领域中已经开发了专门的软件验证工具，可以从一些初创公司获得，并在使用过程中提供咨询和协作。其他有用的工具，如程序分析器，可用于在尽可能早的阶段快速检测编程错误。

Many research centers worldwide are already working towards this project’s goals. Their research has yielded guarantees based on complete computer-checked verification for small microprocessors, operating systems, programming language compilers, communication protocols, several significant mathematical and logical proofs, and even the essential kernels of the proof tools themselves. We are witnessing accelerated growth in the scale and degree of automation of these verification efforts.

世界各地的许多研究中心已经在朝着这个项目的目标努力。他们的研究基于对小型微处理器、操作系统、编程语言编译器、通信协议、几个重要的数学和逻辑证明，甚至是证明工具本身的核心的完整的计算机检查验证，从而得到了保证。我们正在目睹这些核查工作的规模和自动化程度加速增长。

A number of verification tools in existence today have been developed through many years of experience. They have built up loyal communities of experimental users. We thus expect to find a large community of users for the toolset built by this project.

今天存在的一些核查工具是通过多年的经验发展起来的。他们已经建立了忠实的实验用户社区。因此，我们希望为这个项目构建的工具集找到大量的用户社区。

Improvements in the speed, capacity, and affordability of hardware are continuing at the traditional rate. There is evidence from case studies that hardware improvements over the last ten years have turned computer-aided verification into a practical and cost-effective option for real programs.

硬件的速度、容量和可负担性方面的改进仍在以传统的速度进行。案例研究的证据表明，过去十年来硬件的改进已经使计算机辅助验证成为实际程序的一种实用而经济的选择。

Acceleration in hardware capacity has been fully matched by improvements in the algorithms and programs that carry out the basic task of constructing proofs. In some of the basic inner loops of a theorem prover, a thousand-fold increase in speed and capacity has been achieved in roughly ten years, and this is in addition to increased hardware speeds. Both hardware and software improvements are likely to continue.

硬件容量的加速已经被执行构造证明的基本任务的算法和程序的改进完全匹配。在一个定理证明器的一些基本内部循环中，在大约10年的时间里，速度和容量增加了1000倍，这是在硬件速度增加的基础上实现的。硬件和软件的改进可能会继续。

Established competitions exist between the builders of rival proof tools to evaluate their capabilities and speed. These are continuing drivers of measurable advance. The rules of a competition require the use of certain standard interfaces. These are already being further exploited to construct links for the interworking and cooperation of tools; they allow tool builders with varying backgrounds to collaborate in provision of services for their users. We expect that competition and cooperation will play significant roles in the VSI project.

已建立的竞争存在于竞争证明工具的建设者之间，以评估他们的能力和速度。这些都是可衡量进步的持续动力。比赛规则要求使用特定的标准接口。已经在进一步利用这些技术来为工具的相互作用和合作建立联系;它们允许具有不同背景的工具构建者协作为用户提供服务。我们期待竞争与合作在VSI项目中发挥重要作用。

The open source movement shows the power of collective idealism in the development of high-quality software. It gives an example of collaborative evolution of programs, which should be followed in the implementation and evolution of a verification toolset. The open source software provides a wealth of freely published source material, in the form of programs that can be subjected to analysis and verification.

开源运动显示了在高质量软件开发中的集体理想主义的力量。它给出了一个程序协同演化的例子，验证工具集的实现和演化应该遵循这个例子。开源软件以程序的形式提供了大量自由发布的源代码，这些程序可以进行分析和验证。

In addition to correctness concerns, verification technology can aid other software engineering concerns, such as system security, reliability, safety, and general compliance with the system’s requirements. Verification technology can be used to find and prevent known vulnerabilities that have significant economic costs, such as security holes.

除了正确性方面的考虑，验证技术还可以帮助其他软件工程方面的考虑，比如系统安全性、可靠性、安全性，以及系统需求的一般遵从性。验证技术可用于发现和防止已知的具有重大经济成本的漏洞，如安全漏洞。

### 4.2. Weaknesses

The main inhibiting factor during the early years of the project will be the lack of scientific staff with the right background, skills, and motivation. Another factor in the application of the results of the research will be the inexperience and lack of education of the programming profession, particularly in the difficult task of specifying what the program and its components are intended to do.

项目初期的主要制约因素是缺乏具有适当背景、技能和积极性的科研人员。应用研究结果的另一个因素将是缺乏编程专业的经验和教育，特别是在指定程序及其组件的目的是什么这一困难的任务中。

The lack of collaboration with other areas of computing is also a current weakness. Formal methods researchers must recognize the need to collaborate outside of the formal methods community in areas such as requirements analysis, testing, and fault-tolerant systems, in seekingways to aid the cause of reliable software. They should advance the state of programming practice by collaborating with researchers in programming languages, and improve software design tools by building on the insights of researchers in artificial intelligence, database management systems, and humancomputer interfaces.

缺乏与其他计算领域的协作也是当前的一个弱点。正式方法研究人员必须认识到在正式方法社区之外的领域(如需求分析、测试和容错系统)进行协作的必要性，以寻求帮助实现可靠软件的方法。他们应该通过与编程语言的研究人员合作来提高编程实践的水平，并通过构建人工智能、数据库管理系统和人机界面方面的研究人员的见解来改进软件设计工具。

The Computing research community has little experience in long-term collaboration on an international scale. It will be necessary to learn from the experience of physicists and astronomers, who collaborate successfully on the construction and use of satellites and particle accelerators.

计算研究界在国际范围内的长期合作方面几乎没有经验。有必要学习物理学家和天文学家的经验，他们成功地合作建造和使用了卫星和粒子加速器。

The current evaluation and reward structure in academic Computing departments favors rapid publication of conference papers expounding the latest research ideas of its authors. Such papers are rarely studied by practicing software engineers. In the modern world, results of research are typically made accessible, both to engineers and to scientists, through integrated toolsets. Work devoted to development and enhancement of scientific toolsets is insufficiently recognized by the current publication-based reward structures for computer scientists. Even less recognition is given to scientists who conduct experimental work based on tools and benchmarks developed by others.

目前学术计算部门的评价和奖励结构有利于快速发表会议论文，阐述作者的最新研究思路。这样的论文很少被实践的软件工程师研究。在现代社会，通过集成的工具集，研究结果通常对工程师和科学家都是可访问的。致力于开发和增强科学工具集的工作没有得到当前基于公众的计算机科学家奖励结构的充分承认。根据他人开发的工具和基准进行实验工作的科学家得到的认可更少。

The main scientific risk to the success of the project is that there is so much variation in computing application and technology that verification experience cannot readily be transferred from one program to the next; thus, no single tool will be able to meet all the demands placed on it.

该项目成功的主要科学风险在于，计算应用和技术上有太多的变化，验证经验不能轻易地从一个程序转移到下一个程序;因此，没有一种工具能够满足对它的所有要求。

### 4.3. Opportunities

Verification as a process of establishing properties of programs has deep repercussions for all aspects of computing including the design of hardware, the construction of operating systems and middleware, the coordination of concurrent activity, the safety of embedded systems and active networks, the autonomy of model-centric, self-healing systems, and information security. Verification addresses some of the most basic research challenges in computing including modeling, specification, design methods, type systems, language semantics, and verification algorithms.

验证的过程建立项目的属性有很深的影响的各个方面的设计计算包括硬件、操作系统和中间件的建设,协调并发活动,嵌入式系统和主动网络的安全,以模型为中心的自主权,自愈系统和信息安全。验证解决了计算中一些最基本的研究挑战，包括建模、规范、设计方法、类型系统、语言语义和验证算法。

The last two of decades of verification have yielded dramatic progress with the development of model checking, timed and hybrid systems, process calculi, SAT and SMT solvers, refinement calculi, specification languages, type theory, and abstract interpretation, along with the deep connections to control theory, game theory, artificial intelligence, operations research, and systems biology. In the next fifteen years, these ideas will have filtered into the mainstream, and new innovations and connections will be uncovered that radically redefine computational problem solving. The advances stimulated by the VSI challenge should eventually lead to a knowledge infrastructure that is founded on reliable software supported by powerful semantic tools.

在过去的二十年中，随着模型检验、时间和混合系统、过程计算、SAT和SMT求解器、精细化计算、规范语言、类型理论和抽象解释的发展，以及与控制理论、博弈论、人工智能、运筹学和系统生物学的深入联系，验证取得了显著进展。在未来的15年里，这些想法将会渗透到主流中，新的创新和联系将会被发现，从根本上重新定义计算问题的解决。VSI挑战所激发的进步最终应该会导致一种知识基础设施，这种基础设施建立在由强大的语义工具支持的可靠软件之上。

Significant commercial opportunities exist for software verification products and services. A 2002 NIST report on software testing estimated the total sales of software for the year 2000 at $180 billion, and the size of the software testing market for 2004 at $2.6 billion. The arrival of multicore chips brings the challenge of programming and debugging concurrent software to the fore. Verification will be increasingly challenged to address issues of reliability, resilience, interoperability, security, and even usability.

软件验证产品和服务存在重大的商业机会。2002年NIST关于软件测试的报告估计2000年软件的总销售额为1800亿美元，2004年软件测试市场的规模为26亿美元。多核芯片的到来，使并行软件的编程和调试问题日益突出。为了解决可靠性、弹性、互操作性、安全性甚至可用性等问题，验证将面临越来越大的挑战。

### 4.4. Threats

Tool projects are often evaluated by assessing impact on industrial users. This can inhibit ideas that require longer periods of implementation, testing, and reflection. In other branches of science, the most advanced technologies are often incorporated first into scientific instruments for use by other scientists. In this way, the technology more quickly develops the maturity needed for industrial application.

工具项目通常通过评估对工业用户的影响来进行评估。这可能会抑制需要较长时间的实现、测试和思考的想法。在科学的其他分支中，最先进的技术常常首先被纳入科学仪器中，供其他科学家使用。这样，该技术就能更快地发展为工业应用所需要的成熟技术。

Industrial users are often focused on rapid development and deployment to achieve market penetration. For such users reliability and correctness are secondary concerns, calling for methods and tools that are lightweight and adjustable.

工业用户通常专注于快速开发和部署，以实现市场渗透。对于这样的用户来说，可靠性和正确性是次要的，需要轻量级的和可调的方法和工具。

Lightweight techniques and tools have been remarkably successful in finding bugs and problems in software. However, their success must not stop the pursuit of this project’s long-term scientific ideals.

轻量级技术和工具在发现软件中的bug和问题方面非常成功。然而，他们的成功绝不能阻止对这个项目的长期科学理想的追求。

A significant inhibitor to advance in any branch of science is inadequate or intermittent or inappropriately directed funding. Continuity of funding usually requires a specific policy decision on the part of funding agencies. Such a decision has yet to be taken in favor of verification technology. The reasons for this neglect must be recognized, especially if they are likely to persist.

在任何科学分支中，阻碍进步的一个重要因素是资金不足、时断时续或不适当的投入。持续提供资金通常需要供资机构作出具体的政策决定。目前还没有做出有利于验证技术的决定。必须认识到这种忽视的原因，特别是如果它们可能继续存在。

National funding agencies are rightly reluctant to make large-scale and long-term commitments, unless they follow certain politically acceptable goals such as interdisciplinarity. Verification calls on expertise from many schools and branches of Computing; unfortunately, it is not generally recognized as an interdisciplinary topic.

国家资助机构有理由不愿作出大规模和长期的承诺，除非它们遵循某些政治上可以接受的目标，如跨学科性。验证需要许多学校和计算分支的专业知识;不幸的是，它并没有被普遍认为是一个跨学科的课题。

Many funding agencies rightly favor industrial collaboration in research, in the hope that rapid competitive advantage can be obtained. For such a project, the immediate needs of its industrial client are paramount. It is not widely recognized that Computing Science is a normal branch of science with a well-defined scientific agenda that transcends the pragmatic compromises needed for each of its many practical applications.

许多资助机构正确地倾向于在研究中进行产业合作，希望能迅速获得竞争优势。对于这样一个项目，其工业客户的直接需求是最重要的。人们没有广泛认识到，计算科学是一门正常的科学分支，它具有明确的科学议程，超越了它的许多实际应用所需要的实用折衷。

Some funding agencies rightly favor adventure in science. The VSI project as a whole is highly adventurous, and may well fail. Many individual advances that contribute to the challenge are essentially incremental—for example, to improve an existing tool or test it on a new application. These increments will have a compensating advantage: even if the project fails in its overall goal, the incremental improvements alone will more than justify the cost. The substantive advances will support novel ways of programming that can cope with the ever-increasing complexity and reliability demands of applications. The VSI challenge will surely provoke more than a few revolutionary innovations.

一些资助机构支持科学冒险是正确的。VSI项目作为一个整体是高度冒险的，很可能会失败。许多有助于挑战的个人进步本质上是渐进的——例如，改进一个现有的工具或在一个新的应用程序上测试它。这些增量将有一个补偿的优势:即使项目在总体目标上失败了，增量改进本身将比成本更有价值。这些实质性进展将支持能够应付应用程序日益增加的复杂性和可靠性要求的新的编程方法。VSI的挑战肯定会激发更多的革命性创新。

## 5. CONCLUSIONS

Correctness is a basic intellectual challenge for computing research. The Verified Software Initiative is a fifteen-year international program targeting progress in the science and technology of verification. The goal of the initiative is to demonstrate the viability of developing large-scale, bug-free software through the end-to-end use of verification technology. The research agenda is inclusive enough to accommodate all formal approaches to the development of certifiably reliable software. The goals are sufficiently ambitious that failure is a possibility. However, even partial success toward these goals can have a substantial impact on the security of the software infrastructure, the reliability of computer systems, and the scientific foundations of computing.

正确性是计算研究中一个基本的智力挑战。验证软件计划是一个为期15年的国际计划，目标是验证科学和技术的进步。该计划的目标是通过端到端使用验证技术来证明开发大规模、无缺陷软件的可行性。研究议程具有足够的包容性，可以容纳开发可靠软件的所有正式方法。目标足够远大，失败是可能的。然而，即使实现这些目标的部分成功，也会对软件基础设施的安全性、计算机系统的可靠性和计算的科学基础产生重大影响。
