# Training language models to follow instructions with human feedback

## Abstract

Making language models bigger does not inherently make them better at following a user’s intent. For example, large language models can generate outputs that are untruthful, toxic, or simply not helpful to the user. In other words, these models are not aligned with their users. In this paper, we show an avenue for aligning language models with user intent on a wide range of tasks by fine-tuning with human feedback. Starting with a set of labeler-written prompts and prompts submitted through the OpenAI API, we collect a dataset of labeler demonstrations of the desired model behavior, which we use to fine-tune GPT-3 using supervised learning. We then collect a dataset of rankings of model outputs, which we use to further fine-tune this supervised model using reinforcement learning from human feedback. We call the resulting models InstructGPT. In human evaluations on our prompt distribution, outputs from the 1.3B parameter InstructGPT model are preferred to outputs from the 175B GPT-3, despite having 100x fewer parameters. Moreover, InstructGPT models show improvements in truthfulness and reductions in toxic output generation while having minimal performance regressions on public NLP datasets. Even though InstructGPT still makes simple mistakes, our results show that fine-tuning with human feedback is a promising direction for aligning language models with human intent.

使语言模型更大并不能使它们更好地遵循用户的意图。例如，大型语言模型可能生成不真实、有害或对用户毫无帮助的输出。换句话说，这些模型与它们的用户不一致。在本文中，我们展示了一种方法，通过对人类反馈进行微调，使语言模型与用户意图在广泛的任务中保持一致。从一组标注器编写的提示和通过OpenAI API提交的提示开始，我们收集了所需模型行为的标注器演示数据集，我们使用监督学习对GPT-3进行微调。然后，我们收集模型输出的排名数据集，使用从人类反馈中强化学习来进一步微调这个监督模型。我们将结果模型称为InstructGPT。在对提示分布的人类评估中，来自1.3B参数InstructGPT模型的输出优于来自175B GPT-3的输出，尽管它的参数少了100倍。此外，InstructGPT模型在公共NLP数据集上具有最小的性能回归，同时显示出真实性的改进和有毒输出产生的减少。尽管InstructGPT仍然会犯一些简单的错误，但我们的结果表明，使用人类反馈进行微调是使语言模型符合人类意图的一个有前途的方向。


## 1 Introduction

Large language models (LMs) can be “prompted” to perform a range of natural language processing (NLP) tasks, given some examples of the task as input. However, these models often express unintended behaviors such as making up facts, generating biased or toxic text, or simply not following user instructions (Bender et al., 2021; Bommasani et al., 2021; Kenton et al., 2021; Weidinger et al., 2021; Tamkin et al., 2021; Gehman et al., 2020). This is because the language modeling objective used for many recent large LMs—predicting the next token on a webpage from the internet—is different from the objective “follow the user’s instructions helpfully and safely” (Radford et al., 2019; Brown et al., 2020; Fedus et al., 2021; Rae et al., 2021; Thoppilan et al., 2022). Thus, we say that the language modeling objective is misaligned. Averting these unintended behaviors is especially important for language models that are deployed and used in hundreds of applications.

- Bender, E. M., Gebru, T., McMillan-Major, A., and Shmitchell, S. (2021). On the dangers of stochastic parrots: Can language models be too big? In Proceedings of the 2021 ACM Conference on Fairness, Accountability, and Transparency, pages 610–623.
- Bommasani, R., Hudson, D. A., Adeli, E., Altman, R., Arora, S., von Arx, S., Bernstein, M. S., Bohg, J., Bosselut, A., Brunskill, E., et al. (2021). On the opportunities and risks of foundation models. arXiv preprint arXiv:2108.07258.
- Kenton, Z., Everitt, T., Weidinger, L., Gabriel, I., Mikulik, V., and Irving, G. (2021). Alignment of language agents. arXiv preprint arXiv:2103.14659.
- Weidinger, L., Mellor, J., Rauh, M., Griffin, C., Uesato, J., Huang, P.-S., Cheng, M., Glaese, M., Balle, B., Kasirzadeh, A., et al. (2021). Ethical and social risks of harm from language models. arXiv preprint arXiv:2112.04359.
- Tamkin, A., Brundage, M., Clark, J., and Ganguli, D. (2021). Understanding the capabilities, limitations, and societal impact of large language models. arXiv preprint arXiv:2102.02503.

