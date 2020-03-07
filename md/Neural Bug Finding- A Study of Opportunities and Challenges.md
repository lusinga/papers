# Neural Bug Finding: A Study of Opportunities and Challenges

## Abstract

Static analysis is one of the most widely adopted techniques to find software bugs before code is put in production. Designing and implementing effective and efficient static analyses is difficult and requires high expertise, which results in only a few experts able to write such analyses. This paper explores the opportunities and challenges of an alternative way of creating static bug detectors: neural bug finding. The basic idea is to formulate bug detection as a classification problem, and to address this problem with neural networks trained on examples of buggy and non-buggy code. We systematically study the effectiveness of this approach based on code examples labeled by a state-of-the-art, static bug detector. Our results show that neural bug finding is surprisingly effective for some bug patterns, sometimes reaching a precision and recall of over 80%, but also that it struggles to understand some program properties obvious to a traditional analysis. A qualitative analysis of the results provides insights into why neural bug finders sometimes work and sometimes do not work. We also identify pitfalls in selecting the code examples used to train and validate neural bug finders, and propose an algorithm for selecting effective training data.

静态分析是在代码投入生产之前发现软件bug的最广泛采用的技术之一。设计和实现有效的、高效的静态分析是困难的，并且需要很高的专业知识，这导致只有少数专家能够编写这样的分析。本文探讨了创建静态bug检测器的另一种方法的机遇和挑战:神经bug查找。其基本思想是将bug检测作为一个分类问题来表示，并使用针对有bug和无bug代码示例训练的神经网络来解决这个问题。我们系统地研究了这种方法的有效性，它基于由最先进的静态错误检测器标记的代码示例。我们的结果表明，神经缺陷的发现对于某些缺陷模式是非常有效的，有时可以达到80%以上的准确率和召回率，但它也很难理解一些传统分析中明显存在的程序属性。定性分析的结果提供了深入了解为什么神经缺陷探测器有时工作，有时不工作。我们也指出了在选择用于训练和验证神经缺陷查找器的代码示例时的缺陷，并提出了一种选择有效训练数据的算法。

## I. INTRODUCTION

A popular way of finding software bugs early during the development process is static analysis tools that search a code base for instances of common bug patterns. These tools, which we here call bug detectors, often consist of a scalable static analysis framework and an extensible set of checkers that each search for instances of a specific bug pattern. Examples of bug detectors include the pioneering FindBugs tool [1], its successor SpotBugs, Google’s Error Prone tool [2], and the Infer tool by Facebook [3].

在开发过程的早期发现软件缺陷的一种流行的方法是静态分析工具，它为常见缺陷模式的实例搜索代码基础。这些工具，我们在这里称之为bug检测器，通常由一个可伸缩的静态分析框架和一组可扩展的检查器组成，每个检查器搜索特定bug模式的实例。bug检测器的例子包括先驱的FindBugs工具[1]、它的继承者SpotBugs、谷歌的出错倾向工具[2]和Facebook的推断工具[3]。

Despite the overall success of static bug detection tools, there still remains a lot of potential for improvement. A recent study that applied state-of-the-art bug detectors to a set of almost 600 real-world bugs shows that over 95% of the bugs are currently missed [4]. The main reason is that various different bug patterns exist, each of which needs a different bug detector. These bug detectors must be manually created, typically by program analysis experts, and they require significant finetuning to find actual bugs without overwhelming developers with spurious warnings. Bug detectors often require hundreds of lines of code each, even for bug patterns that seem trivial to find at first sight and when being built on top of a static analysis framework.

尽管静态bug检测工具总体上取得了成功，但是仍然有很大的改进潜力。最近的一项研究对一组近600个真实世界的bug应用了最先进的bug检测器，结果显示超过95%的bug目前都没有被发现。主要原因是存在各种不同的bug模式，每种模式都需要不同的bug检测器。这些错误检测器必须手动创建，通常由程序分析专家创建，并且它们需要进行大量的微调来发现实际的错误，而不需要用虚假的警告将开发人员淹没。Bug检测器通常需要数百行代码，甚至对于那些乍一看似乎微不足道的Bug模式，以及在构建于静态分析框架之上的Bug模式也是如此。

