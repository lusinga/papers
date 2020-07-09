# Concrete Semantics with Isabelle/HOL

Isabelle is a generic system for implementing logical formalisms, and Isabelle/ HOL is the specialization of Isabelle for HOL, which abbreviates Higher- Order Logic. We introduce HOL step by step following the equation HOL = Functional Programming + Logic.

Isabelle是一个实现逻辑形式主义的通用系统，Isabelle/ HOL是Isabelle对HOL的专门化，后者缩写为更高阶逻辑。我们按照方程HOL =函数式编程+逻辑一步一步介绍HOL。

We assume that the reader is used to logical and set-theoretic notation and is familiar with the basic concepts of functional programming. Open-minded readers have been known to pick up functional programming through the wealth of examples in Chapter 2 and Chapter 3.

我们假设读者习惯于逻辑和集论符号，并且熟悉函数式编程的基本概念。开明的读者已经通过第2章和第3章中丰富的示例学习了函数编程。

Chapter 2 introduces HOL as a functional programming language and explains how to write simple inductive proofs of mostly equational properties of recursive functions. Chapter 3 contains a small case study: arithmetic and boolean expressions, their evaluation, optimization and compilation. Chapter 4 introduces the rest of HOL: the language of formulas beyond equality, automatic proof tools, single-step proofs, and inductive definitions, an essential specification construct. Chapter 5 introduces Isar, Isabelle’s language for writing structured proofs.

第二章介绍了HOL作为一种函数式编程语言，并解释了如何编写简单的归纳证明递归函数的大部分等式性质。第三章包含一个小的案例研究:算术和布尔表达式，它们的计算，优化和编译。第4章介绍了HOL的其他内容:超越等式的公式语言、自动证明工具、单步证明和归纳定义，一个基本的规范构造。第五章介绍了伊莎贝尔的结构化证明语言Isar。

This introduction to the core of Isabelle is intentionally concrete and example-based: we concentrate on examples that illustrate the typical cases without explaining the general case if it can be inferred from the examples.We cover the essentials (from a functional programming point of view) as quickly and compactly as possible. After all, this book is primarily about semantics.

这篇对Isabelle核心的介绍有意地是具体的和基于实例的:我们专注于说明典型案例的例子，而不是解释一般案例，如果它可以从这些例子推导出来的话。我们将尽可能快速和紧凑地介绍基本内容(从函数式编程的角度)。毕竟，这本书主要是关于语义学的。

For a comprehensive treatment of all things Isabelle we recommend the Isabelle/Isar Reference Manual [92], which comes with the Isabelle distribution. The tutorial by Nipkow, Paulson and Wenzel [68] (in its updated version that comes with the Isabelle distribution) is still recommended for the wealth of examples and material, but its proof style is outdated. In particular it does not cover the structured proof language Isar.

对于Isabelle的全面治疗，我们推荐Isabelle/Isar参考手册[92]，它与Isabelle分布一起。由Nipkow, Paulson和Wenzel[68]编写的教程(在其更新版本中，随Isabelle发行版而来)仍然因其丰富的例子和材料而被推荐，但是它的证明风格已经过时了。特别地，它没有涵盖结构化证明语言Isar。

## Programming and Proving

This chapter introduces HOL as a functional programming language and shows how to prove properties of functional programs by induction.

本章介绍了HOL作为一种函数式编程语言，并展示了如何通过归纳法证明函数式程序的特性。

### 2.1 Basics

#### 2.1.1 Types, Terms and Formulas

HOL is a typed logic whose type system resembles that of functional programming languages. Thus there are

- base types, in particular bool, the type of truth values, nat, the type of natural numbers ($\mathbb{N}$), and int, the type of mathematical integers ($\mathbb{Z}$).
- type constructors, in particular list, the type of lists, and set, the type of sets. Type constructors are written postfix, i.e., after their arguments. For example, nat list is the type of lists whose elements are natural numbers.
- unction types, denoted by $\Rightarrow$.
- type variables, denoted by $'a$, $'b$, etc., like in ML.

Note that $'a \Rightarrow 'b$ list means $'a \Rightarrow ( 'b list)$, not $('a \Rightarrow 'b) list$: postfix type constructors have precedence over $\Rightarrow$.

Terms are formed as in functional programming by applying functions to arguments. If f is a function of type $\tau_1 \Rightarrow \tau_2$ and t is a term of type $\tau_1$ then f t is a term of type $\tau_2$. We write $t :: \tau$ to mean that term t has type $\tau$.

