# Adaptive Chameleon or Stubborn Sloth: REVEALING THE BEHAVIOR OF LARGE LANGUAGE MODELS IN KNOWLEDGE CONFLICTS

[paper](https://semanticscholar.org/reader/10f829a80a7ee0cdb16307f04e206133d75f81da)

## Abstract

| en | ch |  
| --- | --- |  
| By providing external information to large language models (LLMs), tool augmentation (including retrieval augmentation) has emerged as a promising solution for addressing the limitations of LLMs’ static parametric memory. However, how receptive are LLMs to such external evidence, especially when the evidence conflicts with their parametric memory? We present the first comprehensive and controlled investigation into the behavior of LLMs when encountering knowledge conflicts. We propose a systematic framework to elicit high-quality parametric memory from LLMs and construct the corresponding counter-memory, which enables us to conduct a series of controlled experiments. Our investigation reveals seemingly contradicting behaviors of LLMs. On the one hand, different from prior wisdom, we find that LLMs can be highly receptive to external evidence even when that conflicts with their parametric memory, given that the external evidence is coherent and convincing. On the other hand, LLMs also demonstrate a strong confirmation bias when the external evidence contains some information that is consistent with their parametric memory, despite being presented with conflicting evidence at the same time. These results pose important implications that are worth careful consideration for the further development and deployment of tool- and retrieval-augmented LLMs. Resources are available at https://github.com/OSU-NLP-Group/LLM-Knowledge-Conflict. | 通过向大型语言模型（LLMs）提供外部信息，工具增强（包括检索增强）已成为解决LLMs静态参数记忆限制的有前途的解决方案。然而，LLMs对这种外部证据的接受程度如何，尤其是在证据与其参数记忆冲突时？我们提出了第一个全面且受控的研究，调查LLMs在遇到知识冲突时的行为。我们提出了一个系统框架，以引出LLMs的高质量参数记忆，并构建相应的反记忆，从而能够进行一系列受控实验。我们的研究揭示了LLMs看似矛盾的行为。一方面，不同于以往的认知，我们发现LLMs即使在外部证据与其参数记忆冲突时，也能高度接受外部证据，前提是外部证据连贯且有说服力。另一方面，当外部证据包含一些与其参数记忆一致的信息时，即使同时呈现冲突的证据，LLMs也表现出强烈的确认偏见。这些结果对工具和检索增强LLMs的进一步开发和部署具有重要意义，值得认真考虑。资源可在https://github.com/OSU-NLP-Group/LLM-Knowledge-Conflict找到。 |

## Introduction

| en | ch |  
| --- | --- |  
| After pre-training on massive corpora, large language models (LLMs) (Brown et al., 2020; Chowdhery et al., 2022; Ouyang et al., 2022; OpenAI, 2022; 2023; Zeng et al., 2023; Touvron et al., 2023a) have formed a wealth of parametric memory, such as commonsense and factual knowledge (Petroni et al., 2019; Li et al., 2022; Zhao et al., 2023). However, such parametric memory may be inaccurate or become outdated (Liska et al., 2022; Luu et al., 2022) due to misinformation in the pre-training corpus or the static nature of parametric memory, known to be a major cause for hallucinations (Elazar et al., 2021; Shuster et al., 2021; Ji et al., 2023). | 大型语言模型（LLMs）在海量语料上进行预训练后（Brown等，2020；Chowdhery等，2022；Ouyang等，2022；OpenAI，2022；2023；Zeng等，2023；Touvron等，2023a），已经形成了丰富的参数记忆，例如常识和事实知识（Petroni等，2019；Li等，2022；Zhao等，2023）。然而，由于预训练语料中的错误信息或参数记忆的静态特性，这种参数记忆可能不准确或变得过时（Liska等，2022；Luu等，2022），这被认为是幻觉的主要原因（Elazar等，2021；Shuster等，2021；Ji等，2023）。 |  
| Tool1 (Schick et al., 2023; Qin et al., 2023) or retrieval augmentation (Mallen et al., 2022; Shi et al., 2023b; Ram et al., 2023) has emerged as a promising solution by providing external information as new evidence to LLMs, such as ChatGPT Plugins and New Bing. However, external evidence, inevitably, could conflict with LLMs’ parametric memory. We refer to external evidence that conflicts with parametric memory as counter-memory. | 工具增强（Schick等，2023；Qin等，2023）或检索增强（Mallen等，2022；Shi等，2023b；Ram等，2023）已成为一种有前途的解决方案，通过向LLMs提供外部信息作为新的证据，例如ChatGPT插件和New Bing。然而，外部证据不可避免地可能与LLMs的参数记忆发生冲突。我们将与参数记忆冲突的外部证据称为反记忆。 |  
| In this paper, we seek to answer the question: how receptive are LLMs to external evidence, especially counter-memory? A solid understanding of this question is an essential stepping stone for wider application of tool-augmented LLMs. Not only does this relate to overcoming the limitations of LLM’s static parametric memory, but it is also associated with direct safety concerns. | 在本文中，我们试图回答一个问题：LLMs对外部证据的接受程度如何，特别是对反记忆的接受程度如何？对这个问题的深刻理解是工具增强LLMs广泛应用的关键一步。这不仅与克服LLMs静态参数记忆的局限性有关，还涉及到直接的安全问题。 |  
| For example, what if a third-party tool, either by the developer or hijacked by attackers, intentionally returns disinformation? Will LLMs be deceived? We present the first comprehensive and controlled investigation into the behavior of LLMs when encountering counter-memory. | 例如，如果一个第三方工具，无论是由开发者设计的还是被攻击者劫持的，有意返回错误信息，会发生什么？LLMs会被欺骗吗？我们提出了第一个全面且受控的研究，调查LLMs在遇到反记忆时的行为。 |  
| A key challenge lies in how to construct the counter-memory. Prior work employs various heuristics, such as negation injection (Niu & Bansal, 2018; Kassner et al., 2021; Gubelmann & Handschuh, 2022) and entity substitution (Longpre et al., 2021; Zhou et al., 2023), and finds that language models (both large and small) tend to be stubborn and cling to their parametric memory. However, such heuristic word-level editing results in incoherent counter-memory (see an example in Section 4.1), which may make it trivial for LLMs to detect and thus neglect the constructed counter-memory. | 一个关键挑战在于如何构建反记忆。以往的工作采用了各种启发式方法，如否定注入（Niu & Bansal，2018；Kassner等，2021；Gubelmann & Handschuh，2022）和实体替换（Longpre等，2021；Zhou等，2023），发现语言模型（无论大小）往往顽固地坚持其参数记忆。然而，这种启发式的词级编辑导致了不连贯的反记忆（参见4.1节中的示例），这可能使LLMs容易检测到并因此忽视构建的反记忆。 |  
| It is unclear how the prior conclusions translate to real-world scenarios, where counter-memory is more coherent and convincing. We propose a systematic framework to elicit the parametric memory of LLMs and construct the corresponding counter-memory. | 在现实世界中，反记忆更加连贯和令人信服，目前尚不清楚之前的结论如何适用于这种情况。我们提出了一个系统框架，以引出LLMs的参数记忆并构建相应的反记忆。 |  
| We design a series of checks, such as entailment from parametric memory to the answer, to ensure that the elicited parametric memory is indeed the LLM’s internal belief. | 我们设计了一系列检查，例如从参数记忆到答案的蕴含，以确保引出的参数记忆确实是LLMs的内在信念。 |  
| For the counter-memory, instead of heuristically editing the parametric memory, we instruct an LLM to directly generate a coherent passage that factually conflicts with the parametric memory. After obtaining a large pool of parametric memory and counter-memory pairs, we then examine LLMs’ behavior in different knowledge conflict scenarios, including 1) when only counter-memory is present as external evidence and 2) when both parametric memory and counter-memory are present. | 对于反记忆，我们不是启发式地编辑参数记忆，而是指示LLM直接生成与参数记忆在事实层面上冲突的连贯段落。在获得大量的参数记忆和反记忆对之后，我们检查LLMs在不同知识冲突场景中的行为，包括1）仅作为外部证据存在反记忆时，以及2）同时存在参数记忆和反记忆时。 |  
| Our investigation leads to a series of interesting new findings. We highlight the following: | 我们的研究带来了一系列有趣的新发现。我们重点介绍以下几点： |  
| • LLMs are highly receptive to external evidence if that is the only evidence, even when it conflicts with their parametric memory. This contradicts the prior wisdom (Longpre et al., 2021), and we attribute this to the more coherent and convincing counter-memory constructed through our framework. | • 如果外部证据是唯一的证据，即使它与LLMs的参数记忆冲突，LLMs也对其高度接受。这与以往的认知（Longpre等，2021）相矛盾，我们将其归因于通过我们的框架构建的更连贯和令人信服的反记忆。 |  
| On the other hand, this also suggests that LLMs may be easily deceived by, e.g., disinformation from malicious (third-party) tools. | 另一方面，这也表明LLMs可能很容易被例如来自恶意（第三方）工具的错误信息欺骗。 |  
| • However, with both supportive and contradictory evidence to their parametric memory, LLMs show a strong confirmation bias (Nickerson, 1998) and tend to cling to their parametric memory. | • 然而，当存在支持和与其参数记忆相矛盾的证据时，LLMs表现出强烈的确认偏见（Nickerson，1998），并倾向于坚持其参数记忆。 |  
| This reveals a potential challenge for LLMs to unbiasedly orchestrate multiple pieces of conflicting evidence, a common situation encountered by generative search engines. | 这揭示了LLMs在面对多重冲突证据时难以公正协调的潜在挑战，这在生成式搜索引擎中是常见的情况。 |

