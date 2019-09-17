# A Survey of Machine Learning for Big Code and Naturalness

## 1 INTRODUCTION

Software is ubiquitous in modern society. Almost every aspect of life, including healthcare, energy, transportation, public safety, and even entertainment, depends on the reliable operation of high-quality software. Unfortunately, developing software is a costly process: software engineers need to tackle the inherent complexity of software while avoiding bugs, and still delivering highly functional software products on time. There is therefore an ongoing demand for innovations in software tools that help make software more reliable and maintainable. New methods are constantly sought, to reduce the complexity of software and help engineers construct better software.

软件在现代社会中无处不在。几乎生活的每一个方面，包括医疗、能源、交通、公共安全，甚至娱乐，都依赖于高质量软件的可靠运行。不幸的是，开发软件是一个昂贵的过程:软件工程师需要在避免bug的同时解决软件固有的复杂性，并且仍然按时交付高功能的软件产品。因此，对软件工具的不断创新的需求有助于使软件更加可靠和可维护。不断寻求新的方法，以降低软件的复杂性，并帮助工程师构建更好的软件。

Research in this area has been dominated by the formal, or logico-deductive, approach. Practitioners of this approach hold that, since software is constructed in mathematically well-defined programming languages, software tools can be conceived in purely formal terms. The design of software tools is to be approached using formal methods of definition, abstraction, and deduction.
Properties of tools thus built should be proven using rigorous proof techniques such as induction over discrete structures. This logico-deductive approach has tremendous appeal in programming languages research, as it holds the promise of proving facts and properties of the program. Many elegant and powerful abstractions, definitions, algorithms, and proof techniques have been developed, which have led to important practical tools for program verification, bug finding, and refactoring [24, 42, 45]. It should be emphasized that these are theory-first approaches. Software constructions are viewed primarily as mathematical objects, and when evaluating software tools built using this approach, the elegance and rigor of definitions, abstractions, and formal proofs-of properties are of dominant concern. The actual varieties of use of software constructs, in practice, become relevant later, in case studies, that typically accompany presentations in this line of work.

这一领域的研究一直以正式或逻辑演绎的方法为主。这种方法的实践者认为，由于软件是用数学上定义良好的编程语言构建的，软件工具可以用纯粹的形式术语来构思。软件工具的设计将使用定义、抽象和演绎的形式方法来进行。
因此构建的工具的特性应该使用严格的证明技术来证明，例如离散结构上的感应。这种逻辑演绎方法在编程语言研究中具有巨大的吸引力，因为它承诺证明程序的事实和属性。许多优雅而强大的抽象、定义、算法和证明技术已经被开发出来，这导致了重要的实用工具来进行程序验证、错误发现和重构[24,42,45]。应该强调的是，这些都是理论优先的方法。软件结构主要被视为数学对象，当评估使用这种方法构建的软件工具时，定义、抽象和属性的形式证明的优雅和严谨性是主要关注的问题。在实践中，软件结构的实际使用种类在后来的案例研究中变得相关，这些案例研究通常伴随着这一行的演示。

[24] Al Bessey, Ken Block, Ben Chelf, Andy Chou, Bryan Fulton, Seth Hallem, Charles Henri-Gros, Asya Kamsky, Scott McPeak, and Dawson Engler. 2010. A few billion lines of code later: using static analysis to find bugs in the real world. Commun. ACM (2010)
[42] Edmund Clarke, Daniel Kroening, and Karen Yorav. 2003. Behavioral consistency of C and Verilog programs using bounded model checking. In Proceedings of the 40th annual Design Automation Conference。
[45] Patrick Cousot, Radhia Cousot, Jerôme Feret, Laurent Mauborgne, Antoine Miné, David Monniaux, and Xavier Rival. 2005. The ASTRÉE analyzer. In ESPO. Springer.

Of late, another valuable resource has arisen: the large and growing body of successful, widely used, open-source software systems. Open-source software systems such as Linux,MySQL,Django, Ant, and OpenEJB have become ubiquitous. These systems publicly expose not just source code, but also meta-data concerning authorship, bug-fixes, and review processes. The scale of available data is massive: billions of tokens of code and millions of instances of meta-data, such as changes, bug-fixes, and code reviews (“big code”). The availability of “big code” suggests a new, data-driven approach to developing software tools: why not let the statistical distributional properties, estimated over large and representative software corpora, also influence the design of development tools? Thus rather than performingwell in the worst case, or in case studies, our tools can perform well in most cases, thus delivering greater advantages in expectation. The appeal of this approach echoes that of earlier work in computer architecture: Amdahl’s law [15], for example, tells us to focus on the common case. This motivates a similar hope for development tools, that tools for software development and program analysis can be improved by focusing on the common cases using a fine-grained estimate of the statistical distribution of code. Essentially, the hope is that analyzing the text of thousands of well-written software projects can uncover patterns that partially characterize software that is reliable, easy to read, and easy to maintain.

