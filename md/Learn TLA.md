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

We’ve been implicitly using expressions up until now; we just haven’t clarified them. For our purposes, an expression is anything that follows a ==, =, :=, or \in. In this section we’ll cover some general expression modifiers.

到目前为止，我们一直在隐式地使用表达式;我们只是还没有澄清它们。对于我们的目的，表达式是在==、=、:=或\in之后的任何内容。在本节中，我们将介绍一些通用表达式修饰符。

We’ve used these for a while now: `/\` is “and”, `\/` is “or”. We can join expressions with them. The one subtlety is that this is the only case where TLA+ is whitespace sensitive. If you start a line with an indented junction, TLA+ considers that the start of a subclause. For example,

```
/\ TRUE
  \/ TRUE
/\ FALSE \* (T \/ T) /\ F

/\ TRUE
  \/ TRUE
  \/ FALSE \* (T \/ T \/ F)

\/ TRUE
\/ TRUE
  /\ FALSE \* T \/ (T /\ F)
```

Etc. As a general rule of thumb:

- If two logical operators are on the same level of indentation, they are part of the same level of expression.
- If a logical operator is on a higher level of indentation, it’s part of the previous operator statement.
- Use only one type of operator per level of indentation.

一般的经验法则是:

- 如果两个逻辑运算符位于同一缩进级别，则它们是同一表达式级别的一部分。
- 如果一个逻辑运算符在更高的缩进级别，它是前一个运算符语句的一部分。
- 每层缩进只使用一种类型的运算符。

Generally if you mess this up the spec will crash, so you’re unlikely to get a logic bug through this.

一般来说，如果你搞砸了，规范就会崩溃，所以你不太可能通过这个得到一个逻辑错误。

#### LET-IN

Any expression can use LET-IN to add local operators and definitions to just that expression alone.

任何表达式都可以使用LET-IN仅向该表达式添加本地操作符和定义。

```
LET IsEven(x) == x % 2 = 0
IN  IsEven(5)

LET IsEven(x) == x % 2 = 0
    Five == 5
IN  IsEven(Five)

LET IsEven(x) == LET Two == 2
                     Zero == 0
                 IN x % Two = Zero
    Five == 5
IN  IsEven(Five)
```

The whitespace does not matter: we can write `LET IsEven(x) == x % 2 = 0 Five == 5 IN IsEven(Five)` and it will correctly parse it as two separate operators in the LET. You should use newlines though, because you care about legibility.

空格并不重要:我们可以写' LET IsEven(x) == x % 2 = 0 Five == 5 IN IsEven(5) '，它将正确地将其解析为LET中的两个独立的操作符。但是您应该使用换行，因为您关心可读性。

#### IF-THEN-ELSE

This is exactly what you expect it to be.

```
IsEven(x) == IF x % 2 = 0 
             THEN TRUE
             ELSE FALSE
```

As before, alignment doesn’t matter, but you should align them anyway unless you really hate your coworkers.

#### CASE

Case is mostly how you’d expect it to act, with one subtle difference.

```
CASE x = 1 -> TRUE
  [] x = 2 -> TRUE
  [] x = 3 -> 7
  [] OTHER -> FALSE
```

OTHER is the default. If none of the cases match and you leave out an OTHER, TLC considers that an error. If more than one match, though, TLC will pick one for you and not branch. In other words, the following code

```
CASE TRUE -> FALSE
  [] TRUE -> TRUE