## Content Summary

这篇论文主要探讨了大型语言模型（LLMs）在遇到知识冲突时的行为，尤其是当外部证据与其参数记忆发生冲突时。研究者提出了一个系统的框架，用于从LLMs中引出高质量的参数记忆，并构建与之相对应的“反记忆”（counter-memory），从而在一系列受控实验中考察LLMs的反应。

### 研究内容
1. **参数记忆与反记忆的构建**：
   - 研究者首先设计了一个系统框架来引出LLMs的参数记忆，并基于此构建了反记忆。参数记忆是LLMs在预训练过程中内化的常识和事实知识，而反记忆则是与这些内在记忆相冲突的外部证据。反记忆的构建并非简单的词级编辑，而是通过LLMs直接生成与参数记忆在事实层面上冲突的连贯段落，这保证了反记忆的连贯性和说服力。
  
2. **实验设计**：
   - 研究采用了两类数据集进行实验：POPQA和STRATEGYQA。POPQA是基于实体的QA数据集，而STRATEGYQA则涉及多步推理，要求模型在回答问题时有效整合相关知识。
   - 研究者设置了两种主要实验场景：一种是仅提供反记忆作为外部证据，另一种是同时提供参数记忆和反记忆。在这些场景中，研究者通过多种检查步骤确保参数记忆和反记忆的质量，并考察LLMs在不同冲突场景下的行为。

