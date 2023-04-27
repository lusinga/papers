# A Comprehensive Exploration of Languages for Parallel Computing

FEDERICO CICCOZZI, LORENZO ADDAZI, SARA ABBASPOUR ASADOLLAH,
BJÖRN LISPER, ABU NASER MASUD, and SAAD MUBEEN, Mälardalen University

Software-intensive systems in most domains, from autonomous vehicles to health, are becoming predominantly parallel to efficiently manage large amount of data in short (even real-) time. There is an incredibly rich literature on languages for parallel computing, thus it is difficult for researchers and practitioners, even experienced in this very field, to get a grasp on them.With this work we provide a comprehensive, structured, and detailed snapshot of documented research on those languages to identify trends, technical characteristics, open challenges, and research directions. In this article, we report on planning, execution, and results of our systematic peer-reviewed as well as grey literature review, which aimed at providing such a snapshot by analysing 225 studies.

大多数领域的软件密集型系统，从自动驾驶汽车到医疗保健，正变得越来越并行化，以便在短时间内（甚至实时）高效地处理大量数据。关于并行计算语言有着极其丰富的文献资料，因此即使是在这个领域有经验的研究人员和从业者也难以掌握它们。通过这项工作，我们提供了一份详尽、有结构、详细的文献研究快照，以确定趋势、技术特点、挑战和研究方向。在本文中，我们报告了系统的同行评审和灰色文献综述的规划、执行和结果，旨在通过分析225项研究来提供这样的快照。

## 1 INTRODUCTION

There is an ever increasing demand for computational power, which has enabled us to do things that were once considered science fiction: an example is self-driving cars, which require large amounts of computing to do the necessary real-time processing of streaming data from sensors, cameras and so on, and make decisions in real-time based on these data.

对计算能力的需求不断增加，这使得我们能够完成曾经被视为科幻的事情：例如自动驾驶汽车，需要大量的计算来对来自传感器、摄像头等的流数据进行必要的实时处理，并基于这些数据做出实时决策。

Software parallelism, where multiple computations are carried out at the same time, has become the most common way to provide higher levels of computational power leveraging massively parallel hardware computational units. Parallel hardware was first introduced in supercomputers, e.g., in the ILLIAC-IV [22]. As of the time of writing (July 2020), the supercomputer with the highest peak performance is the Japanese Fugaku [233], with 7.3 million cores, giving it a maximal speed of 514 petaFLOPS. Besides increment of performance, parallelism can be used to reduce energy consumption too; this role is becoming more and more important, as shown by several secondary studies on energy awareness [221], how to best achieve energy efficiency [122], and which energy predictive models are available [181], in HPC.

软件并行性，即同时执行多个计算，已成为利用大规模并行硬件计算单元提供更高计算能力的最常见方式。并行硬件最初在超级计算机中被引入，例如在ILLIAC-IV [引用文献22]中。截至撰写本文时（2020年7月），拥有最高峰值性能的超级计算机是日本的富岳 [引用文献233]，拥有730万个核心，最大速度可达514 petaFLOPS。除了提高性能之外，并行性还可以用于降低能源消耗。在高性能计算中，能源问题变得越来越重要，这已经得到了多项关于能源意识[引用文献221]、如何实现最佳能源效率[引用文献122]以及哪些能源预测模型可用[引用文献181]的次要研究的证明。

We are facing a situation where software-intensive systems have become predominantly parallel. Parallelism appears in many forms, at multiple levels, and it is supported by a variety of hardware architectures. This raises the issue how to program these systems. Parallel programming was known to be hard already 30 years ago [126], and it has hardly become easier since then. The programming language is an important link in the software production chain. Many parallel programming languages have been invented over the years, and there is an incredibly rich literature. Due to the importance of the topic and its breadth, we believe that the time is ripe to create a structured and detailed snapshot of this research area, to identify trends, technical characteristics, and potentially open challenges.

我们面临的情况是，软件密集型系统已经成为主要的并行系统。并行性以多种形式出现在多个层次上，并受到各种硬件架构的支持。这引发了如何编写这些系统的问题。30年前，人们已经知道并行编程很难[引用文献126]，而且现在也没有变得更容易。编程语言是软件生产链中重要的一环。多年来，已经发明了许多并行编程语言，有着极其丰富的文献资料。由于该主题的重要性和广度，我们认为现在是创建这个研究领域的结构化和详细快照的时机，以确定趋势、技术特点和潜在挑战。

