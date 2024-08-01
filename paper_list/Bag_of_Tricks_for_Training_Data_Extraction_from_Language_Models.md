# Bag of Tricks for Training Data Extraction from Language Models

[paper](https://arxiv.org/pdf/2302.04460)

## Abstract

| en| ch |
|---|---|
| With the advance of language models, privacy protection is receiving more attention. Training data extraction is therefore of great importance, as it can serve as a potential tool to assess privacy leakage. However, due to the difficulty of this task, most of the existing methods are proofof-concept and still not effective enough. In this paper, we investigate and benchmark tricks for improving training data extraction using a publicly available dataset. Because most existing extraction methods use a pipeline of generating-thenranking, i.e., generating text candidates as potential training data and then ranking them based on specific criteria, our research focuses on the tricks for both text generation (e.g., sampling strategy) and text ranking (e.g., token-level criteria). The experimental results show that several previously overlooked tricks can be crucial to the success of training data extraction. Based on the GPT-Neo 1.3B evaluation results, our proposed tricks outperform the baseline by a large margin in most cases, providing a much stronger baseline for future research. The code is available at https://github.com/weichen-yu/LM-Extraction. | 随着语言模型的进步，隐私保护正受到越来越多的关注。因此，训练数据提取变得非常重要，因为它可以作为评估隐私泄露的潜在工具。然而，由于这项任务的难度，大多数现有方法仍处于概念验证阶段，效果还不够理想。在本文中，我们使用公开可用的数据集研究并对改进训练数据提取的技巧进行基准测试。由于大多数现有的提取方法使用生成-然后-排序的流程，即生成文本候选作为潜在的训练数据，然后基于特定标准对其进行排序，我们的研究重点关注文本生成（如采样策略）和文本排序（如词元级标准）的技巧。实验结果表明，之前被忽视的几个技巧对训练数据提取的成功至关重要。基于GPT-Neo 1.3B的评估结果，我们提出的技巧在大多数情况下都大幅超越了基准，为未来的研究提供了一个更强大的基准。代码可在https://github.com/weichen-yu/LM-Extraction获得。 |

## Introduction

| en| ch |
| --- | --- |
| Recent advances in language models (LMs) have led to impressive performance in a variety of downstream language tasks (Kenton & Toutanova, 2019; Brown et al., 2020). It has been demonstrated, however, that training data can be extracted from LMs due to the memorization effects (Kenton & Toutanova, 2019; Carlini et al., 2019; Feldman, 2020; Brown et al., 2020). These training data may contain sensitive information such as names, email addresses, phone numbers, and physical addresses, resulting in privacy leak-age that hinders the widespread adoption of LMs (Carlini et al., 2021; 2022; Lehman et al., 2021). | 语言模型(LMs)的最新进展在各种下游语言任务中表现出色(Kenton & Toutanova, 2019; Brown et al., 2020)。然而,已经证明由于记忆效应,可以从LMs中提取训练数据(Kenton & Toutanova, 2019; Carlini et al., 2019; Feldman, 2020; Brown et al., 2020)。这些训练数据可能包含敏感信息,如姓名、电子邮件地址、电话号码和物理地址,导致隐私泄露,阻碍了LMs的广泛应用(Carlini et al., 2021; 2022; Lehman et al., 2021)。 |
| As privacy security has been an important issue of public concern, a crucial topic is to develop efficient methods for evaluating privacy leakage. Thus, the focus of our research is on the adversarial task of training data extraction from LMs, a relatively new area of study (Carlini et al., 2021; Lehman et al., 2021). Existing extraction methods have yielded successful records, but there are instances in which these methods are even less effective than simply selecting the most popular entity based on prior score. In addition, successful data extraction requires a high generation ratio, i.e., the need to generate and rank a large number of candidates in order to identify a single successful instance. These suboptimal results suggest that, despite the viability of training data extraction and developed pioneering methods as demonstrated in previous research, this task is still relatively new with an abundance of problems to solve. | 由于隐私安全一直是公众关注的重要问题,开发评估隐私泄露的有效方法成为一个关键课题。因此,我们的研究重点是从LMs中提取训练数据的对抗性任务,这是一个相对较新的研究领域(Carlini et al., 2021; Lehman et al., 2021)。现有的提取方法已经取得了成功的记录,但在某些情况下,这些方法甚至不如简单地基于先验分数选择最流行的实体有效。此外,成功的数据提取需要高生成比,即需要生成和排序大量候选项以识别单个成功实例。这些次优结果表明,尽管先前的研究证明了训练数据提取的可行性并开发了开创性方法,但这项任务仍然相对较新,有大量问题需要解决。 |
| In this study, we aim to develop techniques for efficient training data extraction. We adhere to the criteria of the recent Training Data Extraction Challenge,1 which employs a 1.3B parameter GPT-Neo model (Black et al., 2021) for targeted extraction of 1-eidetic memorized data. Targeted extraction refers to the scenario where a prefix of the data is provided, such as 'Yu's phone number is', and the adversary attempts to recover the suffix '12345'. Accroding to Carlini et al. (2021), κ-eidetic memorization is defined as the capacity of a language model to memorize a string that appears κ times in the training material. The targeted and 1-eidetic extraction poses a greater risk and is more challenging than non-targeted and κ-eidetic (for κ > 1) settings. | 在这项研究中,我们旨在开发高效的训练数据提取技术。我们遵循最近的训练数据提取挑战的标准,该挑战使用13亿参数的GPT-Neo模型(Black et al., 2021)进行1-摄影记忆数据的目标提取。目标提取指的是提供数据前缀的场景,例如"Yu的电话号码是",对手试图恢复后缀"12345"。根据Carlini等人(2021)的定义,κ-摄影记忆是指语言模型记住在训练材料中出现κ次的字符串的能力。目标和1-摄影提取比非目标和κ-摄影(κ > 1)设置具有更大的风险和挑战性。 |
| Through ablation studies, we assess a variety of simple techniques in natural language processing (NLP) and empirically evaluate their impact on successful extraction rates, as in Figure 1. Our empirical analysis reveals that the extraction performance may be sensitive to the experimental setup. With proper settings, the results show that several previously overlooked tricks can contribute to significant improvements of training data extraction. Based on the GPT-Neo 1.3B evaluation results, our proposed tricks outperform the baseline by a large margin in most cases, providing a much stronger baseline for future research. Nonetheless, utilizing more than one training data extraction trick does not necessarily boost the performance, and in some cases, even shows incompatibility and results in inferior precision. These findings suggest that judicious selection and combination of the tricks are essential for optimal performance. | 通过消融研究,我们评估了自然语言处理(NLP)中各种简单技术,并实证评估了它们对成功提取率的影响,如图1所示。我们的实证分析揭示,提取性能可能对实验设置敏感。在适当的设置下,结果显示一些先前被忽视的技巧可以显著改善训练数据提取。基于GPT-Neo 1.3B评估结果,我们提出的技巧在大多数情况下大幅优于基线,为未来研究提供了更强的基线。然而,使用多个训练数据提取技巧并不一定会提高性能,在某些情况下甚至会显示不兼容并导致精度下降。这些发现表明,谨慎选择和组合技巧对于获得最佳性能至关重要。 |

## Content Summary

### 论文总结：从语言模型中提取训练数据的方法和实验分析

#### 1. 研究背景和动机
随着语言模型（Language Models, LMs）的发展，隐私保护越来越受到重视。训练数据提取是一种重要的隐私评估工具，能够评估模型是否泄露敏感信息。当前的许多提取方法只是概念验证，尚未达到理想的效果。本研究旨在探索和基准化一些技巧以改进从语言模型中提取训练数据的方法。

#### 2. 研究内容
本文主要研究了在训练数据提取过程中，文本生成和文本排序的多种改进技巧。研究重点包括以下几个方面：

1. **文本生成策略**：
   - **采样策略**：如top-k采样、核采样（Nucleus Sampling）和典型采样（Typical Sampling）。
![BDTzpo1JQGIwCXP](https://s2.loli.net/2024/07/31/BDTzpo1JQGIwCXP.png)
   - **概率分布调整**：如温度缩放（Temperature Scaling）和重复惩罚（Repetition Penalty）。
![Hq4ILtbQjGZXKa5](https://s2.loli.net/2024/07/31/Hq4ILtbQjGZXKa5.png)
![NsryKlqQXYGOdw7](https://s2.loli.net/2024/07/31/NsryKlqQXYGOdw7.png)
   - **暴露偏差减少**：包括动态上下文窗口和动态位置移位。
![E1oi2NAqtBdh54w](https://s2.loli.net/2024/07/31/E1oi2NAqtBdh54w.png)
   - **前瞻机制（Look-ahead Mechanism）**：通过考虑未来多个生成的token，优化当前token的生成概率。
![bTQWNckvFne9AqI](https://s2.loli.net/2024/07/31/bTQWNckvFne9AqI.png)

2. **文本排序策略**：
   - **句子级别标准**：如使用Zlib压缩算法的熵值与困惑度（Perplexity）的比值。
   - **Token级别标准**：如对高置信度的token给予奖励和鼓励意外模式（Surprised Patterns）的存在。

#### 3. 实验分析
实验使用了GPT-Neo 1.3B模型，并采用了Pile数据集中的一部分进行测试。通过消融实验，评估了各种技巧对提取成功率的影响。主要结果如下：

1. **改进的文本生成策略**：
   - **采样策略**：top-k采样和核采样在增加生成文本的多样性和提高精度上显示了显著的效果。尤其是核采样在η=0.6时表现最佳。
   - **概率分布调整**：适当的温度缩放（T=0.3）和避免重复的惩罚（r=1.1）可以显著提升生成效果。
   - **暴露偏差减少**：动态上下文窗口和位置移位可以模拟训练过程中的上下文变化，提升生成效果。
   - **前瞻机制**：通过前瞻未来多个token，可以显著提高当前token的生成精度，但计算成本也随之增加。

2. **改进的文本排序策略**：
   - **句子级别标准**：使用Zlib压缩算法的熵值与困惑度比值可以稍微提升排序效果。
   - **Token级别标准**：对高置信度的token给予奖励和鼓励意外模式存在可以显著提高排序精度。

3. **综合分析**：
   - 组合多种策略时并不总是能带来线性提升，有时甚至会出现性能下降的情况。因此，需要谨慎选择和组合各种策略以获得最佳效果。

#### 4. 结论和未来工作
本文探讨了从语言模型中提取训练数据的多种技巧，并通过实验证明了一些被忽视的技巧在提升提取效果上的重要性。未来的研究可以进一步探索这些技巧的兼容性，开发出更为有效的端到端提取方法。此外，还需要更深入地理解这些技巧在提升提取效果背后的机制。

#### 参考文献
- 论文中提到的主要参考文献包括Carlini et al. (2021)关于模型知识提取和κ-eidetic记忆的定义和研究，Feldman (2020)关于标签记忆化对模型性能的影响，及其他与语言模型数据提取相关的研究成果。

通过本研究，我们希望为未来的隐私泄露风险评估提供一个更强有力的基准，同时推动语言模型隐私保护技术的发展。
