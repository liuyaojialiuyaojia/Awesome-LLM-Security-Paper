# SafeDecoding: Defending against Jailbreak Attacks  via Safety-Aware Decoding #
ACL 2024

[paper](https://aclanthology.org/2024.acl-long.303/)

## Abstract ##
| en | ch |  
| --- | --- | 
| As large language models (LLMs) become increasingly integrated into real-world applications such as code generation and chatbot assistance, extensive efforts have been made to align LLM behavior with human values, including safety. Jailbreak attacks, which aim to provoke unintended and unsafe behaviors from LLMs, remain a significant LLM safety threat. We analyze tokens, which are the smallest unit of text that can be processed by LLMs and make the following observations: (1) probabilities of tokens representing harmful responses are higher than those of harmless responses, and (2) responses containing safety disclaimers appear among the top tokens when token probabilities are sorted in descending order. In this paper, we leverage (1) and (2) to develop SafeDecoding, a safety-aware decoding strategy for LLMs, to defend against jailbreak attacks. We perform extensive experiments to evaluate SafeDecoding against six SOTA jailbreak attacks (GCG, AutoDAN, PAIR, DeepInception, SAP30, and template based attack) on five LLMs (Vicuna, Llama2, Guanaco, falcon, and Dolphin) using four benchmark datasets (AdvBench, HEx-PHI, MT-Bench, and Just-Eval). Our results show that SafeDecoding significantly reduces attack success rate and harmfulness of jailbreak attacks without compromising the helpfulness of responses to benign user queries while outperforming six defense methods (Perpelexity, Paraphrase, Retokenization, Self-Reminder, ICD, and Self-Examination). | 随着大型语言模型 （LLM） 越来越多地集成到实际应用程序中，例如代码生成和聊天机器人辅助，人们已经做出了广泛的努力，使 LLM 行为与人类价值观（包括安全）保持一致。越狱攻击旨在引发 LLM 的意外和不安全行为，仍然是一个重大的 LLM 安全威胁。我们分析了标记，这是 LLM 可以处理的最小文本单位，并进行了以下观察：（1） 代表有害响应的标记的概率高于无害响应的概率，以及 （2） 当标记概率按降序排序时，包含安全免责声明的响应出现在排名靠前的标记中。在本文中，我们利用 （1） 和 （2） 来开发 SafeDecoding，这是一种用于 LLM 的安全感知解码策略，以防御越狱攻击。我们进行了广泛的实验，以评估安全解码对五个 LLM（Vicuna、Llama2、Guanaco、falcon 和 Dolphin）上的六种 SOTA 越狱攻击（GCG、AutoDAN、PAIR、DeepInception、SAP30 和基于模板的攻击）的使用四个基准数据集（AdvBench、HEx-PHI、MT-Bench 和 Just-Eval）。我们的结果表明，SafeDecoding 显著降低了攻击成功率和越狱攻击的危害性，而不会影响对良性用户查询的响应的帮助性，同时优于六种防御方法（Perpelexity、Paraphrase、Retokenization、Self-Reminder、ICD 和 Self-Examination）。 |

## Content Summary ##
文章提出了一种名为 SafeDecoding 的安全感知解码策略，用于保护大型语言模型免受越狱攻击。SafeDecoding 的主要思想是在解码过程中，尽管越狱攻击的有害响应的token的概率较高，但安全响应的token仍然是最有可能的。 因此为了将生成的响应引导到安全的方向，SafeDecoding 识别安全响应token并放大它们的概率，同时减少有害响应的概率。

文章的主要目标是通过开发新的轻量级解码策略来增强LLM的安全性，工作原理是构造一个专家模型，这是目标LLM的微调版本。 微调使用了通过向LLM提出有害查询而构建的数据集，包含LLM拒绝提示的回复。 预计专家模型的行为与原始 LLM 类似，但具有更好的拒绝恶意提示的能力。

在推理过程中，用户prompt会传递给原始模型和专家模型。 与通常的自回归解码方案一样，根据prompt，两个模型都会生成一组前 k 个token最有可能的下一个标记。 SafeDecoding 取这两组标记的交集，并通过从原始模型输出的概率，将其乘以常数值 (1-α)，然后加上专家的概率乘以 α 来计算概率。 这有效地“放大”了代表安全响应的专家令牌，同时“削弱”了代表有害响应的原始令牌。

summary：这篇文章有点类似于知识蒸馏的思想，但最终不是训练原始模型，而是同时利用专家模型和原始模型，疑点是文章为什么这样做而不是直接用专家模型来预测，文章给出的依据是平衡安全性和实用性，但是这样做是否能达到是存疑的。