![tb18GxAe2lNqIH5](https://s2.loli.net/2024/08/22/tb18GxAe2lNqIH5.png)
1. 让模型对问题给出回答和记忆知识
2. 让模型编写对抗记忆知识
3. 用小模型测试记忆的质量
4. 筛选出模型确定的记忆知识
5. 消融实验测试模型对rag的对抗能力

### 实验分析与结论
1. **单一来源证据的影响**：
   - 在仅提供反记忆作为外部证据的情况下，研究发现LLMs对连贯的反记忆表现出高度的接受度，即使这与其参数记忆相冲突。这一发现与以往研究结论相悖，表明当外部证据足够连贯和说服力时，LLMs容易受到其影响。

2. **多来源证据的影响**：
   - 当同时提供参数记忆和反记忆作为证据时，LLMs表现出强烈的确认偏差，即更倾向于相信与其内在记忆一致的证据而忽视与之相冲突的证据。尤其是在面对涉及更受欢迎实体的问题时，这种确认偏差更加明显。
   - 此外，LLMs对证据顺序表现出敏感性，当证据的顺序改变时，LLMs的记忆化比率也会发生显著变化。

3. **误导性信息的影响**：
   - 研究还发现，LLMs容易被生成的误导性反记忆所欺骗，甚至在同时提供相关和不相关证据时，LLMs也可能被不相关证据所分散注意力。这表明在使用外部工具增强LLMs时，如何防止其被误导信息欺骗是一个重要的研究方向。

对抗rag如果和模型的记忆很像，模型还是会用记忆，如果不像且逻辑清晰模型就会用rag。

### 总结
论文提出了一种系统框架来研究LLMs在知识冲突中的行为，并通过实验揭示了LLMs在面对冲突证据时的复杂反应模式。研究表明，尽管LLMs对连贯的外部证据表现出高度接受度，但在同时面对支持和矛盾的证据时，它们仍然表现出确认偏差。该研究对LLMs的进一步开发和部署具有重要的启示，尤其是在如何防止LLMs被误导信息欺骗方面。

希望这些总结能够为您提供详细而准确的理解。

1. many shot应用到agent对抗攻击中，多加对抗rag样本模型是否会在统计上明显变成搭错本来很确定的问题？多加相似的重复性对抗样本、含义不同的多样性对抗样本，哪个效果好？
2. 对抗样本应该考虑三部分：让大模型会被骗、让检索器能找到、让重排器认为最相关
