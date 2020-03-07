# Unsupervised Learning of API Aliasing Specifications

## Abstract

Real world applications make heavy use of powerful libraries and frameworks, posing a significant challenge for static analysis as the library implementation may be very complex or unavailable. Thus, obtaining specifications that summarize the behaviors of the library is important as it enables static analyzers to precisely track the effects of APIs on the client program, without requiring the actual API implementation.

现实世界中的应用程序大量使用强大的库和框架，这给静态分析带来了巨大的挑战，因为库的实现可能非常复杂或不可用。因此，获得总结库行为的规范非常重要，因为它使静态分析程序能够精确地跟踪API对客户机程序的影响，而不需要实际的API实现。

In this work, we propose a novel method for discovering aliasing specifications of APIs by learning from a large dataset of programs. Unlike prior work, our method does not require manual annotation, access to the library’s source code or ability to run its APIs. Instead, it learns specifications in a fully unsupervised manner, by statically observing usages of APIs in the dataset. The core idea is to learn a probabilistic model of interactions between API methods and aliasing objects, enabling identification of additional likely aliasing relations, and to then infer aliasing specifications of APIs that explain these relations. The learned specifications are then used to augment an API-aware points-to analysis.

在这项工作中，我们提出了一个新的方法来发现别名规范的api学习从一个大的数据集的程序。与以前的工作不同，我们的方法不需要手工注释、访问库的源代码或运行其api的能力。相反，它以完全无监督的方式学习规范，通过静态地观察数据集中api的用法。其核心思想是学习API方法和别名对象之间交互的概率模型，从而能够识别其他可能的别名关系，然后推断出解释这些关系的API的别名规范。然后使用所学习的规范来增强api感知点到分析。

We implemented our approach in a tool called USpec and used it to automatically learn aliasing specifications from millions of source code files. USpec learned over 2000 specifications of various Java and Python APIs, in the process improving the results of the points-to analysis and its clients.

我们在一个名为USpec的工具中实现了我们的方法，并使用它从数百万个源代码文件中自动学习别名规范。USpec学习了2000多种Java和Python api的规范，在这个过程中改进了指向分析和它的客户端的结果。