This paper studies a novel way of creating bug detectors: neural bug finding. Motivated by the huge success of neural networks for various software engineering tasks [5], we ask a simple question: Can we automatically learn bug detectors from data, instead of implementing program analyses manually? Giving a positive answer to this question has the potential of complementing existing bug detectors with additional checkers that address previously ignored bug patterns. Moreover, it may enable non-experts in program analysis, e.g., ordinary software developers, to contribute to the creation of bug detectors.

本文研究了一种新的缺陷检测方法:神经缺陷检测。在各种软件工程任务[5]的神经网络的巨大成功的激励下，我们提出了一个简单的问题:我们可以从数据中自动学习错误检测器，而不是手工实现程序分析吗?对这个问题给出一个肯定的回答有可能补充现有的bug检测器，使用额外的检查程序来处理以前忽略的bug模式。此外，它可以使程序分析中的非专业人员，例如普通的软件开发人员，为错误检测器的创建做出贡献。

Given the importance of bug detection and the power of neural networks, the intersection of these two areas so far has received surprisingly little attention. Existing work focuses on learning-based defect prediction [6], which ranks entire files by their probability to contain any kind of bug, whereas we here aim at pinpointing code that suffers from a specific kind of bug. Other work addresses the problems of predicting code changes [7], predicting identifier names [8]–[10], and predicting how to complete partial code [11]–[13], which are complementary to detecting bugs. The perhaps closest existing work is DeepBugs [14], which trains a neural network to find name-related bugs, and learning-based techniques for identifying security vulnerabilities [15]–[17]. While these approaches show that neural bug finding is possible for a specific class of bugs, we here study the potential of neural bug finding in much more detail and for a broader range of code issues.

考虑到bug检测的重要性和神经网络的强大功能，这两个领域的交集到目前为止几乎没有受到关注。现有的工作侧重于基于学习的缺陷预测[6]，它根据包含任何类型的缺陷的概率对整个文件进行排序，而我们在这里的目标是找出遭受特定类型缺陷的代码。其他工作包括预测代码更改[7]、预测标识符名称[8]-[10]以及预测如何完成部分代码[11]-[13]，这些都是对bug检测的补充。目前最接近的工作可能是DeepBugs[14]，它训练一个神经网络来发现与名称相关的bug，以及基于学习的技术来识别安全漏洞[15]-[17]。虽然这些方法表明，对于特定类别的bug，神经缺陷的发现是可能的，但我们在这里更详细地研究了神经缺陷发现的潜力，并对更广泛的代码问题进行了研究。

Automatically learning bug detectors requires addressing two problems: (1) Obtaining sufficient training data, e.g., consisting of buggy and non-buggy code examples. (2) Training a model that identifies bugs, e.g., by distinguishing buggy code from non-buggy code. The first problem could be addressed by automatically seeding bugs into code, by extracting buggy code examples from version histories, or by manually labeling code examples as instances of specific bug patterns. In this work, we sidestep the first problem and study whether given sufficient training data, the second problem is tractable. Our work therefore does not yield a ready-to-deploy bug detection tool, but rather novel insights into what kinds of bugs neural bug finding can and cannot find. We believe that thoroughly studying this question in isolation is an important step forward toward the ultimate goal of neural bug finding.

自动学习bug检测器需要解决两个问题:(1)获取足够的训练数据，例如包含bug和非bug的代码示例。(2)训练识别bug的模型，例如，通过区分有bug的代码和无bug的代码。第一个问题可以通过自动将bug播种到代码中来解决，通过从版本历史中提取有bug的代码示例来解决，或者通过手动将代码示例标记为特定bug模式的实例来解决。在这项工作中，我们避开了第一个问题，研究是否有足够的训练数据，第二个问题是可处理的。因此，我们的工作并没有提供一个随时可以部署的bug检测工具，而是提供了一种新颖的见解，了解哪些类型的bug可以找到，哪些类型的bug不能找到。我们相信，单独深入研究这个问题是朝着发现神经缺陷的最终目标迈出的重要一步。

