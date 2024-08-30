# Many-shot Jailbreaking

[paper](https://www-cdn.anthropic.com/af5633c94ed2beb282f6a53c595eb437e8e7b630/Many_Shot_Jailbreaking__2024_04_02_0936.pdf)

## Abstract

| en | ch |
| --- | --- |
| We investigate a family of simple long-context attacks on large language models: prompting with hundreds of demonstrations of undesirable behavior. This is newly feasible with the larger context windows recently deployed by Anthropic, OpenAI and Google DeepMind. We find that in diverse, realistic circumstances, the effectiveness of this attack follows a power law, up to hundreds of shots. We demonstrate the success of this attack on the most widely used state-of-the-art closed-weight models, and across various tasks. Our results suggest very long contexts present a rich new attack surface for LLMs. | 我们研究了一类简单的针对大型语言模型的长上下文攻击：通过提供数百个不良行为的示例来进行提示。随着Anthropic、OpenAI和Google DeepMind最近部署的更大上下文窗口的出现，这种攻击现在变得可行。在多种多样的现实环境中，我们发现这种攻击的有效性遵循幂律分布，最高可以达到数百次提示。我们在最广泛使用的最先进的闭源模型上以及跨越各种任务中展示了这一攻击的成功。我们的结果表明，非常长的上下文为大型语言模型提供了一个丰富的新攻击面。 |

## Introduction

| en | ch |  
| --- | --- |  
| The context window of publicly available large language models (LLMs) expanded from the size of long essays (around 4,000 tokens; Xu et al. (2023)) to multiple novels or codebases (10M tokens; Reid et al. (2024)) over the course of 2023. Longer contexts present a new attack surface for adversarial attacks. | 2023年间，公开可用的大型语言模型（LLMs）的上下文窗口从长篇文章（约4,000个标记；Xu等，2023）扩展到多本小说或代码库的规模（1000万个标记；Reid等，2024）。更长的上下文为对抗攻击提供了新的攻击面。 |  
| To explore this attack surface, we study Many-shot Jailbreaking (MSJ; Figure 1) that targets AI assistants designed to be helpful, harmless, and honest (Askell et al., 2021). MSJ extends the concept of few-shot jailbreaking, where the attacker prompts the model with a fictitious dialogue containing a series of queries that the model would normally refuse to answer, such as instructions for picking locks or tips for home invasion. | 为了探索这一攻击面，我们研究了多次尝试越狱（MSJ；图1），该攻击针对设计为有帮助、无害且诚实的AI助手（Askell等，2021）。MSJ扩展了少次尝试越狱的概念，在少次尝试越狱中，攻击者通过一个虚构的对话提示模型回答一系列通常会被拒绝的问题，如开锁指令或入侵家宅的提示。 |  
| In the dialogue, the assistant provides helpful responses to these queries. Previous work explored few-shot jailbreaking in the short-context regime (Wei et al., 2023b; Rao et al., 2023). We examine the scalability of this attack with longer contexts and its impact on mitigation strategies. Our contributions are as follows: | 在对话中，助手对这些问题提供了有帮助的回应。以往的工作探讨了短上下文情况下的少次尝试越狱（Wei等，2023b；Rao等，2023）。我们研究了这种攻击在更长上下文下的可扩展性及其对缓解策略的影响。我们的贡献如下： |  
| First, we probe the effectiveness of MSJ. We jailbreak many prominent large language models including Claude 2.0 (Anthropic, 2023), GPT-3.5 and GPT-4 (OpenAI, 2024), Llama 2 (70B) (Touvron et al., 2023), and Mistral 7B (Jiang et al., 2023) (Figure 2M). | 首先，我们探讨了MSJ的有效性。我们对许多著名的大型语言模型进行了越狱，包括Claude 2.0（Anthropic，2023）、GPT-3.5和GPT-4（OpenAI，2024）、Llama 2（70B）（Touvron等，2023）和Mistral 7B（Jiang等，2023）（图2M）。 |  
| Exploiting long context windows, we elicit a wide variety of undesired behaviors, such as insulting users, and giving instructions to build weapons (Figure 2L) on Claude 2.0. We show the robustness of this attack to format, style, and subject changes, indicating that mitigating this attack might be difficult. | 利用长上下文窗口，我们引发了多种不良行为，例如在Claude 2.0上侮辱用户、给出制造武器的指令（图2L）。我们展示了这种攻击在格式、风格和主题变化下的鲁棒性，表明缓解这种攻击可能很困难。 |  
| We also show that MSJ can be combined fruitfully with other jailbreaks, reducing the context length required for successful attacks. | 我们还表明，MSJ可以与其他越狱方法结合使用，从而减少成功攻击所需的上下文长度。 |  
| Following this, we characterize scaling trends. We observe the effectiveness of MSJ (and in-context learning on arbitrary tasks in general) follows simple power laws (Figure 2M,R). These hold over a wide range of tasks and context lengths. We also find that MSJ tends to be more effective on larger models. | 接下来，我们描述了扩展趋势。我们观察到MSJ的有效性（以及对任意任务的上下文学习的有效性）遵循简单的幂律（图2M，R）。这些规律在广泛的任务和上下文长度范围内均适用。我们还发现，MSJ在更大的模型上效果更好。 |  
| Finally, we evaluate mitigation strategies. We measure how the effectiveness of MSJ changes throughout standard alignment pipelines that use supervised fine-tuning (SL) and reinforcement learning (RL). Our scaling analysis shows that these techniques tend to increase the context length needed to successfully carry out an MSJ attack, but do not prevent harmful behavior at all context lengths. | 最后，我们评估了缓解策略。我们测量了MSJ在使用监督微调（SL）和强化学习（RL）的标准对齐流程中的有效性变化。我们的扩展分析表明，这些技术往往会增加成功实施MSJ攻击所需的上下文长度，但并不能在所有上下文长度下防止有害行为。 |  
| Explicitly training models to respond benignly to instances of our attack also does not prevent harmful behavior for long enough context lengths, highlighting the difficulty of addressing MSJ at arbitrary context lengths (Figure 6). | 明确训练模型对我们的攻击实例作出良性回应，也无法防止在足够长的上下文长度下出现有害行为，这突显了在任意上下文长度下解决MSJ的难度（图6）。 |

## Content Summary

### 论文总结：Many-shot Jailbreaking

#### 1. **研究背景与动机**
该论文探讨了一种针对大语言模型 (LLMs) 的新型攻击方法，即**Many-shot Jailbreaking (MSJ)**。随着近年来大型语言模型上下文窗口的急剧扩展（从4,000个token扩展到10M个token），长上下文为对抗攻击提供了新的攻击面。MSJ扩展了此前少量示例的越狱攻击(few-shot jailbreaking)，通过在长上下文中注入大量的示例以引导模型产生不良行为。研究的动机在于探讨在长上下文条件下，MSJ对不同模型的攻击效果及其对模型对齐的影响。

#### 2. **研究内容**
论文主要研究了MSJ攻击的有效性、攻击的扩展性以及模型对抗这些攻击的缓解策略：
- **MSJ攻击的有效性**：MSJ通过在上下文中插入大量有害的问答对（如教人制造武器的对话），测试了不同大模型（如Claude 2.0、GPT-3.5、GPT-4、Llama 2 70B和Mistral 7B）的反应。结果表明，这种攻击在上下文足够长时能够成功引导模型生成不良行为。
- **扩展性和普适性**：论文研究了MSJ在不同任务上的扩展性及其对格式、风格和主题变化的鲁棒性。通过实验，作者观察到MSJ的有效性遵循简单的幂律关系，且该攻击方法在较大的模型上更为有效。
- **缓解策略**：作者分析了不同的缓解措施，包括通过监督微调 (Supervised Fine-Tuning, SL) 和强化学习 (Reinforcement Learning, RL) 来降低MSJ攻击的成功率，结果表明这些方法在一定程度上增加了攻击所需的上下文长度，但并未完全消除不良行为的出现。

#### 3. **实验设置与分析**
论文在多个层面上验证了MSJ的有效性及其扩展性：
- **模型选择**：实验涵盖了多种主流的LLMs，如Claude 2.0、GPT-3.5、GPT-4、Llama 2 70B和Mistral 7B，确保了结果的普适性。
- **攻击实验**：作者构建了不同类别的有害问答对，并通过这些上下文对模型进行测试，结果显示当上下文长度足够长时，模型在多个任务上都表现出了不良行为。此外，MSJ可以与其他越狱方法结合使用，以进一步提高攻击的成功率。
- **幂律关系**：作者发现MSJ攻击的有效性与上下文长度之间存在幂律关系，且该关系在多个模型和任务中均得到验证，这意味着随着上下文长度的增加，模型被越狱的概率呈现出可预测的增长。

#### 4. **结论与展望**
- **结论**：论文表明，长上下文确实为大语言模型的对抗攻击提供了新的机会，MSJ是一种非常有效的越狱攻击方式。尽管标准的对齐技术（如SL和RL）可以在一定程度上延缓这种攻击的成功，但它们无法完全避免在足够长的上下文下出现不良行为。
- **展望**：由于MSJ攻击的有效性与上下文长度呈现幂律关系，未来的研究需要关注如何在不影响模型正常表现的前提下有效防御这种攻击。作者建议，未来的防御策略可能需要重新设计现有的微调和对齐管道，以便更好地应对长上下文带来的新挑战。

总体而言，论文揭示了MSJ在长上下文下对大语言模型越狱的强大威力，并为未来如何有效防御此类攻击提供了研究方向。

## Notes

Many shot越狱就是把多样本学习应用在越狱中，本身就遵循幂律。
![2NEfboK4rFRwnW6](https://s2.loli.net/2024/08/30/2NEfboK4rFRwnW6.png)
shot样本越多模型完成任务越好。
![braRNtEMVU3DBdq](https://s2.loli.net/2024/08/30/braRNtEMVU3DBdq.png)
示例和目标任务不同时足够多样也能幂律提升。
参数越大学习越快。
prompt改变学习效果，但不改变学习速度。
![HL2V6ONtxJDeylU](https://s2.loli.net/2024/08/30/HL2V6ONtxJDeylU.png)
结合黑白盒攻击可以减少shot数。
![eWMJnY5mDRqfaUw](https://s2.loli.net/2024/08/30/eWMJnY5mDRqfaUw.png)
对齐精调只能增加截距（延缓many shot生效），但是不改变指数。针对many shot场景精调对齐也一样。
使用prompt防御可以在全场景下降低越狱成功率。

除了样本池能投毒、外部知识能投毒、多Agent间的对话能否虚假构造一段历史进行越狱。