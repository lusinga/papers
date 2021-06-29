# Symbolic Execution for JavaScript

## ABSTRACT

We present a framework for trustworthy symbolic execution of JavaScripts programs, whose aim is to assist developers in the testing of their code: the developer writes symbolic tests for which the framework provides concrete counter-models. We create the framework following a new, general methodology for designing compositional program analyses for dynamic languages. We prove that the underlying symbolic execution is sound and does not generate false positives. We establish additional trust by using the theory to precisely guide the implementation and by thorough testing. We apply our framework to whole-program symbolic testing of real-world JavaScript libraries and compositional debugging of separation logic specifications of JavaScript programs.

我们提出了一个javascript程序可信赖的符号执行框架，其目的是帮助开发人员测试他们的代码:开发人员编写符号测试，框架提供了具体的反模型。我们按照一种新的、通用的方法为动态语言设计组合程序分析来创建框架。我们证明了潜在的符号执行是合理的，不会产生假阳性。我们通过使用理论来精确地指导实现和通过彻底的测试来建立额外的信任。我们将我们的框架应用于真实JavaScript库的全程序符号测试和JavaScript程序分离逻辑规范的组合调试。

## 1 INTRODUCTION

JavaScript (JS) is the most widespread dynamic language, used by 95.1% of websites [51]. Due to its dynamic, complex nature, it is a difficult target for symbolic analysis. Recent symbolic execution tools for JS [30, 42, 52] have made significant progress: they are fully automatic, aim at code in the large, and primarily focus on scalability and coverage issues. However, they do have some limitations. They only target specific bug patterns rather than general-purpose symbolic execution, and depend on whole-program analysis.

- [30] G. Li, E. Andreasen, and I. Ghosh. 2014. SymJS: automatic symbolic testing of JavaScript web applications. In Proceedings of the 22nd ACM SIGSOFT International Symposium on Foundations of Software Engineering, (FSE-22), Hong Kong, China, November 16 - 22, 2014. 449–459.
- [42] P. Saxena, D. Akhawe, S. Hanna, F. Mao, S. McCamant, and D. Song. 2010. A Symbolic Execution Framework for JavaScript. In 31st IEEE Symposium on Security and Privacy, S&P 2010, 16-19 May 2010, Berleley/Oakland, California, USA. 513–528.
- [52] E. Wittern, A. T. T. Ying, Y. Zheng, J. Dolby, and J. Alain Laredo. 2017. Statically checking web API requests in JavaScript. In Proceedings of the 39th International Conference on Software Engineering, ICSE 2017, Buenos Aires, Argentina, May 20-28, 2017. 244–254.

JavaScript (JS)是最广泛使用的动态语言，95.1%的网站使用[51]。由于它的动态性、复杂性，是符号分析的难点。最近JS的符号执行工具[30,42,52]已经取得了重大进展:它们是全自动的，针对大量代码，主要关注可伸缩性和覆盖问题。然而，它们确实有一些局限性。它们只针对特定的错误模式，而不是通用的符号执行，并且依赖于整个程序分析。

Whole-program analysis is not sufficient for JavaScript applications, which are commonly run in highly dynamic execution environments: for example, client-side programs dynamically load and execute third-party code as a matter of course. One also needs to be able to analyse incomplete code in a compositional manner. Designing compositional analyses for dynamic languages such as JavaScript, however, is non-trivial. This is because, unlike static languages such as C, C++ and Java, dynamic languages do not observe the frame property [39], essential for compositionality. Intuitively, the frame property means that the output of a program cannot change if the state in which it is run is extended. In JavaScript, it is possible to introduce bugs by extending the state in which a program is run. To our knowledge, there has been no previous work on compositional symbolic execution for JavaScript.

- [39] John C. Reynolds. 2002. Separation Logic: A Logic for Shared Mutable Data Structures. In LICS.

整个程序分析对于JavaScript应用程序是不够的，因为它通常运行在高度动态的执行环境中:例如，客户端程序理所当然地动态加载和执行第三方代码。还需要能够以组合的方式分析不完整的代码。然而，为JavaScript等动态语言设计组合分析并非易事。这是因为，与C、c++和Java等静态语言不同，动态语言没有观察到框架属性[39]，而这对组合性至关重要。直观地说，frame属性意味着如果程序运行的状态被扩展，那么程序的输出就不能改变。在JavaScript中，通过扩展程序运行的状态，可能会引入bug。据我们所知，之前没有针对JavaScript的组合符号执行的工作。