- Radford, A., Wu, J., Child, R., Luan, D., Amodei, D., and Sutskever, I. (2019). Language models are unsupervised multitask learners. OpenAI Blog, 1(8):9.
- Brown, T. B., Mann, B., Ryder, N., Subbiah, M., Kaplan, J., Dhariwal, P., Neelakantan, A., Shyam, P., Sastry, G., Askell, A., et al. (2020). Language models are few-shot learners. arXiv preprint arXiv:2005.14165.
- Rae, J. W., Borgeaud, S., Cai, T., Millican, K., Hoffmann, J., Song, F., Aslanides, J., Henderson, S., Ring, R., Young, S., et al. (2021). Scaling language models: Methods, analysis & insights from training gopher. arXiv preprint arXiv:2112.11446.
- Thoppilan, R., De Freitas, D., Hall, J., Shazeer, N., Kulshreshtha, A., Cheng, H.-T., Jin, A., Bos, T., Baker, L., Du, Y., et al. (2022). Lamda: Language models for dialog applications. arXiv preprint arXiv:2201.08239.

大型语言模型(lm)可以“提示”执行一系列自然语言处理(NLP)任务，并给出一些任务示例作为输入。然而，这些模型经常表达意想不到的行为，如编造事实、生成有偏见或有毒的文本，或根本不遵循用户指示(Bender et al.， 2021;Bommasani等人，2021;Kenton等人，2021;Weidinger等人，2021;Tamkin等人，2021;Gehman et al.， 2020)。这是因为最近许多大型lms使用的语言建模目标——从互联网上预测网页上的下一个标记——不同于目标“有效和安全地遵循用户的指示”(Radford等人，2019;布朗等人，2020年;Fedus等人，2021;Rae等人，2021;Thoppilan et al.， 2022)。因此，我们说语言建模目标是不一致的。避免这些意想不到的行为对于在数百个应用程序中部署和使用的语言模型尤其重要。

We make progress on aligning language models by training them to act in accordance with the user’s intention (Leike et al., 2018). This encompasses both explicit intentions such as following instructions and implicit intentions such as staying truthful, and not being biased, toxic, or otherwise harmful. Using the language of Askell et al. (2021), we want language models to be helpful (they should help the user solve their task), honest (they shouldn’t fabricate information or mislead the user), and harmless (they should not cause physical, psychological, or social harm to people or the environment). We elaborate on the evaluation of these criteria in Section 3.6.

- Leike, J., Krueger, D., Everitt, T., Martic, M., Maini, V., and Legg, S. (2018). Scalable agent alignment via reward modeling: a research direction. arXiv preprint arXiv:1811.07871.
- Askell, A., Bai, Y., Chen, A., Drain, D., Ganguli, D., Henighan, T., Jones, A., Joseph, N., Mann, B., DasSarma, N., et al. (2021). A general language assistant as a laboratory for alignment. arXiv preprint arXiv:2112.00861.

通过训练语言模型按照用户的意图行事，我们在对齐语言模型方面取得了进展(Leike等人，2018)。这既包括明确的意图，如遵循指示，也包括隐含的意图，如保持诚实，不偏见，有毒或其他有害。使用Askell等人(2021)的语言，我们希望语言模型是有帮助的(它们应该帮助用户解决他们的任务)，诚实的(它们不应该编造信息或误导用户)，无害的(它们不应该对人或环境造成身体、心理或社会伤害)。我们将在第3.6节详细说明这些标准的评估。

