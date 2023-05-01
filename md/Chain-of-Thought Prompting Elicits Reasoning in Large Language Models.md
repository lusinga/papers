# Chain-of-Thought Prompting Elicits Reasoning in Large Language Models

- Jason Wei 
- Xuezhi Wang 
- Dale Schuurmans 
- Maarten Bosma
- Brian Ichter 
- Fei Xia 
- Ed H. Chi 
- Quoc V. Le 
- Denny Zhou

Google Research, Brain Team

## Abstract

We explore how generating a chain of thought—a series of intermediate reasoning steps—significantly improves the ability of large language models to perform complex reasoning. In particular, we show how such reasoning abilities emerge naturally in sufficiently large language models via a simple method called chain-ofthought prompting, where a few chain of thought demonstrations are provided as exemplars in prompting.

我们探索了如何通过生成一系列中间推理步骤——即思维链——来显著提高大型语言模型进行复杂推理的能力。具体来说，我们展示了如何通过一种简单的方法，称为思维链提示，来激发足够大的语言模型中自然存在的推理能力，其中在提示中提供了一些思维链示例作为范例。

Experiments on three large language models show that chain-of-thought prompting improves performance on a range of arithmetic, commonsense, and symbolic reasoning tasks. The empirical gains can be striking. For instance, prompting a PaLM 540B with just eight chain-of-thought exemplars achieves state-of-the-art accuracy on the GSM8K benchmark of math word problems, surpassing even finetuned GPT-3 with a verifier.

我们在三个大型语言模型上进行了实验，表明思维链提示在一系列算术、常识和符号推理任务上都提高了性能。实验结果有些惊人。例如，在只用八个思维链示例提示一个540B参数的PaLM模型的情况下，就在数学文字问题的GSM8K基准测试上达到了最新的准确率，甚至超过了经过微调的GPT-3和验证器。

## 1 Introduction

The NLP landscape has recently been revolutionized by language models (Peters et al., 2018; Devlin et al., 2019; Brown et al., 2020, inter alia). Scaling up the size of language models has been shown to confer a range of benefits, such as improved performance and sample efficiency (Kaplan et al., 2020; Brown et al., 2020, inter alia). However, scaling up model size alone has not proved sufficient for achieving high performance on challenging tasks such as arithmetic, commonsense, and symbolic reasoning (Rae et al., 2021).

- Jared Kaplan, Sam McCandlish, Tom Henighan, Tom B Brown, Benjamin Chess, Rewon Child, Scott Gray, Alec Radford, Jeffrey Wu, and Dario Amodei. 2020. Scaling laws for neural language models. arXiv preprint arXiv:2001.08361.

- 研究了语言模型在交叉熵损失上的性能的经验性缩放规律。损失随着模型大小、数据集大小和训练用的计算量呈幂律缩放，有些趋势跨越了七个数量级以上。
- 网络的宽度或深度等其他架构细节在较大的范围内影响很小。
- 给出了一些简单的方程来描述过拟合与模型/数据集大小的依赖关系，以及训练速度与模型大小的依赖关系。
- 这些关系使我们能够确定固定计算预算的最优分配。更大的模型显著地提高了样本效率，因此最优的计算效率训练涉及在相对较少的数据上训练非常大的模型，并且在收敛之前显著地停止。

- Jack W. Rae, Sebastian Borgeaud, Trevor Cai, Katie Millican, Jordan Hoffmann, Francis Song, John Aslanides, Sarah Henderson, Roman Ring, Susannah Young, et al. 2021. Scaling language models: Methods, analysis & insights from training Gopher. arXiv preprint arXiv:2112.11446.

- 分析了基于Transformer的语言模型在不同模型规模下的性能，从几千万参数的模型到一个2800亿参数的模型，称为Gopher。
- 在152个不同的任务上评估了这些模型，在大多数任务上达到了最先进的水平。缩放效果在阅读理解、事实核查和有毒语言识别等领域最大，但逻辑和数学推理收益较小。
- 对训练数据集和模型行为进行了全面的分析，涵盖了模型规模与偏差和有毒性的交叉点。最后讨论了语言模型在人工智能安全和减轻下游危害方面的应用。

自然语言处理领域近年来被语言模型（Peters et al., 2018; Devlin et al., 2019; Brown et al., 2020等）彻底改变了。增加语言模型的规模已经被证明能带来一系列的好处， 比如提高性能和样本效率（Kaplan et al., 2020; Brown et al., 2020等）。然而， 仅仅增加模型规模并不足以在诸如算术、常识和符号推理等具有挑战性的任务上取得高性能（Rae et al., 2021）。

This work explores how the reasoning ability of large language models can be unlocked by a simple method motivated by two ideas. First, techniques for arithmetic reasoning can benefit from generating natural language rationales that lead to the final answer. Prior work has given models the ability to generate natural language intermediate steps by training from scratch (Ling et al., 2017) or finetuning a pretrained model (Cobbe et al., 2021), in addition to neuro-symbolic methods that use formal languages instead of natural language (Roy and Roth, 2015; Chiang and Chen, 2019; Amini et al., 2019; Chen et al., 2019). Second, large language models offer the exciting prospect of in-context few-shot learning via prompting. That is, instead of finetuning a separate language model checkpoint for each new task, one can simply “prompt” the model with a few input–output exemplars demonstrating the task. Remarkably, this has been successful for a range of simple question-answering tasks (Brown et al., 2020).

- Wang Ling, Dani Yogatama, Chris Dyer, and Phil Blunsom. 2017. Program induction by rationale generation: Learning to solve and explain algebraic word problems. ACL.

- 提出了一种通过生成答案理由来学习解决和解释代数问题的方法，答案理由是一种自然语言和人类可读的数学表达式的序列，通过一系列小步骤推导出最终答案。
- 答案理由虽然没有明确地指定程序，但是通过中间里程碑为它们的结构提供了支架。通过从问题-答案对直接归纳程序是一项艰巨的挑战，这使得这项任务更加可行。
- 为了评估我们的方法，我们创建了一个包含10万个问题、答案和理由的新数据集。实验结果表明，通过答案理由间接监督程序学习是一种诱导算术程序的有前途的策略。

- Karl Cobbe, Vineet Kosaraju, Mohammad Bavarian, Jacob Hilton, Reiichiro Nakano, Christopher Hesse, and John Schulman. 2021. Training verifiers to solve math word problems. arXiv preprint arXiv:2110.14168.