最近，另一种有价值的资源出现了:越来越多的成功的、广泛使用的开源软件系统。Linux、MySQL、Django、Ant和OpenEJB等开源软件系统已经变得无处不在。这些系统不仅公开源代码，还公开关于作者、bug修复和审查过程的元数据。可用数据的规模是巨大的:数十亿个代码令牌和数百万元数据实例，例如更改、bug修复和代码评审(“大代码”)。“大代码”的可用性为开发软件工具提供了一种新的、数据驱动的方法:为什么不让统计分布特性(通过大型和有代表性的软件库进行估计)也影响开发工具的设计呢?因此，在最坏的情况下，或者在案例研究中，我们的工具可以在大多数情况下执行得很好，从而在预期中提供更大的优势，而不是执行得很好。这种方法的吸引力与早期在计算机架构方面的工作相呼应:例如，Amdahl 's law[15]告诉我们要关注常见的情况。这激发了对开发工具的类似希望，即软件开发和程序分析工具可以通过使用对代码统计分布的细粒度估计来关注常见的情况，从而得到改进。从本质上说，希望通过分析数千个编写良好的软件项目的文本，可以发现部分描述可靠、易于阅读和易于维护的软件特征的模式。

The promise and power of machine learning rests on its ability to generalize from examples and handle noise. To date, software engineering (SE) and programming languages (PL) research has largely focused on using machine learning (ML) techniques as black boxes to replace heuristics and find features, sometimes without appreciating the subtleties of the assumptions these techniques make. A key contribution of this survey is to elucidate these assumptions and their consequences. Just as natural language processing (NLP) research changed focus from brittle rule-based expert systems that could not handle the diversity of real-life data to statistical methods [99], SE/PL should make the same transition, augmenting traditional methods that consider only the formal structure of programs with information about the statistical properties of code.

机器学习的希望和力量在于它从例子中归纳和处理噪音的能力。到目前为止，软件工程(SE)和编程语言(PL)的研究主要集中在使用机器学习(ML)技术作为黑箱来代替启发式和发现特性，有时没有意识到这些技术所做的假设的微妙之处。这项调查的一个关键贡献是阐明这些假设及其后果。就像自然语言处理(NLP)研究焦点从脆弱的基于规则的专家系统,无法处理的多样性现实生活中的数据统计方法[99],SE / PL应该犯同样的过渡,增加传统方法只考虑项目的正式结构信息的统计特性的代码。

Structure. First, in Section 2, we discuss the basis of this area, which we call the “naturalness hypothesis”.We then review recent work on machine learning methods for analyzing source code, focusing on probabilistic models, such as n-gram language models and deep learning methods. We also touch on other types of machine learning-based source code models, aiming to give a broad overview of the area, to explain the core methods and techniques, and to discuss applications in programming languages and software engineering. We focus on work that goes beyond a “bag of words” representation of code, modeling code using sequences, trees, and continuous representations. We describe a wide range of emerging applications, ranging from recommender systems, debugging, program analysis, and program synthesis. The large body of work on semantic parsing [138], is not the focus of this survey but we include some methods that output code in general-purpose programming languages (rather than carefully crafted domain-specific languages). This review is structured as follows. We first discuss the different characteristics of natural language and source code to motivate the design decisions involved in machine learning models of code (Section 3). We then introduce a taxonomy of probabilistic models of source code (Section 4). Then we describe the software engineering and programming language applications of probabilistic source code models (Section 5). Finally, we mention a few overlapping research areas (Section 7), and we discuss challenges and interesting future directions (Section 6).

结构。首先，在第二节中，我们讨论了这个领域的基础，我们称之为“自然假设”。然后，我们回顾了用于分析源代码的机器学习方法的最新工作，重点是概率模型，如n-gram语言模型和深度学习方法。我们还讨论了其他类型的基于机器学习的源代码模型，目的是对该领域进行广泛的概述，解释核心方法和技术，并讨论在编程语言和软件工程中的应用。我们关注的工作超越了代码的“单词包”表示、使用序列、树和连续表示对代码建模。我们描述了广泛的新兴应用程序，从推荐系统、调试、程序分析和程序合成。语义分析的大量工作[138]并不是本次调查的重点，但是我们包括了一些用通用编程语言(而不是精心设计的领域特定语言)输出代码的方法。本文的结构如下。我们首先讨论自然语言的不同特点和源代码激励设计决策参与机器学习模型的代码(第三节)。然后,我们引入一个分类概率模型的源代码(4节)。然后我们描述概率的软件工程和编程语言的应用程序源代码模型(第五节)。最后,我们提到几个重叠的研究领域(第7节),我们讨论挑战和有趣的未来方向(第6部分)。

Related Reviews and other Resources. There have been short reviews summarizing the progress and the vision of the research area, from both software engineering [52] and programming languages
perspectives [28, 195]. However, none of these articles can be considered extensive literature reviews, which is the purpose of this work. Ernst [57] discusses promising areas of applying
natural language processing to software development, including error messages, variable names, code comments, and user questions. Some resources, datasets and code can be found at
http://learnbigcode.github.io/. An online version of the work reviewed here — which we will keep up-to-date by accepting external contributions — can be found at https://ml4code.github.io.

相关评论和其他资源。从软件工程[52]和编程语言的角度对研究领域的进展和前景进行了简要的综述[28,195]。然而，这些文章都不能被认为是广泛的文献综述，这也是本文的目的。Ernst[57]讨论了将自然语言处理应用于软件开发的有前景的领域，包括错误消息、变量名、代码注释和用户问题。可以在http://learnbigcode.github.io/找到一些资源、数据集和代码。我们将通过接受外部的贡献来保持最新的在线版本，可以在https://ml4code.github.io找到。

## 2 THE NATURALNESS HYPOTHESIS