In this article, we report on the planning, execution, and results of our systematic literature review, which aims at providing such a snapshot. From an initial set of 3,476 papers and 72 languages, we identified 225 primary studies, which we analysed in detail following a precise data extraction, analysis, and synthesis process. A summary of the resulting highlights of our study is as follows:
本文报告了我们进行系统文献综述的规划、执行和结果，旨在提供这样一个快照。从初始的3,476篇论文和72种语言中，我们确定了225个主要研究，并进行了详细分析，遵循精确的数据提取、分析和综合过程。我们研究的结果概述如下：

- Since 1988, effort in this research area has been steadily growing;
- Most languages are high-level and general-purpose;
- Most languages are defined in terms of extensions of existing languages; C/C++ and Java are the most popular programming languages and UML the most popular modelling language;
- Imperative and object-oriented programming are the most popular paradigms, in that order;
- Explicit parallelism is the most commonly supported;
- Task- and data-parallelism are the most common problem decomposition approaches, possibly supported by lower-level forms of parallelism, e.g., pipeline;
- The majority of languages are compiled and the target is usually a high-level language;
- CPUs (multi- and many-core) represent by large the most commonly targeted architectures; interestingly, over 30% of the languages are not tailored to any specific target architecture;
- Support for data-parallelism and multiple hardware platforms (also heterogeneously mixed) are the most longed for features for existing languages;
- Languages and related tooling are in most cases at a prototypical level.
- 自1988年以来，该研究领域的努力一直在稳步增长；
- 大多数语言都是高级和通用的；
- 大多数语言都是基于现有语言的扩展定义的；C/C++和Java是最流行的编程语言，UML是最流行的建模语言；
- 命令式和面向对象编程是最流行的范式，顺序为命令式、面向对象；
- 显式并行是最常见的支持方式；
- 任务并行和数据并行是最常见的问题分解方法，可能支持低级别的并行形式，例如pipeline；
- 大多数语言都是编译型的，目标通常是高级语言；
- CPU（多核和众核）是最常见的目标架构；有趣的是，超过30%的语言并没有针对任何特定的目标架构进行定制；
- 支持数据并行和多个硬件平台（包括异构混合平台）是现有语言最期望的功能；
- 语言和相关工具在大多数情况下都处于原型级别。

The remainder of the article is organised as follows. We describe our research method in Section 2. The results of our study are unwound in Sections 3 and 4. In Section 5, we provide a further discussion of the results focusing on projecting open challenges into what we believe, supported by our results, to be the most urgent matters to solve in the area. An analysis of the potential threats to the validity of our study, and how we handled them, is reported in Section 6. Works related to our study are described in Section 7, and the article is concluded with Section 8.

本文的剩余部分如下。第2节中，我们描述了我们的研究方法。我们的研究结果在第3和第4节中得出。在第5节中，我们进一步讨论了结果，并着重讨论了我们认为根据我们的研究结果，是最紧迫需要解决的问题的开放性挑战。我们在第6节中报告了对我们的研究的潜在威胁的分析以及我们如何处理它们。我们在第7节中描述了与我们的研究相关的工作，并在第8节中对文章进行了总结。

## 2 RESEARCH METHOD

This study was designed and carried out by following guidelines for systematic literature reviews [133]. To carry out this study, we followed the process depicted in Figure 1, which can be divided into the three common phases of planning, conducting, and documenting.

本研究设计和实施遵循系统文献综述的指南[133]。为了进行本研究，我们遵循图1所示的流程，可分为规划、执行和文档三个常见阶段。

## 3 RESULTS: VERTICAL ANALYSIS

## 4 RESULTS: ORTHOGONAL ANALYSIS

## 5 OPEN CHALLENGES AND PROSPECTS

## 6 THREATS TO VALIDITY

There are two aspects that make us confident on the quality of our study. The first one is that we defined and meticulously followed a detailed research protocol document, which was subject to external reviews by independent researchers. The second one is that we conducted our study by following well-established guidelines for systematic studies. In any case, it is important to discuss the potential threats to the validity of our work and its outcomes, as well as the means undertaken to mitigate them.

有两个方面让我们对本研究的质量充满信心。第一个方面是，我们定义了一个详细的研究协议文件，并受到独立研究人员的外部审查。第二个方面是，我们按照系统研究的成熟指南进行研究。无论如何，讨论我们的工作和结果的有效性潜在威胁以及缓解这些威胁的手段是非常重要的。