We introduce Cosette, a framework for trusted symbolic execution of JavaScript (ECMAScript 5 Strict [18]). Its aim is to provide general-purpose symbolic analysis and assist developers in the testing of their code. We present a new, general methodology for developing compositional analyses for languages that do not satisfy the frame property, and apply it to the design of the symbolic execution of Cosette. In contrast to existing tools, the symbolic analysis of Cosette is fully formalised in a way that guides the implementation and is also trusted, in that it follows the JavaScript semantics and does not produce false positive bug reports. We apply Cosette to whole-program symbolic testing of real-world JavaScript libraries and compositional debugging of separation logic specifications of JavaScript programs. We illustrate these use cases in §2.

我们引入了珂赛特，一个可信的JavaScript符号执行框架(ECMAScript 5 Strict[18])。它的目的是提供通用的符号分析，并帮助开发人员测试他们的代码。我们提出了一种新的、通用的方法，用于对不满足框架性质的语言进行组合分析，并将其应用于珂赛特的符号执行设计。与现有的工具相比，珂赛特的符号分析以一种指导实现的方式被完全形式化，也被信任，因为它遵循JavaScript语义，不会产生错误报告。我们将珂赛特应用于真实JavaScript库的全程序符号测试和JavaScript程序分离逻辑规范的组合调试。我们在§2中说明了这些用例。

We show the architecture of Cosette on the right. We extend JS with constructs for creating and reasoning about symbolic values. Using JS-2-JSIL [22], a trusted compiler from JS to the JSIL intermediate language, we compile the extended JS program to an extended JSIL program. The core of Cosette is a new symbolic interpreter for JSIL, written in Rosette [48, 49], a framework for creating symbolic interpreters. The JSIL symbolic interpreter either outputs a concrete counter-model for the given assertions, or guarantees correctness up to a given bound for unfolding loops and recursive predicates.

- [48] E. Torlak and R. Bodík. 2013. Growing solver-aided languages with rosette. In ACM Symposium on New Ideas in Programming and Reflections on Software, Onward! 2013, part of SPLASH ’13, Indianapolis, IN, USA, October 26-31, 2013. 135–152.
- [49] E. Torlak and R. Bodík. 2014. A lightweight symbolic virtual machine for solveraided host languages. In ACM SIGPLAN Conference on Programming Language Design and Implementation, PLDI ’14, Edinburgh, United Kingdom - June 09 - 11, 2014. 54.

右边是珂赛特的建筑。我们用创建和推理符号值的结构来扩展JS。使用JS-2-JSIL[22](从JS到JSIL中间语言的可信编译器)，我们将扩展JS程序编译为扩展JSIL程序。珂赛特的核心是JSIL的一个新的符号解释器，用Rosette[48, 49]编写，这是一个创建符号解释器的框架。JSIL符号解释器要么为给定的断言输出一个具体的反模型，要么为展开循环和递归谓词的给定边界保证正确性。

Our new, general methodology underpinning the compositional
JSIL symbolic interpreter consists of three stages: (1) we design a
JSIL instrumented semantics that exhibits the frame property by
explicitly keeping track of object properties that we know are not present; (2) we define the JSIL symbolic semantics by lifting the JSIL
instrumented semantics; and (3)we link the JSIL symbolic semantics
to the JSIL concrete semantics by describing the frames that can
be safely added to the initial state. The key innovation is to have
the instrumented semantics as a proper interim stage in the design
of the symbolic execution, obtaining more modular reasoning and
substantially simpler proofs. We present this methodology in §3.

## 2 USING COSETTE

## 3 SYMBOLIC EXECUTION FOR JSIL

## 4 SPECIFICATION-DRIVEN BUG-FINDING

### 4.1 Symbolic Execution with SL-Assertions

### 4.2 The Frame Inference Problem

### 4.3 From Specifications to Symbolic Tests

### 4.4 Lifting the results to JavaScript

## 5 EVALUATION

## 6 RELATED WORK