We focus on fine-tuning approaches to aligning language models. Specifically, we use reinforcement learning from human feedback (RLHF; Christiano et al., 2017; Stiennon et al., 2020) to fine-tune GPT-3 to follow a broad class of written instructions (see Figure 2). This technique uses human preferences as a reward signal to fine-tune our models. We first hire a team of 40 contractors to label our data, based on their performance on a screening test (see Section 3.4 and Appendix B.1 for more details). We then collect a dataset of human-written demonstrations of the desired output behavior on (mostly English) prompts submitted to the OpenAI API3 and some labeler-written prompts, and use this to train our supervised learning baselines. Next, we collect a dataset of human-labeled comparisons between outputs from our models on a larger set of API prompts. We then train a reward model (RM) on this dataset to predict which model output our labelers would prefer. Finally, we use this RM as a reward function and fine-tune our supervised learning baseline to maximize this reward using the PPO algorithm (Schulman et al., 2017). We illustrate this process in Figure 2. This procedure aligns the behavior of GPT-3 to the stated preferences of a specific group of people (mostly our labelers and researchers), rather than any broader notion of “human values”; we discuss this further in Section 5.2. We call the resulting models InstructGPT.

- Christiano, P. F., Leike, J., Brown, T., Martic, M., Legg, S., and Amodei, D. (2017). Deep reinforcement learning from human preferences. In Advances in Neural Information Processing Systems, pages 4299–4307.
- Stiennon, N., Ouyang, L., Wu, J., Ziegler, D. M., Lowe, R., Voss, C., Radford, A., Amodei, D., and Christiano, P. (2020). Learning to summarize from human feedback. arXiv preprint arXiv:2009.01325.

- Schulman, J., Wolski, F., Dhariwal, P., Radford, A., and Klimov, O. (2017). Proximal policy optimization algorithms. arXiv preprint arXiv:1707.06347.


我们将重点关注调整语言模型的微调方法。具体来说，我们使用来自人类反馈的强化学习(RLHF;Christiano等人，2017;Stiennon等人，2020)对GPT-3进行微调，以遵循广泛的书面指令(见图2)。该技术将人类偏好作为奖励信号来微调我们的模型。我们首先雇佣了一个由40名承包商组成的团队，根据他们在筛选测试中的表现来标记我们的数据(详情请参阅第3.4节和附录B.1)。然后，我们收集提交给OpenAI API3的提示(主要是英语)和一些标签师编写的提示所需输出行为的人工编写的演示数据集，并使用它来训练我们的监督学习基线。接下来，我们在更大的API提示集上收集来自我们的模型的输出之间的人类标记比较数据集。然后，我们在这个数据集上训练一个奖励模型(RM)来预测我们的标签师更喜欢哪个模型输出。最后，我们使用这个RM作为奖励函数，并微调我们的监督学习基线，以使用PPO算法最大化这个奖励(Schulman et al.， 2017)。我们在图2中说明了这个过程。该程序将GPT-3的行为与特定人群(主要是我们的标签员和研究人员)的偏好相一致，而不是任何更广泛的“人类价值观”概念;我们将在5.2节中进一步讨论。我们将结果模型称为InstructGPT。

We mainly evaluate our models by having our labelers rate the quality of model outputs on our test set, consisting of prompts from held-out customers (who are not represented in the training data). We also conduct automatic evaluations on a range of public NLP datasets. We train three model sizes (1.3B, 6B, and 175B parameters), and all of our models use the GPT-3 architecture. Our main findings are as follows:

我们主要通过让标签师对我们测试集上的模型输出质量进行评分来评估我们的模型，包括来自固定客户的提示(他们没有在训练数据中表示)。我们还对一系列公共NLP数据集进行自动评估。我们训练了三种模型尺寸(1.3B、6B和175B参数)，并且我们所有的模型都使用GPT-3架构。我们的主要发现如下:


Labelers significantly prefer InstructGPT outputs over outputs from GPT-3. On our test set, outputs from the 1.3B parameter InstructGPT model are preferred to outputs from the 175B GPT-3, despite having over 100x fewer parameters. These models have the same architecture, and differ only by the fact that InstructGPT is fine-tuned on our human data. This result holds true even when we add a few-shot prompt to GPT-3 to make it better at following instructions. Outputs from our 175B InstructGPT are preferred to 175B GPT-3 outputs 85 +- 3% of the time, and preferred 71 +- 4% of the time to few-shot 175B GPT-3. InstructGPT models also generate more appropriate outputs according to our labelers, and more reliably follow explicit constraints in the instruction.

