# Just-In-Time Defect Identification and Localization: A Two-Phase Framework

## Abstract

Defect localization aims to locate buggy program elements (e.g., buggy files, methods or lines of code) based on defect symptoms, e.g., bug reports or program spectrum. However, when we receive the defect symptoms, the defect has been exposed and negative impacts have been introduced. Thus, one challenging task is: whether we can locate buggy program prior to the appearance of the defect symptom (e.g., when buggy program elements are being committed to a version control system). We refer to this type of defect localization as “Just-In-Time (JIT) Defect localization”. Although many prior studies have proposed various JIT defect identification methods to identify whether a new change is buggy, these prior methods do not locate the suspicious positions. Thus, JIT defect localization is the next step of JIT defect identification (i.e., after a buggy change is identified, suspicious source code lines are located).

缺陷定位的目的是根据缺陷症状定位有缺陷的程序元素(例如，有缺陷的文件，方法或者代码行)，例如，有缺陷报告或者程序频谱。然而，当我们收到缺陷症状时，缺陷已经暴露，并且引入了负面影响。因此，一个具有挑战性的任务是:我们是否能够在缺陷症状出现之前定位有缺陷的程序(例如，当有缺陷的程序元素被提交到版本控制系统时)。我们将这种类型的缺陷定位称为“即时(JIT)缺陷定位”。尽管许多以前的研究已经提出了各种JIT缺陷识别方法来识别一个新的更改是否有bug，但是这些以前的方法并没有定位可疑的位置。因此，JIT缺陷定位是JIT缺陷识别的下一个步骤(即，在识别出有bug的更改之后，定位可疑的源代码行)。

To address this problem, we propose a two-phase framework, i.e., JIT defect identification and JIT defect localization. Given a new change, JIT defect identification will identify it as buggy change or clean change first. If a new change is identified as buggy, JIT defect localization will rank the source code lines introduced by the new change according to their suspiciousness scores. The source code lines ranked at the top of the list are estimated as the defect location. For JIT defect identification phase, we use 14 change-level features to build a classifier by following existing approach. For JIT defect localization phase, we propose a JIT defect localization approach that leverages software naturalness with the N-gram model. To evaluate the proposed framework, we conduct an empirical study on 14 open source projects with a total of 177,250 changes. The results show that software naturalness is effective for our JIT defect localization. Our model achieves a reasonable performance, and outperforms the two baselines (i.e., random guess and a static bug finder (i.e., PMD)) by a substantial margin in terms of four ranking measures.

为了解决这个问题，我们提出了一个两阶段的框架，即JIT缺陷识别和JIT缺陷定位。给定一个新的变更，JIT缺陷识别将首先识别它是有bug的变更还是干净的变更。如果一个新的变更被识别为有缺陷的，JIT缺陷定位将根据其可疑程度评分对新变更引入的源代码行进行排序。位于列表顶部的源代码行被估计为缺陷位置。对于JIT缺陷识别阶段，我们使用14个变更级别的特征来按照现有的方法构建分类器。对于JIT缺陷定位阶段，我们提出了一种利用N-gram模型的软件自然性的JIT缺陷定位方法。为了评估所提议的框架，我们对14个开源项目进行了实证研究，总共有177,250个变化。结果表明，软件自然性对我们的JIT缺陷定位是有效的。我们的模型实现了合理的性能，并且在四个排名度量中，显著地超过了两个基线(即随机猜测和静态bug查找器(即PMD))。

## 1 INTRODUCTION

During software development and maintenance, developers often spend much effort and resources to debug a program [1]. Defect localization (or bug localization) aims to help developers to locate buggy program elements, such as buggy files, methods or lines of code. Researchers have proposed various techniques for defect localization, including information retrieval (IR) based techniques [2]–[5] and spectrum-based techniques [6]–[8]. These techniques perform localization by analyzing the defect symptoms. These symptoms could be a description of a bug, or a failing test case. For example, IR based techniques analyze the textual description in bug reports, spectrum-based techniques analyze program spectrum of failing and successful execution traces [1].