```

May or may not be true. Be careful.

#### Nesting

All parts of expressions are expressions or identifiers, so you can put expressions inside other expressions. Additionally, all expressions can be used inside PlusCal code.

### TUPLES AND STRUCTURES

#### Tuples

#### Sets of Tuples

#### Structures

#### Type Composition

#### Example

### SETS

#### Filtering

#### Mapping

#### CHOOSE

#### Set Operators

### LOGIC

In this section, we’ll introduce the basics of propositional logic. Even if you’re unfamiliar with the idea, you’ve almost certainly used it in programming before.

#### `\E`

#### `\A`

#### `=>` and `<=>`

#### CHOOSE

### FUNCTIONS

#### Function Sets

#### Using Functions

## MODELS

In the past few chapters, we covered how to write complex specifications. However, our models have always been fairly crude: drop an operator or expression into Invariants and check that it’s satisfied. This is useful, but as you can probably guess by now, there’s also a lot more we can do with them. This chapter will cover some of the tools TLC provides to manage larger and more complex models.

在前几章中，我们讨论了如何编写复杂的规范。然而，我们的模型一直是相当粗糙的:将一个运算符或表达式放入不变量中，并检查它是否满足要求。这很有用，但正如你现在可能猜到的那样，我们还可以用它们做更多的事情。本章将介绍TLC提供的一些工具，用于管理更大更复杂的模型。

### CONSTANTS

### MODEL VALUES

#### Assumptions

### EXAMPLE: ARBITRAGE

## CONCURRENCY

This chapter will cover concurrent systems: ones where multiple processes, sharing a global state, run simultaneously. As a simple example of a concurrent system, you have one model and three different kinds of jobs that can affect the state of that model, all running in an asynchronous worker pool. How do you guarantee that just the right ordering of run jobs doesn’t cause data to somehow break?

本章将介绍并发系统:共享全局状态的多个进程同时运行的系统。作为并发系统的一个简单示例，您有一个模型和三种不同的作业，这些作业可能会影响模型的状态，所有这些作业都运行在异步工作者池中。如何保证正确的运行作业顺序不会导致数据中断?

Reasoning about concurrent systems gets intractable quickly, which is why we get a model checker to reason about it for us.

关于并发系统的推理很快就变得难以处理，这就是为什么我们需要一个模型检查器来为我们进行推理。

### PROCESSES

Our old code ran everything in a single process- that’s the begin block. If we wanted have several things happen simultaneously, we use multiple processes. We set it up like this:

```
---- MODULE module_name ----
\* TLA+ code

(* --algorithm algorithm_name
variables global_variables

process p_name = foo
variables local_variables
begin
  \* pluscal code 
end process

process p_group \in bar \* set
variables local_variables
begin
  \* pluscal code 
end process

end algorithm; *)
====
```

All processes must be assigned a value. There are two ways to do this. First, you can say `process = foo`, which will create one copy of that process. Or you could say `process \in bar`, in which case it will create one copy of that process for each element in `bar`. So if you write `process \in {1, 3, 5}`, you have three copies of that process running your behavior.

必须为所有进程分配一个值。有两种方法可以做到这一点。首先，您可以说' process = foo '，这将创建该进程的一个副本。或者你可以说' process \in bar '，在这种情况下，它会为' bar '中的每个元素创建一个该进程的副本。因此，如果编写“进程\in{1, 3, 5}”，那么就有该进程的三个副本在运行您的行为。

Process values are used to select which process to run, so all processes in a behavior must be comparable and should be unique. If you write `process = 1`, you can’t have a second defined as `process bar = 'bar'` because 1 and ‘bar’ are not comparable. Additionally, you shouldn’t have a second process also defined as `process = 1` because when 1 is selected the first process will always run and the second process will always be ignored.

流程值用于选择要运行的流程，因此行为中的所有流程必须是可比较的，并且应该是惟一的。如果写入“process = 1”，则不能将第二个定义为“process bar = 'bar'”，因为1和'bar'是不可比较的。此外，您不应该将第二个进程定义为“process = 1”，因为当选择1时，第一个进程将始终运行，而第二个进程将始终被忽略。

This is a case where model values and model sets can be very useful, since every model value is comparable to everything else (it’s unequal to everything except itself).

在这种情况下，模型值和模型集可能非常有用，因为每个模型值都可以与其他任何值进行比较(除了自身之外，它不等于其他任何值)。

You can get a process’s value with `self`:

```
process foo = "bar"
begin
  print self; \* prints "bar"