- 提出了一种通过训练验证器来解决数学问题的方法，验证器可以判断模型生成的候选解决方案的正确性。在测试时，生成多个候选解决方案，并选择验证器排名最高的一个。
- 在一个新的包含8.5K个高质量、语言多样的小学数学问题的数据集上评估了这种方法，发现验证器显著提高了模型的性能，使其接近真实孩子的水平。
- 分析了验证器的优势和局限性，以及不同模型规模和数据量对验证器效果的影响。发现验证器比微调基线更有效地利用增加的数据，并且能够纠正模型在算术运算上的错误。

- Subhro Roy and Dan Roth. 2015. Solving general arithmetic word problems. EMNLP
这篇论文提出了一种新颖的方法来自动解决算术文字问题。这是第一个能够处理多步骤和多种运算的算法方法，不依赖于额外的注释或预定义的模板。作者开发了一种表达式树的理论，可以用来表示和评估目标算术表达式；他们用它将目标算术问题唯一地分解为多个分类问题；然后他们通过一个受约束的推理框架，将这些分类结果与世界知识结合起来，构造出一个表达式树。作者的分类器从使用数量模式的优势中获益，这支持了更好的特征提取。实验结果表明，作者的方法超越了现有的系统，在算术文字问题的基准数据集上达到了最先进的性能。

这项工作探索了如何通过一种简单的方法来解锁大型语言模型的推理能力，这种方法受到两个想法的启发。第一，算术推理的技术可以从生成自然语言的理由，引导最终答案中受益。先前的工作已经给模型提供了生成自然语言中间步骤的能力，通过从头开始训练（Ling et al., 2017）或微调预训练模型（Cobbe et al., 2021），除了使用形式语言而不是自然语言的神经符号方法（Roy and Roth, 2015; Chiang and Chen, 2019; Amini et al., 2019; Chen et al., 2019）。第二，大型语言模型提供了通过提示进行上下文少量学习的令人兴奋的前景。也就是说，不需要为每个新任务微调一个单独的语言模型检查点，而只需要用一些展示任务的输入-输出示例“提示”模型。令人惊讶的是，这对于一系列简单的问答任务都很成功（Brown et al., 2020）。

Both of the above ideas, however, have key limitations. For rationale-augmented training and finetuning methods, it is costly to create a large set of high quality rationales, which is much more complicated than simple input–output pairs used in normal machine learning. For the traditional fewshot prompting method used in Brown et al. (2020), it works poorly on tasks that require reasoning abilities, and often does not improve substantially with increasing language model scale (Rae et al., 2021). In this paper, we combine the strengths of these two ideas in a way that avoids their limitations. Specifically, we explore the ability of language models to perform few-shot prompting for reasoning tasks, given a prompt that consists of triples: hinput, chain of thought, outputi. A chain of thought is a series of intermediate natural language reasoning steps that lead to the final output, and we refer to this approach as chain-of-thought prompting. An example prompt is shown in Figure 1.