在软件开发和维护过程中，开发人员经常花费大量的精力和资源来调试程序[1]。缺陷定位(或缺陷定位)的目的是帮助开发人员定位有缺陷的程序元素，比如有缺陷的文件、方法或代码行。研究人员提出了多种缺陷定位技术，包括基于信息检索(IR)的技术[2]-[5]和基于光谱的技术[6]-[8]。这些技术通过分析缺陷症状来进行定位。这些症状可能是错误的描述，也可能是失败的测试用例。例如，基于IR的技术分析bug报告中的文本描述，基于频谱的技术分析失败和成功执行的程序谱跟踪[1]。

However, one main limitation of the above-mentioned localization techniques is that they rely on defect symptoms (i.e., from bug reports or execution traces). When a defect symptom is discovered, the defect has already been exposed and it has already negatively impacted the software. Therefore, one challenging task is: can we locate buggy program prior to the appearance of the defect symptom (e.g., when buggy program elements are being committed to a version control system)? We refer to this type of defect localization as “Just-In-Time (JIT) Defect Localization”.

然而，上述定位技术的一个主要限制是它们依赖于缺陷症状(例如，来自缺陷报告或执行跟踪)。当一个缺陷症状被发现时，这个缺陷已经暴露出来了，并且已经对软件产生了负面影响。因此，一个具有挑战性的任务是:我们能否在缺陷症状出现之前定位有缺陷的程序(例如，当有缺陷的程序元素被提交到版本控制系统中时)?我们将这种类型的缺陷定位称为“即时(JIT)缺陷定位”。

The idea of “JIT Defect Localization” comes from “JIT Defect Identification” (aka, JIT defect prediction) which is a well-known technique for identifying buggy (i.e., defectintroducing) changes at check-in time. Recently, JIT defect identification has attracted an increasing research interest, a number of studies have proposed various techniques for JIT defect identification [9]–[16]. JIT defect identification can yield many benefits. For example, the identification can be performed at the time when the change is submitted; such immediate feedback ensures that the context is still fresh in the minds of developers. This fresh context can speed up the fixing of the buggy change. Additionally, the identification is made at a fine-granularity, i.e., changelevel, that are mapped to a few areas of the large code base. Such a fine-granularity identification can provide large effort saving over coarser grained identification (e.g., filelevel or module-level) [17].

“JIT缺陷定位”的思想来自于“JIT缺陷识别”(又名JIT缺陷预测)，这是一种在签入时识别bug(即引入缺陷)更改的著名技术。近年来，JIT缺陷识别吸引了越来越多的研究兴趣，许多研究提出了各种用于JIT缺陷识别的技术[9]-[16]。JIT缺陷识别可以带来很多好处。例如，标识可以在提交更改时执行;这样的即时反馈确保了开发人员对环境仍然记忆犹新。这个新的上下文可以加速错误更改的修复。此外，识别是在一个细粒度的，即变更级别，映射到大型代码库的一些区域。这样的细粒度标识可以比粗粒度标识(例如，文件级或模块级)[17]节省大量工作。

Despite the achievements of JIT defect identification, one issue remains unanswered is: what is the next step if we identified a buggy change? Our main concern is that all of the prior studies focus on how to classify a change (i.e., classify a change as buggy or clean), or sort changes according to their defective likelihood scores, to find more buggy changes by inspecting fewer changed lines of code (i.e., effort-aware model) [9], [12], [15], [16]. However, after a buggy change is identified, it is also a difficult task to locate the exact buggy positions (e.g., source code lines), especially for a change that has added many lines of code (LOC). Take the Jmeter project as an example: the average newly added LOC across all the changes is 180. It would cost a great amount of inspection effort if we inspect all the added LOC for all the buggy changes. Unfortunately, there is no technique for guiding how to inspect a buggy change in order to locate the exact buggy lines with less inspection effort.

