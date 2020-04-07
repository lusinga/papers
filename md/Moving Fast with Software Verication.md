# Moving Fast with Software Verication

## Abstract

For organizations like Facebook, high quality software is important. However, the pace of change and increasing complexity of modern code makes it difficult to produce error-free software. Available tools are often lacking in helping programmers develop more reliable and secure applications.

对于像 Facebook 这样的组织来说，高质量的软件很重要。然而，现代代码的变化速度和日益增加的复杂性使得很难产生无错软件。在帮助程序员开发更可靠和安全的应用程序方面，通常缺少可用的工具。

Formal verification is a technique able to detect software errors statically, before a product is actually shipped. Although this aspect makes this technology very appealing in principle, in practice there have been many difficulties that have hindered the application of software verification in industrial environments. In particular, in an organization like Facebook where the release cycle is fast compared to more traditional industries, the deployment of formal techniques is highly challenging.

正式验证是一种能够在产品实际发货之前静态检测软件错误的技术。虽然这方面使这项技术在原则上非常吸引人，但在实践中存在许多困难，阻碍了软件验证在工业环境中的应用。特别是，在像 Facebook 这样的发布周期比更传统行业快的组织中，正式技术的部署极具挑战性。

This paper describes our experience in integrating a verification tool based on static analysis into the software development cycle at Facebook.

本文描述了我们在 Facebook 将基于静态分析的验证工具集成到软件开发周期中的经验。

## 1 Introduction

This is a story of transporting ideas from recent theoretical research in reasoning about programs into the fast-moving engineering culture of Facebook. The context is that most of the authors landed at Facebook in September of 2013, when we brought the INFER static analyzer with us from the verification startup Monoidics [4, 6]. INFER itself is based on recent academic research in program analysis [5], which applied a relatively recent development in logics of programs, separation logic [10]. As of this writing INFER is deployed and running continuously to verify select properties of every code modication in Facebook's mobile apps; these include the main Facebook apps for Android and iOS, Facebook Messenger, Instagram, and other apps which are used by over a billion people in total.

- [4]. Cristiano Calcagno and Dino Distefano. Infer: An automatic program verier for memory safety of C programs. In NASA Formal Methods - Third Interna- tional Symposium, NFM 2011, Pasadena, CA, USA, April 18-20, 2011. Proceed- ings, pages 459-465, 2011.
- [6]. Josh Constine. Facebook acquires assets of UK mobile bug-checking software developer Monoidics. http://techcrunch.com/2013/07/18/facebook-monoidics.
- [5]. Cristiano Calcagno, Dino Distefano, Peter W. O'Hearn, and Hongseok Yang. Compositional shape analysis by means of bi-abduction. J. ACM, 58(6):26, 2011.
- [10]. Peter W. O'Hearn, John C. Reynolds, and Hongseok Yang. Local reasoning about programs that alter data structures. In Computer Science Logic, 15th Interna- tional Workshop, CSL 2001. 10th Annual Conference of the EACSL, Paris, France, September 10-13, 2001, Proceedings, pages 1-19, 2001.

这是一个将最近关于程序推理的理论研究中的想法转化为脸谱网快速发展的工程文化的故事。背景是大多数作者在 9月 2013年登陆 Facebook，当时我们从验证启动 Monoidics [4,6] 带来了推断静态分析仪。INFER 本身是基于最近的学术研究在程序分析 [5]，应用了一个相对最近的发展在程序的逻辑，分离逻辑 [10]。截至本文撰写之时，INFER 已部署并持续运行，以验证 Facebook 移动应用程序中每个代码改进的选择属性; 这些包括 Android 和 iOS 的主要 Facebook 应用程序，Facebook Messenger,instagram 和其他总共有超过 10 亿人使用的应用程序。

In the process of trying to deploy the static analyzer the most important issue we faced was integration with Facebook's software development process. The software process at Facebook, and an increasing number of Internet companies, is based on fast iteration, where features are proposed and implemented and changed based on feedback from users, rather than wholly designed at the outset. The perpetual, fast, iterative development employed at Facebook might seem to be the worst possible case for formal verification technology, proponents of which sometimes even used to argue that programs should be developed only after a prior specifications had been written down. But we found that verification technology can be effective if deployed in a fashion which takes into account when and why programmers expect feedback. INFER runs on every "diff", which is a code change submitted by a developer for code review. Each day a number of bugs are reported on diffs and fixed by developers, before the diff is committed and eventually deployed to phones. Technically, the important point is that INFER is a compositional program analysis, which allows feedback to be given to developers in tune with their flow of incremental development.

