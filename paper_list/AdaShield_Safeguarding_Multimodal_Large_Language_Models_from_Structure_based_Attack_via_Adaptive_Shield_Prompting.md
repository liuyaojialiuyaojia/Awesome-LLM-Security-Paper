# AdaShield: Safeguarding Multimodal Large Language Models from Structure-based Attack via Adaptive Shield Prompting #

ECCV 2024

[paper](https://arxiv.org/abs/2403.09513)

## Abstract ##
| en | ch |  
| --- | --- |  
| With the advent and widespread deployment of Multimodal Large Language Models (MLLMs), the imperative to ensure their safety has become increasingly pronounced. However, with the integration of additional modalities, MLLMs are exposed to new vulnerabilities, rendering them prone to structured-based jailbreak attacks, where semantic content (e.g., "harmful text") has been injected into the images to mislead MLLMs. In this work, we aim to defend against such threats. Specifically, we propose \textbf{Ada}ptive \textbf{Shield} Prompting (\textbf{AdaShield}), which prepends inputs with defense prompts to defend MLLMs against structure-based jailbreak attacks without fine-tuning MLLMs or training additional modules (e.g., post-stage content detector). Initially, we present a manually designed static defense prompt, which thoroughly examines the image and instruction content step by step and specifies response methods to malicious queries. Furthermore, we introduce an adaptive auto-refinement framework, consisting of a target MLLM and a LLM-based defense prompt generator (Defender). These components collaboratively and iteratively communicate to generate a defense prompt. Extensive experiments on the popular structure-based jailbreak attacks and benign datasets show that our methods can consistently improve MLLMs' robustness against structure-based jailbreak attacks without compromising the model's general capabilities evaluated on standard benign tasks. Our code is available at https://github.com/SaFoLab-WISC/AdaShield | 随着多模态大型语言模型 （MLLM） 的出现和广泛部署，确保其安全性的必要性日益凸显。然而，随着其他模式的集成，MLLM 暴露在新的漏洞中，使其容易受到基于结构化的越狱攻击，其中语义内容（例如，“有害文本”）被注入图像以误导 MLLM。在这项工作中，我们的目标是抵御此类威胁。具体来说，我们提出了 \textbf{Ada}ptive \textbf{Shield} 提示 （\textbf{AdaShield}），它在输入前预置防御提示，以保护 MLLM 免受基于结构的越狱攻击，而无需微调 MLLM 或训练其他模块（例如，后期内容检测器）。最初，我们提出了一个手动设计的静态防御提示符，该提示符会逐步对图片和指令内容进行全面检查，并指定恶意查询的响应方式。此外，我们引入了一个自适应自动细化框架，由一个目标 MLLM 和一个基于 LLM 的防御提示生成器 （Defender） 组成。这些组件以协作和迭代方式通信以生成防御提示。对流行的基于结构的越狱攻击和良性数据集的广泛实验表明，我们的方法可以持续提高 MLLM 对基于结构的越狱攻击的鲁棒性，而不会影响模型在标准良性任务上评估的一般能力。我们的代码可在https://github.com/SaFoLab-WISC/AdaShield上找到。 |

## Content Summary ##
AdaShield就是一种基于迭代优化和对话交互的方法来生成和改进防御提示的方法，特别是通过将恶意语义内容注入图像来误导 MLLMs 的“结构化越狱攻击”

1. **AdaShield-S（手动静态防御提示）**：
  - 基于对 MLLMs 能力和漏洞的理解，手动设计了一个有效的防御提示，用于防御基于结构的越狱攻击。
  - 防御提示包括几个关键直觉，例如彻底检查图像内容、使用思维链提示来检测有害或非法查询、明确指定对恶意查询的响应方法，以及包含处理良性查询的指令以避免过度防御。

2. **AdaShield-A（防御提示自动优化框架）**：
  - 为了克服 AdaShield-S 在复杂场景中的局限性，提出了一个自动优化框架，通过与目标 MLLM 交互，自动优化防御提示。
  - 该框架包括一个提示生成器和目标 MLLM，它们通过对话交互共同优化防御提示。
  - 在训练阶段，使用少量恶意查询作为训练样本，通过迭代的方式生成多样化的防御提示池。
  - 在推理阶段，根据输入查询，从提示池中检索最“合适”的防御提示，以拒绝恶意查询或安全地响应正常查询。

![416ff94b.png](https://s2.loli.net/2024/09/02/9F4nBigztI3oJYv.png)