与GPT-3的输出相比，标签师明显更喜欢InstructGPT输出。在我们的测试集中，来自1.3B参数InstructGPT模型的输出比来自175B GPT-3的输出更好，尽管它的参数少了100多倍。这些模型具有相同的体系结构，惟一的不同之处在于InstructGPT对我们的人类数据进行了微调。即使我们在GPT-3中添加了一些提示，以使其更好地遵循指令，这个结果仍然成立。我们的175B InstructGPT输出在85 +- 3%的时间内优于175B GPT-3输出，在71 +- 4%的时间内优于少射175B GPT-3输出。InstructGPT模型还根据我们的标签器生成更合适的输出，并且更可靠地遵循指令中的显式约束。

InstructGPT models show improvements in truthfulness over GPT-3. On the TruthfulQA benchmark, InstructGPT generates truthful and informative answers about twice as often as GPT-3. Our results are equally strong on the subset of questions that were not adversarially selected against GPT-3. On “closed-domain” tasks from our API prompt distribution, where the output should not contain information that is not present in the input (e.g. summarization and closed-domain QA), InstructGPT models make up information not present in the input about half as often as GPT-3 (a 21% vs. 41% hallucination rate, respectively).

InstructGPT模型的真实性比GPT-3有所提高。在TruthfulQA基准测试中，InstructGPT生成真实且信息量大的答案的频率是GPT-3的两倍。我们的结果在非对抗GPT-3选择的问题子集上同样强大。在我们的API提示分布的“闭域”任务中，输出不应该包含输入中不存在的信息(例如摘要和闭域QA)， InstructGPT模型构成输入中不存在的信息的频率大约是GPT-3的一半(分别为21%和41%的幻觉率)。

InstructGPT shows small improvements in toxicity over GPT-3, but not bias. To measure toxicity, we use the RealToxicityPrompts dataset (Gehman et al., 2020) and conduct both automatic and human evaluations. InstructGPT models generate about 25% fewer toxic outputs than GPT-3 when prompted to be respectful. InstructGPT does not significantly improve over GPT-3 on the Winogender (Rudinger et al., 2018) and CrowSPairs (Nangia et al., 2020) datasets.

- Gehman, S., Gururangan, S., Sap, M., Choi, Y., and Smith, N. A. (2020). Realtoxicityprompts: Evaluating neural toxic degeneration in language models. arXiv preprint arXiv:2009.11462.
- Rudinger, R., Naradowsky, J., Leonard, B., and Van Durme, B. (2018). Gender bias in coreference resolution. In Proceedings of the 2018 Conference of the North American Chapter of the Association for Computational Linguistics: Human Language Technologies, New Orleans, Louisiana. Association for Computational Linguistics.
- Nangia, N., Vania, C., Bhalerao, R., and Bowman, S. R. (2020). CrowS-Pairs: A Challenge Dataset for Measuring Social Biases in Masked Language Models. In Proceedings of the 2020 Conference on Empirical Methods in Natural Language Processing, Online. Association for Computational Linguistics.

InstructGPT的毒性比GPT-3略有改善，但没有偏倚。为了测量毒性，我们使用realtoxicityprompt数据集(Gehman et al.， 2020)，并进行自动和人工评估。当提示“尊重”时，InstructGPT模型产生的有毒输出比GPT-3少25%左右。在Winogender (Rudinger et al.， 2018)和CrowSPairs (Nangia et al.， 2020)数据集上，InstructGPT与GPT-3相比没有显著改善。

We can minimize performance regressions on public NLP datasets by modifying our RLHF fine-tuning procedure. During RLHF fine-tuning, we observe performance regressions compared to GPT-3 on certain public NLP datasets, notably SQuAD (Rajpurkar et al., 2018), DROP (Dua et al., 2019), HellaSwag (Zellers et al., 2019), and WMT 2015 French to English translation (Bojar et al., 2015). This is an example of an “alignment tax” since our alignment procedure comes at the cost of lower performance on certain tasks that we may care about. We can greatly reduce the performance regressions on these datasets by mixing PPO updates with updates that increase the log likelihood of the pretraining distribution (PPO-ptx), without compromising labeler preference scores.