To study the potential of learned bug detectors while sidestepping the problem of obtaining labeled training data, we use an existing, traditionally developed bug detector as a generator of training data. To this end, we run the existing bug detector on a corpus of code to obtain warnings about specific kinds of bugs. Using these warnings and their absence as a ground truth, we then train a neural model to distinguish code with a particular kind of warning from code without such a warning. For example, we train a model that predicts whether a piece of code uses reference equality instead of value equality for comparing objects in Java. This setup allows us to assess to what extent neural bug finding can imitate existing bug detectors.

为了研究学习的错误检测器的潜力，同时避开获得标记的训练数据的问题，我们使用一个现有的、传统开发的错误检测器作为训练数据的生成器。为此，我们在一个代码集上运行现有的bug检测器，以获得关于特定类型bug的警告。利用这些警告和它们的缺失作为一个基本事实，然后我们训练一个神经模型来区分具有特定类型警告的代码和没有此类警告的代码。例如，我们训练一个模型来预测一段代码是否使用引用相等而不是值相等来比较Java中的对象。这种设置允许我们评估神经缺陷发现在多大程度上可以模拟现有的缺陷检测器。

One drawback of using an existing bug detector as the data generator is that some warnings may be spurious and that some bugs may be missed. To mitigate this problem, we focus on bugs flagged by bug detectors that are enabled in production in a major company and that empirically show false positive rates below 10% [18]. Another drawback is that the learned bug detectors are unlikely to outperform the static analyzers they learn from. However, the purpose of this work is to study whether training a model for neural bug finding is feasible, whereas we leave the problem of obtaining training data beyond existing static analyzers as future work.

使用现有的bug检测器作为数据生成器的一个缺点是，有些警告可能是假的，有些bug可能会被忽略。为了缓解这个问题，我们主要关注由bug检测器标记的bug，这些bug检测器在一家大公司的产品中启用，并且根据经验显示错误阳性率低于10%的[18]。另一个缺点是，所学习的错误检测器不太可能胜过它们所学习的静态分析程序。然而，这项工作的目的是研究训练一个用于发现神经缺陷的模型是否可行，而我们把获取现有静态分析器之外的训练数据的问题留给未来的工作。

The main findings of our study include the following: 
- Learned bug detectors identify instances of a surprisingly large number of bug patterns with precision and recall over 80%. At the same time, the learned models sometimes fail to understand program properties that a traditional analysis easily finds. 
- Neural bug finding works because the models learn to identify common syntactic patterns correlated with bugs, particular API misuses, or common instances of a more general bug pattern. 
- The composition of the training data a neural bug finding model is learned from has a huge impact on the model’s effectiveness. We study several strategies for composing training data and present a novel algorithm for selecting effective training examples. 
- More training data yields more effective models, but surprisingly small data sets, e.g., of only 77 examples, can still yield effective neural bug detectors. 
- Following a naive approach for validating a learned bug detector may lead to very misleading results.

本研究的主要发现包括:
- 学习的错误检测器识别数量惊人的错误模式实例，精确度和召回率超过80%。同时，所学习的模型有时不能理解传统分析很容易发现的程序属性。
- 神经缺陷发现之所以有效，是因为这些模型能够识别与缺陷、特定API误用或更一般的缺陷模式的常见实例相关的常见语法模式。
- 神经缺陷发现模型学习的训练数据的组成对模型的有效性有很大的影响。研究了训练数据合成的几种策略，提出了一种选择有效训练样本的新算法。
- 更多的训练数据产生更有效的模型，但令人惊讶的是，小的数据集，例如，只有77个例子，仍然可以产生有效的神经缺陷检测器。
- 使用一种简单的方法来验证一个学习过的bug检测器可能会导致非常误导的结果。

In summary, this paper contributes the first comprehensive study of neural bug finding. The study reveals novel insights into the opportunities and challenges associated with this novel way of creating bug detectors. We believe that our work is a step forward toward complementing traditional ways of creating bug detectors. In particular, the study provides a basis for future work on generating training data for neural bug finding, for developing machine learning models that reason about rich representations of code, and for building neural bug finding tools. To fuel these and other lines of future work, we make our tool and data set publicly available.

