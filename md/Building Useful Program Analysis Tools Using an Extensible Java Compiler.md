# Building Useful Program Analysis Tools Using an Extensible Java Compiler

## Abstract

Large software companies need customized tools to manage their source code. These tools are often built in an ad-hoc fashion, using brittle technologies such as regular expressions and home-grown parsers. Changes in the language cause the tools to break. More importantly, these ad-hoc tools often do not support uncommon-but-valid code code patterns. We report our experiences building source-code analysis tools at Google on top of a third-party, open-source, extensible compiler. We describe three tools in use on our Java codebase. The first, Strict Java Dependencies, enforces our dependency policy in order to reduce JAR file sizes and testing load. The second, error-prone, adds new error checks to the compilation process and automates repair of those errors at a wholecodebase scale. The third, Thindex, reduces the indexing burden for a Java IDE so that it can support Google-sized projects.

大型软件公司需要定制工具来管理它们的源代码。这些工具通常以特别的方式构建，使用易碎的技术，如正则表达式和自行生成的解析器。语言的变化导致工具崩溃。更重要的是，这些特别的工具通常不支持不同但有效的代码模式。我们报告了在谷歌上基于第三方、开源、可扩展编译器构建源代码分析工具的经验。我们描述了在Java代码库中使用的三种工具。第一个是严格的Java依赖项，它强制执行我们的依赖项策略，以减少JAR文件大小和测试负载。第二种是容易出错的，它将新的错误检查添加到编译过程中，并在整个代码库范围内自动修复这些错误。第三个是Thindex，它减少了Java IDE的索引负担，因此可以支持google大小的项目。