end process
```

Variables declared outside of a process have global scope: any process can read and modify them. Variables declared in a process scope are local to that process. So if you have multiple processes defined in a set, each one will have it’s own private variable scope. If you use \in for the variables, TLC will create one state for each combination of initial states in each process. For example:

```
process p \in 1..3
variable x \in 1..4
begin
  A:
    skip
end process;
```

TLC can choose in which order to run the possible steps, where each step corresponds to all of the code in one label of one process. If any of these paths breaks an invariant, then TLC raise it as usual.

```
variables x = 0;
process one = 1
begin
  A:
    x := x - 1;
  B:
    x := x * 3;
end process

process two = 2
begin
  C:
    x := x + 1;
  D:
    assert x /= 0;
end process
```

This will fail, as the sequence C -> A -> B -> D sets x as 0 + 1 -> 1 - 1 -> 0 * 3 -> 0 /= 0.

If there are multiple instances of the same process, TLC advances them one at a time.

```
(* --algorithm foo
variables x = 0;
process cycle \in 1..3
begin
  A:
    x := x + 1;
  B:
    x := 0;
  C:
    assert x /= 2;
end process
end algorithm; *)
```

This fails on the path `A[1] -> B[1] -> A[2] -> A[3] -> C[1]`.

#### Await

Is there a way to prevent a step from running? We can do this with await:

```
process one = 1
begin
  A:
    x := x - 1;
  B:
    x := x * 3;
end process

process two = 2
begin
  C:
    await x < -1;
    x := x + 1;
  D:
    assert x /= 0;
end process
```

In this case, the entire C step is blocked until the await is true. So the only path that can happen is `A -> B -> C -> D`, which is valid.

A troubling problem here: if we can say “don’t run this step unless X is true”, can we have a situation where we can’t run any steps? For example, if we instead did `await x > 1`, we’d be able to do `A -> B` and then get stuck. This is called a deadlock. This is almost certainly a serious bug and TLC will flag this as an error in your algorithm.

If a deadlock is not an error in your system, then you can disable that check in the model.

#### Example

### PROCEDURES AND MACROS

#### Macros

#### Procedures

#### Order of operations

#### Example

### LABELS

### CONCURRENT INVARIANTS

Sometimes, we want to enforce invariants between processes. For example, only one thread may have a lock at the same time, or the total values across all processes must be less than a certain threshold. Here’s one example:

有时，我们希望在进程之间强制使用不变量。例如，只有一个线程可能同时具有一个锁，或者所有进程的总值必须小于某个阈值。这里有一个例子:

```
process foo \in 1..2
variable x \in 1..2
begin
  Skip:
    skip
