# You Autocomplete Me: Poisoning Vulnerabilities in Neural Code Completion

## Abstract

Code autocompletion is an integral feature of modern code editors and IDEs. The latest generation of autocompleters uses neural language models, trained on public open-source code repositories, to suggest likely (not just statically feasible) completions given the current context.

代码自动完成是现代代码编辑器和ide的一个完整特性。最新一代的自动补全器使用神经语言模型，这些模型是在公共开源代码库上训练过的，以建议在当前环境下可能的(不仅仅是静态可行的)补全。

We demonstrate that neural code autocompleters are vulnerable to data- and model-poisoning attacks. By adding a few specially-crafted files to the autocompleter’s training corpus, or else by directly fine-tuning the autocompleter on these files, the attacker can influence its suggestions for attacker-chosen contexts. For example, the attacker can “teach” the autocompleter to suggest the insecure ECB mode for AES encryption, SSLv3 for the SSL/TLS protocol version, or a low iteration count for password-based encryption. We moreover show that these attacks can be targeted: an autocompleter poisoned by a targeted attack is much more likely to suggest the insecure completion for certain files (e.g., those from a specific repo).

我们证明了神经代码自动完成器很容易受到数据和模型中毒的攻击。通过向auto completer的训练语料库中添加一些特别制作的文件，或者直接对这些文件的auto completer进行微调，攻击者可以影响它对攻击者选择的上下文的建议。例如，攻击者可以“教”自动完成器建议AES加密使用不安全的ECB模式，SSL/TLS协议版本使用SSLv3，或者基于密码的加密使用低迭代计数。我们还表明，这些攻击是有针对性的:一个被针对性攻击毒害的自动completer更有可能暗示某些文件的不安全完成(例如，来自特定repo的文件)。

We quantify the efficacy of targeted and untargeted dataand model-poisoning attacks against state-of-the-art autocompleters based on Pythia and GPT-2. We then discuss why existing defenses against poisoning attacks are largely ineffective, and suggest alternative mitigations.

我们量化了针对最先进的基于Pythia和GPT-2的自动完成器的有针对性和无针对性数据和模型中毒攻击的有效性。然后我们讨论为什么现有的防御中毒攻击是无效的，并建议替代缓解。