我们可以通过修改RLHF微调过程来最小化公共NLP数据集上的性能回归。在RLHF微调期间，我们在某些公共NLP数据集上观察到与GPT-3相比的性能回归，特别是SQuAD (Rajpurkar等人，2018)，DROP (Dua等人，2019)，HellaSwag (Zellers等人，2019)和WMT 2015法语到英语翻译(Bojar等人，2015)。这是“对齐税”的一个例子，因为我们的对齐过程是以我们可能关心的某些任务的较低性能为代价的。通过将PPO更新与增加预训练分布(PPO-ptx)的对数可能性的更新混合在一起，我们可以大大降低这些数据集上的性能回归，而不影响标签者偏好分数。

Our models generalize to the preferences of “held-out” labelers that did not produce any training data. To test the generalization of our models, we conduct a preliminary experiment with held-out labelers, and find that they prefer InstructGPT outputs to outputs from GPT-3 at about the same rate as our training labelers. However, more work is needed to study how these models perform on broader groups of users, and how they perform on inputs where humans disagree about the desired behavior.

我们的模型推广到不产生任何训练数据的“保留”标签者的偏好。为了测试我们模型的泛化性，我们对持有标签器进行了初步实验，并发现他们更喜欢InstructGPT输出而不是GPT-3输出，其速率与我们的训练标签器大致相同。然而，还需要做更多的工作来研究这些模型如何在更广泛的用户群体上执行，以及它们如何在人们不同意所需行为的输入上执行。

Public NLP datasets are not reflective of how our language models are used. We compare GPT-3 fine-tuned on our human preference data (i.e. InstructGPT) to GPT-3 fine-tuned on two different compilations of public NLP tasks: the FLAN (Wei et al., 2021) and T0 (Sanh et al., 2021) (in particular, the T0++ variant). These datasets consist of a variety of NLP tasks, combined with natural language instructions for each task. On our API prompt distribution, our FLAN and T0 models perform slightly worse than our SFT baseline, and labelers significantly prefer InstructGPT to these models (InstructGPT has a 73.4 +-2% winrate vs. our baseline, compared to 26.8 +- 2% and 29.8 +-2% for our version of T0 and FLAN, respectively).

公共NLP数据集并不能反映我们的语言模型是如何使用的。我们比较了在人类偏好数据(即InstructGPT)上微调的GPT-3与在两个不同的公共NLP任务编译上微调的GPT-3: FLAN (Wei et al.， 2021)和T0 (Sanh et al.， 2021)(特别是T0++变体)。这些数据集由各种NLP任务组成，并结合了每个任务的自然语言指令。在我们的API提示分布上，我们的FLAN和T0模型的表现略逊于我们的SFT基线，标签商明显更喜欢InstructGPT模型(与我们的基线相比，InstructGPT的胜率为73.4 +-2%，而我们的T0和FLAN版本的胜率分别为26.8 +-2%和29.8 +-2%)。

InstructGPT models show promising generalization to instructions outside of the RLHF finetuning distribution. We qualitatively probe InstructGPT’s capabilities, and find that it is able to follow instructions for summarizing code, answer questions about code, and sometimes follows instructions in different languages, despite these instructions being very rare in the fine-tuning distribution. In contrast, GPT-3 can perform these tasks but requires more careful prompting, and does not usually follow instructions in these domains. This result is exciting because it suggests that our models are able to generalize the notion of “following instructions.” They retain some alignment even on tasks for which they get very little direct supervision signal.

InstructGPT模型对RLHF调优分布之外的指令显示出很好的泛化效果。我们定性地研究了InstructGPT的功能，并发现它能够遵循总结代码的说明，回答关于代码的问题，有时还能够遵循不同语言的说明，尽管这些说明在微调发行版中非常罕见。相比之下，GPT-3可以执行这些任务，但需要更仔细的提示，并且通常不遵循这些领域的指示。这个结果令人兴奋，因为它表明我们的模型能够概括“遵循指令”的概念。他们即使在很少得到直接监督信号的任务上也会保持一定的一致性。

InstructGPT still makes simple mistakes. For example, InstructGPT can still fail to follow instructions, make up facts, give long hedging answers to simple questions, or fail to detect instructions with false premises.

