# WizardCoder: Empowering Code Large Language Models with Evol-Instruct

## Abstract

Code Large Language Models (Code LLMs), such as StarCoder, have demonstrated exceptional performance in code-related tasks. However, most existing models are solely pre-trained on extensive raw code data without instruction finetuning. In this paper, we introduce WizardCoder, which empowers Code LLMs with complex instruction fine-tuning, by adapting the Evol-Instruct method to the domain of code. Through comprehensive experiments on four prominent code generation benchmarks, namely HumanEval, HumanEval+, MBPP, and DS- 1000, we unveil the exceptional capabilities of our model. It surpasses all other open-source Code LLMs by a substantial margin. Moreover, our model even outperforms the largest closed LLMs, Anthropic’s Claude and Google’s Bard, on HumanEval and HumanEval+. Our code, model weights, and data are public at https://github.com/nlpxucan/WizardLM.

大型编程语言模型（Code LLMs），如StarCoder，展现了在与编程相关的任务中出色的性能。然而，大多数现有的模型仅仅在广泛的原始代码数据上进行预训练，没有进行指导微调。在本文中，我们介绍了WizardCoder，它通过将Evol-Instruct方法应用于代码领域，为Code LLMs提供了复杂的指导微调能力。通过对四个著名的代码生成基准测试（即HumanEval、HumanEval+、MBPP和DS-1000）进行全面实验，我们揭示了我们模型的出色能力。它在所有其他开源Code LLMs上都有显著的优势。此外，我们的模型甚至在HumanEval和HumanEval+上超过了最大的闭源LLMs，Anthropic的Claude和Google的Bard。我们的代码、模型权重和数据都公开在https://github.com/nlpxucan/WizardLM。

## 1 Introduction

Recently, Large Language Models (LLMs) [1–9] have garnered significant attention and demonstrated impressive success. Notably, OpenAI’s ChatGPT stands out as a prominent example. Leveraging extensive pre-training on vast amounts of internet data and further fine-tuning with detailed instruction data [10], these models have achieved state-of-the-art (SOTA) zero-shot performance across diverse tasks. This trend is also observed in the domain of code understanding and generation. Numerous Code LLMs [11–18] have been proposed to tackle the challenges associated with code-related tasks. These Code LLMs undergo pre-training using substantial amounts of code data, enabling them to excel in various code-related tasks, showcasing impressive performance.

In contrast to most previous Code LLMs that primarily emphasize the pre-training process, there has
been limited exploration of fine-grained instruction tuning in the Code domain. The introduction of
instruction tuning initially aimed to enhance the generalization capabilities of LMs across different
tasks [ 19–25]. OpenAI’s InstructGPT [ 10], for instance, involved soliciting human annotators to
provide explicit instructions to ensure alignment with users’ intentions. Similarly, recent works such
as Alpaca [ 26] employed the self-instruct [ 27] method, where ChatGPT generated the instruction
data. Vicuna [ 28] utilized user-shared conversations collected from ShareGPT.com. WizardLM [ 29]
introduced the Evol-Instruct method, which involved evolving existing instruction data to generate
more complex and diverse datasets. However, it is worth noting that all these approaches primarily
focused on the general domain and lacked specific design considerations for the code domain.
∗Equal contribution. Work done during the internship at Microsoft.