在尝试部署静态分析器的过程中，我们面临的最重要的问题是与 Facebook 的软件开发过程集成。脸谱网的软件过程和越来越多的互联网公司是基于快速迭代的，其中功能是根据用户的反馈提出、实现和改变的,而不是一开始就完全设计。脸谱网采用的持久、快速、迭代的开发可能是正式验证技术最糟糕的情况,其支持者有时甚至过去认为，只有在事先写下规范后，才应该开发程序。但是我们发现，如果以一种考虑到程序员期望反馈的时间和原因的方式部署，验证技术可能是有效的。INFER 在每个 “diff” 上运行，这是由开发人员提交用于代码审查的代码更改。在 diff 提交并最终部署到手机之前，每天都会有许多错误在 diffs 上报告并由开发人员修复。从技术上讲，重要的一点是推断是一个成分程序分析，它允许向开发人员提供与他们的增量开发流程相一致的反馈。

## 2 Facebook's Software Development Model

Perpetual development. As many internet companies Facebook adopts a continuous development model [9]. In this model, software will never be considered a nished product. Instead features are continuously added and adapted and shipped to users. Fast iteration is considered to support rapid innovation. For its web version, Facebook pushes new changes in the code twice a day.

永久发展。作为许多互联网公司 Facebook 采用持续发展模式 [9]。在此模型中，软件永远不会被视为成品。相反，功能会不断添加和调整，并运送给用户。快速迭代被认为是支持快速创新。对于其网络版本，Facebook 每天两次在代码中推出新的更改。

This perpetual development model fits well with the product and its use-case. It would be impossible to foresee a-priori how a new feature would be used by the hundreds of million of people using Facebook services every day. The different uses influence the way a new feature is shaped and further developed. In other words, Facebook prioritizes people using the product rather than an initial design proposed in some fixed specification by architects at the company.

这种永久开发模型非常适合产品及其用例。每天有数亿人使用脸谱网服务，不可能事先预见到新功能会如何使用。不同的用途影响了新特性的塑造和进一步发展的方式。换句话说，Facebook 优先考虑使用该产品的人，而不是公司建筑师在一些固定规范中提出的初始设计。

Perpetual development on mobile versus web. In the last couple of years, Facebook has gone through a shift. From being a web-based company Facebook transitioned to embrace mobile. Use of its mobile applications on the Android and iOS platforms has increased substantially, reflecting a global trend for consumers of Internet content.

移动与网络上的永久发展。在过去的几年里，脸谱网经历了一场转变。从基于网络的公司脸谱网过渡到拥抱移动。其移动应用程序在 Android 和 iOS 平台上的使用大幅增加，反映了互联网内容消费者的全球趋势。

For mobile applications, Facebook applies a continuous development model as well. However there are some fundamental differences w.r.t. web development. Although the development cycle is the same, the deployment is fundamentally different. In web development the software runs on Facebook servers in our data centers, and in the client on code downloaded from our servers by a browser. New code can, therefore, be deployed directly to the servers, which then serves the users (including by serving them Javascript); new versions of the software are deployed without the users getting involved.

对于移动应用程序，Facebook 也应用了持续开发模式。然而，w.r.t.有一些根本的区别。Web 开发。虽然开发周期是相同的，但部署是根本不同的。在 web 开发中，软件在我们数据中心的 Facebook 服务器上运行，在客户端通过浏览器从我们的服务器下载代码。因此，新代码可以直接部署到服务器上，然后服务器为用户服务 (包括通过为他们提供 Javascript); 新版本的软件是在用户不参与的情况下部署的。

On the contrary, mobile applications run on users' phones. Therefore, it is up to the user to update to a new version of the app implementing new features or fixing existing bugs. Facebook can only distribute a new version to the Apple App Store or Google Play, but Facebook is not anymore in control of which version a user is running on her mobile device.

相反，移动应用运行在用户的手机上。因此，用户应该更新到应用程序的新版本，实现新功能或修复现有错误。Facebook 只能向 Apple App Store 或 Google Play 发布新版本，但 Facebook 不再控制用户在她的移动设备上运行的版本。

This difference has dramatic impact on bug fixes. On web when a bug is discovered a fix can be shipped to the servers as part of a periodic release or in exceptional cases immediately via a "hotfix". And on the web mechanisms exist to automatically update the JavaScript client software running in the browser, allowing fixes to quickly and automatically be deployed as soon as they have been developed. On current mobile platforms updates must typically be explicitly authorized by the device owner, so there is no guarantee that a fix will be deployed in a timely manner, if ever, once it is developed.