Many aspects of code, such as names, formatting, the lexical order of methods, etc. have no impact on program semantics. This is precisely why we abstract them in most program analyses. But then, why should statistical properties of code matter at all? To explain this, we recently suggested a hypothesis, called the naturalness hypothesis. The inspiration for the naturalness hypothesis can be traced back to the “literate programming” concept of D. Knuth, which draws from the insight that programming is a form of human communication: “Let us change our traditional attitude to the construction of programs: Instead of imagining that our main task is to instruct a computer what to do, let us concentrate rather on explaining to human beings what we want a computer to do...” [105] The naturalness hypothesis, then, holds that
The naturalness hypothesis. Software is a form of human communication; software corpora have similar statistical properties to natural language corpora; and these properties can be exploited to build better software engineering tools.

代码的许多方面，如名称、格式、方法的词法顺序等，对程序语义没有影响。这正是我们在大多数程序分析中抽象它们的原因。但是，为什么代码的统计属性如此重要呢?为了解释这一点，我们最近提出了一个假说，叫做自然假说。自然假说的灵感可以追溯到d . Knuth的“文学编程”概念,从编程的洞察力是人类交流的一种形式:“让我们改变我们的传统态度的建设项目:而不是想象,我们的主要任务是指导计算机要做什么,让我们集中注意力,而在向人类解释我们想要一台电脑做……[105]自然假设是这样认为的
自然假设。软件是人类交流的一种形式;软件语料库与自然语言语料库具有相似的统计特性;这些特性可以用来构建更好的软件工程工具。

The exploitation of the statistics of human communication is a mature and effective technology, with numerous applications [99]. Large corpora of human communication, viz. natural language corpora, have been extensively studied, and highly refined statistical models of these corpora have been used to great effect in speech recognition, translation, error-correction, etc..

利用人类通信统计数据是一项成熟有效的技术，有着广泛的应用[99]。大量的人类交际语料库，即自然语言语料库得到了广泛的研究，这些语料库的高度精细化的统计模型在语音识别、翻译、纠错等方面发挥了巨大的作用。

The naturalness hypothesis holds that, because coding is an act of communication, one might expect large code corpora to have rich patterns, similar to natural language, thus allowing software engineering tools to exploit probabilistic ML models. The first empirical evidence of this hypothesis, showing that models originally developed for natural language were surprisingly effective for source code, was presented by Hindle et al. [87, 88]. More evidence and numerous applications of this approach have followed, which are the subject of this review.

自然假设认为，由于编码是一种通信行为，人们可能期望大型代码库具有丰富的模式，类似于自然语言，从而允许软件工程工具利用概率ML模型。Hindle等人提出了这一假说的第一个实证证据，表明最初为自然语言开发的模型对源代码非常有效[87,88]。随后出现了更多的证据和这种方法的大量应用，这是本审查的主题。

[87] Abram Hindle, Earl T Barr, Mark Gabel, Zhendong Su, and Premkumar Devanbu. 2016. On the naturalness of software. Commun. ACM (2016).
[88] Abram Hindle, Earl T Barr, Zhendong Su, Mark Gabel, and Premkumar Devanbu. 2012. On the naturalness of software. In Proceedings of the International Conference on Software Engineering (ICSE).


These models can be used to augment existing tools with statistical information and enable new machine learning-based software engineering tools, such as recommender systems and program analyses. At a high level, statistical methods allow a system to make hypotheses, along with probabilistic confidence values, of what a developer might want to do next or what formal properties might be true of a chunk of code. Probabilistic methods also provide natural ways of learning correspondences between code and other types of documents, such as requirements, blog posts, comments, etc.— such correspondences will always be uncertain, because natural language is ambiguous, and so the quantitative measure of confidence provided by probabilities is especially natural. As we discuss in Section 5, one could go so far as to claim that almost every area of software engineering and programming language research has potential opportunities for exploiting statistical properties.

这些模型可用于用统计信息扩充现有的工具，并支持新的基于机器学习的软件工程工具，如推荐系统和程序分析。在较高的层次上，统计方法允许系统对开发人员下一步可能要做的事情或代码块的正式属性进行假设，以及概率置信值。概率方法还提供了自然的方式学习代码之间的通讯和其他类型的文档,如需求、博客文章、评论,等等——这样的通讯将永远是不确定的,因为自然语言是模糊的,所以定量测量概率尤其自然提供的信心。正如我们在第5节中所讨论的，我们甚至可以说软件工程和编程语言研究的几乎每个领域都有潜在的机会来利用统计特性。

Although the “naturalness hypothesis” may not seem surprising, one should appreciate the root cause of “naturalness”. Naturalness of code seems to have a strong connection with the fact that developers prefer to write [5] and read [85] code that is conventional, idiomatic, and familiar because it helps understanding and maintaining software systems. Code that takes familiar forms is more transparent, in that its meaning is more readily apparent to an experienced reader. Thus, the naturalness hypothesis leads seamlessly to a “code predictability” notion, suggesting that code artifacts — from simple token sequences to formal verification statements — contain useful recurring and predictable patterns that can be exploited. “Naturalness” and “big code” should be viewed as instances of a more general concept that there is exploitable regularity across human-written code that can be “absorbed” and generalized by a learning component that can transfer its knowledge and probabilistically reason about new code.