尽管在JIT缺陷识别方面取得了许多成就，但仍有一个问题尚未解决：如果我们确定有错误的更改，下一步应该怎么做？我们的主要关注点是，所有先前的研究都集中在如何对更改进行分类（即将更改分类为越野车或清洁车），或根据缺陷可能性评分对变更进行分类，以通过检查更少的更改路线来发现更多越野车变化。代码（即努力意识模型）[9]，[12]，[15]，[16]。但是，在确定了错误更改之后，要找到确切的错误位置（例如，源代码行）也是一项艰巨的任务，尤其是对于添加了许多代码行（LOC）的更改而言。以Jmeter项目为例：所有更改中新添加的LOC平均为180。如果我们检查所有错误更改的所有添加的LOC，则将花费大量的检查工作。不幸的是，没有一种技术可以指导如何检查越野车的变化以便以较少的检查工作来定位确切的越野车线。

Therefore, in response to the concerns raised above, we highlight the framework of JIT Defect Identification and Localization. Different from current defect localization techniques by analyzing defect symptoms, JIT defect localization aims to be an early step of continuous quality control because it can be performed as soon as a code change is checked in. In summary, the main benefits of performing JIT defect identification and localization are as follows:

- Localization is performed at a fine-granularity. JIT defect localization locates buggy program elements at a fine-granularity, i.e., line-level. For each buggy change, JIT defect localization locates the buggy lines in a buggy change. Such a fine-granularity can help developers to locate and address defects using less effort.

定位以细粒度执行。JIT缺陷本地化以精细的粒度 (即行级别) 定位错误的程序元素。对于每个错误更改，JIT缺陷定位都会在错误更改中定位错误线。如此精细的粒度可以帮助开发人员使用更少的精力定位和解决缺陷。

- Localization is performed early on. JIT defect localization is performed at check-in time. Such immediate feedback ensures that the context is still fresh in the minds of developers. This fresh context can help speed up the fixing of located defects immediately.

定位是在早期进行的。JIT缺陷定位在检查时执行。这样的即时反馈确保了上下文在开发人员的脑海中仍然是新鲜的。这种全新的环境可以帮助立即加快定位缺陷的修复速度。

- Localization is performed without relying on defect symptoms. In JIT defect localization, only change properties are needed. Thus, the localization can be invoked without analyzing the defect symptoms. In contrast to current defect localization techniques which require the analysis of defect symptoms, the benefit of JIT defect localization is that it can locate the defect before the appearance of its symptom.

定位是在不依赖缺陷症状的情况下进行的。在JIT缺陷定位中，只需要更改属性。因此，可以在不分析缺陷症状的情况下调用定位。与当前需要分析缺陷症状的缺陷定位技术相反，JIT缺陷定位的好处是它可以在缺陷出现症状之前定位缺陷。

The basic technical idea of our JIT localization technique begins with the “software naturalness” as observed by Hindle et al [18] who noted that “natural” code is highly repetitive and can be captured through a language model (e.g. a N-gram model that was originally developed in Natural Language Processing field). Based on this observation, Ray et al. [19] observed that buggy code tends to be more entropic (i.e. unnatural) compared with clean code. Moreover, they observed that this observation can be helpful for defect identification on release level and can complement the effectiveness of static bug finders (e.g., FindBugs and PMD).

我们的JIT本地化技术的基本技术思想始于Hindle等人 [18] 观察到的 “软件自然性”，他们指出 “自然” 代码是高度重复的，可以通过一种语言模型 (例如g。最初在自然语言处理领域开发的N-gram模型)。基于这一观察，Ray等人 [19] 观察到buggy码倾向于更熵 (即不自然) 与干净的代码相比。此外，他们观察到，这种观察结果有助于在释放级别上识别缺陷，并且可以补充静态错误查找器 (例如FindBugs和PMD) 的有效性。

Inspired by the above-mentioned observations, we propose an automated two-phase framework. The first phase consists of a JIT defect identification (i.e., identify buggy changes). The second phase consists of a JIT defect localization for the buggy changes that were correctly identified by the first phase. The first phase is similar to the prior approaches. We implement a JIT defect identification approach by following prior studies [9], [15], [20]. In the second phase, we propose a localization approach based on software naturalness by learning from historical labeled code.

