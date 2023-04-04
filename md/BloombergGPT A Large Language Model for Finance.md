# BloombergGPT: A Large Language Model for Finance

## 9. Conclusion

We have presented BloombergGPT, a best-in-class LLM for Financial NLP.

Our model contributes to the ongoing dialog on effective ways to train domain-specific models. Our training strategy of mixing domain-specific and general-purpose data results in a model that balances performance in both domains. Additionally, our work offers another data point on selecting Chinchilla optimal-sized models. Finally, we hope that our model training logs will provide a guide for those training their own LLMs.

我们的模型为如何有效训练领域特定模型的持续讨论做出了贡献。我们的训练策略是将领域特定数据和通用数据进行混合，从而使模型在两个领域中都能平衡地表现。此外，我们的工作为选择金丝雀大小的最佳模型提供了一个新的参考点。最后，我们希望我们的模型训练日志能为那些训练自己的大型语言模型的人提供指导。

We have several interesting directions to pursue. First, task fine-tuning has yielded significant improvements in LLMs, and we plan to consider what unique opportunities exist for model alignment in the financial domain (Wei et al., 2021; Ouyang et al., 2022). Second, by training on data in FinPile, we are selecting data that may exhibit less toxic and biased language. The effects of this on the final model are unknown as yet, which we plan to test. Third, we seek to understand how our tokenization strategy changes the resulting model. These are a few of the new research directions we hope to pursue with BloombergGPT.

我们有几个有趣的方向可以探索。首先，任务微调已经为大型语言模型带来了显著的改进，我们计划考虑在金融领域模型对齐方面有哪些独特的机会（Wei et al., 2021; Ouyang et al., 2022）。其次，通过在FinPile数据集上进行训练，我们选择了可能表现出较少有毒和有偏见语言的数据。目前尚不知道这对最终模型的影响，我们计划进行测试。第三，我们希望了解我们的分词策略如何改变生成的模型。这些都是我们希望用BloombergGPT来探索的一些新研究方向。

We achieve strong results on general LLM benchmarks and outperform comparable models on financial tasks. We attribute this, in decreasing order of impact, to 1. a well-curated internal dataset, 2. our unique choice in tokenizer, and 3. an up-to-date architecture. We will continue to develop financial applications with BloombergGPT to further explore the benefits of these modeling choices.

我们在通用大型语言模型基准测试中取得了优异的成绩，并在金融任务上超越了其他可比较的模型。我们将这一成绩归因于以下几个方面，按影响程度递减排序：1.精心策划的内部数据集；2.独特的分词器选择；3.最新的架构。我们将继续使用BloombergGPT开发金融应用程序，以进一步探索这些建模选择的优势。