end process
```

How do we assert the variant “the sum of x between the two processes is not 4?” With a single process algorithm, we could write `x /= 4`. But to do the same with multiple processes, we have to let the PlusCal abstraction leak.

我们如何断言变量“两个进程之间的x的和不是4?”“使用一个单一的过程算法，我们可以写出‘x /= 4’。但是要对多个进程做同样的事情，我们必须让PlusCal抽象泄漏。

When we translate an algorithm, TLA+ will create all of the corresponding variables. When we have multiple processes, TLA+ will instead create a function with a domain on the process identifiers and the range the actual values of x per process. So instead of having for example `x \in 1..2`, we instead have `x == [ProcSet -> 1..2]`. So in this case, the appropriate invariant is `x[1] + x[2] /= 4`.

当我们转换算法时，TLA+将创建所有相应的变量。当我们有多个进程时，TLA+将在进程标识符上创建一个域函数，并在每个进程的实际x值范围内创建一个域函数。而不是像' x \in 1..2 '，我们改为' x == [ProcSet -> 1..2] '。在这种情况下，合适的不变式是' x[1] + x[2] /= 4 '

### EXAMPLE: RATE LIMITING

## TEMPORAL PROPERTIES

When I said we were barely going to cover TLA+, I wasn’t kidding.

当我说我们几乎不能覆盖TLA+的时候，我不是在开玩笑。

Temporal Properties are the heart and soul of TLA+. It’s how the language does anything in the first place. Which is why it’s kind of depressing that I’m going to spend almost no time on it. That’s because of PlusCal abstracts away most of it for us. This section explains a little bit of the parts we, as startup folk, might find useful, and assuage my guilt at not covering a pretty major part of the system.

时间属性是TLA+的核心和灵魂。这就是语言最初的作用。这就是为什么我几乎不会在这上面花时间的原因。这是因为PlusCal为我们抽象了大部分内容。这一节解释了一些我们，作为创业者，可能会发现有用的部分，并减轻我的内疚，因为我没有覆盖系统的大部分。

### TEMPORAL OPERATORS

#### Liveness

Whenever we write invariants, we’re saying “for an arbitrary state, this will never happen.” And this has been very useful. But safety only tells us that bad things won’t happen. Sometimes, we want to ask a whether good things do happen. Will the trade eventually happen? Does every thread at some point get priority? Does our algorithm finish?

当我们写不变量时，我们说的是，对于任意的状态，这永远不会发生。“这是非常有用的。但安全只告诉我们，坏事不会发生。有时候，我们想问a是否有好事发生。交易最终会发生吗?每个线程在某个时候都有优先级吗?我们的算法完成了吗?

We call these properties Liveness. To specify these temporal properties, we use a few new operators.

我们称这些属性为活性。为了指定这些时间属性，我们使用了一些新的操作符。

#### `[]`

`[]P` means that P is true for all states. In other words, an invariant. When you put P in the invariant box, TLC interprets that as the temporal property `[]P`. The only difference is that TLC is hyper-optimized to handle invariants, so the entire invariants box is basically a convenience thing. So while `[]` implicitly powers all of our invariants, we almost never need to write it explicitly.

`[]P`表示P对所有状态都成立。换句话说，一个不变式。当您将P放入固定框时，TLC将其解释为时间属性`[]P`。唯一的区别是TLC是处理不变量的超优化，所以整个不变量框基本上是一个方便的东西。因此，虽然`[]`隐式地为所有不变量提供了幂，但我们几乎不需要显式地写它。

#### `<>`

`<>` means eventually: `<>P` means that for every possible behavior, at least one state has P as true. For example, the following code is wrong under the temporal property `<>(x = 1)`

' <> '意味着最终:' <>P '意味着对于每一个可能的行为，至少有一个状态P为真。例如，下面的代码在时态属性“<>(x = 1)”下是错误的

```
(* --algorithm example
variables x = 3
begin
  while x > 0 do
  with decrement \in {1, 2} do
    x := x - decrement
  end with;
  end while;
end algorithm; *)
```

There exists one timeline where x never passes through 1: “x = 3 -> x = 2 -> x = 0”. So it’s not true that ‘eventually, x is 1’. As long as every behavior has at least one state satisfying the statement, an eventually is true.

存在一个x从未经过1的时间轴:“x = 3 -> x = 2 -> x = 0”。所以x最终等于1是不正确的。只要每一种行为都至少有一种状态能满足这一陈述，最终都是正确的。

#### `~>`

`~>` means leads to: `P ~> Q` implies that if P ever becomes true, at some point afterwards Q must be true. For example:

```
(* --algorithm example
variables x = 4, decrement \in {1, 2}
begin
  while x > 0 do
    x := x - decrement
  end while;
end algorithm; *)
```

' ~> '的意思是导致:' P ~> Q '意味着，如果P曾经成为真的，在某一点后，Q一定是真的。例如:

As with before, `<>(x = 1)` is not true: we can do 4 -> 2 -> 0. But the temporal property `(x = 3) ~> (x = 1)` is true: there’s no way to pass through 3 without also passing through 1 (disregarding stuttering).

和前面一样，' <>(x = 1) '不正确:我们可以执行4 -> 2 -> 0。但是时间属性‘(x = 3) ~> (x = 1)’是正确的:没有办法通过3而不同时通过1(不考虑口吃)。

#### `<>[]`

`<>[]` means stays as: `<>[]P` says that at some point P becomes true and then stays true. If your program terminates, the final state has to have P as true. Note that P can switch between true and false, as long as it eventually becomes permanently true.

`<>[]`的意思是保持:`<>[]P`表示在某一点P变为真，然后保持真。如果程序终止，则最终状态必须使P为真。注意，P可以在真和假之间切换，只要它最终变成永久的真。

#### Example

Let’s go back to the dining philosopher’s algorithm we wrote in the last chapter. Here’s what the code looks like with the release:

让我们回到上一章写的用餐哲学家算法。下面是这个版本的代码:

```
fair process philosopher \in 1..NP
variables hungry = TRUE;
begin P:
  while hungry do
    either
     with fork \in AvailableForks(self) do
       forks[fork] := self;
     end with;
    or
     await AvailableForks(self) = {};
     with fork \in HeldForks(self) do
      forks[fork] := NULL;
     end with;
    end either;
    Eat:
      if Cardinality(HeldForks(self)) = 2 then
        hungry := FALSE;
        forks[LeftFork(self)] := NULL ||
        forks[RightFork(self)] := NULL;
      end if;
  end while;
