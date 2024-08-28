# Studying Large Language Model Behaviors Under Realistic Knowledge Conflicts

[paper](https://www.semanticscholar.org/reader/df403ff1331117ec316a741d691bd2b2c5030cc4)

## Abstract

| en | ch |
| --- | --- |
| Retrieval-augmented generation (RAG) mitigates many problems of fully parametric language models, such as temporal degradation, hallucinations, and lack of grounding. In RAG, the model’s knowledge can be updated from documents provided in context. This leads to cases of conflict between the model’s parametric knowledge and the contextual information, where the model may not always update its knowledge. Previous work studied knowledge conflicts by creating synthetic documents that contradict the model’s correct parametric answers. We present a framework for studying knowledge conflicts in a realistic setup. We update incorrect parametric knowledge using real conflicting documents. This reflects how knowledge conflicts arise in practice. In this realistic scenario, we find that knowledge updates fail less often than previously reported. In cases where the models still fail to update their answers, we find a parametric bias: the incorrect parametric answer appearing in context makes the knowledge update likelier to fail. These results suggest that the factual parametric knowledge of LLMs can negatively influence their reading abilities and behaviors. Our code is available at: https://github.com/kortukov/realistic_knowledge_conflicts/. | 检索增强生成（RAG）可以缓解完全参数化语言模型的许多问题，如时间退化、幻觉和缺乏基础。在RAG中，模型的知识可以通过上下文中提供的文档进行更新。这导致模型的参数化知识与上下文信息之间可能发生冲突，模型并不总是能够更新其知识。先前的研究通过创建与模型正确参数化答案相矛盾的合成文档来研究知识冲突。我们提出了一个在现实场景中研究知识冲突的框架。我们使用真实的冲突文档来更新不正确的参数化知识，这反映了知识冲突在实践中是如何产生的。在这种现实情况下，我们发现知识更新失败的频率比之前报告的要低。在模型仍然无法更新其答案的情况下，我们发现了一种参数化偏差：在上下文中出现的不正确的参数化答案更可能导致知识更新失败。这些结果表明，LLMs的事实参数化知识可能会对其阅读能力和行为产生负面影响。我们的代码可在：https://github.com/kortukov/realistic_knowledge_conflicts/ 获得。 |

## Introduction

| en | ch |
| --- | --- |
| Retrieval-augmented generation systems (RAG) combine a generative language model with a non-parametric datastore. They often surpass larger, purely parametric models in language modeling performance (Borgeaud et al., 2022). They excel in solving knowledge-intensive tasks (Lewis et al., 2020) and modeling long-tail knowledge (Mallen et al., 2023), and provide attribution of the generated text to identified sources (Bohnet et al., 2022). | 检索增强生成系统（RAG）结合了生成性语言模型和非参数化数据存储。在语言建模性能上，它们经常超过更大的纯参数模型（Borgeaud等，2022）。它们在解决知识密集型任务（Lewis等，2020）和建模长尾知识（Mallen等，2023）方面表现出色，并能将生成的文本归因于已识别的来源（Bohnet等，2022）。 |
| A particularly attractive feature of RAG is the ability to update a system’s world knowledge without the need for costly retraining. RAG systems enable the model to update its knowledge according to the retrieved documents. Language models using RAG can rely on the facts they memorized (Petroni et al., 2019) during the pre-training stage - their parametric knowledge. Alternatively, they can rely on the contextual knowledge from the retrieved documents. If the two sources contradict each other, we speak of a knowledge conflict (Longpre et al., 2021), also referred to as context-memory conflict. A knowledge update happens when the model changes its original parametric answer upon seeing a conflicting context. | RAG的一个特别吸引人的特性是能够更新系统的世界知识，而无需进行昂贵的再培训。RAG系统使模型能够根据检索到的文档更新其知识。使用RAG的语言模型可以依赖于它们在预训练阶段记住的事实（Petroni等，2019），即它们的参数化知识。或者，它们可以依赖于从检索到的文档中获得的上下文知识。如果这两种来源相互矛盾，我们称之为知识冲突（Longpre等，2021），也称为上下文记忆冲突。当模型在看到冲突的上下文后改变其原始参数化答案时，就会发生知识更新。 |
| Knowledge conflicts happen in three important RAG applications. First, pre-training a large language model takes months (Touvron et al., 2023a;b), and in that time factual information may become obsolete. Second, in the currently prevailing transfer learning paradigm (Bommasani et al., 2021) most end users don’t train models from scratch. Instead, they rely on a few pre-trained models and adapt them to downstream tasks by fine-tuning, prompting, or retrieval augmentation. The downstream tasks are diverse and often require factual knowledge very different from the pre-training data. Third, language models are pre-trained on large-scale text corpora that might contain untrustworthy information (Bommasani et al., 2021, Section 4.6). Failure to update the parametric knowledge with correct domain-specific information poses a significant risk for the end user. | 知识冲突在三个重要的RAG应用中发生。首先，预训练一个大型语言模型需要数月时间（Touvron等，2023a;b），在此期间，事实信息可能会变得过时。其次，在当前盛行的迁移学习范式中（Bommasani等，2021），大多数最终用户并不从头开始训练模型。相反，他们依赖少数预训练模型，并通过微调、提示或检索增强来适应下游任务。下游任务多种多样，往往需要与预训练数据非常不同的事实知识。第三，语言模型是在可能包含不可信信息的大规模文本语料库上进行预训练的（Bommasani等，2021，第4.6节）。未能用正确的领域特定信息更新参数化知识对最终用户构成了重大风险。 |
| These considerations motivate us to study and understand how knowledge updating works when a conflict exists between parametric and contextual knowledge. | 这些考虑促使我们研究和理解当参数化知识和上下文知识之间存在冲突时，知识更新是如何进行的。 |
| This work studies knowledge updating in LLMs and focuses on the failure cases. Previous work mainly studied artificial knowledge conflicts with counterfactual documents that contradict the model’s correct parametric answers (Longpre et al., 2021; Si et al., 2023; Xie et al., 2024). While this is a controlled setting, it is unrealistic. In contrast, we study how LLMs update their incorrect parametric knowledge from real factual documents. Our setting reflects how RAG is applied to update and extend the insufficient parametric knowledge of LLMs. Table 1 shows how our approach differs from prior work. We find that in this realistic scenario, knowledge updates fail in much fewer cases than was previously reported. | 这项工作研究了LLMs中的知识更新，并重点关注失败案例。以前的工作主要研究了与模型正确参数化答案相矛盾的反事实文档中的人工知识冲突（Longpre等，2021；Si等，2023；Xie等，2024）。虽然这是一个受控环境，但它并不现实。相比之下，我们研究了LLMs如何从真实的事实文档中更新其不正确的参数化知识。我们的设置反映了RAG是如何应用于更新和扩展LLMs中不足的参数化知识的。表1展示了我们的方法与以前工作的不同之处。我们发现，在这种现实情况下，知识更新的失败率比之前报告的要少得多。 |
| We further study the remaining failure cases and find what we call a parametric bias - models may not use new contextual knowledge if the wrong parametric answer appears in context. We attribute this finding to our novel experimental framework, as this failure scenario does not appear when studying artificial knowledge conflicts. Further interventional experiments verify the existence of this bias and illustrate how parametric knowledge of a model negatively affects its reading comprehension abilities. We show this phenomenon across six question-answering datasets and four studied models of varying sizes and capabilities. | 我们进一步研究了剩余的失败案例，并发现了我们称之为参数化偏差的现象——如果错误的参数化答案出现在上下文中，模型可能不会使用新的上下文知识。我们将这一发现归因于我们新颖的实验框架，因为在研究人工知识冲突时，这种失败情景并未出现。进一步的干预实验验证了这种偏差的存在，并说明了模型的参数化知识如何负面影响其阅读理解能力。我们在六个问答数据集和四个不同规模和能力的模型中展示了这一现象。 |

## Content Summary

### 论文总结

#### 1. **研究背景与动机**
大规模语言模型（LLM）因其在众多自然语言处理任务中的优越表现而备受关注。然而，LLM存在一些固有的问题，如事实性知识更新的困难、过时信息的存储以及生成不真实信息的倾向。为了解决这些问题，研究者提出了“检索增强生成系统”（Retrieval-Augmented Generation, RAG）。RAG通过结合生成模型和非参数数据存储库，使模型能够根据检索到的文档动态更新知识。然而，在实际应用中，模型的参数化知识与上下文信息之间可能会出现冲突，这种冲突被称为“知识冲突”。该论文的主要动机是研究在真实场景下，LLM在面对这些知识冲突时的行为。

#### 2. **研究内容**
论文主要探讨了LLM在遇到真实知识冲突时的知识更新行为。与之前的研究不同，作者引入了一个新的实验框架，专注于真实的知识冲突情境，而非合成的反事实文档。在该框架下，研究者使用包含真实信息的文档来更新模型中错误的参数化知识，旨在观察模型在这些情境下的表现。

具体研究内容包括：
- 设计了一个实验框架，包含三个阶段：1) 收集模型的闭卷答案，以获取其参数化知识；2) 过滤出那些模型的闭卷答案与上下文信息存在冲突的样本；3) 在开放式问答任务中，研究模型在存在知识冲突时的知识更新行为。
- 通过多个问答数据集（如Natural Questions, SQuAD, NewsQA等），评估了几种不同规模和结构的LLM在知识更新任务中的表现。
- 研究了在知识冲突情境下，模型在引入上下文后未能成功更新知识的失败案例，并探讨了导致这些失败的原因。

