# Practical TLA+

## PART I - The Semantics of TLA+ and PlusCal

### CHAPTER 1 An Example

Let’s write our first specification! In this chapter we will take a simple problem and translate it into a specification. Then, we’ll model check the specification and see if it has a flaw. Spoiler alert, it will have a flaw. This will be a whirlwind tour: we will be using concepts we will gradually learn over the course of the book.

让我们编写我们的第一个规范!在本章中，我们将把一个简单的问题转化为一个规范。然后，我们将模型检查规范，看看它是否有缺陷。剧透警告，它将有一个缺陷。这将是一个旋风之旅:我们将使用概念，我们将逐步学习的过程中，这本书。

#### The Problem

Alice and Bob have accounts at Bankgroup. Each account has 0 or more dollars in it. Bankgroup wants to add a new “wire” feature, where any user can transfer money to any other user. This feature has the following requirements: 
- Each wire must be between two different people in the bank and wire at least one dollar. 
- If a wire is successful, the value of the wire is deducted from the sender account and added to the receiver account. 
- If the wire fails, the two accounts are unchanged. 
- A wire may not cause an account to have a negative balance. 
- For scaling reasons, multiple wires may happen simultaneously.

爱丽丝和鲍勃在银行集团有账户。每个账户都有0或更多的美元。Bankgroup希望增加一个新的“电汇”功能，任何用户都可以将钱转到其他用户。该功能有以下要求:
- 每个电汇必须是在银行的两个不同的人之间，并电汇至少一美元。
- 如果连线成功，连线的值将从发送方帐户中扣除，并添加到接收方帐户中。
- 如果连线失败，两个帐号保持不变。
- 电汇可能不会导致帐户出现负余额。
- 由于扩展的原因，多个线可能同时发生。

Your implementation must guarantee all properties, even if wires can take an arbitrary amount of time. In other words, even if wire A starts before B, A may still finish after wire B. Your algorithm must guarantee that, even in those cases, we satisfy all of the requirements. Given that money is on the line, you should probably design this in advance.

您的实现必须保证所有属性，即使连接可能需要任意长的时间。换句话说，即使A在B之前开始，A也可能在B之后结束。你的算法必须保证，即使在这种情况下，我们满足所有的要求。考虑到钱的问题，你可能应该提前设计好。

## CHAPTER 7 Algorithms

One of the benefits of TLA+ being a specification language is that operators can be far more expressive and powerful than program functions can be. This is also a drawback: if your spec uses a “too powerful” operator, you cannot directly translate it to code. Usually this is fine: if you’re specifying a large system, you probably aren’t worrying that your sort function is correct.

TLA+作为一种规范语言的好处之一是，操作符可以比程序函数更有表现力和更强大。这也是一个缺点:如果您的规范使用了“过于强大”的操作符，您就不能直接将其转换为代码。通常这是可以的:如果您指定了一个大型系统，您可能并不担心排序函数是正确的。

If you’re directly writing a new sorting algorithm, though, you want to specify it. This chapter is about how we can write and verify algorithms with TLA+. While we will be implementing them, our focus is on the verification, not the implementation.

但是，如果您直接编写一个新的排序算法，则需要指定它。这一章是关于如何编写和验证算法与TLA+。虽然我们将执行这些措施，但我们的重点是核查，而不是执行。

By “algorithm,” we’re assuming that algorithms are code intended to terminate and produce an output, rather than run forever or continuously interact with its environment.

通过“算法”，我们假设算法是终止并产生输出的代码，而不是永久运行或与环境持续交互的代码。

### Single-Process Algorithms

Most single-process algorithm specifications follow a template:

```
---- MODULE name ----
EXTENDS \* whatever
Expected(input) == \* ...
Helpers == \* ...
(*--algorithm name
variables
input \in \* ...
output; \* ...
\* helper variables
begin
\* algorithm implementation
assert output = Expected(input);
end algorithm; *)
====
```

Expected is what we’re actually trying to implement: it takes some input and returns the value our algorithm should, if correct, output. Helpers are anything that the algorithm will use that is outside of our verification scope. For example, if we were specifying some code for Python, we might make a Sort operator, as Python would give us sorted() by default.

我们实际尝试实现的是Expected:它接受一些输入并返回我们的算法应该(如果正确的话)输出的值。helper是算法将使用的任何超出验证范围的内容。例如，如果我们为Python指定一些代码，我们可能会创建一个Sort操作符，因为Python在缺省情况下会为我们提供Sort()。

For the PlusCal algorithm, we want to specify it works for a given range of inputs, and we will store the return value in output. Here we aren’t defining an initial value for output, since that’s something the algorithm would have to assign. TLC will create the constant DefaultInitValue <- [model constant] and initialize output to that. We also place any helper variables here, as we can’t define new variables in the middle of an algorithm (barring use of with). In the body, we write our implementation of the algorithm. Finally, we make sure that whatever output value we get matches our expectation.