受上述观察的启发，我们提出了一个自动化的两阶段框架。第一阶段包括JIT缺陷识别 (即识别错误更改)。第二阶段包括针对第一阶段正确识别的错误更改的JIT缺陷定位。第一阶段与以前的方法相似。我们通过遵循先前的研究 [9]，[15]，[20] 来实现JIT缺陷识别方法。在第二阶段，我们通过从历史标记代码中学习，提出了一种基于软件自然性的本地化方法。

In summary, our work consists of three steps as shown in Figure 1. (1) The first step is a data preparation step. We identify buggy and clean changes using the Refactoring Aware SZZ (RA-SZZ) algorithm first [21], [22], then we link each buggy change and its corresponding bug-fixing change(s). Subsequently, we label the added lines by buggy changes that are modified by the corresponding bug-fixing changes as buggy code; otherwise, the lines are labeled as clean code. (2) The second step is the model building phase. We build a JIT defect identification model (i.e., JIT Defect Identifier) and a JIT defect localization model (i.e., JIT Defect Locator). For the JIT defect identifier, we use 14 changelevel features to build a logistic classifier by following prior studies [15], [20]. For the JIT defect locator, we build a code language model based on historical clean code using the Ngram model that has shown to be an effective technique for source code modeling [18], [19], [23], [24]. (3) The third step is the model application phase. For a new change, we first identify whether or not it is a likely buggy change using our JIT defect identifier. For a likely buggy change, we sort the added lines according to the likelihood values using the JIT defect locator. The lines sorted at the top are more likely to be the defect location.

总而言之，我们的工作包括三个步骤，如图1所示。(1) 第一步是数据准备步骤。我们首先使用重构感知SZZ (ra-szz) 算法识别错误和清理更改 [21]，[22]，然后，我们链接每个错误更改及其相应的错误修复更改。随后，我们通过错误更改将添加的行标记为错误代码，这些错误更改由相应的错误修复更改修改为错误代码; 否则，这些行将标记为干净代码。(2) 第二步是模型建立阶段。我们建立了JIT缺陷识别模型 (即，JIT缺陷标识符) 和JIT缺陷定位模型 (即，JIT缺陷定位器)。对于JIT缺陷标识符，我们通过遵循先前的研究 [15]，[20]，使用14个更改功能来构建逻辑分类器。对于JIT缺陷定位符，我们使用Ngram模型基于历史干净代码构建代码语言模型，该模型已被证明是源代码建模的有效技术 [18]，[19] 、 [23] 、 [24]。(3) 第三步是模型应用阶段。对于新的更改，我们首先使用我们的JIT缺陷标识符来识别它是否是可能的错误更改。对于可能的错误更改，我们使用JIT缺陷定位器根据可能性值对添加的行进行排序。在顶部排序的行更有可能是缺陷位置。

## 1 INTRODUCTION

## 2 OUR FRAMEWORK

## 3 EMPIRICAL STUDY SETUP

## 4 EXPERIMENTAL RESULTS

## 5 DISCUSSION

## 6 THREATS TO VALIDITY

## 7 RELATED WORK

### 7.1 Defect Localization

### 7.2 Software Naturalness

### 7.3 JIT Defect Identification

Researchers has proposed various methods for JIT defect identification. Mockus and Weiss [63] proposed a method for assessing the risk that changes introduce defect in a telecommunication system. Kim et al. [64] proposed an approach to predict the buggy entities and files from cached history at the moment a fault is fixed. Furthermore, Kim et al. [65] proposed a model for classifying a change as clean or buggy by using various change features. Kamei et al. [9] performed a large-scale empirical study of JIT defect identification using effort-aware evaluation. Shihab et al. [11] conducted an industrial study to better understand risky changes. Yang et al. [13], [14] proposed to use more advanced modeling techniques for JIT defect identification, such as ensemble learning and deep learning. Additionally, many studies investigated the comparison between supervised and unsupervised modeling methods for JIT defect identification [12], [15], [16].

