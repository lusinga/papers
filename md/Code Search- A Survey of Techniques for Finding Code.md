# Code Search: A Survey of Techniques for Finding Code

The immense amounts of source code provide ample challenges and opportunities during software development. To handle the size of code bases, developers commonly search for code, e.g., when trying to find where a particular feature is implemented or when looking for code examples to reuse. To support developers in finding relevant code, various code search engines have been proposed. This article surveys 30 years of research on code search, giving a comprehensive overview of challenges and techniques that address them.We discuss the kinds of queries that code search engines support, how to preprocess and expand queries, different techniques for indexing and retrieving code, and ways to rank and prune search results. Moreover, we describe empirical studies of code search in practice. Based on the discussion of prior work, we conclude the article with an outline of challenges and opportunities to be addressed in the future.

大量的源代码在软件开发过程中提供了大量的挑战和机会。为了处理代码库的大小，开发人员通常会搜索代码，例如，当试图找到某个特定特性在哪里实现时，或者在寻找要重用的代码示例时。为了帮助开发者找到相关的代码，各种各样的代码搜索引擎已经被提出。本文回顾了30年来对代码搜索的研究，全面概述了挑战和解决这些挑战的技术。我们讨论了代码搜索引擎支持的各种查询，如何预处理和扩展查询，索引和检索代码的不同技术，以及对搜索结果进行排序和修剪的方法。此外，我们在实践中描述了代码搜索的实证研究。基于之前工作的讨论，我们总结了本文，概述了未来需要解决的挑战和机遇。

# 1 INTRODUCTION

Many kinds of information are stored in digital systems, which offer convenient access, large storage capacities, and the ability to process information automatically. To enable people to quickly find digitally stored information, research on information retrieval has led to powerful search engines. Today, commercial search engines are used by billions of people every day to retrieve various kinds of information [112], such as textual information, images, or videos.

许多种类的信息存储在数字系统中，它提供了方便的访问、大容量的存储和自动处理信息的能力。为了使人们能够快速找到数字存储的信息，对信息检索的研究催生了强大的搜索引擎。今天，数十亿人每天使用商业搜索引擎来检索各种信息[112]，例如文本信息、图像或视频。

# 2 QUERIES FOR SEARCHING CODE

# 3 PREPROCESSING AND EXPANSION OF QUERIES

# 4 INDEXING OR TRAINING, FOLLOWED BY RETRIEVAL OF CODE

# 5 RANKING AND PRUNING OF SEARCH RESULTS

# 6 EMPIRICAL STUDIES OF CODE SEARCH

# 7 OPEN CHALLENGES AND RESEARCH DIRECTIONS

# 8 CONCLUDING REMARKS

This article provides a comprehensive overview of 30 years of research on code search. Given the huge amounts of existing code, searching for specific code examples is a common activity during software development. To support developers during this activity, various techniques for finding relevant code have been proposed, with an increase of interest during recent years. We discuss what kinds of queries code search engines support, and give an overview of the main components used to retrieve suitable code examples. In particular, the article discusses techniques to pre-process and expand queries, approaches toward indexing and retrieving code, and ways of pruning and ranking search results. Our article enables readers to obtain an overview of the field, or to fill in gaps of their knowledge of the state of the art. Based on our survey of past work, we conclude that code search has evolved into a mature research field, with solid results that have already made an impact on real-world software development. Despite all advances, many open challenges remain to be addressed in the future, and we hope our article will provide a useful starting point for addressing them.

本文全面概述了30年来对代码搜索的研究。由于存在大量的现有代码，搜索特定的代码示例是软件开发过程中的一项常见活动。为了在这一活动中支持开发人员，人们提出了各种查找相关代码的技术，近年来人们对这些技术的兴趣有所增加。我们将讨论代码搜索引擎支持哪些类型的查询，并概述用于检索合适的代码示例的主要组件。本文特别讨论了预处理和扩展查询的技术、索引和检索代码的方法，以及修剪和排序搜索结果的方法。我们的文章使读者能够获得该领域的概述，或填补他们的知识空白的艺术状态。基于我们对过去工作的调查，我们得出结论，代码搜索已经发展成为一个成熟的研究领域，其坚实的成果已经对现实世界的软件开发产生了影响。尽管取得了这些进展，但未来仍有许多开放的挑战有待解决，我们希望我们的文章将为解决这些问题提供一个有用的起点。