对于PlusCal算法，我们希望指定它适用于给定的输入范围，并将返回值存储在输出中。这里我们没有定义输出的初始值，因为这是算法必须指定的值。TLC将创建常量DefaultInitValue <- [model constant]并初始化输出。我们还在这里放置了任何辅助变量，因为我们不能在算法中间定义新变量(除非使用with)。在正文中，我们编写了算法的实现。最后，我们要确保我们得到的输出值符合我们的期望。

Of course, this is just a starting guideline, not a strict rule. If our algorithm is complex, we might add procedures and macros to break it into parts. We might add assert statements as guards or sanity checks. Or we might want to add a global invariant to hold at every step of the spec, like we do with our larger systems.

当然，这只是一个开始的指导方针，而不是一个严格的规则。如果我们的算法很复杂，我们可以添加过程和宏来将其分解为多个部分。我们可以添加断言语句作为保护或完整性检查。或者，我们可能希望在规范的每个步骤中添加一个全局不变式，就像我们在大型系统中所做的那样。

Here’s what this might look like, all filled out:

```
EXTENDS Integers, TLC
add(a, b) == a + b
(*--algorithm add
variables
in_a \in -5..5,
in_b \in -5..5,
output;
begin
output := in_a + in_b;
assert output = add(in_a, in_b);
end algorithm; *)
```

Let’s do some examples.

### Max

Given a sequence of numbers, return the largest element of that sequence. For example,
`max(<<1, 1, 2, -1>>) = 2`.

First of all, we need our expected operator. We know that for a set, we can get the maximum with `CHOOSE x \in set: \A y \in set: y <= x`. The maximum of a sequence would just be the maximum of its range. Putting those together:

```
EXTENDS Sequences
Max(seq) ==
LET set == {seq[i]: i \in 1..Len(seq)}
IN CHOOSE x \in set: \A y \in set: y <= x
```

We could also find the index that gives us the largest number, and then return the number at that index. It’s some duplicated effort, but some people might find it clearer.

```
Max(seq) ==
LET index ==
CHOOSE x \in 1..Len(seq):
\A y \in 1..Len(seq): seq[y] <= seq[x]
IN seq[index]
```

Either way, here’s a full version of the algorithm:
```
EXTENDS Integers, Sequences, TLC
CONSTANTS IntSet, MaxSeqLen
ASSUME IntSet \subseteq Int
ASSUME MaxSeqLen > 0
PT == INSTANCE PT
Max(seq) ==
LET set == {seq[i]: i \in 1..Len(seq)}
IN CHOOSE x \in set: \A y \in set: y <= x
AllInputs == PT!SeqOf(IntSet, MaxSeqLen)
(*--algorithm max
variables seq \in AllInputs, i = 1, max;
begin
max := seq[1];
while i <= Len(seq) do
if max < seq[i] then
max := seq[i];
end if;
i := i + 1;
end while;
assert max = Max(seq);
end algorithm; *)
```

### Multiprocess Algorithm

### Summary

We verified single-process algorithms were correct and some additional nonfunctional properties about them, such as their worst-case performance and that they didn’t overflow our computer’s maximum value. We also briefly summarized how to extend these ideas to multiprocess algorithms, using temporal properties instead of bare assertions.

我们验证了单进程算法是正确的，以及关于它们的一些附加的非功能属性，例如最坏情况下的性能，以及它们没有超出我们计算机的最大值。我们还简要总结了如何将这些思想扩展到多进程算法，使用时间属性而不是简单的断言。

Many algorithms are defined for specific data structures. And many specs for systems are designed assuming you have your data organized in a specific way. In the next chapter, we will show how to write reusable data structures for algorithms and specifications.

许多算法是为特定的数据结构定义的。而且许多系统规范都是在假设您以特定的方式组织数据的情况下设计的。在下一章中，我们将展示如何为算法和规范编写可重用的数据结构。

## CHAPTER 8 Data Structures

When we want to write a specification involving some data structure, we need some sort of definition of the data structure. Further, we need one that’s independent of the algorithm. That means we should write data structures as separate modules that are extended or instantiated in our spec. We’ll use the example of linked lists (LL), in a file we’ll call LinkedLists.tla.

当我们想要编写一个包含一些数据结构的规范时，我们需要一些数据结构的定义。此外，我们需要一个独立于算法的。这意味着我们应该将数据结构编写为独立的模块，这些模块在我们的规范中被扩展或实例化。

Warning: If you’re making a new specification for this, do not make LinkedLists.tla the root file. Instead, make the root file something else, such as main.tla, and add LinkedLists.tla as a secondary module. This will make it easier to test later. You can do this under File > Open Module > Add TLA+ Module.

A linked list is a low-level data structure where each element (node) of the LL is a data structure containing the data and a pointer to the next node. The last node in the list points to a null element, which is how we know it’s the last one. Critically, though, the LL might not have a last element that points to null. Instead, what would be the “last” element could instead point to an earlier memory address. This is called having a cycle.