![图1](https://xulun-mooc.oss-cn-beijing.aliyuncs.com/CoT1.png)

然而，上述两种思路都有关键的局限性。对于理由增强的训练和微调方法，创建一大套高质量的理由是非常昂贵的，这比正常机器学习中使用的简单的输入-输出对要复杂得多。对于Brown et al. (2020)中使用的传统的少样本提示方法，它在需要推理能力的任务上表现不佳，而且通常不会随着语言模型规模的增加而有显著的改善(Rae et al., 2021)。在本文中，我们将这两种思路的优势结合起来，以避免它们的局限性。具体来说，我们探索了语言模型在给定由三元组组成的提示时，执行少样本提示推理任务的能力：hinput, chain of thought, outputi。一个思维链是一系列中间自然语言推理步骤，它们导致最终的输出，我们将这种方法称为思维链提示。一个示例提示如图1所示。

We present empirical evaluations on arithmetic, commonsense, and symbolic reasoning benchmarks, showing that chain-of-thought prompting outperforms standard prompting, sometimes to a striking degree. Figure 2 illustrates one such result—on the GSM8K benchmark of math word problems (Cobbe et al., 2021), chain-of-thought prompting with PaLM 540B outperforms standard prompting by a large margin and achieves new state-of-the-art performance. A prompting only approach is important because it does not require a large training dataset and because a single model checkpoint can perform many tasks without loss of generality. This work underscores how large language models can learn via a few examples with natural language data about the task (c.f. automatically learning the patterns underlying inputs and outputs via a large training dataset).

我们在算术、常识和符号推理的基准测试上进行了实证评估，结果显示思维链提示优于标准提示，有时甚至达到惊人的程度。图2展示了一个这样的结果——在数学文字问题的GSM8K基准测试(Cobbe et al., 2021)上，使用PaLM 540B的思维链提示大幅度超过了标准提示，并达到了新的最先进的性能。仅使用提示的方法是很重要的，因为它不需要大量的训练数据集，而且一个单一的模型检查点可以在不失去一般性的情况下执行多个任务。这项工作强调了大型语言模型如何通过少量关于任务的自然语言数据来学习（与通过大量训练数据集自动学习输入和输出背后的模式相比较）。

## 2 Chain-of-Thought Prompting

Consider one’s own thought process when solving a complicated reasoning task such as a multi-step math word problem. It is typical to decompose the problem into intermediate steps and solve each before giving the final answer: “After Jane gives 2 flowers to her mom she has 10 : : : then after she gives 3 to her dad she will have 7 : : : so the answer is 7.” The goal of this paper is to endow language models with the ability to generate a similar chain of thought—a coherent series of intermediate reasoning steps that lead to the final answer for a problem. We will show that sufficiently large language models can generate chains of thought if demonstrations of chain-of-thought reasoning are provided in the exemplars for few-shot prompting.

想象一下，当解决一个复杂的推理任务，比如一个多步的数学文字问题时，自己的思维过程是什么样的。通常会把问题分解成中间步骤，然后在给出最终答案之前解决每一个步骤：“简给她妈妈两朵花后，她还有10朵: : : 然后她给她爸爸三朵，她就会有7朵: : : 所以答案是7。”这篇论文的目标是赋予语言模型生成类似的思维链的能力——一系列连贯的中间推理步骤，它们导向问题的最终答案。我们将展示，如果在少量提示的示例中提供思维链推理的演示，足够大的语言模型就可以生成思维链。

Figure 1 shows an example of a model producing a chain of thought to solve a math word problem that it would have otherwise gotten incorrect. The chain of thought in this case resembles a solution and can interpreted as one, but we still opt to call it a chain of thought to better capture the idea that it mimics a step-by-step thought process for arriving at the answer (and also, solutions/explanations typically come after the final answer (Narang et al., 2020; Wiegreffe et al., 2022; Lampinen et al., 2022, inter alia)).

图1显示了一个模型生成思维链来解决一个数学文字问题的例子，如果没有思维链，它就会得到错误的答案。在这种情况下，思维链类似于一个解决方案，并且可以被解释为一个解决方案，但我们仍然选择称之为思维链，以更好地捕捉它模仿一个逐步到达答案的思维过程的想法（而且，解决方案/解释通常在最终答案之后出现（Narang et al., 2020; Wiegreffe et al., 2022; Lampinen et al., 2022, inter alia））。

Chain-of-thought prompting has several attractive properties as an approach for facilitating reasoning in language models.

思维链提示作为一种促进语言模型推理的方法，具有几个有吸引力的特点。

- First, chain of thought, in principle, allows models to decompose multi-step problems into intermediate steps, which means that additional computation can be allocated to problems that require more reasoning steps.
首先，思维链原则上允许模型将多步问题分解成中间步骤，这意味着可以为需要更多推理步骤的问题分配更多的计算资源。

- Second, a chain of thought provides an interpretable window into the behavior of the model, suggesting how it might have arrived at a particular answer and providing opportunities to debug where the reasoning path went wrong (although fully characterizing a model’s computations that support an answer remains an open question).
其次，一条思路为模型的行为提供了一个可解释的窗口，提示它可能如何得出一个特定的答案，并提供机会调试推理路径出错的地方（尽管完全描述支持答案的模型的计算仍然是一个开放的问题）。

- Third, chain-of-thought reasoning can be used for tasks such as math word problems, commonsense reasoning, and symbolic manipulation, and is potentially applicable (at least in principle) to any task that humans can solve via language.
第三，思维链推理可以用于数学应用题、常识推理和符号操作等任务，并且在原则上可能适用于任何人类可以通过语言解决的任务。

- Finally, chain-of-thought reasoning can be readily elicited in sufficiently large off-the-shelf language models simply by including examples of chain of thought sequences into the exemplars of few-shot prompting.
最后，通过将思维链序列的示例包含在几次提示的示例中，足够大的现成语言模型可以轻松引发思维链推理。

## 3 Arithmetic Reasoning

We begin by considering math word problems of the form in Figure 1, which measure the arithmetic reasoning ability of language models. Though simple for humans, arithmetic reasoning is a task where language models often struggle (Hendrycks et al., 2021; Patel et al., 2021, inter alia). Strikingly, chainof- thought prompting when used with the 540B parameter language model performs comparably with task-specific finetuned models on several tasks, even achieving new state of the art on the challenging GSM8K benchmark (Cobbe et al., 2021).
我们首先考虑图1中的数学词问题，它们衡量了语言模型的算术推理能力。虽然这对人类来说很简单，但算术推理是语言模型经常遇到困难的任务（例如Hendrycks等人，2021年; Patel等人，2021年）。令人惊讶的是，当与540B参数语言模型一起使用时，"思维链提示"在多项任务上的表现与特定任务的微调模型相当，甚至在具有挑战性的GSM8K基准测试中实现了新的最佳表现（Cobbe等人，2021年）。

### 3.1 Experimental Setup

We explore chain-of-thought prompting for various language models on multiple benchmarks.
我们在多个基准测试中探索了思维链提示技术在不同语言模型上的应用。

#### Benchmarks

We consider the following five math word problem benchmarks: (1) the GSM8K benchmark of math word problems (Cobbe et al., 2021), (2) the SVAMP dataset of math word problems with varying structures (Patel et al., 2021), (3) the ASDiv dataset of diverse math word problems (Miao et al., 2020), (4) the AQuA dataset of algebraic word problems, and (5) the MAWPS benchmark (Koncel-Kedziorski et al., 2016). Example problems are given in Appendix Table 12.
我们考虑以下五个数学词问题基准测试：(1) 数学词问题的GSM8K基准测试（Cobbe等人，2021），(2) 含有不同结构数学词问题的SVAMP数据集（Patel等人，2021），(3) 多样化数学词问题的ASDiv数据集（Miao等人，2020），(4) 代数词问题的AQuA数据集，以及(5) MAWPS基准测试（Koncel-Kedziorski等人，2016）。示例问题在附录表12中给出。

#### Standard prompting

For the baseline, we consider standard few-shot prompting, popularized by Brown et al. (2020), in which a language model is given in-context exemplars of input–output pairs before outputting a prediction for a test-time example. Exemplars are formatted as questions and answers. The model gives the answer directly, as shown in Figure 1 (left). 
作为基准线，我们考虑标准的少样本提示技术，这种技术由Brown等人（2020）推广，其中语言模型在输出测试样例的预测之前被提供了输入输出对的上下文示例。示例被格式化为问题和答案。模型直接给出答案，如图1（左侧）所示。

#### Chain-of-thought prompting

Our proposed approach is to augment each exemplar in few-shot prompting with a chain of thought for an associated answer, as illustrated in Figure 1 (right). As most of the datasets only have an evaluation split, we manually composed a set of eight few-shot exemplars with chains of thought for prompting—Figure 1 (right) shows one chain of thought exemplar, and the full set of exemplars is given in Appendix Table 20. (These particular exemplars did not undergo prompt engineering; robustness is studied in Section 3.4 and Appendix A.2.) To investigate whether chain-of-thought prompting in this form can successfully elicit successful reasoning across a range of math word problems, we used this single set of eight chain of thought exemplars for all benchmarks except AQuA, which is multiple choice instead of free response. For AQuA, we used four exemplars and solutions from the training set, as given in Appendix Table 21.
我们提出的方法是在少样本提示中为每个示例增加一系列思路链，以及其相关答案，如图1（右）所示。由于大多数数据集只有一个评估集，我们手动构建了一个包含八个少样本示例的提示思路链集合——图1（右）展示了一个思路链示例，完整的示例集合在附录表格20中给出。（这些特定的示例并未经过提示工程处理；鲁棒性研究在第3.4节和附录A.2中进行。）为了调查这种形式的思路链提示能否成功地引出各种数学问题的成功推理，我们将这一套八个思路链样例集合用于所有基准测试，除了AQuA，因为AQuA是多项选择而不是自由回答。对于AQuA，我们使用了训练集中的四个示例和解决方案，如附录表格21所示。

#### Language models

We evaluate five large language models. The first is GPT-3 (Brown et al., 2020), for which we use text-ada-001, text-babbage-001, text-curie-001, and text-davinci-002, which presumably correspond to InstructGPT models of 350M, 1.3B, 6.7B, and 175B parameters (Ouyang et al., 2022).The second is LaMDA (Thoppilan et al., 2022), which has models of 422M, 2B, 8B, 68B, and 137B parameters. The third is PaLM, which has models of 8B, 62B, and 540B parameters. The fourth is UL2 20B (Tay et al., 2022), and the fifth is Codex (Chen et al., 2021, code-davinci-002 in the OpenAI API). We sample from the models via greedy decoding (though follow-up work shows chain-of-thought prompting can be improved by taking the majority final answer over many sampled generations (Wang et al., 2022a)). For LaMDA, we report averaged results over five random seeds, where each seed had a different randomly shuffled order of exemplars. As LaMDA experiments did not show large variance among different seeds, to save compute we report results for a single exemplar order for all other models. 

我们评估了五个大型语言模型。第一个是GPT-3（Brown等，2020），我们使用text-ada-001，text-babbage-001，text-curie-001和text-davinci-002，这些模型据说对应于InstructGPT模型的350M、1.3B、6.7B和175B个参数（Ouyang等，2022）。第二个是LaMDA（Thoppilan等，2022），它有422M、2B、8B、68B和137B个参数的模型。第三个是PaLM，它有8B、62B和540B个参数的模型。第四个是UL2 20B（Tay等，2022），第五个是Codex（Chen等，2021，在OpenAI API中为code-davinci-002）。我们通过贪心解码从模型中采样（尽管后续工作表明，通过对许多采样生成的大多数最终答案进行取舍，思路链提示可以得到改进（Wang等，2022a））。对于LaMDA，我们对五个随机种子进行平均结果报告，每个种子都有一个不同的随机排序的示例集。由于LaMDA实验在不同种子之间没有显示出很大的差异，为了节省计算资源，我们为所有其他模型报告一个示例顺序的结果。

### 3.2 Results

The strongest results of chain-of-thought prompting are summarized in Figure 4, with all experimental outputs for each model collection, model size, and benchmark shown in Table 2 in the Appendix. There are three key takeaways. First, Figure 4 shows that chain-of-thought prompting is an emergent ability of model scale (Wei et al., 2022b). That is, chain-of-thought prompting does not positively impact performance for small models, and only yields performance gains when used with models of ~100B parameters. We qualitatively found that models of smaller scale produced fluent but illogical chains of thought, leading to lower performance than standard prompting.
思路链提示的最强结果总结在图4中，每个模型集合、模型大小和基准测试的所有实验输出在附录表2中显示。有三个关键的发现。首先，图4显示，思路链提示是模型规模的一种新兴能力（Wei等，2022b）。也就是说，思路链提示对于小型模型没有积极影响，只有在使用大约100B参数的模型时才能产生性能提升。我们在定性上发现，规模较小的模型会产生流畅但不合逻辑的思路链，导致性能低于标准提示。

- JasonWei, Yi Tay, Rishi Bommasani, Colin Raffel, Barret Zoph, Sebastian Borgeaud, Dani Yogatama, Maarten Bosma, Denny Zhou, Donald Metzler, et al. 2022b. Emergent abilities of large language models. Transactions on Machine Learning Research.


Second, chain-of-thought prompting has larger performance gains for more-complicated problems. For instance, for GSM8K (the dataset with the lowest baseline performance), performance more than doubled for the largest GPT and PaLM models. On the other hand, for SingleOp, the easiest subset of MAWPS which only requires a single step to solve, performance improvements were either negative or very small (see Appendix Table 3).
其次，思路链提示对于更复杂的问题具有更大的性能提升。例如，对于GSM8K（基线性能最低的数据集），最大的GPT和PaLM模型的性能提高了一倍以上。另一方面，对于SingleOp，MAWPS的最简单子集，只需要一步即可解决，性能提升要么为负，要么非常小（见附录表3）。

Third, chain-of-thought prompting via GPT-3 175B and PaLM 540B compares favorably to prior state of the art, which typically finetunes a task-specific model on a labeled training dataset. Figure 4 shows how PaLM 540B uses chain-ofthought prompting to achieve new state of the art on GSM8K, SVAMP, and MAWPS (though note that standard prompting already passed the prior best for SVAMP). On the other two datasets, AQuA and ASDiv, PaLM with chain-of-thought prompting reaches within 2% of the state of the art (Appendix Table 2).
第三，通过GPT-3 175B和PaLM 540B的思维链提示，与以往的最先进技术相比表现出色，以往的技术通常在标记的训练数据集上微调特定任务的模型。图4展示了PaLM 540B如何使用思维链提示在GSM8K、SVAMP和MAWPS上实现新的最先进技术（尽管请注意，标准提示已经超过了SVAMP的先前最佳表现）。在另外两个数据集AQuA和ASDiv上，具有思维链提示的PaLM达到了最先进水平的接近2％（附录表2）。

To better understand why chain-of-thought prompting works, we manually examined modelgenerated chains of thought by LaMDA 137B for GSM8K. Of 50 random examples where the model returned the correct final answer, all of the generated chains of thought were also logically and mathematically correct except two that coincidentally arrived at the correct answer (see Appendix D.1, and Table 8 for examples of correct model-generated chains of thought). We also randomly examined 50 random samples for which the model gave the wrong answer. The summary of this analysis is that 46% of the chains of thought were almost correct, barring minor mistakes (calculator error, symbol mapping error, or one reasoning step missing), and that the other 54% of the chains of thought had major errors in semantic understanding or coherence (see Appendix D.2). To provide a small insight into why scaling improves chain-of-thought reasoning ability, we performed a similar analysis of errors made by PaLM 62B and whether those errors were fixed by scaling to PaLM 540B. The summary is that scaling PaLM to 540B fixes a large portion of one-step missing and semantic understanding errors in the 62B model (see Appendix A.1).
为了更好地理解为什么思维链提示有效，我们手动检查了LaMDA 137B生成的GSM8K思维链。在50个随机示例中，模型返回正确的最终答案，除了两个巧合地到达正确答案的思维链之外，所有生成的思维链都是逻辑上和数学上正确的（请参见附录D.1和表8，其中列出了正确的模型生成思维链的示例）。我们还随机检查了50个模型给出错误答案的样本。这项分析的总结是，46％的思维链几乎是正确的，除了一些小错误（计算器错误、符号映射错误或缺少一步推理），而另外54％的思维链在语义理解或连贯性方面存在重大错误（请参见附录D.2）。

为了提供一些关于为什么扩大规模可以提高思维链推理能力的见解，我们对PaLM 62B所犯的错误以及这些错误是否通过扩大规模至PaLM 540B而得到纠正进行了类似的分析。总结是，将PaLM扩大至540B可以纠正62B模型中大部分缺少一步推理和语义理解错误（请参见附录A.1）。

### 3.3 Ablation Study

The observed benefits of using chain-of-thought prompting raises the natural question of whether the same performance improvements can be conferred via other types of prompting. Figure 5 shows an ablation study with three variations of chain of thought described below.
使用思维链提示所观察到的好处引发了一个自然的问题，即是否可以通过其他类型的提示来获得相同的性能改进。图5展示了一个消融研究，其中包括三个思维链提示的变体，这些变体的描述如下。

#### Equation only

One reason for why chain-of-thought prompting might help is that it produces the mathematical equation to be evaluated, and so we test a variation where the model is prompted to output only a mathematical equation before giving the answer. Figure 5 shows that equation only prompting does not help much for GSM8K, which implies that the semantics of the questions in GSM8K are too challenging to directly translate into an equation without the natural language reasoning steps in chain of thought. For datasets of one-step or two-step problems, however, we find that equation only prompting does improve performance, since the equation can be easily derived from the question (see Appendix Table 6). 
思维链提示可能有用的原因之一是它产生要评估的数学方程式，因此我们测试了一种变体，在该变体中，模型在给出答案之前只被提示输出一个数学方程式。图5显示，对于GSM8K，仅方程提示并没有太大的帮助，这意味着GSM8K中的问题语义太具有挑战性，无法直接将其转化为方程式，而没有思维链中的自然语言推理步骤。然而，对于一步或两步问题的数据集，我们发现仅方程提示确实提高了性能，因为可以轻松地从问题中推导出方程式（请参见附录表6）。

#### Variable compute only

Another intuition is that chain of thought allows the model to spend more computation (i.e., intermediate tokens) on harder problems. To isolate the effect of variable computation from chain-of-thought reasoning, we test a configuration where the model is prompted to output a only sequence of dots (: : :) equal to the number of characters in the equation needed to solve the problem. This variant performs about the same as the baseline, which suggests that variable computation by itself is not the reason for the success of chainof- thought prompting, and that there appears to be utility from expressing intermediate steps via natural language.
另一种想法是，思维链允许模型在更难的问题上花费更多的计算资源（即中间标记）。为了隔离变量计算量和思维链推理的影响，我们测试了一种配置，其中模型被提示仅输出一个由（: : :)组成的点序列，该序列的长度等于解决问题所需的方程式中的字符数。这个变体的表现与基线差不多，这表明变量计算本身并不是思维链提示成功的原因，而自然语言表达中间步骤似乎有用。