end process;
```

The only change to the code is that we made the process fair, so as to avoid stuttering. We can guarantee in here that the system never deadlocks; at any point, at least one philosopher can do something. However, that’s not quite enough: can we guarantee that every philosopher finishes eating? Normal invariants aren’t enough to model that. But we can do that with a temporal operator:

对代码的唯一更改是我们使过程变得公平，以避免出现口吃。我们可以在这里保证系统永远不会死锁;在任何时候，至少有一个哲学家可以做一些事情。然而，这还不够:我们能保证每个哲学家都吃完饭吗?正态不变量不足以对其进行建模。但我们可以用一个时间算子来做:

```
<>(\A p \in 1..NP: ~hungry[p])
```

Checks that at some point in every behavior, every philosopher is not hungry. However, this doesn’t check they remain not hungry, and if something flips them back, it’d still be a valid spec. We could instead use []<> to get that extra guarantee, but there’s no need for that here. If we put that into our temporal properties to check and run the spec, we get a very long error:

在每个行为的某个时刻，每个哲学家都不会感到饥饿。但是，这并不能检查它们是否仍然不饿，如果有东西将它们翻转回来，这仍然是一个有效的规范。我们可以使用[]<>来获得额外的保证，但是这里不需要这样做。如果我们把它放到我们的时间属性中去检查并运行规范，我们会得到一个很长的错误:

Each philosopher can put their fork down and immediately pick it back up again, which prevents anybody else from having two forks. This is a livelock. How could rewrite the algorithm to prevent livelocks? Does your solution scale to three or more philosophers?

每个哲学家都可以放下他们的叉子，然后马上把它捡起来，这就防止了其他人拥有两把叉子。这是一把活锁。如何重写算法来防止活锁?你的解决方案是否适用于三位或三位以上的哲学家?

### USING TEMPORAL PROPERTIES

Most often, you’re not interested in checking temporal properties. If you are, though, here are some things to watch out for.

#### Liveness is Slow

It’s easy to check that reachable states satisfy invariants. But to check liveness, how you reach those states also matters. If a thousand routes lead through state S, that’s 1,000 routes that need to be checked for liveness and only one state that needs to be checked for safety. So liveness is intrinsically much, much slower than checking invariants.

检查可达状态是否满足不变量是很容易的。但要检查你的活性，如何达到这些状态也很重要。如果有1000条路径经过状态S，那么就有1000条路径需要检查活性，而只有一个状态需要检查安全性。所以活性本质上比检查不变量慢得多。

#### Temporal Properties are Very Slow

The Toolbox has a number of optimizations to make it easier to check invariants: multithreading, model symmetry sets, cloud computing, etc. These all assume, though, that we only care about individual states. None of these apply to temporal properties. So it’s even slower to check than you’d think at first.

工具箱中有许多优化，可以更容易地检查不变量:多线程、模型对称集、云计算等等。这些都假设，我们只关心单个的状态。这些都不适用于时间属性。所以它比你一开始想的还要慢。

An especially important one to watch out for are symmetric models. TLC can check them for temporal properties. However, the symmetry properties can provide false negatives: it can say that your spec satisfies the properties when it actually doesn’t.

需要特别注意的是对称模型。TLC可以检查它们的时间属性。然而，对称属性可以提供错误的否定:它可以说您的规范满足属性，但实际上并不满足。

#### Stuttering and Fairness

Regular processes can stutter. Sometimes this is part of what you expect in your system- maybe you can’t rely on the message queue always being available. If not, though, you need to explicitly use fair process.

常规流程可能会出现故障。有时，这是您在系统中所期望的内容的一部分—可能您不能总是依赖于消息队列。如果不是，那么您需要明确地使用公平过程。

#### Recommendations

- Use separate models for invariants and temporal properties.
- Use smaller sets of model values to check invariants than you do for temporal properties.
- Consider testing the same temporal property among several models with different setups.
- Do not use symmetric model sets when testing liveness.

- 对不变量和时间属性使用单独的模型。
- 使用比时间属性更小的模型值集来检查不变量。
- 考虑在不同设置的几个模型中测试相同的时间属性。
- 测试活性时不要使用对称模型集。

### TERMINATION

Will the following PlusCal snippet ever finish?

下面的PlusCal片段会结束吗?

```
EXTENDS TLC, Integers

