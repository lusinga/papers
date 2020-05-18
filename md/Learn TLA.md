# Learn TLA+

## INTRODUCTION

## PLUSCAL

PlusCal is a tool for working with TLA+: it adds a pseudocode-like interface to the specs, making them easier for programmers to understand. While not every interesting spec can be written with PlusCal, quite a few can, and it’s a great entry point into modelling. All of the specs we’ll write over the course of this guide will run with PlusCal at the core.

PlusCal是一个用于处理TLA+的工具:它在规范中添加了一个类似于伪代码的接口，使程序员更容易理解它们。虽然不是每个有趣的规范都可以用PlusCal编写，但是有很多可以，这是建模的一个很好的切入点。我们将在本指南中编写的所有规范都将以PlusCal为核心。

### A SIMPLE SPEC

TLA+ started as a notation, and the model checker, TLC, only came out 5 years later. As it was never intended to be run, there’s some assumptions of it being a read document instead of being runnable code. That means that there’s a little bit of boilerplate we’ll need to get through first.

TLA+开始是一种符号，而模型检查器TLC在5年后才出现。因为它从来没有打算运行，所以有一些假设认为它是一个可读文档，而不是可运行代码。这意味着我们需要先处理一些样板文件。

In order for TLC to analyze a spec, it must have the following format:

```
---- MODULE module_name ----
\* TLA+ code

(* --algorithm algorithm_name
begin
\* PlusCal code
end algorithm; *)
====
```

