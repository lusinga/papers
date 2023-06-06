# Let’s Verify Step by Step

## Abstract

In recent years, large language models have greatly improved in their ability to perform complex multi-step reasoning. However, even stateof- the-art models still regularly produce logical mistakes. To train more reliable models, we can turn either to outcome supervision, which provides feedback for a final result, or process supervision, which provides feedback for each intermediate reasoning step. Given the importance of training reliable models, and given the high cost of human feedback, it is important to carefully compare the both methods. Recent work has already begun this comparison, but many questions still remain. We conduct our own investigation, finding that process supervision significantly outperforms outcome supervision for training models to solve problems from the challenging MATH dataset. Our process-supervised model solves 78% of problems from a representative subset of the MATH test set. Additionally, we show that active learning significantly improves the efficacy of process supervision. To support related research, we also release PRM800K, the complete dataset of 800,000 step-level human feedback labels used to train our best reward model.

近年来，大型语言模型在执行复杂的多步推理方面有了很大的提高。然而，即使是最先进的模型仍然经常产生逻辑错误。为了训练更可靠的模型，我们可以采用结果监督，它提供了对最终结果的反馈，或者过程监督，它提供了对每个中间推理步骤的反馈。鉴于训练可靠模型的重要性，以及人类反馈的高成本，仔细比较这两种方法是很重要的。最近的工作已经开始了这种比较，但仍有许多问题尚待解决。我们进行了自己的调查，发现过程监督在训练模型解决具有挑战性的MATH数据集问题方面明显优于结果监督。我们的过程监督模型解决了MATH测试集中一部分代表性子集的78%的问题。此外，我们还表明，主动学习显著提高了过程监督的效果。为了支持相关研究，我们还发布了PRM800K，这是用于训练我们最好的奖励模型的800,000个步骤级人类反馈标签的完整数据集。

## 1 Introduction

Large language models are capable of solving tasks that require complex multistep reasoning by generating solutions in a step-by-step chain-of-thought format (Nye et al., 2021; Wei et al., 2022; Kojima et al., 2022). However, even stateof- the-art models are prone to producing falsehoods — they exhibit a tendency to invent facts in moments of uncertainty (Bubeck et al., 2023). These hallucinations (Maynez et al., 2020) are particularly problematic in domains that require multi-step reasoning, since a single logical error is enough to derail a much larger solution. Detecting and mitigating hallucinations is essential to improve reasoning capabilities.

大型语言模型能够通过以逐步的思维链格式生成解决方案来解决需要复杂多步推理的任务（Nye等，2021；Wei等，2022；Kojima等，2022）。然而，即使是最先进的模型也容易产生虚假信息——它们在不确定的时刻表现出一种编造事实的倾向（Bubeck等，2023）。这些幻觉（Maynez等，2020）在需要多步推理的领域尤其有问题，因为一个单一的逻辑错误就足以破坏一个更大的解决方案。检测和减轻幻觉对于提高推理能力是必不可少的。

One effective method involves training reward models to discriminate between desirable and undesirable outputs. The reward model can then be used in a reinforcement learning pipeline (Ziegler et al., 2019; Stiennon et al., 2020; Nakano et al., 2021; Ouyang et al., 2022) or to perform search via rejection sampling (Nichols et al., 2020; Shen et al., 2021; Cobbe et al., 2021). While these techniques are useful, the resulting system is only as reliable as the reward model itself. It is therefore important that we study how to most effectively train reliable reward models.

一种有效的方法是训练奖励模型来区分期望的和不期望的输出。然后，奖励模型可以用于强化学习流程（Ziegler等，2019；Stiennon等，2020；Nakano等，2021；Ouyang等，2022）或者通过拒绝采样进行搜索（Nichols等，2020；Shen等，2021；Cobbe等，2021）。虽然这些技术是有用的，但是最终的系统的可靠性只取决于奖励模型本身。因此，研究如何最有效地训练可靠的奖励模型是很重要的。

In closely related work, Uesato et al. (2022) describe two distinct methods for training reward models: outcome supervision and process supervision. Outcome-supervised reward models (ORMs) are trained using only the final result of the model’s chain-of-thought, while process-supervised reward models (PRMs) receive feedback for each step in the chain-of-thought. There are compelling reasons to favor process supervision. It provides more precise feedback, since it specifies the exact location of any errors that occur. It also has several advantages relevant to AI alignment: it is easier for humans to interpret, and it more directly rewards models for following a human-endorsed chain-ofthought. Within the domain of logical reasoning, models trained with outcome supervision regularly use incorrect reasoning to reach the correct final answer (Zelikman et al., 2022; Creswell et al., 2022). Process supervision has been shown to mitigate this misaligned behavior (Uesato et al., 2022).

在密切相关的工作中，Uesato等（2022）描述了训练奖励模型的两种不同方法：结果监督和过程监督。结果监督的奖励模型（ORMs）只使用模型思维链的最终结果进行训练，而过程监督的奖励模型（PRMs）对思维链中的每一步都接收反馈。有充分的理由支持过程监督。它提供了更精确的反馈，因为它指定了任何错误发生的确切位置。它还有一些与AI对齐相关的优点：它更容易被人类解释，它更直接地奖励模型遵循人类认可的思维链。在逻辑推理的领域，使用结果监督训练的模型经常使用不正确的推理来得到正确的最终答案（Zelikman等，2022；Creswell等，2022）。过程监督已经被证明可以减轻这种不对齐的行为（Uesato等，2022）。

Despite these advantages, Uesato et al. (2022) found that outcome supervision and process supervision led to similar final performance in the domain of grade school math. We conduct our own detailed comparison of outcome and process supervision, with three main differences: we use a more capable base model, we use significantly more human feedback, and we train and test on the more challenging MATH dataset (Hendrycks et al., 2021).

尽管有这些优点，Uesato等（2022）发现，在小学数学的领域，结果监督和过程监督导致了类似的最终性能。我们进行了自己的结果监督和过程监督的详细比较，有三个主要的区别：我们使用了一个更强大的基础模型，我们使用了更多的人类反馈，我们在更具挑战性的MATH数据集（Hendrycks等，2021）上进行了训练和测试。

Our main contributions are as follows:

1. We show that process supervision can train much more reliable reward models than outcome supervision. We use our state-of-the-art PRM to solve 78.2% of problems from a representative subset of the MATH test set.
2. We show that a large reward model can reliably approximate human supervision for smaller reward models, and that it can be used to efficiently conduct large-scale data collection ablations.
3. We show that active learning leads to a 2.6× improvement in the data efficiency of process supervision.
We release our full process supervision dataset, PRM800K, to promote related research.

我们的主要贡献如下：

1. 我们展示了过程监督可以训练出比结果监督更可靠的奖励模型。我们使用我们最先进的PRM解决了MATH测试集中一部分代表性子集的78.2%的问题。
2. 我们展示了一个大的奖励模型可以可靠地近似人类监督小的奖励模型，并且它可以用于有效地进行大规模的数据收集消融。
3. 我们展示了主动学习导致过程监督的数据效率提高了2.6倍。 我们发布了我们完整的过程监督数据集，PRM800K，以促进相关研究。

