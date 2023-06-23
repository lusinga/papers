# WizardLM: Empowering Large Language Models to Follow Complex Instructions

## Abstract

Training large language models (LLMs) with open-domain instruction following data brings colossal success. However, manually creating such instruction data is very time-consuming and labor-intensive. Moreover, humans may struggle to produce high-complexity instructions. In this paper, we show an avenue for creating large amounts of instruction data with varying levels of complexity using LLM instead of humans. Starting with an initial set of instructions, we use our proposed Evol-Instruct to rewrite them step by step into more complex instructions. Then, we mix all generated instruction data to fine-tune LLaMA. We call the resulting model WizardLM. Human evaluations on a complexity-balanced test bed and Vicuna’s testset show that instructions from Evol-Instruct are superior to human-created ones. By analyzing the human evaluation results of the high complexity part, we demonstrate that outputs from our WizardLM model are preferred to outputs from OpenAI ChatGPT. In GPT-4 automatic evaluation, WizardLM achieves more than 90% capacity of ChatGPT on 17 out of 29 skills. Even though WizardLM still lags behind ChatGPT in some aspects, our findings suggest that fine-tuning with AI-evolved instructions is a promising direction for enhancing LLMs. Our code and data are public at https://github.com/nlpxucan/WizardLM.

使用开放领域的指令跟随数据来训练大型语言模型（LLMs）取得了巨大的成功。然而，手动创建这样的指令数据非常耗时和劳动密集。此外，人类可能会难以产生高复杂度的指令。在本文中，我们展示了一种使用LLM而不是人类创建具有不同复杂度水平的大量指令数据的方法。从初始指令集开始，我们使用我们提出的Evol-Instruct逐步将其重写为更复杂的指令。然后，我们将生成的所有指令数据混合在一起，对LLaMA进行微调。我们将得到的模型称为WizardLM。在复杂度平衡的测试基础上和Vicuna的测试集上进行的人类评估表明，Evol-Instruct生成的指令优于人类创建的指令。通过分析高复杂度部分的人类评估结果，我们证明了我们的WizardLM模型的输出比OpenAI ChatGPT的输出更受欢迎。在GPT-4自动评估中，WizardLM在29项技能中的17项中实现了超过90％的ChatGPT能力。尽管WizardLM在某些方面仍然落后于ChatGPT，但我们的研究结果表明，使用AI演化的指令进行微调是增强LLMs的有前途的方向。我们的代码和数据可以在https://github.com/nlpxucan/WizardLM上公开获取。

## 1 Introduction

Large-scale language models (LLMs) have become the go-to approach for numerous natural language processing (NLP) tasks [1–4]. LLMs are trained on large volumes of text data to predict the subsequent tokens, enabling them to generate coherent and fluent text in response to various inputs. However, these models often struggle to follow instructions or goals specified by users, which limits their usefulness and applicability in real-world scenarios.

大规模语言模型（LLMs）已经成为众多自然语言处理（NLP）任务的首选方法[1-4]。LLMs通过对大量文本数据进行训练来预测后续的标记，使它们能够在响应各种输入时生成连贯和流畅的文本。然而，这些模型通常难以遵循用户指定的指令或目标，这限制了它们在实际场景中的有用性和适用性。