虽然“自然性假说”似乎并不令人惊讶，但我们应该认识到“自然性”的根本原因。代码的自然性似乎与开发人员更喜欢编写[5]并阅读传统的、惯用的和熟悉的代码(因为这有助于理解和维护软件系统)有很强的联系。采用熟悉形式的代码更透明，因为经验丰富的读者更容易理解代码的含义。因此，自然假设无缝地引入了“代码可预测性”概念，这表明代码构件——从简单的令牌序列到正式的验证语句——包含有用的可重复使用的可预测模式。“自然性”和“大代码”应该被看作是一个更普遍的概念的实例，即人类编写的代码中存在可利用的规律性，可以通过一个学习组件“吸收”和推广，该组件可以转移其关于新代码的知识和概率推理。

[5] Miltiadis Allamanis, Earl T Barr, Christian Bird, and Charles Sutton. 2014. Learning Natural Coding Conventions. In Proceedings of the International Symposium on Foundations of Software Engineering (FSE).
[85] Vincent J Hellendoorn, Premkumar T Devanbu, and Alberto Bacchelli. 2015. Will they like this?: Evaluating code contributions with language models. In Proceedings of the Working Conference on Mining Software Repositories (MSR).

This article reviews the emerging area of machine learning and statistical natural language processing methods applied to source code.We focus on probabilistic models of code, that is, methods that estimate a distribution over all possible source files. Machine learning in probabilistic models has seen wide application throughout artificial intelligence, including natural language processing, robotics, and computer vision, because of its ability to handle uncertainty and to learn in the face of noisy data. One might reasonably ask why it is necessary to handle uncertainty and noise in software development tools, when in many cases the program to be analyzed is known (there is no uncertainty about what the programmer has written) and is deterministic. In fact, there are several interesting motivations for incorporating probabilistic modeling into machine learning methods for software development. First, probabilistic methods offer a principled method for handling uncertainty and fusing multiple, possibly ambiguous, sources of information. Second, probabilistic models provide a natural framework for connecting prior knowledge to data — providing a natural framework to design methods based on abstractions of statistical properties of code corpora.
In particular, we often wish to infer relationships between source code and natural language text, such as comments, bug reports, requirements documents, documentation, search queries, and so on. Because natural language text is ambiguous, it is useful to quantify uncertainty in the correspondence between code and text. Finally, when predicting program properties, probabilities provide a way to relax strict requirements on soundness: we can seek unsound methods that predict program properties based on statistical patterns in the code, using probabilities as a way to quantify the method’s confidence in its predictions.

本文综述了机器学习和统计自然语言处理方法应用于源代码的新兴领域。我们关注代码的概率模型，也就是说，评估所有可能源文件的分布的方法。概率模型中的机器学习在人工智能中得到了广泛的应用，包括自然语言处理、机器人和计算机视觉，因为它能够处理不确定性，并在有噪声的数据中学习。有人可能会合理地问，为什么有必要在软件开发工具中处理不确定性和噪声，而在许多情况下，要分析的程序是已知的(对于程序员编写的程序没有不确定性)，并且是确定的。事实上，将概率建模合并到软件开发的机器学习方法中有几个有趣的动机。首先，概率方法提供了处理不确定性和融合多个(可能是模糊的)信息源的原则方法。其次，概率模型为连接先验知识和数据提供了一个自然的框架——提供了一个自然的框架来设计基于代码库统计属性抽象的方法。
特别是，我们经常希望推断源代码和自然语言文本之间的关系，例如注释、bug报告、需求文档、文档、搜索查询等等。由于自然语言文本是模糊的，因此对代码与文本之间的通信中的不确定性进行量化是很有用的。最后，在预测程序属性时，概率提供了一种方法来放松对可靠性的严格要求:我们可以根据代码中的统计模式寻找不可靠的方法来预测程序属性，使用概率来量化方法对其预测的信心。

## 3 TEXT, CODE AND MACHINE LEARNING

Programming languages narrow the gap between computers and the human mind: they construct palatable abstractions out of a multitude of minute state transitions. Source code has two audiences
and is inherently bimodal: it communicates along two channels: one with humans, and one with computers. Humans must understand code to read and write it; computers must be able to
execute it. The bimodality of code drives the similarities and differences between it and text. Below, we discuss these similarities and differences with forward pointers to how they have been exploited, handled, or remain open. Although code and text are similar, code written in a general purpose programming languages, is a relatively new problem domain for existing ML and NLP techniques. Hindle et al. [87] not only showed that exploitable similarity exists between the two via an n-gram language model, but that code is even less surprising than text. Although it may seem manifestly obvious that code and text have many differences, it is useful to enumerate these differences carefully, as this allows us to gain insight into when techniques from NLP need to be modified to deal with code. Perhaps the most obvious difference is that code is executable and has formal syntax and semantics. We close by discussing how source code bimodality manifests itself as synchronization points between the algorithmic and explanatory channels.

编程语言缩小了计算机和人脑之间的差距:它们从大量的细微状态转换中构造出令人满意的抽象。源代码有两个受众，本质上是双峰的:它通过两个通道进行通信:一个是与人通信，另一个是与计算机通信。人类必须理解代码才能读写;计算机必须能够执行它。代码的双峰性决定了它与文本之间的相似性和差异性。下面，我们将通过前向指针来讨论这些相似性和不同点，这些前向指针指出了它们是如何被利用、处理或保持开放的。虽然代码和文本相似，但是用通用编程语言编写的代码对于现有的ML和NLP技术来说是一个相对较新的问题领域。Hindle等[87]不仅通过一个n-gram语言模型证明了两者之间存在可利用的相似性，而且代码甚至比文本更令人惊讶。虽然代码和文本有许多不同之处，但仔细列举这些不同之处是很有用的，因为这使我们能够了解何时需要修改NLP技术来处理代码。也许最明显的区别是代码是可执行的，并且具有正式的语法和语义。最后，我们将讨论源代码的双峰性如何作为算法通道和解释通道之间的同步点来表示。