综上所述，本文首次对神经缺陷的发现进行了全面的研究。这项研究揭示了与这种创建bug检测器的新方法相关的机遇和挑战。我们相信，我们的工作是朝着补充传统的错误检测器的方向前进了一步。特别是，这项研究为将来的工作提供了基础，这些工作包括生成用于发现神经缺陷的训练数据，开发关于丰富代码表示的机器学习模型，以及构建神经缺陷发现工具。为了支持这些和其他未来的工作，我们公开了我们的工具和数据集。

## VI. RELATED WORK

### A. Static Bug Finding

Techniques for scanning source code for particular bug patterns go back to the pioneering lint tool [38]. More recent tools deployed in industry include Error Prone [2], which is used at Google and serves as an oracle for our study, and Infer [3], which is used at Facebook. Detailed accounts of deploying static bug detectors consider a name-based static checker [39], applying the FindBugs tool [40], [41], and a rule inferencebased static bug detector [42]. Research on static bug finding includes work on finding API misuses [43]–[45], name-based bug detection [46], security bugs [47], finding violations of inferred programmer beliefs [48], and other kinds of anomaly detection [49]. These approaches involve significant manual effort for creating and tuning the bug detectors, whereas we here study bug detectors learned from examples only.

- [38] S. C. Johnson, “Lint, a C program checker,” 1978.
- [2] E. Aftandilian, R. Sauciuc, S. Priya, and S. Krishnan, “Building useful program analysis tools using an extensible java compiler,” in 12th IEEE International Working Conference on Source Code Analysis and Manipulation, SCAM 2012, Riva del Garda, Italy, September 23-24, 2012, 2012, pp. 14–23.
- [3] C. Calcagno, D. Distefano, J. Dubreil, D. Gabi, P. Hooimeijer, M. Luca, P. O’Hearn, I. Papakonstantinou, J. Purbrick, and D. Rodriguez, “Moving fast with software verification,” in NASA Formal Methods Symposium. Springer, 2015, pp. 3–11.
- [39] A. Rice, E. Aftandilian, C. Jaspan, E. Johnston, M. Pradel, and Y. Arroyo-Paredes, “Detecting argument selection defects,” in Con- ference on Object-Oriented Programming, Systems, Languages, and Applications (OOPSLA), 2017.
- [40] N. Ayewah, D. Hovemeyer, J. D. Morgenthaler, J. Penix, and W. Pugh, “Using static analysis to find bugs,” IEEE Software, vol. 25, no. 5, pp. 22–29, 2008. 
- [41] N. Ayewah and W. Pugh, “The google findbugs fixit,” in Proceedings of the Nineteenth International Symposium on Software Testing and Analysis, ISSTA 2010, Trento, Italy, July 12-16, 2010, 2010, pp. 241–252.
- [42] A. Bessey, K. Block, B. Chelf, A. Chou, B. Fulton, S. Hallem, C. Henri- Gros, A. Kamsky, S. McPeak, and D. R. Engler, “A few billion lines of code later: Using static analysis to find bugs in the real world,” Communications of the ACM, vol. 53, no. 2, pp. 66–75, 2010. (coverity)
- [43] A. Wasylkowski and A. Zeller, “Mining temporal specifications from object usage,” in International Conference on Automated Software Engineering (ASE). IEEE, 2009, pp. 295–306.
- [44] T. T. Nguyen, H. A. Nguyen, N. H. Pham, J. M. Al-Kofahi, and T. N. Nguyen, “Graph-based mining of multiple object usage patterns,” in European Software Engineering Conference and Symposium on the Foundations of Software Engineering (ESEC/FSE). ACM, 2009, pp. 383–392.
- [45] M. Pradel, C. Jaspan, J. Aldrich, and T. R. Gross, “Statically checking API protocol conformance with mined multi-object specifications,” in International Conference on Software Engineering (ICSE), 2012, pp. 925–935.
- [46] M. Pradel and T. R. Gross, “Detecting anomalies in the order of equallytyped method arguments,” in International Symposium on Software Testing and Analysis (ISSTA), 2011, pp. 232–242.
- [47] F. Brown, S. Narayan, R. S.Wahby, D. R. Engler, R. Jhala, and D. Stefan, “Finding and preventing bugs in javascript bindings,” in 2017 IEEE Symposium on Security and Privacy, SP 2017, San Jose, CA, USA, May 22-26, 2017, 2017, pp. 559–578. 
- [48] D. Engler, D. Y. Chen, S. Hallem, A. Chou, and B. Chelf, “Bugs as deviant behavior: A general approach to inferring errors in systems code,” in Symposium on Operating Systems Principles (SOSP). ACM, 2001, pp. 57–72. 
- [49] B. Liang, P. Bian, Y. Zhang, W. Shi, W. You, and Y. Cai, “Antminer: Mining more bugs by reducing noise interference,” in ICSE, 2016.