#### Chain of thought after answer

Another potential benefit of chain-of-thought prompting could simply be that such prompts allow the model to better access relevant knowledge acquired during pretraining. Therefore, we test an alternative configuration where the chain of thought prompt is only given after the answer, isolating whether the model actually depends on the produced chain of thought to give the final answer. This variant performs about the same as the baseline, which suggests that the sequential reasoning embodied in the chain of thought is useful for reasons beyond just activating knowledge.
思维链提示的另一个潜在好处可能仅仅是这样的提示允许模型更好地访问预训练时获得的相关知识。因此，我们测试了一种替代配置，其中思维链提示仅在答案之后给出，以隔离模型实际是否依赖所生成的思维链来给出最终答案。这个变种的表现与基线差不多，这表明思维链中体现的顺序推理具有超出激活知识之外的其他有用性质。

### 3.4 Robustness of Chain of Thought

Sensitivity to exemplars is a key consideration of prompting approaches—for instance, varying the permutation of few-shot exemplars can cause the accuracy of GPT-3 on SST-2 to range from near chance (54.3%) to near state of the art (93.4%) (Zhao et al., 2021). In this final subsection, we evaluate robustness to chains of thought written by different annotators. In addition to the results above, which used chains of thought written by an Annotator A, two other co-authors of this paper (Annotators B and C) independently wrote chains of thought for the same few-shot exemplars (shown in Appendix H). Annotator A also wrote another chain of thought that was more concise than the original, following the style of solutions given in Cobbe et al. (2021).
对样本的敏感性是提示方法的关键考虑因素——例如，改变几个示例的排列顺序会导致GPT-3在SST-2上的准确率从接近随机（54.3％）到接近最优状态（93.4％）（Zhao等，2021）。在本文的最后一小节中，我们评估了对不同注释者编写的思考链的鲁棒性。除了上面的结果，即使用注释者A编写的思考链之外，本文的另外两位合著者（注释者B和C）也独立地为相同的几个示例编写了思考链（详见附录H）。注释者A还按照Cobbe等人（2021）给出的解决方案风格，编写了比原始版本更简洁的思考链。