### 6.1 External Validity

External validity refers to the generalisability of causal findings with respect to the desired population and settings [254]. In a systematic review, one of the most severe threats is that the selected set of primary studies is not representative of the state of the art on languages for parallel computing. This potential threat to validity is mitigated by targeting multiple data sources, i.e., ACM Digital Library, IEEE Xplore, Scopus, and Web of Science. The used data sources cover the area of software engineering well [41] and are five of the largest and most complete scientific databases and indexing systems in software engineering. Moreover, we further complement the results of the automatic searches by performing closed recursive backward and forward snowballing.

外部有效性是指针对所需的人群和环境的因果发现的普适性[254]。在系统性审查中，最严重的威胁之一是所选的一组主要研究不能代表并行计算语言领域的最新发展。通过定位多个数据源（即ACM Digital Library，IEEE Xplore，Scopus和Web of Science），可以缓解这种潜在的有效性威胁。所使用的数据源充分涵盖软件工程领域[41]，并且是软件工程中最大和最完整的科学数据库和索引系统之一。此外，我们通过执行封闭递归向前和向后滚雪球的方法来进一步补充自动搜索的结果。

Only studies published in the English language were included. Although, this decision could result in a possible threat to validity due to potentially missing primary studies published in other languages, English is the de-facto standard language for scientific papers, so the threat can be reasonably considered negligible.

该研究只包括发表在英语语言期刊或会议上的研究。尽管这种决定可能会导致遗漏其他语言发表的研究的威胁，但英语是科学论文的事实上的标准语言，因此这种威胁可以被合理地认为是可以忽略的。

### 6.2 Internal Validity

Internal validity refers to extraneous variables and inaccurate settings that may have had a negative impact on the design of the study [254].We mitigated this potential threat by defining meticulously the process to follow and the data extraction form according to well-established guidelines. Concerning the validity of the data analysis and synthesis, threats were minimal since we used descriptive statistics. Moreover, we cross-analysed the different categories of the data extraction form to make a sanity check of the extracted data. This task made us identify and solve potential issues on the consistency of the extracted data and make us confident of the internal validity of our study.

内部有效性是指可能对研究设计产生负面影响的外部变量和不准确的设置。我们通过根据已建立的指南精细定义遵循的过程和数据提取表单来减轻这种潜在威胁。关于数据分析和综合的有效性，由于我们使用描述性统计方法，威胁很小。此外，我们交叉分析数据提取表单的不同类别，以对提取的数据进行合理性检查。这项任务使我们识别和解决了提取数据一致性的潜在问题，让我们对我们的研究的内部有效性有信心。

### 6.3 Construct Validity

Construct validity refers to the extent to which an identified causal relationship can be generalised from the particular methods and operations of a specific study to the theoretical constructs and processes they were meant to represent [254]. As already argued for external validity, the initial automated search has been performed on four different data sources and complemented with snowballing. One remaining potential threat to validity may be caused by a malformed search string. We mitigated this potential threat by (i) following a rigorous process for defining it and (ii) piloting the search string in preliminary queries using all four data sources. Once we gathered all relevant studies from the automatic search, we rigorously screened them in a collaborative fashion and according to well-documented inclusion and exclusion criteria. Moreover, the selection team members classified a certain number of common studies (∼15%) in order to check agreement, achieving a Cohen’s kappa [28] of 0.87 (almost perfect agreement according to Cohen’s suggested interpretation).

构造效度是指已鉴定的因果关系从特定研究的方法和操作，能够推广到所代表的理论构造和过程的程度[254]。正如在外部效度方面所论述的，我们在四个不同的数据源上执行了初始的自动化搜索，并通过雪球法进行了补充。可能存在的一个潜在威胁是由于搜索字符串不当而引起的。为了减轻这种潜在的威胁，我们 (i) 遵循了一个严格的流程来定义搜索字符串， (ii) 在所有四个数据源上使用初步查询来测试搜索字符串。一旦我们从自动搜索中收集到所有相关研究，我们依据明确的纳入和排除标准，在协作方式下严格地筛选它们。此外，选择团队成员对一定数量的共同研究 (∼15%) 进行分类，以检查一致性，并达到 Cohen's kappa[28] 为0.87 (根据 Cohen 提出的解释，几乎完全一致)。

### 6.4 Conclusion Validity