链表是一种低级数据结构，其中LL的每个元素(节点)都是一个数据结构，包含数据和指向下一个节点的指针。列表中的最后一个节点指向一个null元素，这就是我们知道它是最后一个元素的原因。但关键的是，LL可能没有指向null的最后一个元素。相反，“最后一个”元素可以指向更早的内存地址。这叫做循环。

In most cases, LLs with cycles are unwanted and indicate there is a bug in the system. This gives us several uses for speccing them: we may want to ensure some algorithm never produces LLs with cycles, or we may want to write an algorithm that detects cycles, or we may want to ensure a system still works properly even if fed a cyclic LL. To support all of these use cases, we want LinkedLists.tla to generate all possible LLs and let us select the subset that has the properties we need for our spec.

在大多数情况下，带有循环的LLs是不需要的，这表明系统中存在错误。这为我们提供了几种指定它们的用途:我们可能希望确保某些算法永远不会产生带有循环的LLs，或者我们可能希望编写一个检测循环的算法，或者我们可能希望确保即使使用循环LL，系统仍然正常工作。为了支持所有这些用例，我们需要linkedlist。tla生成所有可能的LLs，并让我们选择具有我们的规范所需属性的子集。

In TLA+, we generally represent data structures as functions or structures (which are also functions). By convention the module should have a LinkedLists(Nodes) operator that generates all matching functions where Nodes is the set of memory addresses.

在TLA+中，我们通常将数据结构表示为函数或结构(它们也是函数)。按照惯例，模块应该有一个LinkedLists(Nodes)操作符，该操作符生成所有匹配函数，其中节点是内存地址集。

While LL’s have data in them, that data is not central to the core topology of a linked list. All that matters for the base case is that, for a given node, we know what the next node will be. Then our linked list will be some element of the function set [Nodes -> Nodes]. We’ll start by defining all possible mappings between nodes.

虽然LL中有数据，但这些数据不是链表核心拓扑的中心。对于基本情况，最重要的是，对于给定的节点，我们知道下一个节点是什么。那么我们的链表将是函数集[Nodes -> Nodes]中的某个元素。我们首先定义节点之间所有可能的映射。

```
PointerMaps(Nodes) == [Nodes -> Nodes]
LinkedLists(Nodes) == \* ...
```

Next, we need a concept of a final node. It’s simply a node that points to a null value, which means we need a null value. We can add a NULL constant and then assert that none of the nodes are in NULL. This means using TLC to get Assert. We will use LOCAL INSTANCE instead of EXTENDS, so that any spec extending LinkedLists.tla does not also import the TLC operators.

接下来，我们需要一个最终节点的概念。它只是一个指向null值的节点，这意味着我们需要一个null值。我们可以添加一个NULL常量，然后断言所有节点都不是NULL。这意味着使用TLC获取断言。我们将使用本地实例而不是扩展，这样任何扩展linkedlist的规范都可以使用。tla也不导入TLC操作符。

Here’s what we have so far:

```
CONSTANT NULL
LOCAL INSTANCE TLC \* For Assert
PointerMaps(Nodes) == [Nodes -> Nodes \union {NULL}]
LinkedLists(Nodes) ==
IF NULL \in Nodes THEN Assert(FALSE, "NULL cannot be in Nodes") ELSE
\* ...
```

Almost there. PointerMaps is the set of possible memory mappings. But not all possible mappings are LLs. Consider the mapping [n \in Nodes |-> NULL] (Figure 8-1). That’s not a single LL, that’s multiple disjoint LLs, each one element long. We need some way of restricting our function space to “actual” LLs. That’s one where, if you start from the appropriate initial element and keep going to the next node, you eventually reach all of the other nodes and eventually either cycle or hit NULL.

差不多了。PointerMaps是一组可能的内存映射。但并非所有可能的映射都是LLs。考虑节点|-> NULL的映射' [n \in Nodes |-> NULL] '(图8-1)。它不是单个的LL，而是多个不相交的LLs，每个LLs都是一个元素长。我们需要一些方法来将函数空间限制为“实际的”LLs。在这种情况下，如果从适当的初始元素开始，一直到下一个节点，最终会到达所有其他节点，最终要么循环，要么命中NULL。

How do we collect the subset of nodes reachable from a given starting point? One way is to use a recursive operator, such as PT!ReduceSet. We’d begin to add some first node, then whatever add first connects to, then whatever that connects to, and so on. But recursive operators are messy and hard to get right, plus we would need some way of finding first before we even start.

我们如何从给定的起始点收集可到达的节点的子集?一种方法是使用递归运算符，例如PT!ReduceSet。我们开始添加第一个节点，然后添加第一个连接到的节点，然后添加第一个连接到的节点，然后添加第一个连接到的节点，以此类推。但是递归运算符是混乱的，很难得到正确的结果，而且在开始之前我们需要先找到一些方法。