扫描特定bug模式的源代码的技术可以追溯到最早的lint工具[38]。最近在业界部署的工具包括谷歌使用的易出错[2]和Facebook使用的推断[3]。谷歌是我们的研究对象。部署静态bug检测器的详细信息考虑一个基于名称的静态检查器[39]，应用FindBugs工具[40]、[41]和一个基于规则推理的静态bug检测器[42]。静态bug发现的研究包括API误用[43]-[45]、基于名称的bug检测[46]、安全bug[47]、违反推断程序员信念[48]、以及其他类型的异常检测[49]。这些方法需要大量的手工工作来创建和调优bug检测器，而我们在这里只从示例中学习bug检测器。

The presence of false positives, a problem shared by both traditional and learned bug detectors, motivates work on prioritizing analysis warnings, e.g., based on the frequency of true and false positives [50], the version history of a program [51], and statistical models based on features of warnings and code [52]. These efforts are orthogonal to the bug detection problem addressed in this paper, and could possibly be combined with neural bug detectors.

误报的存在，是一个传统的和学习过的错误检测器所共有的问题，它激发了对分析警告进行优先排序的工作，例如，基于真误和误报的频率[50]，程序[51]的版本历史，以及基于警告特性和代码[52]的统计模型。这些工作与本文所解决的错误检测问题是正交的，并且可能与神经错误检测器相结合。

### B. Machine Learning and Language Modeling for Bug Finding

Learned models are becoming increasingly popular for bug finding. DeepBugs exploits identifier names, e.g., of variables and methods, to find buggy code [14]. Vasic et al. [53] use pointer networks to jointly find and fix variable mis-use bugs. Choi et al. train a memory network [54] to predict whether a piece of code may cause a buffer overrun [15]. A broader set of coding mistakes that may cause vulnerabilities is considered in other learning-based work [16]. Harer et at. [17] train a CNN to classify methods as vulnerable or not based on heuristics built on labels from a static analyzer. The main contribution of our work is to systematically study general neural bug detection and to predict the bug kind.

- [14] M. Pradel and K. Sen, “DeepBugs: A learning approach to name-based bug detection,” in OOPSLA, 2018.
- [53] M. Vasic, A. Kanade, P. Maniatis, D. Bieber, and R. singh, “Neural program repair by jointly learning to localize and repair,” in International Conference on Learning Representations, 2019. [Online]. Available: https://openreview.net/forum?id=ByloJ20qtm
- [54] J. Weston, S. Chopra, and A. Bordes, “Memory networks,” CoRR, vol. abs/1410.3916, 2014.
- [15] M. je Choi, S. Jeong, H. Oh, and J. Choo, “End-to-end prediction of buffer overruns from raw source code via neural memory networks,” CoRR, vol. abs/1703.02458, 2017.
- [16] Z. Li, S. X. Deqing Zou and, X. Ou, H. Jin, S. Wang, Z. Deng, and Y. Zhong, “VulDeePecker: A deep learning-based system for vulnerability detection,” in NDSS, 2018. 
- [17] J. A. Harer, L. Y. Kim, R. L. Russell, O. Ozdemir, L. R. Kosta, A. Rangamani, L. H. Hamilton, G. I. Centeno, J. R. Key, P. M. Ellingwood, M. W. McConley, J. M. Opper, S. P. Chin, and T. Lazovich, “Automated software vulnerability detection with machine learning,” CoRR, 2018. [Online]. Available: http://arxiv.org/abs/1803.04497

