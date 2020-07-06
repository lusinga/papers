# Are Deep Neural Networks the Best Choice for Modeling Source Code?

## ABSTRACT

Current statistical language modeling techniques, including deeplearning based models, have proven to be quite effective for source code. We argue here that the special properties of source code can be exploited for further improvements. In this work, we enhance established language modeling approaches to handle the special challenges of modeling source code, such as: frequent changes, larger, changing vocabularies, deeply nested scopes, etc.We present a fast, nested language modeling toolkit specifically designed for software, with the ability to add & remove text, and mix & swap out many models. Specifically, we improve upon prior cache-modeling work and present a model with a much more expansive, multi-level notion of locality that we show to be well-suited for modeling software. We present results on varying corpora in comparison with traditional N-gram, as well as RNN, and LSTM deep-learning language models, and release all our source code for public use. Our evaluations suggest that carefully adapting N-gram models for source code can yield performance that surpasses even RNN and LSTM based deep-learning models.

目前的统计语言建模技术，包括基于深度学习的模型，已被证明是非常有效的源代码。我们在这里认为源代码的特殊属性可以用于进一步的改进。在这项工作中,我们加强了语言的建模方法来处理建模源代码的特殊挑战,如:频繁变化,大,变化的词汇表,深深地嵌套范围,完美呈现出快速、嵌套专门为软件设计语言建模工具包,能够添加和删除文本和混合&交换了许多模型。具体来说，我们改进了以前的缓存建模工作，并提供了一个具有更广泛的、多层次的局部性概念的模型，我们认为它非常适合建模软件。与传统的N-gram、RNN和LSTM深度学习语言模型相比，我们展示了不同语料库的结果，并发布了我们所有的源代码供公众使用。我们的评估表明，为源代码仔细地调整N-gram模型可以产生甚至超过RNN和基于LSTM的深度学习模型的性能。

## 1 INTRODUCTION

There has been much interest in the idea of “naturalness": viz., modeling and exploiting the repetitive nature of software using statistical techniques from natural language processing (NLP) [17, 26, 38]. Statistical models from NLP, estimated over the large volumes of code available in GitHub, have led to a wide range of applications in software engineering. High-performance language models are widely used to improve performance on NLP-related tasks, such as translation, speech-recognition, and query completion; similarly, better language models for source code are known to improve performance in tasks such as code completion [15]. Developing models that can address (and exploit) the special properties of source code is central to this enterprise.

人们对“自然性”的想法很感兴趣:即，使用来自自然语言处理(NLP)的统计技术建模和开发软件的重复性特性[17,26,38]。来自NLP的统计模型，通过对GitHub中大量可用代码的估计，在软件工程中得到了广泛的应用。高性能语言模型被广泛用于提高nlp相关任务的性能，如翻译、语音识别和查询完成;类似地，更好的源代码语言模型可以提高任务的性能，比如代码完成[15]。开发能够处理(并利用)源代码的特殊属性的模型是这个企业的核心。

Language models for NLP have been developed over decades, and are highly refined; however, many of the design decisions baked-into modern NLP language models are finely-wrought to exploit properties of natural language corpora. These properties aren’t always relevant to source code, so that adapting NLP models to the special features of source code can be helpful. We discuss 3 important issues and their modeling implications in detail below.

NLP的语言模型已经发展了几十年，并且高度精炼;然而，现代NLP语言模型中的许多设计决策都经过精细处理，以利用自然语言语料库的特性。这些属性并不总是与源代码相关，因此使NLP模型适应于源代码的特殊特性是有帮助的。我们将在下面详细讨论3个重要问题及其建模含义。

- Unlimited Vocabulary

Code and NL can both have an unbounded vocabulary; however, in NL corpora, the vocabulary usually saturates quickly: when scanning through a large NL corpus, pretty soon, one rarely encounters new words. New proper nouns (people & place names) do pop up–but do so infrequently. Code is different; while each language only has a fixed set of keywords and operators, new identifier names tend to proliferate [4].

代码和NL都可以有一个无限的词汇表;然而，在NL语料库中，词汇通常饱和得很快:当浏览大量的NL语料库时，很快就很少遇到新单词。新的专有名词(人名和地名)确实会出现，但很少出现。代码是不同的;虽然每种语言只有一组固定的关键字和操作符，但是新的标识符名称倾向于增加[4]。

Modeling Implications: In NLP, it’s de regeur to limit vocabulary to the most common e.g., 50,000 words in a pre-processing step, before model estimation. Words outside this vocabulary are treated as an unknown word, or omitted entirely. This artificially limits the space of events over which to distribute probability mass. Similarly, numerals and strings are replaced with generic tokens. This works for NLP, since words outside the dominant vocabulary are so rare. Virtually all work in modeling of source code borrows this approach. In source code, given the constant vocabulary innovation, this approach is not appropriate. We demonstrate that a closed vocabulary (even if large) does indeed negatively affect performance (Section 5.4), and introduce methods to address this.


- Nested, Scoped, Locality

While developers do invent new names for variables, classes and methods, the repeated use of these names tends to be localized. In Java, e.g., local variables, parameters and private methods can be introduced & used, repeatedly, in one scope, and never used elsewhere. The package structures in large systems can introduce nesting of such vocabulary scopes, with different identifiers going in and out of use as one traverses the package hierarchy [7, 24, 34, 36]. Researchers have even noted applicationand developer-specific vocabularies [35].

虽然开发人员确实为变量、类和方法发明了新的名称，但重复使用这些名称往往是本地化的。例如，在Java中，局部变量、参数和私有方法可以在一个范围内被重复地引入和使用，而在其他地方从不使用。大型系统中的包结构可以引入这种词汇表范围的嵌套，在遍历包层次结构时使用不同的标识符进出使用[7,24,34,36]。研究人员甚至注意到特定于应用程序和开发人员的词汇表[35]。

Modeling Implications: This type of nested, scoped vocabulary innovation
is accompanied by corresponding repetition, where certain code structures involving specific local names repeat, locally, within
their own nested scopes. This requires a nested modeling approach,
which captures the local repetition within a scope si , and then
makes it available to scopes si , si+1 . . . nested within si . Furthermore,
if such nested models are used within an interactive tool
(such as an IDE) the model would need to be rapidly re-estimated
as the programmer’s working context changes.

- Dynamism 

Evolution is normal for well-used software systems; bug fixes and newfeatures keep rolling in. NLP corpora evolve much more slowly. Furthermore, during interactive coding, software tools must quickly adjust to newlocalities and contexts: in a single coding session, a developer may open and close many files. As she explores the code, a language model that works within the IDE (for code completion [15, 33], defect localization [31], etc.) must rapidly adapt to the working context.

对于使用良好的软件系统来说，进化是正常的;bug修复和新特性不断涌现。NLP语料库的进化要慢得多。此外，在交互编码期间，软件工具必须快速适应新位置和上下文:在单个编码会话中，开发人员可能打开和关闭许多文件。在她研究代码的过程中，在IDE中工作的语言模型(用于代码完成[15,33]、缺陷定位[31]等)必须迅速适应工作环境。