这种差异对 bug 修复有巨大的影响。在 web 上，当发现错误时，可以将修复程序作为定期发布的一部分发送到服务器，或者在特殊情况下，立即通过 “修补程序” 发送。并且在 web 上存在自动更新浏览器中运行的 JavaScript 客户端软件的机制，允许修复程序在开发完成后立即快速自动部署。在当前的移动平台上，更新通常必须得到设备所有者的明确授权，因此不能保证修复程序一旦开发出来就会及时部署。

The sandboxes provided by modern web browsers also make it easier to isolate the effects of a bug in one part of the interface from another, allowing the experience to gracefully degrade in the face of runtime errors. Current mobile platforms typically provide a model closer to processes running on a traditional operating system and will often terminate the entire app when a runtime error is detected in any part of it. This lower fault tolerance increases the potential severity of bugs which would have a minor impact on the web.

现代 web 浏览器提供的沙箱还可以更容易地将界面一部分中的 bug 效果与另一部分隔离开来，从而在面对运行时错误时优雅地降低体验。当前的移动平台通常提供更接近传统操作系统上运行的进程的模型，并且当在任何部分检测到运行时错误时，通常会终止整个应用。这种较低的容错能力增加了可能对 web 产生轻微影响的 bug 的严重程度。

Thus mobile development at Facebook presents a strong dichotomy: on one hand it employs continuous development; on the other hand it could benet from techniques like formal verification to prevent bugs before apps are shipped.

因此，Facebook 的移动开发呈现了一种强烈的二分法: 一方面，它采用了持续的开发; 另一方面，它可以从正式验证等技术中受益，以防止应用程序发货前的错误。

When the INFER team landed at Facebook there was a well developed version of INFER for C programs, and a rudimentary version for Java. Facebook has considerable amounts of C++, Javascript, php, objective-C and Java code, but less development is being done in pure C. This, together with the above discussion determined our first targets, Android and iPhone apps.

当 INFER 团队登陆 Facebook 时，有一个开发良好的 C 程序 INFER 版本，以及 Java 的基本版本。Facebook 有相当多的 C + +，Javascript，php，objective-C 和 Java 代码，但在纯 C 中进行的开发较少。这一点，连同上述讨论确定了我们的第一个目标，Android 和 iPhone 应用程序。

## 3 Software Verication in the Perpetual Development Era

As we have seen, Facebook employs a perpetual development model both for web and mobile software. While in such an environment it is dicult to envisage requiring specs to always be written before programming starts, a common approach in static analysis has been to work towards the implicit specication that (certain) runtime errors cannot occur. Of course, when an assertion is placed into code it can help the analysis along. In INFER's case at the beginning the implicit safety properties were null pointer exceptions and resource leaks for Android code, and additionally memory leaks for iOS.

正如我们所见，脸谱网对网络和移动软件都采用了永久的发展模式。虽然在这样的环境中，很难设想要求规范总是在编程开始之前编写，静态分析中的一种常见方法是朝着隐含的规范 (某些) 工作不能发生运行时错误。当然，当断言被放入代码中时，它可以帮助分析。在 INFER 的案例中，开始时隐含的安全属性是 null 指针异常和 Android 代码的资源泄漏，以及 iOS 的内存泄漏。

Unlike many other software companies, Facebook does not have a separate quality assurance (QA) team or professional testers. Instead, engineers write (unit) tests for their newly developed code. But as a part of the commit and push process there is a set of regression tests that are automatically run and the code must pass them before it can be pushed. This juncture, when diffs are reviewed by humans and by tests, is a key point where formal verification techniques based on static analysis can have impact.

与许多其他软件公司不同，Facebook 没有单独的质量保证 (QA) 团队或专业测试人员。相反，工程师为他们新开发的代码编写 (单元) 测试。但是作为提交和推送过程的一部分，有一组回归测试是自动运行的，代码必须在推送之前通过它们。当人类和测试对差异进行审查时，这是一个关键点，基于静态分析的正式验证技术可以产生影响。

There are several features that the verification technique should offer to be adopted in such different environment: 
- Full automation and integration. The technique should be push-button and integrated into the development environment used by programmers. 
- Scalability. The technique scales to millions of lines of code. 
- Precision. Developers' time is an important resource. An imprecise tool providing poor results would be seen as a waste of that resource. 
- Fast Reporting. The analysis should not get in the way of the development cycle; therefore it has to report to developers in minutes, before programmers commit or make further changes. As we will see in Section 5, fast reporting is not only about analyzing code fast, but it also involves good integration with the existing infrastructure where many other tasks need to be performed.