学习模型在bug发现方面正变得越来越流行。DeepBugs利用标识符名称(例如变量和方法)来查找有bug的代码[14]。Vasic等人的[53]使用指针网络共同发现和修复变量滥用的错误。Choi等人训练内存网络[54]来预测一段代码是否会导致缓冲区溢出[15]。在其他基于学习的工作[16]中考虑了可能导致漏洞的一组更广泛的编码错误。哈勒尔等。[17]训练CNN根据基于静态分析器的标签构建的启发式将方法分类为易受攻击的或非易受攻击的。本工作的主要贡献是系统地研究了一般的神经缺陷检测方法，并对缺陷类型进行了预测。

Instead of classifying whether a piece of code suffers from a bug, anomaly detection approaches search for code that stands out and therefore may be buggy. Bugram uses a statistical language model that warns about uncommon ngrams of tokens [25]. Salento learns a probabilistic model of API usages and warns about unusual usages [55]. In contrast to our work, these techniques learn from non-buggy examples only. Ray et al. [56] explains why this is possible and shows that buggy code is less natural than non-buggy code.

- [25] S. Wang, D. Chollak, D. Movshovitz-Attias, and L. Tan, “Bugram: bug detection with n-gram language models,” in Proceedings of the 31st IEEE/ACM International Conference on Automated Software Engineer- ing, ASE 2016, Singapore, September 3-7, 2016, 2016, pp. 708–719.
- [55] V. Murali, S. Chaudhuri, and C. Jermaine, “Bayesian specification learning for finding api usage errors,” in FSE, 2017.
- [56] B. Ray, V. Hellendoorn, S. Godhane, Z. Tu, A. Bacchelli, and P. T. Devanbu, “On the "naturalness" of buggy code,” in Proceedings of the 38th International Conference on Software Engineering, ICSE 2016, Austin, TX, USA, May 14-22, 2016, 2016, pp. 428–439.

异常检测方法不是对一段代码是否存在bug进行分类，而是搜索突出的代码，因此可能存在bug。Bugram使用一种统计语言模型来警告不常见的标记[25]。Salento学习API用法的概率模型，并对异常用法[55]发出警告。与我们的工作相反，这些技术只从没有错误的示例中学习。Ray等人解释了为什么这是可能的，并表明有bug的代码比没有bug的代码更不自然。

Orthogonal to bug detection is the problem of defect prediction [57], [58]. Instead of pinpointing specific kinds of errors, as our work, it predicts whether a given software component will suffer from any bug at all. Wang et al. [6] propose a neural network-based model for this task [6].

- [57] N. E. Fenton and M. Neil, “A critique of software defect prediction models,” IEEE Transactions on software engineering, vol. 25, no. 5, pp. 675–689, 1999. 
- [58] T. Zimmermann, N. Nagappan, H. Gall, E. Giger, and B. Murphy, “Cross-project defect prediction: a large scale experiment on data vs. domain vs. process,” in Proceedings of the the 7th joint meeting of the European software engineering conference and the ACM SIGSOFT symposium on The foundations of software engineering. ACM, 2009, pp. 91–100.
- [6] S. Wang, T. Liu, and L. Tan, “Automatically learning semantic features for defect prediction,” in ICSE, 2016, pp. 297–308.

与缺陷检测正交的是缺陷预测问题[57]、[58]。它不是像我们的工作那样精确地指出特定类型的错误，而是预测给定的软件组件是否会遭受任何错误。Wang等人提出了一个基于神经网络的任务[6]模型。

### C. Machine Learning on Programs

Beyond bug detection, machine learning has been applied to other programming-related tasks [5], such as predicting identifier names [8], [10] and types [8], [9], [59]. A challenge for any learning-based program analysis is how to represent code. Work on this problem includes graph-based representations [29], [30], embeddings learned from sequences of API calls [28], embeddings learned from paths through ASTs [31], [32], and embeddings for edits of code [7]. Future work should study the impact of these representations on neural bug finding.