- Executability. 
All code is executable; text often is not. So code is often semantically brittle — small changes (e.g. swapping function arguments) can drastically change the meaning of code; whereas natural language is more robust in that readers can often understand text even if it contains mistakes.  For example, existing work builds probabilistic models then applies strict formal constraints to filter their output (Section 4.1) or uses them to guide formal methods (Section 5.8). Nevertheless, further research on bridging formal and probabilistic methods is needed (Section 6.1).

所有代码都是可执行的;文本通常不是。因此，代码在语义上往往很脆弱——小的变化(例如交换函数参数)就能极大地改变代码的含义;而自然语言更强大，因为即使文本中有错误，读者也能理解。例如，现有的工作构建概率模型，然后应用严格的形式约束来过滤它们的输出(第4.1节)，或者使用它们来指导形式方法(第5.8节)。然而，需要进一步研究桥接形式方法和概率方法(第6.1节)。

Whether it is possible to translate between natural languages in away that completely preserves meaning is a matter of debate. Programming languages, on the other hand, can be translated between each other exactly, as all mainstream programming languages are Turing-complete. (That said, porting real-world programs to new languages and platforms remains challenging in practice [32]). ML techniques have not yet comprehensively tackled such problems and are currently limited to solely translating among languages with very similar characteristics, e.g. Java and C# (Section 6.1). Programming languages differ in their expressivity and intelligibility, ranging from Haskell to Malbolge2, with some especially tailored for certain problem domains; in contrast, natural languages are typically used to communicate across a wide variety of domains. Executability of code induces control and data flows within programs, which have only weak analogs in text. Finally, executability gives rise to additional modalities of code — its static and dynamic views (e.g. execution traces), which are not present in text. Learning over traces or flows are promising directions (Section 6.1).

是否有可能在完全保留意义的自然语言之间进行翻译是一个有争议的问题。另一方面，由于所有主流编程语言都是图灵完备的，所以编程语言之间可以精确地相互转换。(也就是说，将真实世界的程序移植到新的语言和平台在实践中仍然具有挑战性)。ML技术还没有全面解决这些问题，目前仅限于在具有非常相似特征的语言之间进行翻译，例如Java和c#(第6.1节)。编程语言的表达性和可理解性各不相同，从Haskell到Malbolge2，其中一些语言专门针对特定的问题领域;相比之下，自然语言通常用于跨多个领域进行通信。代码的可执行性导致程序内部的控制和数据流，而这些控制和数据流在文本中只有弱类比。最后，可执行性带来了额外的代码模式——它的静态和动态视图(例如，执行跟踪)，这在文本中是不存在的。通过跟踪或流学习是很有前途的方向(第6.1节)。

- Formality. 

Programming languages are formal languages, whereas formal languages are only mathematical models of natural language. As a consequence, programming languages are designed top-down by a few designers for many users. Natural languages, in contrast, emerge, bottom up, “through social dynamics” [46]. Natural languages change gradually, while programming languages exhibit punctuated change: new releases, like Python 3, sometimes break backward compatibility. Formatting can also be meaningful in code: Python’s whitespace sensitivity is the canonical example. Text has a robust environmental dependence, whereas code suffers from bit rot — the deterioration of software’s functionality through time because of changes in its environment (e.g. dependencies) — because all its explicit environmental interactions must be specified upfront and execution environments evolve much more quickly than natural languages.

编程语言是形式语言，而形式语言只是自然语言的数学模型。因此，编程语言是由少数设计师为许多用户自顶向下设计的。相反，自然语言则是自下而上地“通过社会动态”[46]出现的。自然语言是逐渐变化的，而编程语言则表现出间断的变化:像Python 3这样的新版本有时会破坏向后兼容性。格式化在代码中也很有意义:Python的空格敏感性就是一个典型的例子。文本具有健壮的环境依赖性，而代码则遭受着比特腐烂的痛苦——由于环境的变化(例如依赖性)，软件功能会随着时间的推移而退化——因为所有显式的环境交互都必须预先指定，而且执行环境的发展速度要比自然语言快得多。

[46] William Croft. 2008. Evolutionary linguistics. Annual Review of Anthropology (2008).

Source code’s formality facilities reuse. Solving a problem algorithmically is cognitively expensive, so developers actively try to reuse code [95], moving common functionality into libraries to facilitate reuse. As a result, usually functions are semantically unique within a project. Coding competitions or undergraduate projects are obvious exceptions. In contrast, one can find thousands of news articles describing an important global event. On the other hand, Gabel and Su [67] have found that locally, code is more pattern dense than text (Section 4.1). This has led to important performance improvements on some applications, such as code completion (Section 5.1).

源代码的形式工具重用。用算法解决问题的认知成本很高，因此开发人员积极地尝试重用代码[95]，将公共功能转移到库中以促进重用。因此，通常函数在项目中在语义上是惟一的。编码竞赛或本科项目是明显的例外。相比之下，你可以找到成千上万篇描述重大全球事件的新闻文章。另一方面，Gabel和Su[67]发现，在局部，代码比文本的模式密度更大(第4.1节)。这导致了一些应用程序的重要性能改进，例如代码完成(第5.1节)。