This refers to the relationship between extracted data and obtained findings [254]. We mitigated potential threats by systematically applying and documenting well-defined processes; we also provide a publicly accessible replication package,8 which allows to replicate each step of our study. The data extraction form definition can be a notable threat to conclusion validity if based on own experience or other informal sources. We mitigated this by (i) letting the categories and their values emerge from the pilot studies and refining them throughout the entire data extraction activity, and (ii) making five researchers be actively involved in the definition of the form as well as the extraction and analysis/synthesis of data.

这指的是提取的数据与得出的发现之间的关系[254]。我们通过系统地应用和记录明确定义的流程来缓解潜在的威胁；我们还提供了一个可公开访问的复制包，允许复制我们研究的每个步骤。如果数据提取表格基于自己的经验或其他非正式来源，那么定义数据提取表格可能是对结论有效性的显著威胁。我们通过（i）让类别和值从试验中出现并在整个数据提取过程中进行完善，并（ii）让五名研究人员积极参与表格的定义、数据提取和分析/综合来减轻这个潜在威胁。

## 7 RELATEDWORK

In the past few years, systematic literature reviews have gained considerable popularity in many research areas, in particular, software engineering [133]. In this article, we conduct a systematic literature review of programming and modeling languages for parallel computing platforms. This topic overlaps with the research areas of software engineering, computer science, and computer engineering. The research area targeted in this article is quite dispersed as there is a large body of research on programming and modelling languages for parallel computing platforms. This is indicated by 219 identified publications as shown in Figure 2 and discussed in Section 3.

- 133 Barbara Kitchenham and Pearl Brereton. 2013. A systematic reviewof systematic reviewprocess research in software engineering. Information and Software Technology 55, 12 (2013), 2049–2075.

在过去几年中，系统文献综述在许多研究领域，特别是软件工程[引用文献133]中，获得了相当大的流行。在本文中，我们对并行计算平台的编程和建模语言进行了系统文献综述。这个主题与软件工程、计算机科学和计算机工程的研究领域有所重叠。本文目标的研究领域非常分散，因为有大量关于并行计算平台的编程和建模语言的研究。这在图2中的219个已识别出的出版物中得到了体现，并在第3节中进行了讨论。

There are very few systematic literature reviews, systematic mapping studies, and surveys of the state-of-the-art that discuss some aspects of parallel computing platforms. The most recent one is a survey on parallel programming models by Fang et al. [81], Brodtkorb et al. [44] conduct an investigation of the state-of-the-art in the area of heterogeneous computing. In this study, they limit the scope of their investigation by focusing on only three heterogeneous platforms, namely, the Cell Broadband Engine Architecture, GPU, and FPGA. Similar to the work in [44], Mittal and Vetter [170] present a survey of heterogeneous computing techniques, where they only consider CPU-GPU computing platform. Fernando et al. [82] perform a systematic mapping study to create a structured map of the existing research with respect to parallel computing in various execution platforms such as multi-core, cluster, and GPU. Similarly, Liu et al. [151] conduct a survey of software and hardware aspects of heterogeneous computing platforms. In this work, the authors focus only on a specific architecture, namely, the Heterogeneous System Architecture (HSA). However, these works do not investigate programming and modelling languages for the considered parallel computing platforms. In comparison to the above mentioned works, we conduct a systematic literature review of programming and modelling languages for generally a broad range of parallel computing platforms.

关于并行计算平台的某些方面，目前很少有系统性的文献综述、系统性的映射研究和现有技术的调查。最近的一项是Fang等人关于并行编程模型的调查[引用文献81]，Brodtkorb等人[引用文献44]对异构计算领域的现有技术进行了调查。在这项研究中，他们通过只关注三个异构平台（即Cell Broadband Engine Architecture、GPU和FPGA）来限定调查的范围。类似于[引用文献44]中的工作，Mittal和Vetter[引用文献170]提出了一项异构计算技术的调查，他们只考虑了CPU-GPU计算平台。Fernando等人[引用文献82]进行了一项系统映射研究，以创建现有研究的结构化地图，涉及多核、集群和GPU等各种执行平台的并行计算。同样，Liu等人[引用文献151]对异构计算平台的软件和硬件方面进行了调查。在这项工作中，作者只关注了特定的体系结构，即异构系统体系结构（HSA）。然而，这些工作并没有调查所考虑的并行计算平台的编程和建模语言。与上述工作相比，我们对广泛范围的并行计算平台的编程和建模语言进行了系统文献综述。