The existing literature covers a wide range of JavaScript analysis techniques, including: type systems [2, 8, 20, 26, 31, 38, 47], control flow analysis [21], pointer analysis [25, 45] and abstract interpretation [4, 26, 28, 35], among others.We focus on the existing work on symbolic execution and logic-based verification for JavaScript, and discuss general techniques for specification-driven test generation.

现有的文献涵盖了广泛的JavaScript分析技术，包括:类型系统[2,8,20,26,31,38,47]，控制流分析[21]，指针分析[25,45]和抽象解释[4,26,28,35]等。我们将重点关注JavaScript的符号执行和基于逻辑的验证的现有工作，并讨论规范驱动的测试生成的通用技术。

- [21] A. Feldthaus, M. Schäfer, M. Sridharan, J. Dolby, and F. Tip. 2013. Efficient construction of approximate call graphs for JavaScript IDE services. In 2013 35th International Conference on Software Engineering (ICSE). IEEE, 752–761.
- [25] D. Jang and K.-M. Choe. 2009. Points-to analysis for JavaScript. In Proceedings of the 2009 ACM symposium on Applied Computing. ACM, 1930–1937.
- [45] M. Sridharan, J. Dolby, S. Chandra, M. Schäfer, and F. Tip. 2012. Correlation Tracking for Points-To Analysis of JavaScript. In ECOOP. 435–458.
- [4] E. Andreasen and A. Møller. 2014. Determinacy in Static Analysis for jQuery. In Proceedings of the 29th International Conference on Object Oriented Programming Systems Languages & Applications (OOPSLA’14). 17–31.
- [26] S. Holm Jensen, A. Møller, and P. Thiemann. 2009. Type Analysis for JavaScript. In Proceedings of the 16th International Static Analysis Symposium (SAS) (Lecture Notes in Computer Science), Vol. 5673. Springer, 238–255.
- [28] V. Kashyap, K. Dewey, E. A. Kuefner, J. Wagner, K. Gibbons, J. Sarracino, B. Wiedermann, and B. Hardekopf. 2014. JSAI: a static analysis platform for JavaScript. In FSE. 121–132.
- [35] Changhee Park and Sukyoung Ryu. 2015. Scalable and Precise Static Analysis of
JavaScript Applications via Loop-Sensitivity. In ECOOP. 735–756.

*Symbolic Execution for JS*. The majority of the existing bugfinding symbolic execution tools for JavaScript target specific bug patterns, such as security vulnerabilities related to the misuse of strings [42] (for example, absence of sanitisation before security critical operations), malformed Web API requests [52], and DOM API specific bugs [30]. These tools are fully automatic and aim at code in the large, primarily focusing on scalability and coverage issues. Cosette has a different purpose: it is not designed to be fully automatic, but to assist developers in testing their code. The work closest to ours is Jalangi [44], a general-purpose symbolic execution tool for JavaScript that implements a sophisticated state merging algorithm to deal with the problem of symbolic state explosion. However, Jalangi, as all existing symbolic execution tools for JavaScript, does not follow the semantics of the language precisely. In contrast, Cosette is trustworthy: it does follow the semantics of JavaScript and its theoretical underpinnings are formalised and proven sound. Therefore, it can be used both as a basis for building other more specific analyses (e.g., combining symbolic execution with a type-based analysis to increase the precision of the latter) and as a testing oracle for other symbolic execution tools for JavaScript that purposely ignore some corner cases of the JavaScript semantics. Furthermore, Cosette is the first symbolic execution tool for JavaScript that provides frame resilience guarantees, which are essential for any specification-related reasoning.

- [42] P. Saxena, D. Akhawe, S. Hanna, F. Mao, S. McCamant, and D. Song. 2010. A Symbolic Execution Framework for JavaScript. In 31st IEEE Symposium on Security and Privacy, S&P 2010, 16-19 May 2010, Berleley/Oakland, California, USA. 513–528.
- [52] E. Wittern, A. T. T. Ying, Y. Zheng, J. Dolby, and J. Alain Laredo. 2017. Statically
checking web API requests in JavaScript. In Proceedings of the 39th International
Conference on Software Engineering, ICSE 2017, Buenos Aires, Argentina, May 20-28, 2017. 244–254.
- [30] G. Li, E. Andreasen, and I. Ghosh. 2014. SymJS: automatic symbolic testing of JavaScript web applications. In Proceedings of the 22nd ACM SIGSOFT International Symposium on Foundations of Software Engineering, (FSE-22), Hong Kong, China, November 16 - 22, 2014. 449–459.
- [44] K. Sen, G. C. Necula, L. Gong, and W. Choi. 2015. MultiSE: multi-path symbolic execution using value summaries. In Proceedings of the 2015 10th Joint Meeting on Foundations of Software Engineering, ESEC/FSE 2015, Bergamo, Italy, August 30 September 4, 2015. 842–853.