[67] Mark Gabel and Zhendong Su. 2010. A study of the uniqueness of source code. In Proceedings of the International Symposium on Foundations of Software Engineering (FSE).

Because programming languages are automatically translated into machine code, they must be syntactically, even to a first approximation semantically, unambiguous3. In contrast to NLP models, which must always account for textual ambiguity, probabilistic models of code can and do take advantage of the rich and unambiguous code structure. Although it is less pervasive, ambiguity remains a problem in the analysis of code, because of issues like polymorphism and aliasing. Section 4.2 and Section 5.4 discuss particularly notable approaches to handling them. Co-reference ambiguities can arise when viewing code statically, especially in dynamically typed languages (Section 6.1). The undefined behavior that some programming languages permit can cause semantic ambiguity and, in the field, syntactic problems can arise due to nonstandard compilers [24]; however, the dominance of a handful of compilers/interpreters for most languages ameliorates both problems.

因为程序设计语言是自动翻译成机器码的，所以它们必须在语法上，甚至在语义上都要达到一级近似，不能含糊不清。与必须始终考虑文本歧义的NLP模型相反，代码的概率模型能够而且确实利用了丰富而明确的代码结构。尽管歧义不那么普遍，但由于多态性和别名等问题，歧义仍然是代码分析中的一个问题。第4.2节和第5.4节讨论了处理这些问题的特别值得注意的方法。当静态地查看代码时，特别是在动态类型语言中，可能会出现共同引用歧义(第6.1节)。一些编程语言允许的未定义的行为可能导致语义模糊，并且在该领域中，由于非标准编译器[24]，可能会出现语法问题;然而，少数编译器/解释器在大多数语言中的主导地位改善了这两个问题。

[24] Al Bessey, Ken Block, Ben Chelf, Andy Chou, Bryan Fulton, Seth Hallem, Charles Henri-Gros, Asya Kamsky, Scott McPeak, and Dawson Engler. 2010. A few billion lines of code later: using static analysis to find bugs in the real world. Commun. ACM (2010).

- Cross-Channel Interaction. 
Code’s two channels, the algorithmic and the explanatory channels, interact through their semantic units, but mapping code units to textual units remains an open problem. Natural semantic units in code are identifiers, statements, blocks, and functions. None of these universally maps to textual semantic units. For example, identifiers, even verbose function names that seek to describe their function, carry less information than words like “Christmas” or “set”. In general, statements in code and sentences in text differ in how much background knowledge the reader needs in order to understand them in isolation; an arbitrary statement is far more likely to use domain-specific, even project-specific, names or neologisms than an arbitrary sentence is. Blocks vary greatly in length and semantics richness and often lack clear boundaries to a human reader. Functions are clearly delimited and semantically rich, but long. In text, a sentence is the natural multiword semantic unit and usually contains fewer than 50 words (tokens). Unfortunately, one cannot, however, easily equate them. A function differs from a sentence or a sequence of sentences, i.e. a paragraph, in that it is named and called, while, in general settings, sentences or paragraphs rarely have names or are referred to elsewhere in a text. Further, a single function acts on the world, so it is like a single action sentence, but is usually much longer, often containing hundreds of tokens, and usually performs multiple actions, making a function closer to a sequence of sentences, or a paragraph, but paragraphs are rarely solely composed of action sentences.

代码的两个通道，算法通道和解释通道，通过它们的语义单元进行交互，但是将代码单元映射到文本单元仍然是一个开放的问题。代码中的自然语义单元是标识符、语句、块和函数。所有这些都不能映射到文本语义单元。例如，标识符，甚至是试图描述其函数的冗长函数名，所包含的信息都少于“Christmas”或“set”之类的单词。一般来说，代码中的语句和文本中的句子的区别在于读者需要多少背景知识才能独立地理解它们;一个任意的语句比一个任意的句子更可能使用领域特定的、甚至是项目特定的名称或新词。块在长度和语义丰富度上差异很大，而且对人类读者来说常常缺乏清晰的边界。函数有明确的分隔，语义丰富，但很长。在文本中，句子是自然的多词语义单位，通常包含少于50个单词(标记)。然而，不幸的是，人们不能轻易地把它们等同起来。函数与句子或句子序列(即段落)的不同之处在于，函数被命名和调用，而在一般情况下，句子或段落很少有名称或在文本的其他地方引用。进一步说,一个函数作用于这个世界,所以它就像一个单一动作的句子,但通常是更长的时间,通常包含数以百计的令牌,并且通常执行多个操作,使一个函数接近一系列句子,段落或一个段落,但很少单独行动的句子组成的。

Additionally, parse trees of sentences in text tend to be diverse, short, and shallow compared to abstract syntax trees of functions, which are usually much deeper with repetitive internal structure. Code bases are multilingual (i.e. contain code in more than one programming language, e.g. Java and SQL) with different tasks described in different languages, more frequently than text corpora; this can drastically change the shape and frequency of its semantic units. Code has a higher neologism rate than text. Almost 70% of all characters are identifiers and a developer must choose a name for each one [50]; when writing text, an author rarely names new things but usually chooses an existing word to use. Existing work handles code’s neologism rate by introducing cache mechanisms or decomposing identifiers at a subtoken level (Section 4.1).