研究人员提出了多种JIT缺陷识别方法。 Mockus和Weiss [63]提出了一种评估变化会在电信系统中引入缺陷的风险的方法。 Kim等。 [64]提出了一种在故障修复时从缓存的历史记录中预测越野车实体和文件的方法。 此外，金等。 [65]提出了一种通过使用各种变更特征将变更分类为干净或越野车的模型。 龟井等。 文献[9]使用努力意识评估对JIT缺陷识别进行了大规模的实证研究。 Shihab等。 [11]进行了一项工业研究，以更好地了解风险变化。 杨等。 [13]，[14]提出使用更高级的建模技术来进行JIT缺陷识别，例如集成学习和深度学习。 另外，许多研究调查了用于JIT缺陷识别的有监督和无监督建模方法之间的比较[12]，[15]，[16]。

Recently, Nayrolles and Hamou-Lhadj [66] proposed a two-phase approach (called CLEVER) for intercepting risky commits using code clone detection. The first phase is to assess the likelihood that an incoming commit is risky or not. The second phase is to use clone detection to suggest fixes when clone code is detected from the risky commit in the first phase. As a result, 66.7% of the suggested fixes were accepted by developers at industry. Comparing their work to this paper, the similarity is that both of us conducted a two-phase analysis to support how to fix the commits. The difference is the aim. They aim to suggest the fixes from prior similar fixes at the moment clone code is detected. We aim to locate the most suspicious lines in any buggy change to save the code inspection effort.

- [66] M. Nayrolles and A. Hamou-Lhadj, “Clever: Combining code metrics with clone detection for just-in-time fault prevention and resolution in large industrial projects,” in Proceedings of the 15th International Conference on Mining Software Repositories. ACM, 2018, pp. 153–164.

最近，Nayrolles和Hamou-Lhadj [66]提出了一种两阶段方法（称为CLEVER），用于使用代码克隆检测来拦截有风险的提交。 第一阶段是评估传入的提交是否有风险的可能性。 第二阶段是在第一阶段从有风险的提交中检测到克隆代码时，使用克隆检测来建议修复程序。 结果，行业中的开发人员接受了66.7％的建议修复程序。 将他们的工作与本文进行比较，相似之处在于我们俩都进行了两阶段分析以支持如何修复提交。 区别在于目的。 他们的目的是在检测到克隆代码时，根据先前的类似修复建议这些修复。 我们旨在在任何错误更改中找到最可疑的行，以节省代码检查的工作量。

Pascarella et al. [67] proposed a fine-grained JIT defect identification approach which can identify defective files within changes. They actually conducted a file-level defect prediction that focus on the changed files in commits. Different from our work, they did not conduct the integrated analysis on JIT defect identification and localization. Additionally, they focus on file-level, while we focus on line-level.

Pascarella等。 [67]提出了一种细粒度的JIT缺陷识别方法，该方法可以识别更改中的缺陷文件。 他们实际上进行了文件级缺陷预测，重点放在提交中已更改的文件上。 与我们的工作不同，他们没有对JIT缺陷的识别和定位进行综合分析。 此外，它们专注于文件级别，而我们专注于行级别。

Despite the success achieved by the above-mentioned techniques, there is no attempt for telling developers where is the most suspicious defective lines after JIT defect identification. Thus, our work aims to be served as the next step of JIT defect identification. In other words, our JIT defect localization phase can be employed when we identify a change as buggy.

尽管通过上述技术获得了成功，但没有尝试告诉开发人员在JIT缺陷识别之后最可疑的缺陷行在哪里。 因此，我们的工作旨在作为JIT缺陷识别的下一步。 换句话说，当我们将变更确定为越野车时，可以采用我们的JIT缺陷定位阶段。

## 8 CONCLUSION AND FUTURE WORK

In this paper, we propose a two-phase framework, i.e., JIT defect identification and localization. For JIT defect identification phase, we implement a logistic regression based approach using 14 change-level features. For JIT defect localization phase, we use N-gram modeling technique to build a source code language model training on historical clean source code. When given a new change, JIT defect identification phase will identify it as buggy or clean first. If a new change is identified as buggy, JIT defect localization phase will compute an entropy value for each line introduced by the change. Then, we can sort all the introduced lines according to the entropy value. The lines sorted at the top are more likely to be the defect location. To evaluate the effectiveness of our framework, we conduct an empirical study on 14 open source projects with totally 177,250 changes.