JS的符号执行。现有的JavaScript bug查找符号执行工具主要针对特定的bug模式，例如与字符串[42]的误用相关的安全漏洞(例如，在安全关键操作之前缺乏安全处理)，畸形的Web API请求[52]，以及特定于DOM API的bug[30]。这些工具是全自动的，主要针对大量的代码，主要关注于可伸缩性和覆盖率问题。珂赛特有一个不同的目的:它不是被设计成完全自动化的，而是帮助开发人员测试他们的代码。与我们最接近的工作是Jalangi[44]，一个通用的JavaScript符号执行工具，它实现了一个复杂的状态合并算法来处理符号状态爆炸的问题。然而，Jalangi，作为所有现有的JavaScript符号执行工具，并没有完全遵循语言的语义。相反，珂赛特是值得信赖的:它遵循JavaScript的语义，它的理论基础是形式化的和被证明是合理的。因此,它可以用于为基础构建其他更具体的分析(例如,结合符号执行基于类型分析增加后者的精度)和oracle测试其他符号执行的JavaScript工具故意忽略某个角落的JavaScript语义。此外，珂赛特是第一个提供框架弹性保证的JavaScript象征性执行工具，这对于任何与规范相关的推理都是必不可少的。

## 7 CONCLUSIONS

We have presented Cosette, a trustworthy, compositional symbolic execution framework for JavaScript, combining the JS-2-JSIL compiler and our JSIL symbolic interpreter written in Rosette. We have applied Cosette to whole-program symbolic testing of real-world JavaScript libraries and compositional debugging of separation logic specifications of JavaScript programs.

我们提出了珂赛特，一个值得信赖的、组合的JavaScript符号执行框架，它结合了JS-2-JSIL编译器和我们用Rosette编写的JSIL符号解释器。我们将珂赛特应用于现实JavaScript库的全程序符号测试和JavaScript程序分离逻辑规范的组合调试。

We have developed a methodology for designing compositional program analyses for dynamic languages in general, and symbolic execution for JSIL in particular. We achieved this by introducing a new, abstract semantics for JSIL, which we instantiated to obtain the concrete, instrumented, and symbolic semantics. This abstract semantics is the bedrock for both the theoretical results and the implementation of the analysis. We prove that the JSIL symbolic execution of Cosette is sound, frame-resilient and does not generate false positives. We establish additional trust by using the theory to precisely guide the implementation and by thorough testing.

我们开发了一种方法，用于为动态语言设计组合程序分析，特别是为JSIL设计符号执行。我们通过为JSIL引入一个新的抽象语义来实现这一点，我们对其进行实例化以获得具体的、插装的和符号语义。这种抽象语义是理论结果和分析实现的基础。我们证明了JSIL对珂赛特的象征性执行是健全的、框架弹性的，并且不会产生误报。我们通过使用理论来精确地指导实现和通过彻底的测试来建立额外的信任。

Cosette brings ideas from current separation logic research to the well-established setting of classical symbolic execution [3]. We believe that it is a stepping stone towards a fully automatic compositional symbolic execution tool for JavaScript in the style of Infer [12]. In future, our goal is to implement such a tool, drawing inspiration the JSIL abstract semantics presented here.

- [3] E. Andreasen, L. Gong, A. Møller, M. Pradel, M. Selakovic, K. Sen, and C.-A. Staicu. 2017. A Survey of Dynamic Analysis and Test Generation for JavaScript. Comput. Surveys 50, 5 (2017).

珂赛特从目前的分离逻辑研究到经典的符号执行[3]的既定背景。我们相信，它是一个迈向Infer[12]风格的全自动组合符号执行工具的垫脚石。将来，我们的目标是实现这样一个工具，从中获得JSIL抽象语义的灵感。