此外，与函数的抽象语法树相比，文本中句子的解析树往往是多样化的、短小的、浅显的，而函数的抽象语法树通常要深得多，内部结构重复。代码库是多语言的(即包含不止一种编程语言的代码，如Java和SQL)，使用不同的语言描述不同的任务，比文本语料库更频繁;这可以极大地改变其语义单元的形状和频率。代码比文本具有更高的新词率。几乎70%的字符都是标识符，开发人员必须为每个[50]选择一个名称;写作时，作者很少给新事物命名，但通常会选择一个已有的词来使用。现有工作通过引入缓存机制或在子令牌级别分解标识符来处理代码的新词率(第4.1节)。

Determining which semantic code unit is most useful for which task is an open question. Consider the problem of automatically generating comments that describe code, which can be formalized as a machine translation problem from code to text. Statistical machine translation approaches learn from an aligned corpus. Statement-granular alignment yields redundant comments, while function granular alignment has saliency issues (Section 5.5). As another example, consider code search, where search engines must map queries into semantic code units. Perhaps the answer will be in maps from code to text whose units vary by granularity or context (Section 6.1).

确定哪个语义代码单元对哪个任务最有用是一个悬而未决的问题。考虑自动生成描述代码的注释的问题，可以将其形式化为从代码到文本的机器翻译问题。统计机器翻译方法从对齐的语料库中学习。语句粒度对齐产生冗余注释，而函数粒度对齐存在显著性问题(第5.5节)。另一个例子是代码搜索，搜索引擎必须将查询映射到语义代码单元。也许答案将在从代码到文本的映射中找到，这些映射的单元因粒度或上下文而异(第6.1节)。

## 4 PROBABILISTIC MODELS OF CODE

In this section, we turn our attention to probabilistic machine learning models of source code. A probabilistic model of source code is a probability distribution over code artifacts. As allmodels do, probabilistic machine learning models make simplifying assumptions about the modeled domain. These assumptions make the models tractable to learn and use, but introduce error. Since each modelmakes different assumptions, eachmodel has its own strengths and weaknesses and is more suitable for some applications. In this section, we group existing work into families of models and discuss their assumptions. To group these family of models in terms of shared design choices, we separate these models into three categories, based on the form of the equation of the modeled probability distribution and their inputs and outputs, with the caveat that some models fall into multiple categories.We also discuss how and why these models differ from their natural language counterparts. In Section 5, we discuss applications of these models in software engineering and programming languages.

在本节中，我们将注意力转向源代码的概率机器学习模型。源代码的概率模型是代码构件上的概率分布。与所有模型一样，概率机器学习模型简化了对建模域的假设。这些假设使得模型易于学习和使用，但也带来了错误。由于每个模型的假设不同，每个模型都有自己的优缺点，更适合某些应用。在本节中，我们将现有的工作分组到模型家族中，并讨论它们的假设。为了根据共享的设计选择对这些模型族进行分组，我们根据建模概率分布方程的形式及其输入和输出将这些模型分为三类，但需要注意的是，有些模型属于多个类别。我们还讨论了这些模型如何以及为什么与自然语言模型不同。在第5节中，我们将讨论这些模型在软件工程和编程语言中的应用。

Code-generating Models define a probability distribution over code by stochastically modeling the generation of smaller and simpler parts of code, e.g. tokens or AST nodes.
Representational Models of Code take an abstract representation4 of code as input. Example representations include token contexts or data flow. The resulting model yields a
conditional probability distribution over code element properties, like the types of variables, and can predict them.
Pattern Mining Models infer, without supervision, a likely latent structure within code.
These models are an instantiation of clustering in the code domain; they can find reusable and human-interpretable patterns.

代码生成模型通过随机建模生成更小和更简单的代码部分(例如令牌或AST节点)来定义代码上的概率分布。
代码的表示模型以代码的抽象表示作为输入。示例表示包括令牌上下文或数据流。生成的模型生成代码元素属性(如变量类型)上的条件概率分布，并能够预测它们。
模式挖掘模型在没有监督的情况下，推断出代码中可能存在的潜在结构。这些模型是代码域中集群的实例;他们可以找到可重用的和可人类解释的模式。

Code-generating models find analogues in generative models of text, such as language models and machine translation models. Code representational models are analogous to systems for named entity recognition, text classification, and sentiment analysis in NLP. Finally, code pattern mining models are analogous to probabilistic topic models and ML techniques for mining structured information (e.g. knowledge-bases) from text. To simplify notation below, we use c to denote an arbitrary code abstraction, like an AST.

代码生成模型可以在文本的生成模型中找到类似物，比如语言模型和机器翻译模型。代码表示模型类似于NLP中的命名实体识别、文本分类和情感分析系统。最后，代码模式挖掘模型类似于从文本中挖掘结构化信息(例如知识库)的概率主题模型和ML技术。为了简化下面的表示法，我们使用c表示任意的代码抽象，比如AST。

### 4.1 Code-generating Probabilistic Models of Source Code

Code-generating probabilistic models of code are probability distributions that describe a stochastic process for generating valid code, i.e. they model how code is written. Given training data D, an output code representation c, and a possibly empty context C(c), these models learn the probability distribution PD(c|C(c)) and sample PD to generate code. When C(c) = , the probability distribution PD is a language model of code, i.e. it models how code is generated when no external context information is available. When C(c) is a non-code modality (e.g. natural language), PD describes a code-generative multimodal model of code.When C(c) is also code, the probability distribution PD is a transducer model of code. In addition to generating code, by definition, code generating probabilistic models act as a scoring function, assigning a non-zero probability to every possible snippet of code. This score, sometimes referred to as “naturalness” of the code [87], suggests how probable the code is under a learned model.