在本文中，我们提出了一个两阶段的框架，即JIT缺陷识别和定位。 在JIT缺陷识别阶段，我们使用14个变更级别的功能实现了基于逻辑回归的方法。 在JIT缺陷定位阶段，我们使用N-gram建模技术在历史干净的源代码上构建源代码语言模型训练。 当进行新的更改时，JIT缺陷识别阶段将首先将其识别为有缺陷的或干净的。 如果将新更改标识为错误，则JIT缺陷定位阶段将为更改所引入的每条线计算一个熵值。 然后，我们可以根据熵值对所有引入的行进行排序。 在顶部排序的线更有可能是缺陷位置。 为了评估我们框架的有效性，我们对14个开源项目进行了177,250个变更的实证研究。

In summary, the empirical study results show that: (1) Our framework achieves a reasonable and better performance than the baselines (i.e., PMD and RG) in terms of MRR, MAP and Top-k accuracy. (2) Considering the buggy changes that are correctly identified by our first phase (i.e., identified-buggy changes), our approach can successfully locate the first buggy line in near 3 lines on average (i.e, a MRR of 0.396). And our approach can successfully locate at least one buggy line in top-1 line among 26.5% and in top-5 lines among 54.4%. (3) A buggy model can also be possible for JIT defect localization. However, a clean model is a better choice on average.

总之，实证研究结果表明：（1）在MRR，MAP和Top-k准确性方面，我们的框架实现了比基线（即PMD和RG）合理且更好的性能。 （2）考虑到我们的第一阶段正确识别的越野车变化（即已识别的缺陷变化），我们的方法可以成功地将第一越野车线平均定位在近3条线中（即MRR为0.396）。 而且我们的方法可以成功地在26.5％的top-1线和54.4％的top-5线中至少找到一条越野车线。 （3）对于JIT缺陷定位，越野车模型也是可能的。 但是，平均而言，干净的模型是更好的选择。

Our paper is an important step in studying the effectiveness of JIT defect localization. We envision many future efforts to extend our work for JIT defect localization as well as to improve JIT defect identification (and possibly even crafting approaches that create a feedback loop between these two approaches). For example, further research can investigate whether or not the entropy of the changed lines could be used to enhance (or even replace) the state-ofthe- art JIT defect identification approach. Whether or not the JIT defect identification and localization are effective on pull requests needs further investigation, since it is hard to obtain the ground truth whether commits in unaccepted pull requests are buggy or clean using the SZZ algorithm that we leverage in this study. With respect to the language modeling step, further research can investigate whether or not a cache model (i.e., considering the locality of changed content), or a nested model (i.e., combining CP, WP and Cache models) can improve the effectiveness of JIT defect localization.With respect to the granularity, further research can adapt our approach at a wider granularity for the JIT defect localization (e.g., a block-level defect localization by considering a larger context of each change). Finally, additional in depth case studies on JIT defect localization in practice and the perception of developers should be conducted. This can help the community obtain a better understanding of the problem and collect useful information to improve this task further.

本文是研究JIT缺陷定位有效性的重要一步。我们设想了许多未来的工作，以扩展我们在JIT缺陷本地化方面的工作，并改善JIT缺陷识别（甚至可能设计在这两种方法之间创建反馈回路的方法）。例如，进一步的研究可以调查改变后的线的熵是否可以用于增强（甚至替代）最新的JIT缺陷识别方法。 JIT缺陷识别和定位是否对拉动请求有效是否需要进一步研究，因为使用本研究利用SZZ算法很难获得地面事实，即不可接受的拉动请求中的提交是错误的还是干净的。关于语言建模步骤，进一步的研究可以调查缓存模型（即考虑更改内容的本地性）或嵌套模型（即结合CP，WP和Cache模型）是否可以提高JIT的有效性。缺陷定位。关于粒度，进一步的研究可以使我们的方法在更大的粒度上适应JIT缺陷定位（例如，通过考虑每个更改的较大上下文来进行块级缺陷定位）。最后，应在实践中进行其他有关JIT缺陷本地化和开发人员感知的深入案例研究。这可以帮助社区更好地理解问题，并收集有用的信息以进一步改善此任务。