## 4 Commonsense Reasoning

## 5 Symbolic Reasoning

## 6 Discussion

## 7 Related Work

This work is inspired by many research areas, which we detail in an extended related work section (Appendix C). Here we describe two directions and associated papers that are perhaps most relevant.

这项工作受到了许多研究领域的启发，我们在一个扩展的相关工作部分（附录C）中详细介绍了这些领域。在这里，我们描述了两个方向和一些可能最相关的论文。

The first relevant direction is using intermediate steps to solve reasoning problems. Ling et al. (2017) pioneer the idea of using natural language rationales to solve math word problems through a series of intermediate steps. Their work is a remarkable contrast to the literature using formal languages to reason (Roy et al., 2015; Chiang and Chen, 2019; Amini et al., 2019; Chen et al., 2019). Cobbe et al. (2021) extend Ling et al. (2017) by creating a larger dataset and using it to finetune a pretrained language model rather than training a model from scratch. In the domain of program synthesis, Nye et al. (2021) leverage language models to predict the final outputs of Python programs via first line-to-line predicting the intermediate computational results, and show that their step-by-step prediction method performs better than directly predicting the final outputs.

第一个相关的方向是使用中间步骤解决推理问题。Ling等人（2017）首创使用自然语言理性通过一系列中间步骤解决数学单词问题的想法。他们的工作与使用形式语言推理的文献形成了显著的对比（Roy等人，2015；Chiang和Chen，2019；Amini等人，2019；Chen等人，2019）。Cobbe等人（2021）通过创建一个更大的数据集，并使用它微调预训练语言模型而不是从头开始训练模型来扩展Ling等人（2017）的工作。在程序合成领域，Nye等人（2021）利用语言模型预测Python程序的最终输出，通过首先逐行预测中间计算结果，展示他们的逐步预测方法比直接预测最终输出更好的性能。