There are many predefined infix symbols like + and 6. The name of the corresponding binary function is (+), not just +. That is, x + y is nice surface syntax (“syntactic sugar”) for (+) x y.

HOL also supports some basic constructs from functional programming:
```
(if b then t1 else t2)
(let x = t in u)
```

$(case \ t \ of \ pat_1 \Rightarrow t_1 | ... | pat_n \Rightarrow t_n)$

Terms may also contain $\lambda-abstractions$. For example, $\lambda x . x$ is the identity function.

Formulas are terms of type bool. There are the basic constants True and False and the usual logical connectives (in decreasing order of precedence): $\lnot,\land,\lor,\rightarrow$.

公式是bool类型的术语。有基本的常量True和False以及通常的逻辑连接词(优先级递减):$\lnot,\land,\lor,\rightarrow$

Equality is available in the form of the infix function = of type $'a \Rightarrow 'a \Rightarrow bool$ bool. It also works for formulas, where it means “if and only if”.

Quantifiers are written $\forall x. P$ and $\exists x. P$.

Isabelle automatically computes the type of each variable in a term. This is called type inference. Despite type inference, it is sometimes necessary to attach an explicit type constraint (or type annotation) to a variable or term. The syntax is $t :: \tau$ as in m + (n::nat). Type constraints may be needed to disambiguate terms involving overloaded functions such as +.

Isabelle自动计算每个变量在一个术语中的类型。这称为类型推断。尽管有类型推断，但有时需要将显式类型约束(或类型注释)附加到变量或术语。语法是m + (n::nat)中的$t:: \tau$。可能需要类型约束来消除涉及重载函数(如+)的术语的歧义。

Finally there are the universal quantifier $\Lambda$ and the implication $\Rightarrow$. They are part of the Isabelle framework, not the logic HOL. Logically, they agree with their HOL counterparts $\forall$ and $\rightarrow$, but operationally the behave diffrerently. This will become clearer as we go along.

Right-arrows of all kinds always associate to the right. In particular, the formula $A_1 \Rightarrow A_2 \Rightarrow A_3$ means $A_1 \Rightarrow (A_2 \Rightarrow A_3)$. Sometimes we also employ inference rule notation $\frac{A_1 ... A_n}{A}$

#### 2.1.2 Theories

Roughly speaking, a theory is a named collection of types, functions, and theorems, much like a module in a programming language. All Isabelle text needs to go into a theory. The general format of a theory T is 

$
theory\ T\\
imports\ T_1\ ...\ T_n\\
begin\\
definitions, theorems\ and\ proofs\\
end\\
$
where $T_1\ ... \ T_n$ are the names of existing theroies that T is based on. The $T_i$ are the direct parent theories of T. Everything defined in the parent theories(and their parents, recursively) is automatically visible. Each theory T must reside in a theory file named T.thy. 

HOL addition to the theory Main, the union of all the basic predefined theories like arithmetic, lists, sets, etc. Unless you know what you are doing, always include Main as a direct or indirect parent of all your theories. 