Andrade and Crnkovic [14] conduct a systematic mapping study of software architectures for heterogeneous computing platforms. The authors identify 28 publications of interest and using these they present various trends and identify gaps in the research area. Similarly, Andrade et al. [15] conduct a systematic mapping study that focuses on the deployment of software on heterogeneous computing platforms. In this study, the authors provide an overview of the research area with the aim at identifying and categorising the published research results according to a defined classification. Although the studies in [14, 15] focus on heterogeneous computing platforms, they do not consider programming and modelling languages. In comparison, this article presents a systematic literature review of programming and modelling languages for parallel computing while considering homogeneous as well as heterogeneous computing platforms.

Andrade和Crnkovic [14]进行了一项软件体系结构对异构计算平台的系统性映射研究。作者确定了28篇有关的出版物，并利用这些出版物展示了各种趋势，并确定了研究领域的差距。同样，Andrade等人[15]进行了一项系统性映射研究，重点关注了软件在异构计算平台上的部署。在这项研究中，作者概述了研究领域，旨在根据定义的分类标准识别和分类已发表的研究结果。尽管[14, 15]中的研究重点放在异构计算平台上，但它们并没有考虑编程和建模语言。相比之下，本文在考虑同构和异构计算平台的同时，对并行计算的编程和建模语言进行了系统文献综述。

There are a few works that study and survey the existing programming languages and programming models for multi-core and many-core computing platforms. For instance, Diaz et al. [75] present a survey of parallel and distributed programming models for multi-core and many-core processors. Soares et al. [143] present a systematic mapping study of parallel programming on multi-core platforms. However, their core focus is on the programming languages that are used in undergraduate education. Frank et al. [87] conduct a systematic literature reviewon parallelisation, modelling and performance prediction of applications that are run on multi-core and many-core processors. In this work, the authors identify and analyse 34 relevant publications that mainly focus on providing support for performance prediction of parallel applications on these parallel computing platforms. However, both these works focus only on homogeneous multi-core and many-core computing platforms. In comparison to these works, the systematic literature review conducted in our article identifies and analyses parallel programming and modeling languages for homogeneous as well as heterogeneous parallel computing platforms.

有一些研究和调查现有的多核和众核计算平台的编程语言和编程模型。例如，Diaz等人[75]提出了一份关于多核和众核处理器的并行和分布式编程模型的调查报告。Soares等人[143]对多核平台上的并行编程进行了系统性映射研究。然而，他们的核心重点是用于本科教育的编程语言。Frank等人[87]对运行在多核和众核处理器上的应用程序的并行化、建模和性能预测进行了系统文献综述。在这项工作中，作者识别并分析了34个相关出版物，主要关注为这些并行计算平台上的并行应用程序提供性能预测支持。然而，这些工作都只关注同构的多核和众核计算平台。相比之下，我们的系统文献综述研究针对同构和异构并行计算平台，对并行编程和建模语言进行了识别和分析。

Kessler and Keller [129] review and analyse a few parallel computing models with respect to execution styles, type of parallelism (data and task parallelism), memory and communication models. Memeti et al. [166] perform a comparative evaluation of four parallel programming frameworks, namely OpenMP, OpenCL, OpenACC, and CUDA. The evaluation parameters considered in this study include programming productivity, performance, and energy. In comparison with these works, our study is comprehensive in the sense that it considers a wide range of programming and modelling languages, programming models, target hardware architectures (SISD, SIMD, MISD, and MIMD), communication and memory models, and parallel execution models.

Kessler和Keller [129]对几种并行计算模型进行了回顾和分析，涉及执行方式、并行类型（数据并行和任务并行）、内存和通信模型。Memeti等人[166]对四个并行编程框架进行了比较评估，即OpenMP、OpenCL、OpenACC和CUDA。这项研究考虑的评估参数包括编程生产力、性能和能耗。与这些工作相比，我们的研究是全面的，因为它考虑了广泛的编程和建模语言、编程模型、目标硬件体系结构（SISD、SIMD、MISD和MIMD）、通信和内存模型以及并行执行模型。