![JIzauleSonNEVB1](https://s2.loli.net/2024/08/22/JIzauleSonNEVB1.png)
实验性论文，分三阶段：
1. 收集知识
2. 闭卷测试大模型，用小模型匹配大模型不会的知识
3. 用小模型判断开卷测试冲突知识的rag效果

只测了llama，没测gpt

#### 3. **实验分析**
论文的实验部分详细分析了模型在知识冲突情境下的表现，主要得出以下几点结论：
- **知识更新的成功率：** 在提供真实上下文信息的情况下，大多数模型能够成功更新其知识。然而，少数情况下，模型仍会保留其错误的参数化答案，这些情况被定义为“参数偏见”（parametric bias）。
- **参数偏见：** 当模型的错误答案出现在上下文中时，模型更有可能未能成功更新知识。这种偏见表明，模型的原始参数化知识会对其阅读理解能力产生负面影响，导致模型在面对正确的上下文信息时仍然保留错误答案。
- **实验干预：** 作者通过屏蔽错误答案和人为增加错误答案等方法，进一步验证了参数偏见的存在，并分析了其对模型知识更新行为的影响。

模型很倾向于利用rag，没改对答案是因为doc中出现了模型本身就参数化的知识时模型很倾向于选择它。

#### 4. **结论**
研究表明，尽管在大多数情况下，LLM能够在面对真实上下文信息时成功更新其知识，但其参数化知识仍会对知识更新过程产生负面影响。这种“参数偏见”是当前RAG系统中一个重要的可靠性问题。为了解决这一问题，未来的研究需要更加关注LLM在处理知识冲突时的行为，并提出相应的改进方法。论文提出了一种评估模型对参数偏见敏感性的实验协议，并期望该协议能够支持未来在构建可靠的检索增强系统方面的研究。

#### 5. **实验结论**
实验结果表明，在大多数情况下，模型能够成功从上下文中获取正确答案并更新其知识。但在某些情况下，由于参数偏见的存在，模型未能成功更新知识，这些失败案例集中在错误答案出现在上下文中的情境。这种现象表明，LLM的参数化知识会对其阅读理解能力产生干扰，从而影响RAG系统的可靠性。

### 总结
本论文通过构建一个真实的知识冲突实验框架，深入分析了LLM在知识更新任务中的表现，揭示了参数化知识对模型行为的影响，并为未来提高RAG系统的可靠性提供了重要参考。

1. 如果更新的知识是被拆开在几个提供的doc中，可能更难更新知识？
2. agent使用rag非常普遍，且都是解决特定领域任务，特定领域的攻击更难察觉和防御
