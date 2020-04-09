# Separation logic and bi-abduction

## Separation logic

Separation logic is a novel kind of mathematical logic which facilitates reasoning about mutations to computer memory. It enables scalability by breaking reasoning into chunks corresponding to local operations on memory, and then composing the reasoning chunks together.

分离逻辑是一种新的数学逻辑，它有助于推理计算机内存的突变。它通过将推理分解为与内存上的本地操作相对应的块，然后将推理块组合在一起，从而实现可扩展性。

Separation logic is based on a logical connective (*) called the separating conjunction and pronounced "and separately". Separation logic formulae are interpreted over program allocated heaps. The logical formula (A * B)
holds of a piece of program heap (a heaplet) when it can be divided into two sub-heaplets described by (A) and (B). For example, the formula $(x\mapsto y * y\mapsto x)$
can be read "(x) points to (y) and separately (y) points to (x)". This formula describes precisely two allocated memory cells. The first cell is allocated at the address denoted by the pointer  and the content of this cell is the value of . The second cell is allocated at the address denoted by the pointer  and the content of this second cell is the value of . Crucially, we know that there are precisely two cells because  stipulates that they are separated and therefore the cells are allocated in two different parts of memory. In other words,  says that

 and  do not hold the same value (i.e., these pointers are not aliased). The heaplet partitioning defined by the formula above can visualized like so:

分离逻辑是基于一个逻辑连接(*)，称为分离连接词和发音“和分别”。分离逻辑公式通过程序分配的堆来解释。逻辑公式(A * B)
当一段程序堆可以被分成(a)和(B)所描述的两个子堆时(一个状态)。例如，公式

The important thing about separating conjunction is the way that it fits together with mutation to computer memory; reasoning about program commands tends to work by updating (*)-conjuncts in-place, mimicking the operational in-place update of RAM.

分离连接的重要之处在于它与计算机内存中的突变相匹配;有关程序命令的推理往往通过更新(*)—适当的连接来工作，模拟RAM的适当的操作更新。



## Bi-abduction

Bi-abduction is a form of logical inference for separation logic which automates the key ideas about local reasoning.

双绑架是分离逻辑的一种逻辑推理形式，它自动化了局部推理的关键思想。

Usually, logic works with validity or entailment statements like
$(A\vdash B)$

通常，逻辑与有效性或包含语句一起工作，如$(A\vdash B)$

which says that  implies . Infer uses an extension of this inference question in an internal theorem prover while it runs over program statements. Infer's question

$(A*?antiframe \vdash B*frame)$

is called bi-abduction. The problem here is for the theorem prover to discover a pair of frame and antiframe formulae that make the entailment statement valid.

这意味着。Infer 在运行程序语句时，在内部定理证明器中使用这个推理问题的扩展。Infer 的问题$(A*?antiframe \vdash B*frame)$
叫做双绑架。这里的问题是定理证明器发现一对框架和反框架公式，使 entailment 语句有效。

Global analyses of large programs are normally computational untractable. However, bi-abduction allows to break the large analysis of a large program in small independent analyses of its procedures. This gives Infer the ability to scale independently of the size of the analyzed code. Moreover, by breaking the analysis in small independent parts, when the full program is analyzed again because of a code change the analysis results of the unchanged part of the code can be reused and only the code change needs to be re-analyzed. This process is called incremental analysis and it is very powerful when integrating a static analysis tool like infer in a development environment.

大型程序的全局分析通常是不可计算的。然而，bi-ablation 允许在对其程序的小型独立分析中打破对大型程序的大型分析。这使得推断能够独立于分析代码的大小进行扩展。此外，通过打破小的独立部分的分析,当由于代码更改而再次分析完整程序时，可以重用代码的未更改部分的分析结果，并且只需要重新分析代码更改。这个过程被称为增量分析，当在开发环境中集成像 infer 这样的静态分析工具时，它非常强大。

In order to be able to decompose a global analysis in small independent analyses, let's first consider how a function call is analyzed in separation logic. Assume we have the following spec for a function

$(f()): (\{pre_f\}f()\{post_f\})$

and by analyzing the caller function, we compute that before the call of

$f()$

the formula $(CallingState)$ hold. Then to utilize the specification of  the following implication must holds:

$(CallingState \vdash pre_f (FunctionCall))$

为了能够在小型独立分析中分解全局分析，让我们首先考虑如何在分离逻辑中分析函数调用。假设我们有一个函数的以下规范
$(f()): (\{pre_f\}f()\{post_f\})$
通过分析调用者函数，我们在调用
$f()$
公式 $(CallingState)$ 保持。然后，要利用以下含义的规范必须保持:
$(CallingState \vdash pre_f (FunctionCall))$

Given that, biabduction is used at procedure call sites for two reasons: to discover missing state that is needed for the above implication to hold and allow the analysis to proceed (the antiframe) as well as state that the procedure leaves unchanged (the frame).

考虑到这一点，双绑架被用于程序呼叫站点有两个原因: 发现上述含义所需的缺失状态，以保持并允许分析继续进行 (反框架) 以及声明过程保持不变 (帧)。

To see how this works suppose we have some bare code
$(closeResource(R1);closeResource(R2))$

but no overall specification; we are going to describe how to discover a pre/post spec for it. Considering the first statement and the (spec) above, the human might say: if only we had $r1\mapsto open$ in the precondition then we could proceed. Technically, we ask a bi-abduction question
$emp*?$

In general, bi-abduction provides a way to infer a pre/post specs from bare code, as long as we know specs for the primitives at the base level of the code. The human does not need to write preconditions and postconditions for all the procedures, which is the key to having a high level of automation. This is the basis for how Infer works, why it can scale, and how it can analyze code changes incrementally.

一般来说，双诱拐提供了一种从裸代码推断前/后规范的方法，只要我们知道原语在代码的基本级别上的规范。人类不需要为所有的过程编写先决条件和后置条件，这是实现高水平自动化的关键。这是Infer如何工作的基础，它为什么可以伸缩，以及它如何增量地分析代码更改。

Context: The logical terminology we have been using here comes from AI and philosophy of science. Abductive inference was introduced by the philosopher Charles Peirce, and described as the mechanism underpinning hypothesis formation (or, guessing what might be true about the world), the most creative part of the scientific process. Abduction and the frame problem have both attracted significant attention in AI. Infer uses an automated form of abduction to generate preconditions describing the memory that a program touches (the antiframe part above), and frame inference to discover what isn't touched. Infer then uses deductive reasoning to calculate a formula describing the effect of a program, starting from the preconditions. In a sense, Infer approaches automated reasoning about programs by mimicking what a human might do when trying to understand a program: it abduces what the program needs, and deduces conclusions of that. It is when the reasoning goes wrong that Infer reports a potential bug.

上下文:我们在这里使用的逻辑术语来自人工智能和科学哲学。外展推理是由哲学家查尔斯·皮尔斯(Charles Peirce)提出的，它被描述为支撑假设形成的机制(或者，猜测世界上什么可能是真实的)，是科学过程中最具创造性的部分。在人工智能中，诱拐和框架问题都备受关注。Infer使用一种自动的外展形式来生成描述程序所触及的内存的前提条件(上面的反框架部分)，并使用框架推断来发现未触及的内存。然后使用演绎推理从前提条件出发，计算一个描述程序效果的公式。在某种意义上，Infer通过模仿人类在试图理解一个程序时可能会做的事情来实现对程序的自动推理:它绑架程序需要什么，并从中推断出结论。当推理出错时，推断报告了一个潜在的错误。