(* --algorithm counter
variable x = 0;
begin
  while x < 10 do
    x := x + 1;
  end while;
end algorithm; *)
```

The most common temporal logic you’ll want to check is Termination, and TLC provides a handy button to test just that.

您要检查的最常见的时间逻辑是终止，TLC提供了一个方便的按钮来测试这一点。

This will succeed if, for all behaviors, the spec ends. Let’s see what happens when we run this:

Well, that was certainly unexpected! This is called stuttering. The problem is that the system can choose not to run any step at all. This has never been a problem before because we were making sure things didn’t break if time passed, but now things break if time doesn’t pass.

嗯，这确实出乎意料!这叫做口吃。问题是系统可以选择根本不运行任何步骤。这在以前从来都不是一个问题，因为我们要确保事情不会因为时间的流逝而中断，但现在如果时间没有流逝，事情就会中断。

How do we get around this? What we want is something called fairness: the property that if a given label is always enabled, we will eventually run it. We make this happen by specifying the process as a `fair process`.

我们该如何解决这个问题?我们想要的是所谓的公平性:如果一个给定的标签总是启用，我们最终将运行它的属性。我们通过将流程指定为“公平流程”来实现这一点。

```
EXTENDS TLC, Integers

(* --algorithm counter
variable x = 0;

fair process counter = "counter"
begin Count:
  while x < 10 do
    x := x + 1;
  end while;
end process;

end algorithm; *)
```

Confirm this works as expected.

Note: There’s a way to enforce fairness for a single process app, but it’s a bit finicky so I’m leaving it out.

注意:有一种方法可以增强单个进程应用程序的公平性，但是它有点麻烦，所以我就省略了它。

## TECHNIQUES

While TLA+ is a powerful tool, it does not provide a lot of guidance on how to write good specifications. This section will cover some of the problems you might run into with your specs, as well as ways around them.

虽然TLA+是一个强大的工具，但是它并没有提供很多关于如何编写好的规范的指导。本节将介绍在使用配置时可能遇到的一些问题，以及解决这些问题的方法。

### CONDITIONAL STEPS

Sometimes you may want to combine either with labels, so that branching steps are possible. For example:

```
either
  Push:
    \* code
or
  Pop:
    \* code
end either
After:
    \* code
```

### MERGING FUNCTIONS

### USING MODEL VALUES

### MULTISTEP INVARIANTS

## APPENDIX