In addition to the theories that come with the Isabelle/HOL distribution (see https://isabelle.in.tum.de/library/HOL) there is also the Archive of Formal Proofs at https://isa-afp.org, a growing collection of Isabelle theories that everybody can contribute to.

#### 2.1.3 Quotation Marks

The textual definition of a theory follows a fixed syntax with keywords like begin and datatype. Embedded in this syntax are the types and formulas of HOL. To distinguish the two levels, everything HOL-specific(terms and types) must be enclosed in quotation marks:"...". Quotation marks around a single identifier can be dropped. When Isabelle prints a syntax error message, it refers to the HOL syntax as the inner syntax and the enclosing theory language as the outer syntax. 

理论的文本定义遵循固定的语法，其中包含begin和datatype等关键字。嵌入在该语法中的是HOL的类型和公式。为了区分这两个层次，所有特定于霍尔的东西(术语和类型)都必须用引号括起来:“…”。可以删除单个标识符周围的引号。当Isabelle打印一个语法错误消息时，它将HOL语法作为内部语法，将封闭的理论语言作为外部语法。

### 2.2 Types bool, nat and list

There are the most important predefined types. We go through them one by one. Based on examples we learn how to define (possible recursive) functions and prove theorems about them by induction and simplification.

有一些最重要的预定义类型。我们一个接一个地讲。基于例子，我们学习如何定义(可能的递归)函数，并通过归纳和简化证明关于它们的定理。

#### 2.2.1 Type bool

The type of boolean values is a predefined datatype
$datatype\ bool = True | False$
with the two values True and False and with many predefined functions: $\lnot, \land, \lor, \rightarrow$, etc. Here is how conjunction could be defined by pattern matching:
$
fun\ conj::"bool \Rightarrow bool \Rightarrow bool" where\\
"conj \ True \ True = True" | \\
"conj \_\_ = False"
$

Both the datatype and function definitions roughly follow the syntax of functional programming language.

#### 2.2.2 Type nat

Natural numbers are another predefined datatype: 
$
datatype\ nat=0 | Suc\ nat
$
All values of type nat are generated bhy the constructors 0 and Suc. Thus the values of type nat are 0, Suc 0, Suc(Suc(0)),etc. There are many predefined functions: +, *, $\leq$, etc. Here is hwo you could define your own addition:
$
fun add :: "nat \Rightarrow nat \Rightarrow nat" where \\
"add\ 0\ n = n" | \\
"add\ (Suc\ m)\ n" = Suc(add\ m\ n) \\
$
And here is a proof of the fact that add m 0 = m:
$
lemma add_02: "add m 0 = m"\\
apply (induction m)\\
apply (auto)\\
done
$

The lemma command starts the proof and gives the lemma a name, add_02. Properties of recursively defined functions need to be established be induction in most cases. Command apply(induction m) instructs Isabelle to start a proof by induction on m. In response, it will show the following proof state:
1. Add 0 0 = 0
2. $\Lambda$m. add m 0 = m $\Rightarrow$ add (Suc m) 0 = Suc m

The numbered lines are known as subgoals. The first subgoal is the base case, the second one is the induction step. The prefix ^m. is Isabelle's way of saying "for an arbitrary but fixed m". The $\Rightarrow$ separetes assumptions from the conclusion. The command apply(auto) instructs Isabelle to try and prove all subgoals automatically, essentially by simplifying them. Because both subgoals are easy, Isabelle can do it. The base case add 0 0 = 0 holds by definition of add, and the induction step is almost as simple: add (Suc m) 0 = Suc(add m 0) = Suc m using first the definition of add and then the induction hypothesis. In summary, both subproofs rely on simplification with function definitions and the induction hypothesis. As a result of that final done, Isabelle associates the lemma just proved with its name. You can now inspect the lemma with the command

The free variable m has been replaced by the unknown ?m. There is no logical difference between the two but there is an operational one: unknowns can be instantiated, which is what you want after some lemma has been proved. 

We have now seen three proofs of add m 0 = 0: the Isabelle one, the terse four lines explaining the base case and the induction step, and just now a model of a traditional inductive proof. The three proofs differ in the level of detail given and the intended reader: the Isabelle proof is for the machine, the informal proofs are for humans. Although this book concentrates on Isabelle proofs, it is important to be able to rephrase those proofs as informal text comprehensible to a reader familiar with traditional mathematical proofs. Later on we will introduce an Isabelle proof language that is closer to traditional informal mathematical language and is often directly readable.

我们现在已经看到了三个证明:伊莎贝尔证明，简洁的四行解释基本情况和归纳步骤，以及刚才的一个传统的归纳证明模型。三种证明在给出的细节水平和目标读者上有所不同:伊莎贝尔证明是给机器的，非正式证明是给人的。虽然这本书集中在伊莎贝尔证明，它是重要的，能够重新措辞这些证明作为非正式文本易于理解的读者熟悉传统数学证明。稍后我们将介绍一种Isabelle证明语言，它更接近于传统的非正式数学语言，通常是直接可读的。

#### 2.2.3 Type list

Although lists are already predefined, we define our own copy for demostration purpose:
$datatype 'a\ list = Nil | Cons 'a "'a\ list"$

- Type 'a list is the type of lists over elements of type 'a. Because 'a is a type variable, lists are in fact polymporphic: the elements of a list can be of arbitrary type(but mjust all be of the same type).
- Lists have two constuctors: Nil, the empty list, and Cons, which puts an element (of type 'a) in front of a list(of type 'a list). Hence all lists are of the form Nil, or Cons x Nil, or Cons x (Cons y Nil), etc. 
- datatype requires no quotation marks on the left-hand side, but on the right-hand side each of the argument types of a constructor needs to be enclosed in quotation marks, unless it is just an identifier(e.g. nat or 'a)

We also define two standard functions, append and reverse:
$
fun\ app :: "'a\ list \Rightarrow 'a\ list \Rightarrow 'a\ list" where\\
"app\ Nil\ ys = ys" | \\
"app(Cons\ x\ xs) ys = Cons x (app xs ys)"
$

```
fun app :: "'a list \<Rightarrow>'a list \<Rightarrow> 'a list " where
"app Nil ys = ys" |
"app (Cons x xs) ys = Cons x (app xs ys)"
fun rev :: "'a list \<Rightarrow> 'a list" where
"rev Nil = Nil" |
"rev (Cons x xs) = app (rev xs) (Cons x Nil)"
```

#### 2.2.4 The Proof Process

We will now demostrate the typical proof process, which involves the formulation and proof of auxiliary lemmas. Our goal is to show that reversing a list twice produces the original list. 

#### 2.2.5 Predefined Lists

Isabelle's predefined lists are the same as the ones above, but with more syntactic sugar:
- [] is Nil
- x # xs is Cons x xs
- $[x_1, ... , x_n] is\ x_1 \# ... \# x_n$
- xs @ ys is app xs ys

### 2.3 Type and Function Definitions

Type synonyms are abbreviations for existing types, for example
```
type_synoyms string = "char list"
```

Type synonyms are expanded after parsing and are not present in internal representation and output. They are mere conveniences for the reader. 

类型同义词在解析后展开，不会出现在内部表示和输出中。它们只不过是为了方便读者。

#### 2.3.1 Datatypes

The general form of a datatype definition looks like this: 
$datatype('a_1,...,'a_n)t = C_1 "\tau_{1,1}"..."\tau_{1,n_1}" | ... | C_k "\tau_{k,1}"..."\tau_{k,n_k}"$

It introduces the constructors $C_i :: \tau_{i,1} \Rightarrow ... \Rightarrow \tau_{i,n_i} \Rightarrow ('a_1,...'a_n)t$ and expresses that any value of this type is built from these constructors in a unique manner. Uniqueness is implied by the following properties of the constructors:
- Distinctness: $C_i ... \neq C_j ... if\ i\neq j$
- Injectivity: $(C_i x_1 ... x_{n_i})=(x_1 = y_1 \land ... \land x_{n_i} = y_{n_i})$

The fact that any value of the datatype is built from the constructors implies the stuctural induction rule: to show P x for all x of type $('a_1,...,'a_n)t$ one need to show $P(C_i x_1 ... x_n)$(for each i) assuming $P(x_j)$ for all j where $\tau_{i,j}=('a_1,...,'a_n)t$. Distinctness and injectivity are applied automatically by auto and other proof methods. Induction must be applied explicitly by auto and other proof methods. Induction must be applied explicitly. 
Like in functional programming language, datatype values can be taken apart with case expression, for example
$(case\ xs\ of [] \Rightarrow 0 | x \# \_ \Rightarrow Suc\ x)$
Case expression must be enclosed in parentheses. 
As an example of a datatype beyond nat and list, consider binary tress: 
```
datatype 'a tree = Tip | Node "'a tree" 'a "'a tree"
fun mirror :: "'a tree ⇒ 'a tree" where
"mirror Tip = Tip" | 
"mirror (Node l a r) = Node(mirror r) a (mirror l)"
lemma "mirror(mirror t) = t"
  apply(induction t)
  apply(auto)
  done
```

A very simple but also very useful datatype is the predefined
```
datatype 'a option = None | Some 'a
```

Its sole purpose is to add a new element None to an existing type 'a. To make sure that None is distinct from all the elements of 'a, you wrap them up in Some and call the new type a' option. A typical application is a lookup function on a list of key-value pairs, often called an association list

#### 2.3.2 Definitions

Non-recursive functions can be defined as in the following example:
```
definition sq :: "nat => nat" where
"sq n = n * n"
```

Such definitions do not allow pattern matching but only $f x_1 ... x_n = t$, where f does not occur in t. 

#### 2.3.3 Abbreviations

Abbreviations are similar to definitions:
$
abbreviation sq' :: "nat \Rightarrow nat" where 
"sq' n \equiv n * n" 
$
The key difference is that sq' is only syntactic sugar: after parsing, sq' t is replaced by t*t; before printing, every occurrence of u*u is replaced by sq' u. Internally, sq' does not exist. This is the advantage of abbreviations over definitions: definitions need to be expanded explicitly whereas abbreviations are already expanded upon parsing. However, abbreviations should be introduced sparingly: if abused, they can lead to confusing discrepancy between the internal and external view of a term. 
The ASCII representation of $\equiv$ is == or \<equiv>

#### 2.3.4 Recursive Functions

Recursive functions are defined with fun by pattern matching over datatype constructors. The order of equations matters, as in functional programming languages. However, all HOL functions must be total. This simplifies the logic - terms are always defined - but means that recursive functions must terminate. Otherwise one could define a function f n = f n +1 and conclude 0 = 1 by subtracting f n on both sides. 

Isabelle's automatic termination checker requires that the arguments of recursive calls on the right-hand side must be strictly smaller than the arguments on the left-hand side. In the simplest case, this means that one fixed argument position decreases in size with each recursive call. The size is measured as the number of constructors(excluding 0-ary ones, e.g. Nil). Lexicograhic combinations are also recognized. In more complicated situations, the user may have to prove termination by hand. 

### 2.4 Induction Heuristics

We have already noted that theorems about recursive functions are proved by induction. In case the function has more than one argument, we have followed the following heuristic in the proofs about the append function:

我们已经注意到关于递归函数的定理是用归纳法证明的。如果函数有多个参数，我们在证明附加函数时遵循以下启发式方法:

Perform induction on argument number i
if the function is defined by recursion on argument number i.

The key heuristic, and the main point of this section, is to generalize the goal before induction. The reason is simple: if the goal is too specific, the induction hypothesis is too weak to allow the induction step to go through. Let us illustrate the idea with an example.


### 2.5 Simplification

#### 2.5.1 Simplification Rules

#### 2.5.2 Conditional Simplification Rules

#### 2.5.3 Termination

#### 2.5.4 The simp Proof Method

#### 2.5.5 Rewriting with Definitions

#### 2.5.6 Case Splitting With simp

## 3. Case Study: IMP Expressions

The methods of the previous chapter suffice to define the arithmetic and boolean expressions of the programming language IMP that is the subject of this book. In this chapter we define their syntax and semantics, write little optimizers for them and show how to compile arithmetic expressions to a simple stack machine. Of course we also prove the correctness of the optimizers and compiler!

上一章的方法足以定义编程语言IMP的算术和布尔表达式，这也是本书的主题。在本章中，我们将定义它们的语法和语义，为它们编写小的优化器，并展示如何将算术表达式编译到一个简单的堆栈机器上。当然，我们也证明了优化器和编译器的正确性!

### 3.1 Arithmetic Expressions

### 3.2 Boolean Expressions

### 3.3 Stack Machine and Compilation

## 4 Logic and Proof Beyond Equality

### 4.1 Formulas

### 4.2 Sets

### 4.3 Proof Automation

### 4.4 Single Step Proofs

### 4.5 Inductive Definitions

## 5 Isar: A Language for Structured Proofs

### 5.1 Isar by Example

### 5.2 Proof Patterns

### 5.3 Streamlining Proofs

### 5.4 Case Analysis and Induction

# Semantics

## Introduction

## 7 IMP: A Simple Imperative Language

### 7.1 IMP Commands

### 7.2 Big-Step Semantics

### 7.3 Small-Step Semantics

### 7.4 Summary and Further Reading

## 8 Compiler

### 8.1 Introductions and Stack Machine

### 8.2 Reasoning about machine executions

### 8.3 Compilation

### 8.4 Preservation of Semantics

### 8.5 Summary and Further Reading

## 9 Types

### 9.1 Typed IMP

### 9.2 Security Type Systems

### 9.3 Summary adn Further Reading

## 10 Program Analysis

### 10.1 Definite Initialization Analysis

### 10.2 Constant Folding and Propagation

### 10.3 Live Variable Analysis

### 10.4 True Liveness

## 11 Denotational Semantics
指称语义

### 11.1 A Relational Denotational Semantics

### 11.2 Summary and Further Reading

## 12 Hoare Logic

### 12.1 Proof via Operational Semantics

### 12.2 Hoare Logic for Partial Correctness

### 12.3 Soundness and Completeness

### 12.4 Verification Condition Generation

### 12.5 Hoare Logic for Total Correctness

### 12.6 Summary and Further Reading

## 13 Abstract Interpretation

### 13.1 Informal Introduction

### 13.2 Annotated Commands

### 13.3 Collecting Semantics

### 13.4 Abstract Values

### 13.5 Generic Abstract Interpreter

### 13.6 Executable Abstract States

### 13.7 Analysis of Boolean Expression

### 13.8 Interval Analysis

### 13.9 Widening and Narrowing

### 13.10 Summary and Further Reading
