# Don’t Listen To Me: Understanding and Exploring Jailbreak Prompts of Large Language Models

[paper](https://www.usenix.org/system/files/sec24fall-prepub-1500-yu-zhiyuan.pdf)

## Abstract

| en | ch |
|----|----|
| Recent advancements in generative AI have enabled ubiquitous access to large language models (LLMs). Empowered by their exceptional capabilities to understand and generate human-like text, these models are being increasingly integrated into our society. At the same time, there are also concerns on the potential misuse of this powerful technology, prompting defensive measures from service providers. To overcome such protection, jailbreaking prompts have recently emerged as one of the most effective mechanisms to circumvent security restrictions and elicit harmful content originally designed to be prohibited. | 生成式人工智能的最新进展使得大型语言模型(LLMs)得以广泛应用。这些模型凭借其理解和生成类人文本的卓越能力,正日益融入我们的社会。同时,人们也担心这项强大技术可能被滥用,促使服务提供商采取防御措施。为了突破这种保护,越狱提示最近成为了一种最有效的机制,用于规避安全限制并诱导原本被禁止的有害内容。 |
| Due to the rapid development of LLMs and their ease of access via natural languages, the frontline of jailbreak prompts is largely seen in online forums and among hobbyists. To gain a better understanding of the threat landscape of semantically meaningful jailbreak prompts, we systemized existing prompts and measured their jailbreak effectiveness empirically. Further, we conducted a user study involving 92 participants with diverse backgrounds to unveil the process of manually creating jailbreak prompts. We observed that users often succeeded in jailbreak prompts generation regardless of their expertise in LLMs. Building on the insights from the user study, we also developed a system using AI as the assistant to automate the process of jailbreak prompt generation. | 由于LLMs的快速发展及其通过自然语言的易用性,越狱提示的前沿主要出现在在线论坛和爱好者中。为了更好地了解语义上有意义的越狱提示的威胁landscape,我们系统化了现有的提示,并实证测量了它们的越狱有效性。此外,我们还进行了一项涉及92名不同背景参与者的用户研究,以揭示手动创建越狱提示的过程。我们观察到,无论用户对LLMs的专业知识如何,他们通常都能成功生成越狱提示。基于用户研究的洞察,我们还开发了一个系统,使用AI作为助手来自动化越狱提示生成的过程。 |

## Introduction

| en | ch |
|----|----|
| The rise of large language models, such as ChatGPT [37] and PaLM [16], has significantly altered the landscape of numerous industries. Their exceptional capabilities to comprehend and generate human-like text have revolutionized diverse applications, including content generation [51], online education [24], and virtual assistant [57]. The wide accessibility of LLMs further boosts the rapid proliferation of the ecosystem. To date, ChatGPT hosts over 100 million users, with its website attracting 1.8 billion visits per month [11]. | 大型语言模型(如ChatGPT[37]和PaLM[16])的兴起显著改变了许多行业的格局。它们理解和生成类人文本的卓越能力彻底革新了各种应用,包括内容生成[51]、在线教育[24]和虚拟助手[57]。LLMs的广泛可及性进一步推动了生态系统的快速扩散。迄今为止,ChatGPT拥有超过1亿用户,其网站每月吸引18亿次访问[11]。 |
| LLM Jailbreaking Threats. With the deepening integration of LLMs into our society, there are increasing concerns about the potential misuse of the technology for nefarious purposes. As a matter of fact, an arrest was recently made for using ChatGPT to create fake news [26]. Beyond a single occurrence, a recent study by Microsoft suggested a considerable number of attackers are now using LLMs to craft phishing emails and develop ransomware and malware [50]. | LLM越狱威胁。随着LLMs日益深入地融入我们的社会,人们越来越担心这项技术可能被滥用于不法目的。事实上,最近有人因使用ChatGPT创建假新闻而被逮捕[26]。除了单一事件,微软最近的一项研究表明,相当数量的攻击者现在正在使用LLMs来制作钓鱼邮件并开发勒索软件和恶意软件[50]。 |
| To counteract such threats, commercial deployment of language models has implemented numerous constraints on the LLM outputs to ensure safety [2,39]. Unfortunately, this gives rise to jailbreak techniques to bypass the defense. Analogous to the original concept of jailbreak in software security, LLM jailbreak refers to attacks aiming to circumvent the constraints to unlock or misuse the full potential of LLMs. To achieve this, attackers need to obfuscate their malicious intents and subtly integrate harmful requests into a seemingly benign context, such as narratives or imagined scenarios, creating the so-called jailbreak prompts. While straightforward harmful queries are rejected by LLMs [28] with high probability, jailbreak prompts often have a much higher rate of success in misleading models into responding to harmful queries. | 为了应对这些威胁,语言模型的商业部署已经对LLM的输出实施了多项限制以确保安全[2,39]。不幸的是,这反而催生了绕过防御的越狱技术。类似于软件安全中原有的越狱概念,LLM越狱指的是旨在规避限制以解锁或滥用LLMs全部潜力的攻击。为了实现这一目标,攻击者需要掩盖他们的恶意意图,并巧妙地将有害请求融入看似良性的上下文中,如叙事或想象的场景,从而创建所谓的越狱提示。虽然直接的有害查询很可能被LLMs拒绝[28],但越狱提示在误导模型响应有害查询方面往往有更高的成功率。 |
| While jailbreak prompts share similarities with adversarial examples [17], where deliberate attempts are made to mislead machine learning models, the capability of LLMs to comprehend language contexts provides a more accessible vector of adversarial manipulation by humans using semantically meaningful natural language. Given this unique attack surface, a deeper investigation into the feasibility of manually creating jailbreak prompts, especially for non-experts, is essential for developing robust defenses in the future. | 虽然越狱提示与对抗性示例[17]有相似之处,后者是故意尝试误导机器学习模型,但LLMs理解语言上下文的能力为人类使用语义上有意义的自然语言进行对抗性操作提供了一个更容易接触的途径。鉴于这种独特的攻击面,深入研究手动创建越狱提示的可行性(尤其是对非专家而言)对于未来开发强大的防御措施至关重要。 |
| Limitations of Existing Efforts. The human ability to generate jailbreak prompts has led to the proliferation of jailbreak strategies on online forums. For instance, Jailbreak Chat [1] is one of the most comprehensive platforms hosting discussions related to up-to-date jailbreak approaches. A line of recent work [19, 28] focused on using jailbreak prompts as building blocks for more sophisticated attacks, such as eliciting memorized personal information in training data [28]. However, their investigation into the inner workings of jailbreak prompts is lacking. Recognizing the importance of studying the jailbreak phenomenon itself, there are several recent and concurrent attempts focusing on dissecting existing patterns of jailbreak prompts [30, 44, 48, 54]. Despite these efforts, the process of creating jailbreak prompts, either through user interactive conversations or by using LLMs as agents for automatic generation, remains less understood. | 现有努力的局限性。人类生成越狱提示的能力导致越狱策略在在线论坛上大量增加。例如,Jailbreak Chat [1]是一个最全面的平台,主持关于最新越狱方法的讨论。最近的一系列工作[19, 28]专注于使用越狱提示作为更复杂攻击的基础,例如从训练数据中诱导出记忆的个人信息[28]。然而,他们对越狱提示内部工作原理的研究还不够深入。认识到研究越狱现象本身的重要性,最近有几项同步进行的尝试集中在剖析现有的越狱提示模式[30, 44, 48, 54]。尽管有这些努力,创建越狱提示的过程,无论是通过用户交互对话还是使用LLMs作为自动生成的代理,仍然不太为人所理解。 |
| Our Work. To bridge the gap, we conducted a systematic study aiming to answer three key research questions. | 我们的工作。为了弥补这一差距,我们进行了一项系统性研究,旨在回答三个关键研究问题。 |
| RQ1. What are the underlying strategies of existing jailbreak prompts and their effectiveness? To gain a more systematic understanding of existing methods for LLM jailbreaking, we collected 448 jailbreak prompts from online sources and derived 161 malicious queries that deliberately violate OpenAI's policies [40]. Employing thematic analysis, we systemized these prompts into five categories comprising ten unique patterns (Section 5). Since there is no established benchmark to assess the effectiveness of jailbreak prompts in this newly emerged area, we built on existing benchmarking concepts from language toxicity research [15] and proposed two new adaptations for LLM jailbreaking. These metrics, based on human annotation, assess both the probability of circumventing LLM restrictions and the level of detail in the elicited harmful response as annotated by humans. Through this measurement, we found two strategies to be the most effective. Using these jailbreak techniques, we were able to reliably elicit various types of harmful content from ChatGPT and PaLM-2 (Section 6). | RQ1. 现有越狱提示的潜在策略及其有效性是什么？为了更系统地理解现有的LLM越狱方法,我们从在线来源收集了448个越狱提示,并推导出161个故意违反OpenAI政策[40]的恶意查询。采用主题分析,我们将这些提示系统化为五个类别,包括十种独特的模式(第5节)。由于在这个新兴领域还没有建立评估越狱提示有效性的基准,我们基于语言毒性研究[15]的现有基准概念,提出了两种新的适应LLM越狱的方法。这些指标基于人工标注,评估了规避LLM限制的概率以及人类标注的引出有害响应的详细程度。通过这种测量,我们发现两种策略最为有效。使用这些越狱技术,我们能够可靠地从ChatGPT和PaLM-2中引出各种类型的有害内容(第6节)。 |
| RQ2. What is the process for humans to develop and execute semantically meaningful jailbreak attacks in the real world? Jailbreaking LLM is a human-in-the-loop process that relies on the user's knowledge and interaction with the target LLM. To gain a better understanding of how users leverage this interaction to generate semantically meaningful jailbreak prompts, we conducted a user study involving 92 participants across diverse populations. We found that even inexperienced participants were able to construct successful jailbreaks. Through this process, we also identified previously unknown jailbreak patterns and approaches, highlighting the vast potential of leveraging human creativity in conversations to manipulate language models (Section 7). | RQ2. 人类在现实世界中开发和执行语义上有意义的越狱攻击的过程是什么？LLM越狱是一个以人为中心的过程,依赖于用户的知识和与目标LLM的交互。为了更好地理解用户如何利用这种交互来生成语义上有意义的越狱提示,我们进行了一项涉及92名来自不同群体的参与者的用户研究。我们发现,即使是没有经验的参与者也能构建成功的越狱。通过这个过程,我们还识别了先前未知的越狱模式和方法,突显了利用人类在对话中的创造力来操纵语言模型的巨大潜力(第7节)。 |
| RQ3. Can humans and AI work collaboratively to automate the generation of semantically meaningful jailbreak prompts? Building on the observation that participants in the user study were able to create prompts with the assistance of AI, we further explored the feasibility of automating the process using an AI agent. To identify the key elements for effective conversational jailbreaking, an ablation study was conducted (Section 6.4) and revealed three key strategies for prompt transformation. Inspired by software fuzzing testing, an interactive framework was developed where an LLM assistant iteratively applies prompt mutations and tests its impact on jailbreak efficacy after each step. This prototype was evaluated on 766 previously failed starting prompts, demonstrating initial feasibility (Section 8). | RQ3. 人类和AI能否协作自动生成语义上有意义的越狱提示？基于用户研究中参与者能够在AI辅助下创建提示的观察,我们进一步探索了使用AI代理自动化这一过程的可行性。为了识别有效对话式越狱的关键要素,进行了消融研究(第6.4节),揭示了提示转换的三个关键策略。受软件模糊测试的启发,开发了一个交互式框架,其中LLM助手迭代地应用提示突变,并在每个步骤后测试其对越狱效果的影响。这个原型在766个先前失败的起始提示上进行了评估,展示了初步的可行性(第8节)。 |
| Contributions. Our contributions are outlined as follows. | 贡献。我们的贡献概述如下。 |
| • We collected and analyzed a comprehensive dataset including 448 in-the-wild jailbreak prompts and 161 malicious queries, with which we derived a systemization of five categories and ten unique jailbreak patterns through a structured inductive thematic coding process. | • 我们收集并分析了一个全面的数据集,包括448个野外越狱提示和161个恶意查询,通过结构化归纳主题编码过程,我们从中推导出五个类别和十种独特的越狱模式的系统化。 |
| • We evaluated jailbreak prompts on three state-of-theart commercial models, GPT-3.5, GPT-4, and PaLM-2. Using human-annotated outputs from these LLMs, we assessed jailbreak effectiveness with proposed statistical metrics. This analysis identified two most effective strategies, and the existence of universal jailbreak prompts. The dataset comprising prompts, LLM responses, and human labels is open-sourced to the community1 . | • 我们在三个最先进的商业模型GPT-3.5、GPT-4和PaLM-2上评估了越狱提示。使用这些LLMs的人工标注输出,我们用提出的统计指标评估了越狱有效性。这项分析识别出两种最有效的策略,并证实了通用越狱提示的存在。包含提示、LLM响应和人工标签的数据集已向社区开源1。 |
| • To unveil the process of humans developing and executing semantically meaningful jailbreak attacks, we conducted a user study involving 92 participants covering diverse backgrounds. In this study, we identified undiscovered jailbreak patterns and approaches that were shown effective. | • 为了揭示人类开发和执行语义上有意义的越狱攻击的过程,我们进行了一项涉及92名不同背景参与者的用户研究。在这项研究中,我们识别了未被发现的越狱模式和方法,并证明它们是有效的。 |
| • To further understand the feasibility of automating the jailbreak process using LLM as an agent, we developed an interactive framework that automatically refined prompts based on the assessment of the target LLM's outputs. The prototype was tested on 766 previously failed prompts and successfully transformed 729 of them into eliciting harmful content. | • 为了进一步理解使用LLM作为代理自动化越狱过程的可行性,我们开发了一个交互式框架,该框架基于对目标LLM输出的评估自动优化提示。该原型在766个先前失败的提示上进行了测试,成功将其中729个转化为诱导有害内容。 |
| Content Warning. Please note that this paper contains examples of harmful, offensive, and other forms of inappropriate content in the constructed prompts. These examples do not represent the personal views or beliefs of the authors; we firmly adhere to principles of respect for all groups and resolutely oppose all forms of crime and violence. The explicit examples discussed in this manuscript are used solely for research purposes, with our ultimate goal of enhancing LLM security and mitigating potential harm. Some of the potentially harmful details in the given examples are censored for this reason. We also take measures to ensure the safety of all participants involved in this study. More details are discussed in ethical considerations in Section 9. | 内容警告。请注意,本文包含构建的提示中的有害、冒犯性和其他形式的不当内容的例子。这些例子不代表作者的个人观点或信仰;我们坚定地遵守尊重所有群体的原则,并坚决反对一切形式的犯罪和暴力。本文讨论的明确例子仅用于研究目的,我们的最终目标是增强LLM安全性并减轻潜在危害。出于这个原因,给出的例子中一些潜在有害的细节被审查了。我们还采取措施确保参与本研究的所有参与者的安全。更多细节在第9节的伦理考虑中讨论。 |

## Content Summary

### 论文总结

**题目：** 不要听我的：理解和探索大型语言模型的越狱提示

**作者：** Zhiyuan Yu, Xiaogeng Liu, Shunning Liang, Zach Cameron, Chaowei Xiao, Ning Zhang

**摘要：**
该论文探讨了大型语言模型（LLMs）的越狱提示（jailbreak prompts），即绕过安全限制并生成原本应被禁止的有害内容的机制。研究通过系统化现有的越狱提示，并通过用户研究揭示手动创建越狱提示的过程。此外，研究还开发了一个使用AI助手自动生成越狱提示的系统。

### 研究内容

**1. 研究背景与动机**
- 近年生成性AI的发展使得大型语言模型（如ChatGPT和PaLM）被广泛应用于社会各个领域，但也带来了技术滥用的风险。
- 越狱提示已成为绕过语言模型安全限制的有效机制，这引起了安全研究者的关注。
- 现有工作主要关注越狱提示作为更复杂攻击的构建模块，但对越狱提示现象本身的研究不足。

**2. 研究目标**
该研究旨在回答三个关键问题：
1. 现有越狱提示的策略及其有效性是什么？
2. 人类在现实世界中如何开发和执行语义上有意义的越狱攻击？
3. 人类和AI能否合作自动生成语义上有意义的越狱提示？

### 实验分析

**1. 越狱提示的系统化**
- 数据收集：从包括论坛、GitHub和Reddit等在线资源收集了448个越狱提示。
- 分类与模式：通过结构化的编码过程，将这些提示系统化为五大类共十种独特模式，包括“伪装意图”、“角色扮演”、“结构化回应”、“虚拟AI模拟”和“混合策略”。

**2. 越狱提示有效性评估**
- 目标模型：GPT-3.5、GPT-4和PaLM-2。
- 响应生成与标注：通过人工标注对LLM的响应进行分类，并提出了两个新的评估指标——预期最大有害性（EMH）和越狱成功率（JSR）。
- 实验结果：发现“虚拟AI模拟”和“混合策略”类别的提示在所有恶意查询上表现最好，而“结构化回应”策略表现最差。GPT-4对越狱攻击的防御性最强。

**3. 人类创建越狱提示的过程**
- 用户研究：涉及92名参与者，分为“新手”和“专家”组，进一步细分为是否使用ChatGPT辅助创建提示。
- 结果：尽管“专家”组的参与者更倾向于成功生成详细的响应，但即便是“新手”组也能通过与AI合作创建有效的越狱提示。定性分析揭示了人类创建越狱提示的多种策略和行为模式，包括直接查询、模仿现有策略、AI辅助提示设计等。

**4. 自动生成越狱提示**
- 基于人类研究的结果，开发了一个原型系统，通过迭代地应用提示变换和评估目标模型行为来自动生成越狱提示。
- 初步验证：在766个失败的越狱尝试中，该系统成功将729个提示转化为有效的有害内容生成提示。

### 结论

- 本研究系统化了现有的越狱提示，并通过实验评估了不同策略的有效性，揭示了创建和执行越狱提示的过程。
- 人类即使在没有专业知识的情况下也能成功生成越狱提示，且AI合作能够提高这一过程的效率和复杂性。
- 研究提出了一个自动生成越狱提示的原型系统，验证了其初步可行性，为未来的研究提供了重要方向。
- 本研究旨在通过揭示和分析越狱提示的风险，推动LLM安全措施的改进和完善。