核查技术应提供在如此不同的环境中采用的几个特点:
- 完全自动化和集成。该技术应该是按钮，并集成到程序员使用的开发环境中。
- 可扩展性。这项技术可以扩展到数百万行代码。
- 精度。开发人员的时间是一个重要的资源。提供糟糕结果的不精确工具将被视为对该资源的浪费。
- 快速报告。分析不应该妨碍开发周期; 因此，在程序员提交或做出进一步更改之前，它必须在几分钟内向开发人员报告。正如我们将在第 5 节中看到的，快速报告不仅仅是关于快速分析代码，它还涉及与需要执行许多其他任务的现有基础架构的良好集成。

These requirements are challenging. In our context we are talking about analysis of large Android and iPhone apps (millions of lines of code are involved in the codebases). The analysis must be able to run on thousands of code diffs in a day, and it should report in under 10 minutes on average to t in with the developer work ow. There are intra-procedural analyses and linters which t these scaling requirements, and which are routinely deployed at Facebook and other companies with similar scale codebases and workflows. But if an analysis is to detect or exclude bugs involving chains of procedure calls, as one minimally expects of verification techniques, then an inter-procedural analysis is needed, and making inter-procedural analyses scale to this degree while maintaining any degree of accuracy has long been a challenge.

这些要求具有挑战性。在我们的上下文中，我们正在讨论对大型 Android 和 iPhone 应用程序的分析 (代码库中涉及数百万行代码)。分析必须能够在一天内运行数千个代码差异，并且它应该在平均不到 10 分钟的时间内报告给开发人员。有程序内的分析和术语来满足这些扩展要求，这些分析和术语通常部署在 Facebook 和其他具有类似规模代码库和工作流的公司。但是如果一个分析是检测或排除涉及过程调用链的错误，就像人们对验证技术的最低期望一样，那么就需要一个过程间分析,长期以来，让程序间分析达到这种程度，同时保持任何程度的准确性一直是一个挑战。

## 4 Background: the INFER Static Analyser

INFER [4] is a program analyzer aimed at verifying memory safety and developed initially by Monoidics Ltd. It was first aimed at C code and later extended to Java. After the acquisition of Monoidics by Facebook, INFER's development now continues inside Facebook.

INFER [4] 是一种旨在验证内存安全的程序分析器，最初由 Monoidics Ltd.开发，最初针对 C 代码，后来扩展到 Java。在 Facebook 收购 Monoidics 后，INFER 的发展现在继续在 Facebook 内部进行。

INFER combines several recent advances in automatic verification. It's underlying formalism is separation logic [10]. It implements a compositional, bottom-up variant of the classic RHS inter-procedural analysis algorithm based on procedure summaries [11]. There are two main novelties. First, it uses compact summaries, based on the ideas of footprints and frame inference [2] from separation logic, to avoid the need for huge summaries that explicitly tabulate most of the input-output possibilities. Second, it uses a variation on the notion of abductive inference to discover those summaries [5].

[10] Peter W. O'Hearn, John C. Reynolds, and Hongseok Yang. Local reasoning about programs that alter data structures. In Computer Science Logic, 15th Interna- tional Workshop, CSL 2001. 10th Annual Conference of the EACSL, Paris, France, September 10-13, 2001, Proceedings, pages 1-19, 2001.

[11] Thomas W. Reps, Susan Horwitz, and Shmuel Sagiv. Precise interprocedural data ow analysis via graph reachability. In Conference Record of POPL'95: 22nd ACM SIGPLAN-SIGACT Symposium on Principles of Programming Languages, San Francisco, California, USA, January 23-25, 1995, pages 49{61, 1995.

[2] Josh Berdine, Cristiano Calcagno, and Peter W. O'Hearn. Smallfoot: Modular automatic assertion checking with separation logic. In Formal Methods for Com- ponents and Objects, 4th International Symposium, FMCO 2005, Amsterdam, The Netherlands, November 1-4, 2005, Revised Lectures, pages 115-137, 2005.

[5] Cristiano Calcagno, Dino Distefano, Peter W. O'Hearn, and Hongseok Yang. Compositional shape analysis by means of bi-abduction. J. ACM, 58(6):26, 2011.

INFER 结合了自动验证的几个最新进展。它的潜在形式主义是分离逻辑 [10]。它实现了基于程序摘要的经典 RHS 程序间分析算法的成分，自下而上的变体 [11]。有两种主要的新奇事物。首先，它使用紧凑的摘要，基于分离逻辑的足迹和帧推断 [2] 的思想,以避免需要明确列出大多数输入输出可能性的巨大摘要。其次，它使用了外展推论概念的变体来发现那些总结 [5]。


## 5 Integration with the Development Infrastructure

## 6 Conclusions
