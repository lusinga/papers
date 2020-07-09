# Code generation from Isabelle/HOL theories

## 1 Introduction

This tutorial introduces the code generator facilities of Isabelle/HOL. It allows to turn (a certain class of) HOL specifications into corresponding executable code in the programming languages SML [9], OCaml [8], Haskell [11] and Scala [5].

本教程介绍了Isabelle/HOL的代码生成器设施。它允许将(某种类型的)HOL规范转换为编程语言SML[9]、OCaml[8]、Haskell[11]和Scala[5]中相应的可执行代码。

To profit from this tutorial, some familiarity and experience with Isabelle/HOL [10] and its basic theories is assumed.

为了从本教程中获益，我们假设您对Isabelle/HOL[10]及其基本理论有一些熟悉和经验。

#### 1.1 Code generation principle: shallow embedding

The key concept for understanding Isabelle’s code generation is shallow embedding: logical entities like constants, types and classes are identified with corresponding entities in the target language. In particular, the carrier of a generated program’s semantics are equational theorems from the logic. If we view a generated program as an implementation of a higher-order rewrite system, then every rewrite step performed by the program can be simulated in the logic, which guarantees partial correctness [7].

理解Isabelle的代码生成的关键概念是浅嵌入:像常量、类型和类这样的逻辑实体在目标语言中用相应的实体标识。特别地，生成程序的语义的载体是来自逻辑的等式定理。如果我们将生成的程序看作是一个高阶重写系统的实现，那么程序执行的每一个重写步骤都可以在逻辑上进行模拟，从而保证了[7]的部分正确性。

#### 1.2 A quick start with the Isabelle/HOL toolbox

In a HOL theory, the datatype and definition/primrec/fun declarations form the core of a functional programming language. By default equational theorems stemming from those are used for generated code, therefore “naive” code generation can proceed without further ado.

在HOL理论中，数据类型和definition/primrec/fun声明构成了函数式编程语言的核心。在默认情况下，生成的代码将使用由这些定理派生的等式定理，因此“朴素”的代码生成可以继续进行，无需更多的麻烦。

## 2 Code generation foundations