Naturally, this paper also relates closely to the large body of recent work on prompting. Since the popularization of few-shot prompting as given by Brown et al. (2020), several general approaches have improved the prompting ability of models, such as automatically learning prompts (Lester et al., 2021) or giving models instructions describing a task (Wei et al., 2022a; Sanh et al., 2022; Ouyang et al., 2022). Whereas these approaches improve or augment the input part of the prompt (e.g., instructions that are prepended to inputs), our work takes the orthogonal direction of augmenting the outputs of language models with a chain of thought.

自然地，本文与近期关于提示的大量研究也有密切关系。自Brown等人（2020）提出few-shot提示以来，已经出现了几种通用方法来提高模型的提示能力，例如自动学习提示（Lester等人，2021）或为模型提供描述任务的指令（Wei等人，2022a；Sanh等人，2022；Ouyang等人，2022）。尽管这些方法改善或增强了提示的输入部分（例如，附加到输入的指令），但我们的工作采取了一种正交的方法，即通过思维链来增强语言模型的输出。

## 8 Conclusions

We have explored chain-of-thought prompting as a simple and broadly applicable method for enhancing reasoning in language models. Through experiments on arithmetic, symbolic, and commonsense reasoning, we find that chain-of-thought reasoning is an emergent property of model scale that allows sufficiently large language models to perform reasoning tasks that otherwise have flat scaling curves. Broadening the range of reasoning tasks that language models can perform will hopefully inspire further work on language-based approaches to reasoning.

我们探索了思维链提示作为一种简单而广泛适用的方法，来增强语言模型的推理能力。通过在算术、符号和常识推理上的实验，我们发现思维链推理是模型规模的一种自然产生的性质，它使得足够大的语言模型能够执行那些否则有平坦的缩放曲线的推理任务。扩大语言模型能够执行的推理任务的范围，希望能够激发更多关于基于语言的推理方法的研究。

## C Extended Related Work

Chain-of-thought prompting is a general approach that is inspired by several prior directions: prompting, natural language explanations, program synthesis/execution, numeric and logical reasoning, and intermediate language steps.

思维链提示是一种通用的方法，它受到了几个先前方向的启发：提示、自然语言解释、程序合成/执行、数值和逻辑推理以及中间语言步骤。

### C.1 Prompting

The recent success of large-scale language models has led to growing interest in improving their capability to perform tasks via prompting (Brown et al. (2020), and see Liu et al. (2021) for a survey). This paper falls in the category of general prompting approaches, whereby input prompts are optimized to allow a single large language model to better perform a variety of tasks (Li and Liang, 2021; Lester et al., 2021; Reif et al., 2022, inter alia).

- Pengfei Liu, Weizhe Yuan, Jinlan Fu, Zhengbao Jiang, Hiroaki Hayashi, and Graham Neubig. 2021. Pre-train, prompt, and predict: A systematic survey of prompting methods in natural language processing. arXiv preprint arXiv:2107.13586.
这篇论文调查和组织了自然语言处理中一种新的范式的研究工作，作者称之为“基于提示的学习”。与传统的监督学习不同，后者训练一个模型接受一个输入x并预测一个输出y作为P (y|x)，基于提示的学习是基于语言模型的，它直接对文本的概率进行建模。要使用这些模型来执行预测任务，原始的输入x被使用一个模板修改成一个文本字符串提示x’，它有一些未填充的槽，然后语言模型被用来概率地填充未填充的信息，得到一个最终的字符串x，从中可以得到最终的输出y。这个框架有很多优点和吸引力：它允许语言模型在大量的原始文本上进行预训练，并且通过定义一个新的提示函数，模型能够执行少样本甚至零样本学习，适应新场景而几乎或没有标注数据。在本文中，我们介绍了这个有前途的范式的基础，描述了一套统一的数学符号，可以涵盖各种现有的工作，并沿着几个维度组织了现有的工作，例如预训练模型、提示和调优策略的选择。为了让感兴趣的初学者更容易接触这个领域，我们不仅系统地回顾了现有的工作和一个高度结构化的基于提示概念的分类，而且还发布了其他资源，例如包括不断更新的调查和论文列表的网站this http URL。

- Xiang Lisa Li and Percy Liang. 2021. Prefix-tuning: Optimizing continuous prompts for generation. ACL.
这篇论文提出了一种新颖的方法来利用大型预训练语言模型进行自然语言生成任务，称为前缀调优。与传统的微调不同，前缀调优保持语言模型参数不变，而是优化一系列连续的任务特定向量，称为前缀。前缀调优受到语言模型提示的启发，允许后续的token对这个前缀进行注意，就像它们是“虚拟token”一样。作者将前缀调优应用于GPT-2进行表格到文本生成和BART进行摘要生成。作者发现，通过学习只有0.1%的参数，前缀调优在全数据设置下获得了可比较的性能，在低数据设置下超过了微调，并且在训练中未见过的主题的例子上有更好的外推能力。

- Brian Lester, Rami Al-Rfou, and Noah Constant. 2021. The power of scale for parameter-efficient prompt tuning. EMNLP.
这篇论文的主要观点是探索了一种简单而有效的机制，称为“提示调优(prompt tuning)”，用于学习“软提示(soft prompts)”来调节固定的语言模型，使其执行特定的下游任务。与GPT-3使用的离散文本提示不同，软提示是通过反向传播学习的，并且可以根据任意数量的标注样本进行调优。作者的端到端学习方法大幅度超过了GPT-3的“少样本”学习。更值得注意的是，通过使用T5模型对模型大小进行消融实验，作者发现提示调优随着规模而变得更有竞争力：当模型超过数十亿参数时，他们的方法“缩小了差距”，并且达到了与模型调优(其中所有模型权重都被调优)相当的强大性能。这一发现尤其相关，因为大型模型很难共享和服务，而能够重用一个固定的模型来执行多个下游任务可以减轻这一负担。作者的方法可以看作是Li和Liang (2021)最近提出的“前缀调优(prefix tuning)”的简化版本，并且作者提供了与这种方法和其他类似方法的比较。最后，作者展示了用软提示调节一个固定的模型相比于完全模型调优，在域转移方面具有更好的鲁棒性。

