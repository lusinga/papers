# CodeSearchNet Challenge Evaluating the State of Semantic Code Search

## ABSTRACT

Semantic code search is the task of retrieving relevant code given a natural language query. While related to other information retrieval tasks, it requires bridging the gap between the language used in code (often abbreviated and highly technical) and natural language more suitable to describe vague concepts and ideas.

语义代码搜索是在给定自然语言查询的情况下检索相关代码的任务。 虽然与其他信息检索任务相关，但它需要弥合代码中使用的语言（通常是缩写和高度技术性的）与更适合描述模糊概念和想法的自然语言之间的差距。

To enable evaluation of progress on code search, we are releasing the CodeSearchNet Corpus and are presenting the CodeSearch- Net Challenge, which consists of 99 natural language queries with about 4k expert relevance annotations of likely results from Code- SearchNet Corpus. The corpus contains about 6 million functions from open-source code spanning six programming languages (Go, Java, JavaScript, PHP, Python, and Ruby). The CodeSearchNet Corpus also contains automatically generated query-like natural language for 2 million functions, obtained from mechanically scraping and preprocessing associated function documentation. In this article, we describe the methodology used to obtain the corpus and expert labels, as well as a number of simple baseline solutions for the task.

为了能够评估代码搜索的进展，我们发布了 CodeSearchNet 语料库并展示了 CodeSearch-Net 挑战赛，其中包含 99 个自然语言查询，以及来自 Code-SearchNet 语料库的可能结果的大约 4k 专家相关性注释。 该语料库包含来自跨越六种编程语言（Go、Java、JavaScript、PHP、Python 和 Ruby）的开源代码的约 600 万个函数。 CodeSearchNet 语料库还包含为 200 万个函数自动生成的类似查询的自然语言，这些函数是从机械抓取和预处理相关函数文档中获得的。 在本文中，我们描述了用于获取语料库和专家标签的方法，以及该任务的一些简单基线解决方案。

We hope that CodeSearchNet Challenge encourages researchers and practitioners to study this interesting task further and will host a competition and leader board to track the progress on the challenge. We are also keen on extending CodeSearchNet Challenge to more queries and programming languages in the future.

我们希望 CodeSearchNet Challenge 鼓励研究人员和从业者进一步研究这个有趣的任务，并将举办一个竞赛和排行榜来跟踪挑战的进展。 我们也热衷于在未来将 CodeSearchNet Challenge 扩展到更多查询和编程语言。

## 1 INTRODUCTION

## 2 THE CODE SEARCH CORPUS

## 3 THE CODE SEARCH CHALLENGE

## 4 BASELINE CODE SEARCH MODELS



## 6 CONCLUSIONS & OPEN CHALLENGES

We hope that CodeSearchNet is a good step towards engaging with the machine learning, IR and NLP communities towards developing new machine learning models that understand source code and natural language. Despite the fact this report gives emphasis on semantic code search we look forward to other uses of the presented datasets. There are still plenty of open challenges in this area.

我们希望 CodeSearchNet 是与机器学习、IR 和 NLP 社区合作开发能够理解源代码和自然语言的新机器学习模型的良好步骤。 尽管该报告强调语义代码搜索，但我们期待所呈现数据集的其他用途。 在这个领域仍然有很多公开的挑战。

- Our ElasticSearch baseline, that performs traditional keyword based search, performs quite well. It has the advantage of being able to efficiently use rare terms, which often appear in code. Researching neural methods that can efficiently and accurately represent rare terms will improve performance.

- Code semantics such as control and data flow are not exploited explicitly by existing methods, and instead search methods seem to be mainly operate on identifiers (such as variable and function) names. How to leverage semantics to improve results remains an open problem.

现有方法没有明确利用控制和数据流等代码语义，而是搜索方法似乎主要对标识符（例如变量和函数）名称进行操作。 如何利用语义来改善结果仍然是一个悬而未决的问题。

- Recently, in NLP, pretraining methods such as BERT [8] have found great success. Can similar methods be useful for the encoders considered in this work?

最近，在 NLP 中，BERT [8] 等预训练方法取得了巨大成功。 类似的方法对这项工作中考虑的编码器有用吗？

• Our data covers a wide range of general-purpose code queries. However, anecdotal evidence indicates that queries in specific projects are usually more specialized. Adapting search methods to such use cases could yield substantial performance improvements.

我们的数据涵盖了广泛的通用代码查询。 然而，轶事证据表明特定项目中的查询通常更专业。 使搜索方法适应此类用例可以产生显着的性能改进。

- Code quality of the searched snippets was a recurrent issue with our expert annotators. Despite its subjective nature, there seems to be agreement on what constitutes very bad code. Using code quality as an additional signal that allows for filtering of bad results (at least when better results are available) could substantially improve satisfaction of search users.

搜索片段的代码质量是我们的专家注释者经常遇到的问题。 尽管它具有主观性，但对于什么构成非常糟糕的代码似乎是一致的。 使用代码质量作为允许过滤不良结果（至少在有更好的结果可用时）的附加信号可以显着提高搜索用户的满意度。