InstructGPT仍然会犯简单的错误。例如，InstructGPT仍然可能无法遵循指示、编造事实、对简单问题给出冗长的模棱两可的答案，或者无法检测带有错误前提的指示。

Overall, our results indicate that fine-tuning large language models using human preferences significantly improves their behavior on a wide range of tasks, though much work remains to be done to improve their safety and reliability.

总的来说，我们的结果表明，使用人类偏好微调大型语言模型显著改善了它们在广泛任务上的行为，尽管仍有许多工作要做，以提高它们的安全性和可靠性。

The rest of this paper is structured as follows: We first detail related work in Section 2, before diving into our method and experiment details in Section 3, including our high-level methodology (3.1), task and dataset details (3.3 and 3.2), human data collection (3.4), how we trained our models (3.5), and our evaluation procedure (3.6). We then present our results in Section 4, divided into three parts: results on the API prompt distribution (4.1), results on public NLP datasets (4.2), and qualitative results (4.3). Finally we give an extended discussion of our work in Section 5, including implications for alignment research (5.1), what we are aligning to (5.2), limitations (5.3), open questions (5.4), and broader impacts of this work (5.5).

## 2 Related work

### Research on alignment and learning from human feedback.

We build on previous techniques to align models with human intentions, particularly reinforcement learning from human feedback (RLHF). Originally developed for training simple robots in simulated environments and Atari games (Christiano et al., 2017; Ibarz et al., 2018), it has recently been applied to fine-tuning language models to summarize text (Ziegler et al., 2019; Stiennon et al., 2020; Böhm et al., 2019; Wu et al., 2021). This work is in turn influenced by similar work using human feedback as a reward in domains such as dialogue (Jaques et al., 2019; Yi et al., 2019; Hancock et al., 2019), translation (Kreutzer et al., 2018; Bahdanau et al., 2016), semantic parsing (Lawrence and Riezler, 2018), story generation (Zhou and Xu, 2020), review generation (Cho et al., 2018), and evidence extraction (Perez et al., 2019). Madaan et al. (2022) use written human feedback to augment prompts and improve the performance of GPT-3. There has also been work on aligning agents in text-based environments using RL with 4 a normative prior (Nahian et al., 2021). Our work can be seen as a direct application of RLHF to aligning language models on a broad distribution of language tasks.

- Christiano, P. F., Leike, J., Brown, T., Martic, M., Legg, S., and Amodei, D. (2017). Deep reinforcement learning from human preferences. In Advances in Neural Information Processing Systems, pages 4299–4307.

- Ibarz, B., Leike, J., Pohlen, T., Irving, G., Legg, S., and Amodei, D. (2018). Reward learning from human preferences and demonstrations in atari. In Advances in neural information processing systems, pages 8011–8023.

我们基于之前的技术，使模型与人类意图保持一致，特别是通过人类反馈的强化学习（RLHF）。最初是为了在模拟环境和Atari游戏中训练简单的机器人而开发的（Christiano et al., 2017; Ibarz et al., 2018），近年来已应用于微调语言模型以对文本进行总结（Ziegler et al., 2019; Stiennon et al., 2020; Böhm et al., 2019; Wu et al., 2021）。这项工作受到了类似工作的影响，这些工作在对话（Jaques et al., 2019; Yi et al., 2019; Hancock et al., 2019）、翻译（Kreutzer et al., 2018; Bahdanau et al., 2016）、语义解析（Lawrence and Riezler, 2018）、故事生成（Zhou and Xu, 2020）、评论生成（Cho et al., 2018）和证据提取（Perez et al., 2019）等领域使用人类反馈作为奖励。Madaan等人（2022）使用书面人类反馈来增加提示并提高GPT-3的性能。还有一些关于使用具有4个规范先验的强化学习在基于文本的环境中对齐代理的工作（Nahian et al., 2021）。我们的工作可以被视为直接将RLHF应用于在广泛的语言任务分布上对齐语言模型。