TLC will only analyze the code between the ---- beginning and the ==== end. Anything outside of those boundaries is ignored. module_name must be the same as the filename, while algorithm_name can be whatever you want. \* is a line comment and (* is a block comment. Note we’re putting our algorithm in a comment. If you don’t put it in a comment, you’ll get a syntax error, because PlusCal isn’t TLA+.

TLC将只分析——开始和===结束之间的代码。任何超出这些界限的东西都会被忽略。module_name必须与文件名相同，而algorithm_name可以是您想要的任何名称。\*是行注释，(*是块注释。注意，我们把算法放在注释中。如果你不把它放在注释中，你会得到一个语法错误，因为PlusCal不是TLA+。

Note: PlusCal came out fifteen years after TLA+ did. TLC is supposed to perfectly follow the semantics of TLA+, and since PlusCal is a completely different style it can’t be fit into the same schema. Behind the scenes, we transpile the PlusCal to raw TLA+, and the --algorithm in comments is there to let the PlusCal translator know what it’s supposed to be translating.

注:PlusCal比TLA+晚了15年。TLC应该完全遵循TLA+的语义，因为PlusCal是一种完全不同的风格，它不能适应相同的模式。在幕后，我们将PlusCal转换为raw TLA+，而注释中的——算法可以让PlusCal翻译器知道它应该翻译什么。

You can only have one PlusCal algorithm per file.

每个文件只能有一个PlusCal算法。

As for the PlusCal itself, here’s the layout of a basic single process algorithm:

```
variables x = 1, y \in {3, 4}, z = {3, 4};
begin
  \* your code here
end algorithm;
```

In the variables block, = is assignment. Everywhere else, = is the equality check and := is assignment.

在变量块中，=是赋值。在其他地方，=是等式检查，:=是赋值。

You might have noticed the `y \in {3, 4}`. That’s set notation. It means that for the purposes of this algorithm, y can be 3 or 4. When you check the model, TLC will make sure it works for all possible values of y. It will first test that nothing fails with `y = 3`, and then test that nothing fails with `y = 4`.

您可能已经注意到了“y \in{3,4}”。这就是集合记号。这意味着对于这个算法来说，y可以是3或4。当您检查模型时，TLC将确保它对所有可能的y值都有效。它将首先测试' y = 3 '没有失败，然后测试' y = 4 '没有失败。

Contrast `y \in {3, 4}` with `z = {3, 4}`, which means that z is the set `{3, 4}`. Any sort of data structure can be assigned to a variable in TLA+.

将“y \in{3,4}”与“z ={3,4}”进行比较，这意味着z是集合“{3,4}”。在TLA+中，任何类型的数据结构都可以分配给一个变量。

Note: Since TLA+ is a specification language, it was designed to output really nice documents. That’s why we use TeX like \in and \union and such. Fun fact: Leslie Lamport, the inventor of TLA+, also invented LaTeX! Another fun fact: we won’t be talking about outputting as a specification in any way whatsoever. Cool stuff, but not immediately relevant to model checking.

注:由于TLA+是一种规范语言，所以它的设计目的是输出非常好的文档。这就是为什么我们用TeX，比如\in和\union之类的。有趣的事实:Leslie Lamport, TLA+的发明者，也发明了LaTeX!另一个有趣的事实是:我们不会以任何方式讨论输出作为规范。很酷的东西，但与模型检查没有直接关系。

Let’s get Hello World out of the way.

```pluscal
EXTENDS TLC

(* --algorithm hello_world
variable s \in {"Hello", "World!"};
begin
  A:
    print s;
end algorithm; *)
```

Warning: If you leave the A: out, your PlusCal will still transpile. This is because the TLC can infer the labels in a single process app. For the most part it’s fine to leave them out in single process apps, but you should keep them in mind.

警告:如果你不定A:，你的PlusCal仍然会转译。这是因为TLC可以在单个流程应用程序中推断标签。在大多数情况下，在单个流程应用程序中不使用它们是没有问题的，但是您应该记住它们。

I assume that you’re familiar with other programming languages. To make modeling more familiar, PlusCal has similar constructs. The semantics are fairly obvious, so here’s what they look like. Assume all variables have been initialized before and we’re in a begin block.

我假设你熟悉其他编程语言。为了使建模更加熟悉，PlusCal有类似的构造。语义是相当明显的，下面是它们的样子。假设所有变量之前都已初始化，并且我们处于begin块中。

Logic Operators

|logic	|operator	|TRUE	|FALSE|
|---|---|---|---|
|equal|	=	|1 = 1	| 1 = 2| 
|not equal	|/=	1 | /= 2	| 1 /= 1 |
|and|	/\	|TRUE /\ TRUE	| TRUE /\ FALSE |
|or|	\/  |FALSE \/ TRUE	| FALSE \/ FALSE |
|not|	~	| ~FALSE | ~TRUE |

If

```
if x = 5 then
  skip;
elsif x = 6 then
  skip;
else
  skip;
end if;
```

While

```
x := 5;
while x > 0 do
  x := x - 1;
end while;
```

This is the only form of looping.

Goto

```
A:
  if TRUE then goto B else goto C endif;
B:
  skip;
C:
  skip;
```

Goto considered useful in PlusCal.

### USING THE TOOLBOX

Our goal with writing specifications is to look for errors in the specification. To do this, we first need to convert our PlusCal code into TLA+, and then tell a model checker what to look for. Both of these can be a little tricky. Fortunately, we have an IDE available called TLA+ Toolbox. We’ll be using the following example as the introduction.

我们编写规范的目的是查找规范中的错误。要做到这一点，我们首先需要将我们的PlusCal代码转换成TLA+，然后告诉模型检查器要查找什么。这两个都有点棘手。幸运的是，我们有一个叫做TLA+ Toolbox的IDE。我们将使用以下示例作为介绍。

```
---- MODULE example ----
EXTENDS Integers

(* --algorithm example
variables x = 5
begin
 Add:
  x := x + 1;
end algorithm; *)
====
```

Create a new spec (remember that the name must match) and copy that in. Run the translator. The first thing you will see that your file was updated with something like this:

```
\* BEGIN TRANSLATION

A WHOLE LOTTA STUFF

\* END TRANSLATION
```

That’s the generated TLA+. Probably makes sense now as to why we’re starting with PlusCal. Don’t worry, we’ll be covering TLA+ in depth in the next chapter, but you’re not expected to completely understand the entire translation. But that’s the actual code we’ll be model checking.

这就是生成的TLA+。也许我们从PlusCal开始讲是有道理的。不要担心，我们将在下一章深入讨论TLA+，但是您不需要完全理解整个翻译。但这是我们要检查模型的实际代码。

Terms:

- Model Description: No semantic meaning, just for documentation.
- What is the behavior spec?: Out of scope. Just leave this at “Temporal Formula” for now.
- What is the model?: We’ll cover this in the “Models” chapter.
- What to check?: This is the meat of what we care about.
  - Deadlock checks that the program can’t Catch-22 itself.
  - Invariants check that some equation, for example x = TRUE, is true for all possible states TLC can reach.
  - Temporal Properties check that something is true over all possible behaviors, aka “lifetimes”. For example, “x switches from TRUE to FALSE and back”.
- How to run?: Runtime optimizations. This is out of scope.

术语:
- 模型描述:没有语义意义，只是为了文档。
- 什么是行为规范?:超出范围。暂时把这个留在“时态公式”中。
- 模型是什么?我们将在“模型”一章中讨论这个问题。
- 检查什么?这就是我们所关心的肉。
- 死锁检查，程序不能捕获-22本身。
- 不变量检查某个方程，例如x = TRUE，对于TLC可以达到的所有可能状态都为TRUE。
- 时间属性检查某物在所有可能的行为上是否为真，即“寿命”。例如，“x从TRUE切换到FALSE，然后返回”。
- 怎么跑?:运行时优化。这超出了范围。

We can run the model by clicking the green arrow in the top left. TLC will explore the entire state space, looking for possible failures in our invariants. Since we don’t actually have any invariants yet, it won’t find any failures, so this will be successful. Notice that it lists the diameter as 2 and the number of distinct states as 2. The former means that the longest interesting progression was two steps long: the initial state where x = 5 and the Add step where x = 6. Since there’s only one possible starting state, there are 2 distinct states total.

我们可以通过点击左上角的绿色箭头来运行模型。TLC将探索整个状态空间，在我们的不变量中寻找可能的失败。因为我们实际上还没有任何不变量，它不会发现任何失败，所以这将是成功的。注意，它列出的直径为2，不同的状态数为2。前者意味着最长的有趣的进展是两个步骤:x = 5的初始状态和x = 6的添加步骤。因为只有一种可能的起始状态，所以总共有两种不同的状态。

What happens if we change `x = 5` to `x \in 1..5`? Retranslate the algorithm and rerun the model. You’ll see that while the diameter is still 2, there are now 10 distinct states. Since there are five potential initial values, TLC will run the model with all of them in case one of them fails the (still nonexistent) invariants.

如果我们将“x = 5”改为“x \in 1..5”会发生什么?重新翻译算法并重新运行模型。你会看到直径仍然是2，现在有10个不同的状态。因为有5个可能的初始值，TLC将使用所有的初始值运行模型，以防其中一个不变量失败(仍然不存在)。

Info: These numbers are mostly metadata: they don’t effect if the spec is valid or not, but it’s helpful to look at them if you need to make optimizations.

信息:这些数字大多是元数据:它们不会影响规范是否有效，但如果您需要进行优化，查看这些数据是很有帮助的。

Let’s actually make it fail an invariant, then! Move back to the model overview and add x /= 6 to the “Invariants” section. This, unsurprisingly, says that a core invariant of our program is that x is never 6. This obviously fails when x is five, which is one of the potential initial states. When we rerun the model, we get something like this:

那么，让我们实际让它成为一个不变量吧!回到模型概述，并将x /= 6添加到“不变量”部分。这毫不奇怪地说明，我们程序的核心不变量是x不等于6。当x = 5时，这显然是失败的，这是一种可能的初始状态。当我们重新运行模型时，我们会得到这样的结果:

Not only does this show us what failed, it shows us how it failed. In this case, “Initial Predicate” tells us the starting state: x is five. The following “action” line shows that the “Add” step finished and x changed to 6, which invalidates our invariant.

这不仅告诉我们什么失败了，还告诉我们它是如何失败的。在本例中，“Initial Predicate”告诉我们初始状态:x = 5。下面的“操作”行显示“添加”步骤已经完成，并且x更改为6，这将使我们的不变式无效。

#### Evaluating Expressions

Switch back to the main overview section. Change the behavior spec to “no behavior spec”. This tells TLC that there’s no system you are modelling, and you’re just playing around with the TLA+ syntax. We can’t run anything here. But what we can do is go over to the “model checking results” tab and put TLA in “evaluate constant expression”. Try putting in `CHOOSE y \in {1, 2, 3} : y*y = 4` in that box and rerun the model. You should see 2 appear in the “Value” box.

切换回主概述部分。将行为规范更改为“无行为规范”。这告诉TLC，您没有要建模的系统，您只是在玩弄TLA+语法。我们不能在这里运行任何东西。但是我们可以做的是转到“模型检查结果”选项卡，将TLA放在“计算常量表达式”中。试着在{1,2,3}:y*y = 4 '中输入' CHOOSE y \in {1,2,3}: y*y = 4 '，然后重新运行模型。您应该会看到2出现在“值”框中。

We won’t be using the “no behavior spec” mode for testing actual systems, but it’s a nice tool to test that you understand how some TLA+ expression will work. Don’t worry, we’ll be reintroducing this when we dive into TLA+ proper.

我们不会使用“无行为规范”模式来测试实际的系统，但是它是一个很好的工具，可以帮助您了解一些TLA+表达式是如何工作的。别担心，我们会在深入TLA+的时候重新介绍它。

### BEHAVIORS

So far we’ve seen the basics of PlusCal as well as how to run a model. We’ve also seen that if our starting variables are specified as belonging to a set, it expands the state space that TLC can search. This takes us most of the way to writing useful specifications. The last step is to add divergent behaviors: allow the system to do different things at a given step. In a single process PlusCal algorithm, there are two simple ways to introduce concurrency: a with statement and an either statement.

到目前为止，我们已经了解了PlusCal的基础知识以及如何运行模型。我们还看到，如果我们的开始变量被指定为属于一个集合，它就会扩展TLC可以搜索的状态空间。这为我们提供了编写有用规范的大部分方法。最后一步是添加不同的行为:允许系统在给定的步骤中做不同的事情。在单个进程PlusCal算法中，有两种简单的方法来引入并发性:with语句和either语句。

#### Either

either looks a lot like a basic if statement. The syntax is as follows:

```
either
  skip;
or
  skip;
or
  skip;
end either;
```

The important thing is that TLC will run every branch. When it encounters an either, it creates a separate behavior and executes a different branch for each one. For example, given:

```
variables x = 3, i = 2;
begin
while i > 0 do
  either 
    x := x + 2;
  or 
    x := x * 2;
  end either;
  i := i - 1;
end while
```

With
with does the same thing, except instead of creating a new behavior for each possible branch, it creates a behavior for each element in the set. In this case, we have three possible behaviors:

```
with a \in {1, 2, 3} do
  x := x + a
end with;
```

This creates a separate timeline for each element in the set.

#### Example

Specify a system with three flags that can be on or off, as well as can change the state of a flag.

指定一个带有三个标志的系统，这些标志可以是on或off，也可以更改标志的状态。

Right now we’re a little limited in what we can practically do, but we can already start constructing simple patterns. Here’s one way to write this in PlusCal:

```
---- MODULE flags ----
EXTENDS TLC, Integers
(* --algorithm flags
variables f1 \in BOOLEAN, f2 \in BOOLEAN, f3 \in BOOLEAN
begin
  while TRUE do
    with f \in {1, 2, 3} do
      if f = 1 then
        either
          f1 := TRUE;
        or
          f1 := FALSE;
        end either;
      elsif f = 2 then
        either
          f2 := TRUE;
        or
          f2 := FALSE;
        end either;
      else
        either
          f3 := TRUE;
        or
          f3 := FALSE;
        end either;
      end if;
    end with;
  end while;
end algorithm; *)

====
```

This isn’t the most optimal way of writing it, but I wanted to showcase both with and either here. You could probably use just the either. BOOLEAN is a TLA+ builtin and is equal to the set {TRUE, FALSE}. As you can see, every step this picks a single flag and either sets it to true or false. Fairly simple, if cumbersome.

这并不是最理想的写法，但是我想在这里展示一下。你可以用任意一个。布尔值是TLA+内建值，等于set {TRUE, FALSE}。如您所见，每一步都会选择一个标志，并将其设置为true或false。相当简单，虽然有点麻烦。

To give a better sense of where we’re going, here’s how we could write it instead, once we’re more comfortable with the language:

```
---- MODULE flags ----
EXTENDS TLC, Integers
(* --algorithm flags
variables flags \in [1..3 -> BOOLEAN], next = TRUE;
begin
  while next do
    with f \in DOMAIN flags, n \in BOOLEAN do
      flags[f] := ~flags[f];
      next := n;
    end with;
  end while;
end algorithm; *)

====
```

The flags `\in [1..3 -> BOOLEAN]` bit is called a function set. We’ll be covering it later.

## TLA+

Now that we have our PlusCal scaffold, it’s time to learn TLA+ proper. TLA+ is a mathematical description language. Instead of saying how to find the value you want, you instead say the properties of what you want. For example, the largest element of a set is `CHOOSE x \in S : \A y \in S : y <= x`. That’s not an algorithm. It’s just “the number that’s bigger than the other numbers.”

现在我们有了PlusCal支架，是时候学习TLA+合适了。TLA+是一种数学描述语言。不是说如何找到你想要的值，而是说你想要的属性。例如，一个集合中最大的元素是`CHOOSE x \in S: \ a y \in S: y <= x`。这不是一个算法。它只是“比其他数字大的数字”。

This gives us incredible specification power. Complex properties and invariants can be expressed in just a few lines. On the downside, it can also be very intimidating. It’s also possible to accidentally build uncheckable models. It’s easy, for example, to define the set of all universal turing machines that halt. If you feed that into TLC, though, it will throw an error back. There’s a bit of an art to writing TLA+ specs. Let’s get started.

这给了我们难以置信的规范能力。复杂的性质和不变量可以用几行代码来表示。不利的一面是，它也可能非常令人生畏。意外地构建不可检查的模型也是可能的。例如，定义所有停止的通用图灵机的集合是很容易的。但是，如果您将其提供给TLC，它将返回一个错误。编写TLA+规格是一门艺术。让我们开始吧。

Point of note: we’re going to be almost completely ignoring the “temporal” part of “Temporal Logic of Actions”. As a simple heuristic, we’re never, ever going to be talking about “primed and unprimed operators”, which make up like 95% of Specifying Systems. At it’s core, TLA+ is a beautiful way to elegantly express the temporal properties of a complicated system. For our uses, TLA+ is a way of writing better PlusCal specs. Sacrilegious? Probably. Easier? Yup.

注意:我们将几乎完全忽略“动作的时间逻辑”中的“时间”部分。一个简单的启发式，我们永远不会，永远不会讨论“已启动和未启动的操作符”，它们构成了95%的指定系统。在其核心，TLA+是一种优美的方式来优雅地表达一个复杂系统的时间特性。对于我们来说，TLA+是一种编写更好的PlusCal规格的方法。该受天谴的吗?可能。容易吗?是的。

### OPERATORS

An operator is a thing that does a thing. It’s what programmers might refer to as a function. You use a == to define them.

运算符是做一件事的东西。程序员可能会把它称为函数。可以使用a ==来定义它们。

```
Five == 5
Five + 5 \* 10
```

You can also give them arguments.

```
IsFive(x) == x = 5
```

Or multiple arguments.

```
SumWithFive(a, b) == a + b + 5
```

You can use them anywhere you use any other expression.

```
{ Five, SumWithFive(Five, Five) } \* { 5, 15 }
```

#### Higher-Order Operators

#### Integrating With PlusCal

If your operator doesn’t reference any PlusCal variables (such as `IsEmpty(S) == S = {}`), you can put it above the start of the PlusCal algorithm and it will be usable like any other expression.

If your operator does reference any PlusCal variables (such as HasMoneyLeft == money > 0), it has to appear in or after the algorithm. The first way to do this is to put it after the TLA+ translation. This is simple, but it also means the PlusCal can’t call the operator. Fine for invariants, less-fine for conditionals. To place the operator directly in your code, you can use a define block:

```
define
  Foo(bar) == baz
  \* ...
end define
```

Your `define` block must come before the `begin`.

### EXPRESSIONS

### TUPLES AND STRUCTURES

### SETS

### LOGIC

### FUNCTIONS

## MODELS

### CONSTANTS

### MODEL VALUES

### EXAMPLE: ARBITRAGE

## CONCURRENCY

### PROCESSES

### PROCEDURES AND MACROS

### LABELS

### CONCURRENT INVARIANTS

### EXAMPLE: RATE LIMITING

## TEMPORAL PROPERTIES

### TEMPORAL OPERATORS

### USING TEMPORAL PROPERTIES

### TERMINATION

## TECHNIQUES

### CONDITIONAL STEPS

### MERGING FUNCTIONS

### USING MODEL VALUES

### MULTISTEP INVARIANTS



## APPENDIX

