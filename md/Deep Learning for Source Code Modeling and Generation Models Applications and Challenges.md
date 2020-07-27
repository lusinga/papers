# Deep Learning for Source Code Modeling and Generation: Models, Applications, and Challenges

Deep Learning (DL) techniques for Natural Language Processing have been evolving remarkably fast. Recently, the DL advances in language modeling, machine translation, and paragraph understanding are so prominent that the potential of DL in Software Engineering cannot be overlooked, especially in the field of program learning. To facilitate further research and applications of DL in this field, we provide a comprehensive review to categorize and investigate existing DL methods for source code modeling and generation. To address the limitations of the traditional source code models, we formulate common program learning tasks under an encoder-decoder framework. After that, we introduce recent DL mechanisms suitable to solve such problems. Then, we present the state-of-the-art practices and discuss their challenges with some recommendations for practitioners and researchers as well.

用于自然语言处理的深度学习(DL)技术发展非常迅速。近年来，数字图书馆在语言建模、机器翻译和段落理解等方面的研究进展十分突出，使得数字图书馆在软件工程尤其是程序学习领域的潜力不容忽视。为了促进DL在该领域的进一步研究和应用，我们对现有的用于源代码建模和生成的DL方法进行了分类和研究。为了解决传统源代码模型的局限性，我们在编码器-解码器框架下制定了共同的程序学习任务。在此基础上，介绍了近年来适合解决这类问题的DL机制。我们将介绍最先进的实践，并讨论他们的挑战，同时也为从业者和研究人员提出一些建议。

## 1 INTRODUCTION

Deep Learning (DL) has recently emerged as an important branch of Machine Learning (ML) because of its incredible performance in Computer Vision and Natural Language Processing (NLP) [75]. In the field of NLP, it has been shown that DL models can greatly improve the performance of many classic NLP tasks such as semantic role labeling [96], named entity recognition [210], machine translation [256], and question answering [175]. Similarly, source code is a special type of structured natural language written by programmers [101], which can be analyzed by DL models. Machine intelligence that understands and creates complex structure of software has a lot of applications in Software Engineering (SE). Specifically, Big Code is the research area that uses ML and DL for source code modeling.1 To facilitate the building of ML models, software community offers valuable datasets such as online code corpora like GitHub, question answering forums like Stack Overflow, as well as documentation of various software and programming tools that are highly rich in content. There have been many applications of ML in SE thesaurus construction [40], language models for code [54], and information retrieval of answers, projects, and documentation [39, 271, 279]. Compared to other domains, DL for Big Code is still growing and requiring more research, tasks, and well-established datasets.

由于深度学习(DL)在计算机视觉和自然语言处理(NLP)方面的出色表现，最近已经成为机器学习(ML)的一个重要分支[75]。在自然语言处理领域，已有研究表明DL模型可以极大地提高许多经典的自然语言处理任务的性能，如语义角色标注[96]、命名实体识别[210]、机器翻译[256]和问题回答[175]。同样，源代码是程序员编写的一种特殊类型的结构化自然语言[101]，可以通过DL模型对其进行分析。机器智能能够理解和创建复杂的软件结构，在软件工程中有着广泛的应用。具体来说，大代码是使用ML和DL进行源代码建模的研究领域。为了方便ML模型的构建，软件社区提供了有价值的数据集，如GitHub这样的在线代码库，Stack Overflow这样的问题回答论坛，以及各种软件和编程工具的文档，这些文档内容非常丰富。在SE同义词典构造[40]、代码[54]的语言模型以及答案、项目和文档的信息检索中有许多ML的应用[39,271,279]。与其他领域相比，用于大代码的DL仍在发展，需要更多的研究、任务和完善的数据集。

Among the Big Code tasks, source code generation is an important field to predict explicit code or program structure from multimodal data sources such as incomplete code, programs in another programming language, natural language descriptions, or execution examples [5]. Code generation tools can assist the development of automatic programming tools to improve programming productivity. Such models can also represent the temporal context and discrete logic of programs. However, traditional source code models were either inflexible [25, 220], time-consuming to design for specific languages/tasks [87, 92], or unable to capture long-term dependencies of code statements [9, 222]. Listings 1 and 2 show that the difference of buggy and clean versions of the same code snippet depends on the relative location of the close( file ) function with respect to the open( file ) function, which is an example of long-term dependency in source code. DL models can help address the aforementioned issues because of their superior ability in extracting features from various data formats (e.g., natural language and symbol sequences) and capturing both syntactic and semantic information at various scales [96].

在大量的代码任务中，源代码生成是一个重要的领域，可以从多模式数据源(如不完整代码、另一种编程语言的程序、自然语言描述或执行示例[5])中预测显式代码或程序结构。代码生成工具可以帮助开发自动编程工具，提高编程效率。这样的模型也可以代表时间上下文和程序的离散逻辑。然而，传统的源代码模型要么不灵活[25,220]，为特定语言/任务设计耗时[87,92]，要么无法捕获代码语句的长期依赖关系[9,222]。清单1和2显示了相同代码片段的bug版本和干净版本的区别取决于close(file)函数相对于open(file)函数的相对位置，这是源代码中长期依赖的一个示例。DL模型能够帮助解决上述问题，因为它能够从各种数据格式(如自然语言和符号序列)中提取特征，并在各种尺度上捕获语法和语义信息[96]。

## 2 TRADITIONAL SOURCE CODE MODELING APPROACHES AND THEIR LIMITATIONS

### 2.1 Domain-specific Language Guided Models

### 2.2 Probabilistic Grammars

### 2.3 n-gram Language Models

### 2.4 Simple Neural Program Models

### 2.5 Advantages of Deep Learning Models over Traditional Approaches

## 3 DEEP SEQUENCE MODELING WITH ENCODER-DECODER FRAMEWORK

### 3.1 Deep Learning Models for Sequence Modeling

### 3.2 Input Embeddings of Deep Learning Models

### 3.3 Stable Training of Deep Learning Model

## 4 RECENT PRACTICES OF BUILDING DEEP LEARNING MODELS FOR SOURCE CODE MODELING AND GENERATION

### 4.1 Deep Encoder Models

### 4.2 Deep Decoder Models

### 4.3 Deep Controller Models

## 5 DEEP LEARNING FOR BIG CODE APPLICATIONS

### 5.1 Source Code Analysis

### 5.2 Program Generation

## 6 DATASETS FOR BIG CODE APPLICATIONS

### 6.1 Datasets for Source Code Analysis

### 6.2 Datasets for Program Generation

## 7 CHALLENGES AND DIRECTIONS OF DEEP CODE MODELING AND GENERATION

### 7.1 Data Efficiency

### 7.2 Discrete and Symbolic Representations

### 7.3 Real-world Application and Evaluation

### 7.4 Human-like Programming

## 8 CONCLUSIONS