- Emily Reif, Daphne Ippolito, Ann Yuan, Andy Coenen, Chris Callison-Burch, and Jason Wei. 2022. A recipe for arbitrary text style transfer with large language models. ACL.
大规模语言模型的近期成功引起了人们对提高它们通过提示执行任务的能力的越来越多的兴趣（Brown et al. (2020)，并参见Liu et al. (2021)的一项调查）。本文属于一般提示方法的范畴，即通过优化输入提示，使一个大型语言模型能够更好地执行各种任务（Li and Liang, 2021; Lester et al., 2021; Reif et al., 2022等）。

One recent line of work aims to improve the ability of language models to perform a task by providing instructions that describe the task (Raffel et al., 2020; Wei et al., 2022a; Ouyang et al., 2022; Sanh et al., 2022; Wang et al., 2022b). This line of work is related because it also augments input–output pairs with meta-data. But whereas an instruction augments the input to a task (instructions are typically prepended to the inputs), chain-of-thought prompting augments the outputs of language models. Another related direction is sequentially combining the outputs of language models; human–computer interaction (HCI) work (Wu et al., 2022a,b) has shown that combining sequential generations of language models improves task outcomes in a 20-person user study.

- Jason Wei, Maarten Bosma, Vincent Y. Zhao, Kelvin Guu, Adams Wei Yu, Brian Lester, Nan Du, Andrew M. Dai, and Quoc V. Le. 2022a. Finetuned language models are zero-shot learners. ICLR.
这篇论文的主要观点是利用大型语言模型(LLMs)来进行零样本学习。作者展示了一种称为指令调优(instruction tuning)的简单方法，通过在一系列用指令描述的数据集上微调语言模型，显著提高了在未见任务上的零样本性能。作者将一个137B参数的预训练语言模型在60多个用自然语言指令模板表达的NLP数据集上进行指令调优。作者评估了这个指令调优的模型，称为FLAN，在未见任务类型上的表现。FLAN显著提高了其未修改版本的性能，并且在25个评估的数据集中有20个超过了零样本175B GPT-3。FLAN甚至在ANLI、RTE、BoolQ、AI2-ARC、OpenbookQA和StoryCloze等数据集上大幅度超过了少样本GPT-3。消融实验揭示了微调数据集的数量、模型规模和自然语言指令对于指令调优成功的关键作用。

- Victor Sanh, Albert Webson, Colin Raffel, Stephen H. Bach, Lintang Sutawika, Zaid Alyafeai, Antoine Chaffin, Arnaud Stiegler, Teven Le Scao, Arun Raja, et al. 2022. Multitask prompted training enables zero-shot task generalization. ICLR.
这篇论文的主要观点是利用大型语言模型(LLMs)来进行零样本任务泛化。作者提出了一种称为多任务提示训练(multitask prompted training)的方法，将任意的自然语言任务映射为一种人类可读的提示形式，并在多个用不同提示表达的数据集上微调预训练的编码器-解码器模型。这些提示数据集允许评估模型在完全未见的任务上的表现。作者在涵盖多种任务的多任务混合上微调了一个137B参数的预训练语言模型。该模型在几个标准数据集上达到了强大的零样本性能，通常超过了其16倍大小的模型。此外，该方法在BIG-bench基准测试的一个子集上也表现出强大的性能，超过了其6倍大小的模型。

- YizhongWang, Swaroop Mishra, Pegah Alipoormolabashi, Yeganeh Kordi, Amirreza Mirzaei, Anjana Arunkumar, Arjun Ashok, Arut Selvan Dhanasekaran, Atharva Naik, David Stap, et al. 2022b. Benchmarking generalization via in-context instructions on 1,600+ language tasks. arXiv preprint arXiv:2204.07705.
这篇论文的主要观点是利用大型语言模型(LLMs)来进行零样本任务泛化。作者提出了一种称为超自然指令(Super-NaturalInstructions)的基准测试，包含了1,616个多样的NLP任务和它们的专家编写的指令。该基准测试涵盖了76种不同的任务类型，包括分类、抽取、填充、序列标注、文本重写和文本生成等。这些任务是通过社区中的NLP从业者的贡献和一个迭代的同行评审过程来收集的，以确保它们的质量。借助这个大型和多样的任务集合，作者能够严格地评估模型在跨任务泛化上的能力——在一部分任务上进行训练，并在剩余的未见任务上进行评估。例如，作者量化了不同的缩放参数，如观察到的任务数量、每个任务的实例数量和模型大小等，对泛化的影响。基于这些洞察，作者提出了一个编码器-解码器变换器模型Tk-INSTRUCT，它被训练来遵循各种上下文指令（纯语言任务定义或k-shot示例），在基准测试上超过了现有的更大的模型。作者希望这个基准测试能够促进未来更通用的语言理解模型的进展。

- Tongshuang Wu, Ellen Jiang, Aaron Donsbach, Jeff Gray, Alejandra Molina, Michael Terry, and Carrie J Cai. 2022a. PromptChainer: Chaining large language model prompts through visual programming. CHI Extended Abstracts.


最近的一些工作旨在通过提供描述任务的指令来提高语言模型执行任务的能力（Raffel et al., 2020; Wei et al., 2022a; Ouyang et al., 2022; Sanh et al., 2022; Wang et al., 2022b）。这一系列工作与我们的工作相关，因为它们也用元数据增强了输入-输出对。但是，与指令增强任务的输入不同（指令通常是附加在输入之前的），思维链提示增强了语言模型的输出。另一个相关的方向是顺序地组合语言模型的输出；人机交互（HCI）工作（Wu et al., 2022a,b）已经表明，在20人的用户研究中，组合语言模型的顺序生成可以改善任务结果。

### C.2 Natural language explanations

Another closely related direction uses natural language explanations (NLEs), often with the goal of improving model interpretability (Zhou et al., 2020; Wiegreffe and Marasovi´c, 2021, inter alia). That line of work typically focuses on natural language inference (Camburu et al., 2018; Yordanov et al., 2021; Bostrom et al., 2021), and produces explanations either simultaneously to or after the final prediction (Narang et al., 2020; Majumder et al., 2021; Wiegreffe et al., 2021, 2022). By contrast, the chain of thought processing considered in this paper occurs before the final answer. And while NLE aims mostly to improve neural network interpretability (Rajagopal et al., 2021), the goal of chain-of-thought prompting is to allow models to decompose multi-hop reasoning tasks into multiple steps—interpretability is just a side effect. Marasovi´c et al. (2022) show that prompt-based finetuning with NLE improves NLI and classification performance, though they largely focus on evaluating explanation plausibility. In comparison, our work focuses on a range of arithmetic, commonsense, and symbolic tasks that require multi-hop reasoning.

