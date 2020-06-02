# Programming and Proving in Isabelle/HOL

## Introduction

Isabelle is a generic system for implementing logical formalisms, and Isabelle/ HOL is the specialization of Isabelle for HOL, which abbreviates Higher-Order Logic. We introduce HOL step by step following the equation:

```
HOL = Functional Programming + Logic
```

Isabelle是实现逻辑形式的通用系统，而Isabelle/HOL是Isabelle对HOL的专门化，是高阶逻辑的缩写.

We assume that the reader is used to logical and set-theoretic notation and is familiar with the basic concepts of functional programming.

我们假设读者熟悉逻辑和集论符号，并且熟悉函数式编程的基本概念。

Chapter 2 introduces HOL as a functional programming language and explains how to write simple inductive proofs of mostly equational properties of recursive functions. Chapter 3 introduces the rest of HOL: the language of formulas beyond equality, automatic proof tools, single-step proofs, and inductive definitions, an essential specification construct. Chapter 4 introduces Isar, Isabelle’s language for writing structured proofs.

第二章介绍了作为函数式编程语言的HOL，并解释了如何编写简单的递归函数的等式性的归纳证明。第三章介绍了HOL的其余部分:超越等式的公式语言、自动证明工具、单步证明和归纳定义，这是一个基本的规范构造。第四章介绍伊莎贝尔的结构化证明语言Isar。

This introduction to the core of Isabelle is intentionally concrete and example-based: we concentrate on examples that illustrate the typical cases without explaining the general case if it can be inferred from the examples. We cover the essentials (from a functional programming point of view) as quickly and compactly as possible.

本文对Isabelle核心的介绍有意采用了具体的、基于实例的方法:如果可以从示例中推断出一般的情况，那么我们将重点介绍说明典型情况的示例，而不解释一般情况。我们尽可能快速、简洁地介绍了基本内容(从函数式编程的角度)。

For a comprehensive treatment of all things Isabelle we recommend the Isabelle/Isar Reference Manual [7], which comes with the Isabelle distribution. The tutorial by Nipkow, Paulson and Wenzel [6] (in its updated version that comes with the Isabelle distribution) is still recommended for the wealth of examples and material, but its proof style is outdated. In particular it does not cover the structured proof language Isar.

对于所有Isabelle的综合治疗，我们推荐Isabelle/Isar参考手册[7]，这是随Isabelle发行版。Nipkow、Paulson和Wenzel[6](在Isabelle发行版的更新版本中)编写的教程仍然被推荐用于丰富的示例和材料，但是它的证明风格已经过时了。特别地，它不包括结构化证明语言Isar。

If you want to apply what you have learned about Isabelle we recommend you download and read the book Concrete Semantics [5], a guided tour of the wonderful world of programming langage semantics formalised in Isabelle. In fact, Programming and Proving in Isabelle/HOL constitutes part I of Concrete Semantics. The web pages for Concrete Semantics also provide a set of LATEX-based slides and Isabelle demo files for teaching Programming and Proving in Isabelle/HOL.

如果您想应用您对Isabelle的了解，我们建议您下载并阅读《具体语义[5]》这本书，这是在Isabelle中形式化的编程语言语义的奇妙世界的向导之旅。事实上，Isabelle/HOL的编程和证明构成了具体语义的第一部分。具体语义的web页面还提供了一组基于latelle的幻灯片和Isabelle演示文件，用于在Isabelle/HOL中教授编程和证明。

## Programming and Proving

## Logic and Proof Beyond Equality

## Isar: A Language for Structured Proofs

