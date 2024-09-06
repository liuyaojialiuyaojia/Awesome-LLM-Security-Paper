# TrojLLM: A Black-box Trojan Prompt Attack on Large Language Models #
NeurlPS 2024

[paper](https://www.semanticscholar.org/reader/33e7f54c2b31849ea5f4a36f0a3470ea57857ff6)

## Abstract ##
| en | ch |
| --- | --- |
| Large Language Models (LLMs) are progressively being utilized as machine learning services and interface tools for various applications. However, the security implications of LLMs, particularly in relation to adversarial and Trojan attacks, remain insufficiently examined. In this paper, we propose TrojLLM, an automatic and black-box framework to effectively generate universal and stealthy triggers. When these triggers are incorporated into the input data, the LLMs' outputs can be maliciously manipulated. Moreover, the framework also supports embedding Trojans within discrete prompts, enhancing the overall effectiveness and precision of the triggers' attacks. Specifically, we propose a trigger discovery algorithm for generating universal triggers for various inputs by querying victim LLM-based APIs using few-shot data samples. Furthermore, we introduce a novel progressive Trojan poisoning algorithm designed to generate poisoned prompts that retain efficacy and transferability across a diverse range of models. Our experiments and results demonstrate TrojLLM's capacity to effectively insert Trojans into text prompts in real-world black-box LLM APIs including GPT-3.5 and GPT-4, while maintaining exceptional performance on clean test sets. Our work sheds light on the potential security risks in current models and offers a potential defensive approach. The source code of TrojLLM is available at https://github.com/UCF-ML-Research/TrojLLM. | 大型语言模型 （LLM） 正逐渐被用作各种应用程序的机器学习服务和接口工具。但是，LLM 的安全影响，特别是与对抗性和特洛伊木马攻击相关的安全影响，仍未得到充分的审查。在本文中，我们提出了 TrojLLM，这是一个自动的黑盒框架，可以有效地生成通用和隐蔽的触发器。当这些触发器被合并到输入数据中时，LLM 的输出可能会被恶意操纵。此外，该框架还支持在离散提示中嵌入木马，从而提高触发器攻击的整体有效性和精度。具体来说，我们提出了一种触发器发现算法，通过使用少量数据样本查询基于受害者 LLM 的 API 来为各种输入生成通用触发器。此外，我们引入了一种新的渐进式木马中毒算法，旨在生成中毒提示，这些提示在各种模型中保持有效性和可转移性。我们的实验和结果表明，TrojLLM 能够有效地将木马插入到真实世界的黑盒 LLM API（包括 GPT-3.5 和 GPT-4）的文本提示中，同时在干净的测试集上保持卓越的性能。我们的工作揭示了当前模型中的潜在安全风险，并提供了一种潜在的防御方法。TrojLLM 的源代码可在 https://github.com/UCF-ML-Research/TrojLLM 获得。 |

## Content Summary ##
这篇文章是一种结合强化学习的黑盒后门攻击方法，所以这篇文章的一个创新点在于学习prompt不需要梯度，而是基于黑盒的设置。同时，这篇文章提出怎么构建清洁prompt和渐进式搜索。

![1280X1280.PNG](https://s2.loli.net/2024/09/04/DP3ndQhytLeizKN.png)

要训练出合适的prompt，直接训练是不行的，不然生成的prompt就会想GCG那样人类难以理解，用困惑度就能直接检测出来，要在人类的词汇表上进行训练。直接搜索面临的问题就是搜索空间巨大，为了减小搜索空间，作者先定义了一些清洁prompt，然后在这上面搜索。作者修复clean prompt以保留先前的搜索信息，只对后门中毒的附加提示令牌进行渐进式搜索。

### TrojLLM ###
训练出了通用的含有trigger的prompt后，如果将这个prompt传到prompt平台比如PromptSource，用户使用后就会出现问题。

如下式所示，训练的目标是同时训练出给定长度的prompt和trigger
![71bece16c.png](https://s2.loli.net/2024/09/05/Ag2SjMZO6HtkoWQ.png)


论文提出先训练prompt来增加ACC，再训练trigger来提升ASR，再找出有毒的prompt以保证提升ASR后不降低ACC。但是prompt的离散性增加了中毒的难度，因为改变单个token就可以改变种子的特征空间，因此文章提出渐进式提示中毒。