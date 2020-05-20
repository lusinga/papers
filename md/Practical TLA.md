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