代码生成概率模型是描述生成有效代码的随机过程的概率分布，即它们对代码的编写方式建模。给定训练数据D、输出代码表示形式c和可能的空上下文c (c)，这些模型学习概率分布PD(c| c (c))和样本PD来生成代码。当C (C) =,概率分布PD的语言模型代码,即它模型如何生成代码在没有外部上下文信息是可用的。当C(C)是非编码模态(如自然语言)时，PD描述了代码生成的多模态模型。当C(C)也是码时，概率分布PD是一个码的换能器模型。除了生成代码之外，根据定义，生成概率模型的代码还充当评分函数，为每一个可能的代码片段分配非零概率。这个分数，有时被称为代码的“自然性”[87]，表明代码在学习模型下的可能性有多大。

Since code-generating models predict the complex structure of code, they make simplifying assumptions about the generative process and iteratively predict elements of the code to generate a full code unit, e.g. code file or method. Because of code’s structural complexity and the simplifying assumptions these models make to cope with it, none of the existing models in the literature generate code that always parses, compiles, and executes. Some of the models do, however, impose constraints that take code structure into account to remove some inconsistencies; for instance, [126] only generate variables declared within each scope.

由于代码生成模型预测代码的复杂结构，因此它们简化了生成过程的假设，并迭代地预测代码的元素，以生成完整的代码单元，例如代码文件或方法。由于代码的结构复杂性和这些模型为处理它所做的简化假设，文献中没有一个现有模型生成总是解析、编译和执行的代码。然而，有些模型确实施加了考虑代码结构的约束，以消除一些不一致;例如，[126]只生成在每个范围内声明的变量。

[126] Chris Maddison and Daniel Tarlow. 2014. Structured Generative Models of Natural Source Code. In Proceedings of the International Conference on Machine Learning (ICML)

We structure the discussion about code-generating models of code as follows. We first discuss how models in this category generate code and then we show how the three different types of models (language, transducer and multimodal models) differ.

我们对代码生成模型的讨论结构如下。我们首先讨论这个类别中的模型如何生成代码，然后展示三种不同类型的模型(语言、换能器和多模态模型)如何不同。

#### 4.1.1 Representing Code in Code-Generating Models. 

Probabilistic models for generating structured objects are widely in use in machine learning and natural language processing with a wide range of applications. Machine learning research is considering a wide range of structures from natural language sentences to chemical structures and images. Within the source code domain, we can broadly find three categories of models based on the way they generate code’s structure: token-level models that generate code as a sequence of tokens, syntactic models that generate code as a tree and semantic models that generate graph structures. Note that this distinction is about the generative process and not about the information used within this process. For example Nguyen et al. [144] uses syntactic context, but is classified as a token-level model that generates tokens.

生成结构化对象的概率模型在机器学习和自然语言处理中得到了广泛的应用。机器学习研究正在考虑从自然语言句子到化学结构和图像的广泛结构。在源代码域中，我们可以根据生成代码结构的方式大致找到三类模型:将代码生成为令牌序列的令牌级别模型、将代码生成为树的语法模型和生成图结构的语义模型。注意，这个区别是关于生成过程的，而不是关于这个过程中使用的信息。例如Nguyen等人[144]使用语法上下文，但被归类为生成令牌的令牌级别模型。

- Token-level Models (sequences). 
Sequence-based models are commonly used because of their simplicity. They view code as a sequence of elements, usually code tokens or characters, i.e. c = t1 . . . tM. Predicting a large sequence in a single step is infeasible due to the exponential number of possible sequences; for a set of V elements, there are |V |N sequences of length N. Therefore, most sequence-based models predict sequences by sequentially generating each element, i.e. they model the probability distribution P(tm|t1 . . . tm−1, C(c)). However, directly modeling this distribution is impractical and all models make different simplifying assumptions.

基于序列的模型由于其简单性而被广泛使用。它们将代码看作元素的序列，通常是代码标记或字符，即c = t1…tM。由于可能的序列数呈指数增长，单步预测大序列是不可行的;对于一组V元素，有长度为N的|V |N个序列。因此，大多数基于序列的模型通过序列生成每个元素来预测序列，即对概率分布P(tm|t1…tm−1 C (C))。然而，直接对这种分布进行建模是不切实际的，所有模型都做出了不同的简化假设。

The n-gram model has been a widely used sequence-based model, most commonly used as a language model. It is an effective and practical LM for capturing local and simple statistical dependencies in sequences. n-gram models assume that tokens are generated sequentially, left-to-right and that the next token can be predicted using only the previous n −1 tokens. The consequence of capturing a short context is that n-gram models cannot handle long-range dependencies, notably scoping information. Formally, the probability of a token tm, is conditioned on the context C(c) (if any) and the generated sequence so far t1 . . . tm−1, which is assumed to depend on only the previous n − 1 tokens. Under this assumption, we write

n-gram模型是一种应用广泛的基于序列的模型，最常用作语言模型。它是一种有效和实用的LM，用于捕获序列中局部和简单的统计依赖关系。n-gram模型假定标记是按顺序从左到右生成的，并且只能使用前面的n- 1个标记来预测下一个标记。捕获短上下文的结果是，n-gram模型不能处理长期依赖关系，尤其是范围信息。在形式上，令牌tm的概率取决于上下文C(C)(如果有的话)和到目前为止t1生成的序列……tm - 1，假定它只依赖于前面的n - 1令牌。在这个假设下，我们写



