另一个密切相关的方向是使用自然语言解释(NLEs)，通常是为了提高模型的可解释性(Zhou et al., 2020; Wiegreffe and Marasovi´c, 2021等等)。这一系列工作通常关注自然语言推理(NLI)任务(Camburu et al., 2018; Yordanov et al., 2021; Bostrom et al., 2021)，并且在最终预测的同时或之后生成解释(Narang et al., 2020; Majumder et al., 2021; Wiegreffe et al., 2021, 2022)。相比之下，本文考虑的思维链处理是在最终答案之前发生的。而且，NLE的主要目的是提高神经网络的可解释性(Rajagopal et al., 2021)，而思维链提示的目的是让模型能够将多跳推理任务分解为多个步骤——可解释性只是一个副作用。Marasovi´c et al. (2022)表明，基于提示的微调与NLE相结合可以提高NLI和分类性能，尽管他们主要关注评估解释的合理性。相比之下，我们的工作关注一系列需要多跳推理的算术、常识和符号任务。

### C.3 Program synthesis and execution

Using intermediate reasoning steps has a long history in program synthesis and execution (Zaremba and Sutskever, 2014, inter alia). Recent work along in this direction has included a number of architectural innovations (Cai et al., 2017; Dong et al., 2019; Yan et al., 2020), as well as the use of large language models (Chen et al., 2021; Austin et al., 2021). The program execution work closest to ours is perhaps Nye et al. (2021), which show that large language models can perform up to 10-digit addition, evaluate polynomials, and execute python programs. Whereas generating a program and then executing it can be viewed as a type of reasoning, our work generalizes such domain-specific primitives to natural language, which is open-domain and relevant to any text-to-text NLP task in principle.

使用中间推理步骤在程序合成和执行方面有着悠久的历史(Zaremba and Sutskever, 2014等等)。这一方向的最近的工作包括了一些架构方面的创新(Cai et al., 2017; Dong et al., 2019; Yan et al., 2020)，以及使用大型语言模型(Chen et al., 2021; Austin et al., 2021)。与我们的工作最接近的程序执行工作可能是Nye et al. (2021)，他们展示了大型语言模型可以进行高达10位数的加法，求解多项式，以及执行python程序。虽然生成一个程序然后执行它可以被视为一种推理，但我们的工作将这种领域特定的原语推广到自然语言，这是开放域的，并且原则上与任何文本到文本的NLP任务相关。

### C.4 Numeric and logical reasoning

Numeric and logical reasoning has been a long-studied task in machine learning and natural language processing (Lev et al., 2004, inter alia). Recent work has also aimed to inject numeric reasoning abilities in language models in various ways, such as augmenting BERT with a predefined set of executable operations (Andor et al., 2019), including a graph neural network (Ran et al., 2019), and using specialized training procedures (Pi˛ekos et al., 2021). Another line of work aims to enable language models to perform logical or formal reasoning, often by verablizing the rules in natural language formal rules using language (Clark et al., 2020; Saeed et al., 2021; Liang et al., 2021). Perhaps the most-related work here is Recchia (2021), which shows that finetuning enables longhand module operations, which has previously been difficult for performers. Whereas work in this direction is often task-specific and uses finetuning, we show that chain-of-thought prompting works for a broad range of tasks without any finetuning.

数值和逻辑推理一直是机器学习和自然语言处理领域的一个长期研究任务（Lev et al., 2004, inter alia）。最近的工作也旨在以各种方式为语言模型注入数值推理能力，例如用一组预定义的可执行操作来增强BERT（Andor et al., 2019），包括一个图神经网络（Ran et al., 2019），以及使用专门的训练过程（Pi˛ekos et al., 2021）。另一条工作线旨在使语言模型能够进行逻辑或形式推理，通常是通过使用自然语言来表达规则（Clark et al., 2020; Saeed et al., 2021; Liang et al., 2021）。也许与这里最相关的工作是Recchia (2021)，它显示了微调可以实现长手模块操作，这在以前对于执行者来说是很困难的。与这个方向的工作通常是任务特定的并且使用微调不同，我们展示了链式思维提示对于一系列广泛的任务都有效，而且不需要任何微调。

### C.5 Intermediate language steps

Extensive prior work has shown the benefits of endowing neural networks with the ability to produce intermediate steps via training or finetuning confers various benefits in a range of scenarios. As examples, it has been shown that natural language intermediate steps can improve performance (Zaidan et al., 2007; Yao et al., 2021; Hase and Bansal, 2022; Gu et al., 2022), improve robustness (Chen et al., 2022), speed up training (Hancock et al., 2018), mitigate bias (Dua et al., 2020), and even help in image and reinforcement learning settings (Andreas et al., 2018). To endow models with the ability to produce intermediate steps, prior work typically finetunes models on either manually annotated training datasets (Camburu et al., 2018; Rajani et al., 2019, inter alia) or generates synthetic datasets (Talmor et al., 2020; Zelikman et al., 2022). Compared with these training or finetuning methods, our work shows that various natural language reasoning abilities can be elicited in off-theshelf language models of sufficient scale simply via prompting. This prompting setup is important because it allows for intermediate step reasoning without a large number of labeled annotations, and because a single model can perform a range of reasoning tasks without any gradient updates.

大量的先前工作已经显示了通过训练或微调赋予神经网络产生中间步骤的能力在一系列场景中带来的各种好处。例如，已经证明了自然语言中间步骤可以提高性能（Zaidan et al., 2007; Yao et al., 2021; Hase and Bansal, 2022; Gu et al., 2022），提高鲁棒性（Chen et al., 2022），加速训练（Hancock et al., 2018），减轻偏见（Dua et al., 2020），甚至在图像和强化学习设置中有所帮助（Andreas et al., 2018）。为了赋予模型产生中间步骤的能力，先前的工作通常在人工标注的训练数据集（Camburu et al., 2018; Rajani et al., 2019, inter alia）或生成合成数据集（Talmor et al., 2020; Zelikman et al., 2022）上对模型进行微调。与这些训练或微调方法相比，我们的工作显示了各种自然语言推理能力可以通过提示在足够规模的现成语言模型中被引出。这种提示设置很重要，因为它允许在没有大量标注的情况下进行中间步骤推理，而且因为一个模型可以在没有任何梯度更新的情况下执行一系列推理任务。