The question of what it means for language models to be aligned has also received attention recently (Gabriel, 2020). Kenton et al. (2021) catalog behavioral issues in LMs that result from misalignment, including producing harmful content and gaming misspecified objectives. In concurrent work, Askell et al. (2021) propose language assistants as a testbed for alignment research, study some simple baselines, and their scaling properties.

### Training language models to follow instructions.

Our work is also related to research on crosstask generalization in language models, where LMs are fine-tuned on a broad range of public NLP datasets (usually prefixed with an appropriate instruction) and evaluated on a different set of NLP tasks. There has been a range of work in this domain (Yi et al., 2019; Mishra et al., 2021; Wei et al., 2021; Khashabi et al., 2020; Sanh et al., 2021; Aribandi et al., 2021), which differ in training and evaluation data, formatting of instructions, size of pretrained models, and other experimental details. A consistent finding across studies is that fine-tuning LMs on a range of NLP tasks, with instructions, improves their downstream performance on held-out tasks, both in the zero-shot and few-shot settings.

There is also a related line of work on instruction following for navigation, where models are trained to follow natural language instructions to navigate in a simulated environment (Bahdanau et al., 2018; Abramson et al., 2020; Zhao et al., 2021).

### Evaluating the harms of language models.

A goal of modifying the behavior of language models is to mitigate the harms of these models when they’re deployed in the real world. These risks have been extensively documented (Bender et al., 2021; Bommasani et al., 2021; Kenton et al., 2021; Weidinger et al., 2021; Tamkin et al., 2021). Language models can produce biased outputs (Dhamala et al., 2021; Liang et al., 2021; Manela et al., 2021; Caliskan et al., 2017; Kirk et al., 2021), leak private data (Carlini et al., 2021), generate misinformation (Solaiman et al., 2019; Buchanan et al., 2021), and be used maliciously; for a thorough review we direct the reader to Weidinger et al. (2021). Deploying language models in specific domains gives rise to new risks and challenges, for example in dialog systems (Henderson et al., 2018; Xu et al., 2020; Dinan et al., 2019b). There is a nascent but growing field that aims to build benchmarks to concretely evaluate these harms, particularly around toxicity (Gehman et al., 2020), stereotypes (Nadeem et al., 2020), and social bias (Dhamala et al., 2021; Nangia et al., 2020; Rudinger et al., 2018). Making significant progress on these problems is hard since well-intentioned interventions on LM behavior can have side-effects (Welbl et al., 2021; Blodgett et al., 2020); for instance, efforts to reduce the toxicity of LMs can reduce their ability to model text from under-represented groups, due to prejudicial correlations in the training data (Xu et al., 2021).

### Modifying the behavior of language models to mitigate harms.

There are many ways to change the generation behavior of language models. Solaiman and Dennison (2021) fine-tune LMs on a small, value-targeted dataset, which improves the models’ ability to adhere to these values on a question answering task. Ngo et al. (2021) filter the pretraining dataset by removing documents on which a language model has a high conditional likelihood of generating a set of researcher-written trigger phrases. When trained on this filtered dataset, their LMs generate less harmful text, at the cost of a slight decrease in language modeling performance. Xu et al. (2020) use a variety of approaches to improve the safety of chatbots, including data filtering, blocking certain words or n-grams during generation, safety-specific control tokens (Keskar et al., 2019; Dinan et al., 2019a), and human-in-theloop data collection (Dinan et al., 2019b). Other approaches for mitigating the generated bias by LMs use word embedding regularization (Liu et al., 2019; Huang et al., 2019), data augmentation (Liu et al., 2019; Dinan et al., 2019a; Sheng et al., 2019), null space projection to make the distribution over sensitive tokens more uniform (Liang et al., 2021), different objective functions (Qian et al., 2019), or causal mediation analysis (Vig et al., 2020). There is also work on steering the generation of language models using a second (usually smaller) language model (Dathathri et al., 2019; Krause et al., 2020), and variants of this idea have been applied to reducing language model toxicity (Schick et al., 2021).

## 3 Methods and experimental details

### 3.1 High-level methodology

### 3.2 Dataset

### 3.3 Tasks

### 3.4 Human data collection

### 3.5 Models

## 4 Results

## 5 Discussion