Nestmann [174] performs a systematic literature review with the aim at identifying a taxonomy for parallel programming models. The authors identify five different taxonomies in the existing literature. Based on the identified taxonomies, the authors build a consolidated and consistent taxonomy for parallel programming models. Amaral et al. [11] conduct a systematic mapping study of programming languages for High-performance Computing (HPC) platforms. In this study, the authors identify 26 programming languages for the HPC platforms that are presented in 33 relevant publications. Although this work is closely related to the work presented in our article, there are a number of key differences between the two works. That is, Amaral et al. [11] present a systematic mapping study focusing on a large-scale cluster of computing nodes that are connected by networks. Furthermore, the heterogeneity in the parallel computing platforms is not explicitly addressed. On the other hand, we present a systematic literature review that considers programming and modelling languages for a broad range of homogeneous and heterogeneous parallel computing platforms ranging from on-chip and on-board to large-scale cluster platforms.

Nestmann [174] 进行了一项系统文献综述，旨在确定并行编程模型的分类法。作者在现有文献中确定了五个不同的分类法。基于所确定的分类法，作者建立了一个统一和一致的并行编程模型分类法。Amaral等人[11] 对高性能计算（HPC）平台的编程语言进行了系统性映射研究。在这项研究中，作者确定了26种在33个相关出版物中介绍的HPC平台的编程语言。尽管这项工作与我们的文章密切相关，但两项工作之间存在一些关键差异。即Amaral等人[11]提供了一个关注网络连接的大规模计算节点集群的系统性映射研究。此外，并行计算平台中的异构性没有明确地被提及。另一方面，我们提供了一个系统文献综述，考虑了从芯片和板上到大规模计算集群平台的广泛的同构和异构并行计算平台的编程和建模语言。

To the best of our knowledge, the state-of-the-art is lacking a systematic literature review on programming and modelling languages for parallel computing platforms. There is an urgent need for constructing a structured map of the research area, analysing the existing works, and identifying various trends and gaps in the area. The work presented in this paper takes a major step in addressing these needs.

据我们所知，当前还没有一篇系统的文献综述关于并行计算平台的编程和建模语言。迫切需要构建一个结构化的研究领域地图，分析现有工作，并识别该领域的各种趋势和差距。本文所述的工作在解决这些需求方面迈出了重要的一步。

## 8 CONCLUSIONS AND FUTURE DIRECTIONS

In this article, we reported on the planning, execution, and results of a systematic literature review on languages for describing parallel software. From an initial set of 3,476 papers (3,469 from automatic searches plus seven pilot studies) and 72 languages, after a set of refinements, we selected 225 primary studies to represent the treated topic. We extracted data from them according to a thoroughly defined data extraction process; then, we analysed and synthesised the data. The result of thiswork was a comprehensive, structured, and detailed snapshot of the current landscape on languages for parallel computing, useful for both the more and the less experienced researcher or practitioner. Additionally, based on the study results and our own interpretation of the current and forthcoming trends in relevant application domains, we provided hints on challenges that remain open as well as on what we believe being some of the “hot” research directions to pursue in this research area. An interesting extension to this study could be a separate review of embedded/ internal domain-specific languages for parallel computing, where the focus would be on aspects related to their design/implementation and the relation to the host languages. A retrospective on the host languages and the additional features brought by hosted languages could be then synthesised too. Moreover, another direction for extending this study could be represented by an in-depth analysis of the new challenges in HPC systems in terms of programming, such as those related to persistent memory.

在本文中，我们报道了一项关于描述并行软件语言的系统文献综述的规划、执行和结果。从一个最初的3,476篇论文（3,469篇来自自动搜索加上7篇试点研究）和72种语言的初始集合中，经过一系列的细化，我们选择了225篇主要研究来代表所研究的主题。我们根据一个经过深入定义的数据提取过程从中提取数据，然后对数据进行分析和综合。这项工作的结果是对并行计算语言的当前情况进行了详尽、有结构、详细的快照，对于经验丰富的研究人员和从业者以及缺乏经验的研究人员都是有用的。此外，基于研究结果和我们对相关应用领域当前和未来趋势的解释，我们提出了一些挑战仍然存在的提示，以及我们认为是该研究领域一些“热门”研究方向的提示。这项研究的有趣扩展可能是对嵌入式/内部领域特定的并行计算语言进行单独的审查，重点关注与宿主语言相关的设计/实现方面以及它们之间的关系。然后，可以综合总结宿主语言和托管语言带来的附加功能的回顾性研究。此外，扩展这项研究的另一个方向可能是对HPC系统中的新编程挑战进行深入分析，例如与持久性内存相关的挑战。