除了bug检测之外，机器学习还应用于其他与编程相关的任务[5]，比如预测标识符名称[8]、[10]和类型[8]、[9]、[59]。任何基于学习的程序分析的一个挑战是如何表示代码。解决这个问题的工作包括基于图的表示[29]、[30]、从API调用[28]的序列中学习的嵌入，从通过转换[31]、[32]的路径中学习的嵌入，以及对代码[7]的编辑的嵌入。未来的工作应该研究这些表现对神经缺陷发现的影响。

### D. Studies of Bug Finding Techniques

A study related to ours applies different learning techniques to the bug detection problem [60]. Their data set includes seeded bugs, whereas we use real bugs. Another difference is that most of their study uses manually extracted features of code, whereas we learn models fully automatically, without any feature engineering. Their preliminary results with neural networks are based on a bit-wise representation of source code, which they find to be much less effective than we show token sequence-based models to be.

一项与我们相关的研究将不同的学习技术应用于bug检测问题[60]。他们的数据集包括种子bug，而我们使用真正的bug。另一个不同之处在于，他们的研究大多使用手工提取的代码特征，而我们完全自动地学习模型，没有任何特征工程。他们使用神经网络的初步结果是基于源代码的按位表示的，他们发现这比我们展示的基于令牌序列的模型要低效得多。

More traditional bug finding techniques have been subject to other studies, some of which focus on the recall of bug detectors [4], [61], while others focus on their precision [62], [63]. The effectiveness of test generation techniques has been studied as well [64], [65]. Our work complements those studies by systematically studying neural bug finding.

更传统的bug发现技术也受到了其他研究的关注，其中一些研究关注bug检测器[4]的回收[61]，而另一些研究则关注它们的精确性[62]和[63]。测试生成技术的有效性也得到了研究[64]，[65]。我们的工作通过系统地研究神经缺陷发现来补充那些研究。

### E. Defect Prediction and Unbalanced Data

Machine learning models for software defect prediction [66] suffer from data imbalance [67] (IV-G). Skewed training data is usually tackled either by sampling techniques [68], costsensitive learning [69], or ensemble learning [70]. Under-, over-, or synthetic-sampling techniques [68], [71] have been applied to alleviate data imbalance in software defect prediction. Our approximated nearest neighbor (ANN) sampling of non-buggy examples (II-C1) is a form of guided undersampling.

软件缺陷预测的机器学习模型[66]存在数据不平衡[67](IV-G)。扭曲的训练数据通常通过抽样技术[68]、成本敏感学习[69]或集成学习[70]来处理。不足、过度或合成采样技术[68]、[71]已经被用于缓解软件缺陷预测中的数据不平衡。我们对非错误样本的近似近邻抽样(ANN)是一种引导下的欠抽样。

## VII. CONCLUSION

This paper explores the opportunities and challenges of a novel way of creating bug detectors via deep learning. We present neural bug finding and systematically study its effectiveness based on warnings obtained from a traditional static bug detection tool. Studying neural bug detection models for 20 common kinds of programming errors shows that (i) neural bug finding can be highly effective for some bug patterns but fails to work well for other bug patterns, (ii) learned models pick up common code patterns associated with buggy code, as well as common instances of more general bug patterns, and (iii) surprisingly small data sets can yield effective models. Our work also identifies some pitfalls associated with training and validating neural bug detectors and presents ways to avoid them. We believe that this work is an important step into a promising new direction, motivating future work on more advanced neural bug finding tools and on improving the process of obtaining training data.

本文探讨了通过深度学习创建bug检测器的新方法的机遇和挑战。提出了一种基于传统静态缺陷检测工具的神经缺陷检测方法，并对其有效性进行了系统的研究。研究神经缺陷检测模型20常见的编程错误显示,(i)神经缺陷发现可以为一些bug模式高效但不适合其他缺陷模式,(2)学习模型挑选通用代码模式与车相关的代码,以及更普遍的缺陷模式的常见的实例,和(3)令人惊讶的小数据集可以产生有效的模型。我们的工作还确定了与训练和验证神经缺陷检测器相关的一些缺陷，并提出了避免这些缺陷的方法。我们相信，这项工作是朝着一个有希望的新方向迈出的重要一步，它将激发未来在更先进的神经缺陷发现工具和改进训练数据获取过程方面的工作。
