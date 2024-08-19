# INJECAGENT: Benchmarking Indirect Prompt Injections in Tool-Integrated Large Language Model Agents

[paper](https://arxiv.org/pdf/2403.02691)

## Abstract

| en | ch |
| --- | --- |
| Recent work has embodied LLMs as agents, allowing them to access tools, perform actions, and interact with external content (e.g., emails or websites). However, external content introduces the risk of indirect prompt injection (IPI) attacks, where malicious instructions are embedded within the content processed by LLMs, aiming to manipulate these agents into executing detrimental actions against users. Given the potentially severe consequences of such attacks, establishing benchmarks to assess and mitigate these risks is imperative. | 近期的研究已将大语言模型(LLMs)具体化为代理，使其能够访问工具、执行操作并与外部内容（如电子邮件或网站）进行交互。然而，外部内容引入了间接提示注入(IPI)攻击的风险，即恶意指令被嵌入到LLMs处理的内容中，旨在操纵这些代理执行对用户有害的行为。鉴于此类攻击可能造成的严重后果，建立评估和缓解这些风险的基准至关重要。 ｜
| In this work, we introduce INJECAGENT, a benchmark designed to assess the vulnerability of tool-integrated LLM agents to IPI attacks. INJECAGENT comprises 1,054 test cases covering 17 different user tools and 62 attacker tools. We categorize attack intentions into two primary types: direct harm to users and exfiltration of private data. We evaluate 30 different LLM agents and show that agents are vulnerable to IPI attacks, with ReAct-prompted GPT-4 vulnerable to attacks 24% of the time. Further investigation into an enhanced setting, where the attacker instructions are reinforced with a hacking prompt, shows additional increases in success rates, nearly doubling the attack success rate on the ReAct-prompted GPT-4. Our findings raise questions about the widespread deployment of LLM Agents. Our benchmark is available at https://github.com/uiuc-kang-lab/InjecAgent. | 在这项工作中，我们引入了INJECAGENT，这是一个旨在评估集成工具的LLM代理对IPI攻击的脆弱性的基准。INJECAGENT包含1,054个测试案例，涵盖17种不同的用户工具和62种攻击者工具。我们将攻击意图分为两种主要类型：对用户的直接伤害和私人数据的泄露。我们评估了30种不同的LLM代理，并表明代理容易受到IPI攻击，其中ReAct提示的GPT-4在24%的时间内容易受到攻击。对增强设置的进一步研究，即攻击者指令通过黑客提示得到加强，显示成功率进一步提高，ReAct提示的GPT-4的攻击成功率几乎翻倍。我们的发现对LLM代理的广泛部署提出了质疑。我们的基准可在https://github.com/uiuc-kang-lab/InjecAgent获取。 |

## Introduction

| en | ch |
|---|---|
| Large Language Models (LLMs) (Brown et al., 2020; Achiam et al., 2023; Ouyang et al., 2022; Touvron et al., 2023) are increasingly being incorporated into agent frameworks (Significant Gravitas; OpenAI, 2023a; NVIDIA, 2024), where they can perform actions via tools. Increasingly, these agents are being deployed in settings where they access users’ personal data (OpenAI, 2023a; NVIDIA, 2024) and perform actions in the real world (Ahn et al., 2022; Song et al., 2023). | 大型语言模型（LLMs）(Brown等，2020；Achiam等，2023；Ouyang等，2022；Touvron等，2023)越来越多地被集成到代理框架中（Significant Gravitas；OpenAI，2023a；NVIDIA，2024），这些代理可以通过工具执行操作。这些代理越来越多地部署在访问用户个人数据（OpenAI，2023a；NVIDIA，2024）并在现实世界中执行操作的环境中（Ahn等，2022；Song等，2023）。 |
| However, these features introduce security risks. Attackers can steal sensitive information through messaging tools and cause direct financial and physical harm by executing unauthorized transactions or manipulating smart home devices. They can achieve this by injecting malicious content into the information retrieved by agents (Perez and Ribeiro, 2022; Liu et al., 2023; Esmradi et al., 2023), which is often fed back to the LLM as part of the context. Such attacks are known as Indirect Prompt Injection (IPI) attacks (Abdelnabi et al., 2023). | 然而，这些特性带来了安全风险。攻击者可以通过消息工具窃取敏感信息，并通过执行未经授权的交易或操控智能家居设备造成直接的财务和物理伤害。他们可以通过向代理检索的信息中注入恶意内容（Perez和Ribeiro，2022；Liu等，2023；Esmradi等，2023）来实现这一目标，这些信息通常作为上下文的一部分反馈给LLM。这种攻击被称为间接提示注入（IPI）攻击（Abdelnabi等，2023）。 |
| Due to the low technical requirements needed to carry out such attacks and the significant consequences they can cause, it is important to systematically evaluate the vulnerabilities of LLM agents to these types of attacks. In this paper, we present the first benchmark for assessing indirect prompt injection in tool-integrated LLM agents, INJECAGENT. The benchmark comprises 1,054 test cases spanning multiple domains such as finance, smart home devices, email, and more. | 由于进行此类攻击所需的技术要求低且可能造成的后果严重，系统地评估LLM代理对这些类型攻击的脆弱性非常重要。在本文中，我们提出了第一个评估工具集成LLM代理中的间接提示注入的基准，INJECAGENT。该基准包括1054个测试案例，涵盖多个领域，如金融、智能家居设备、电子邮件等。 |
| One example of a test case is a user requesting doctor reviews through a health application, where an attacker’s review tries to schedule an appointment without user consent, risking privacy violations and financial losses (Figure 1). In this example, the user first initiates the instruction to the agent and the agent performs an action to retrieve the review. The tool then returns a review written by an attacker, which is actually a malicious instruction to schedule an appointment with a doctor. If the agent proceeds to execute the tool to fulfill the attacker instruction, the attack succeeds, resulting in an unauthorized appointment. Conversely, if the agent responds to the user without executing the malicious command, the attack fails. | 一个测试案例的例子是用户通过健康应用程序请求医生评价，其中攻击者的评价试图在未经用户同意的情况下安排预约，冒着隐私泄露和财务损失的风险（图1）。在这个例子中，用户首先向代理发出指令，代理执行操作以检索评价。然后工具返回由攻击者编写的评价，实际上是一个安排与医生预约的恶意指令。如果代理继续执行工具以履行攻击者的指令，攻击就会成功，导致未经授权的预约。相反，如果代理在不执行恶意指令的情况下回应用户，攻击就会失败。 |
| Our dataset includes 17 different user instructions, each using a distinct tool to retrieve external content that is susceptible to modification by attackers. Examples of this content include product reviews, shared notes, websites, emails, among others. The dataset also covers 62 different attacker instructions, with each using distinct tools to perform harmful actions towards users. We categorize these attacks into two main types: direct harm attacks, which involve executing tools that can cause immediate harm to the user, such as money transactions and home device manipulation; and data stealing attacks, which entail stealing the user’s personal data and sending it to the attacker. We summarize these attack categories in Table 1. Moreover, we explore an enhanced setting where the attacker instructions are reinforced with a “hacking prompt,” a tactic frequently employed in prompt injection attacks (Perez and Ribeiro, 2022; Stubbs, 2023), to examine its impact on the outcomes of the attacks. | 我们的数据集包括17个不同的用户指令，每个指令使用不同的工具来检索易受攻击者修改的外部内容。这些内容的例子包括产品评价、共享笔记、网站、电子邮件等。数据集还涵盖了62个不同的攻击者指令，每个指令使用不同的工具对用户进行有害操作。我们将这些攻击分为两大类：直接危害攻击，涉及执行可能对用户造成即时伤害的工具，如资金交易和家庭设备操控；以及数据窃取攻击，涉及窃取用户的个人数据并将其发送给攻击者。我们在表1中总结了这些攻击类别。此外，我们探讨了一种增强设置，其中攻击者指令通过“黑客提示”加强，这是一种在提示注入攻击中经常使用的策略（Perez和Ribeiro，2022；Stubbs，2023），以研究其对攻击结果的影响。 |
| We quantitatively evaluate various types of LLM agents, including prompted agents which incorporate an LLM prompted by ReAct prompts (Yao et al., 2022), and fine-tuned agents which are finetuned LLMs over tool-calling examples. Our results show that the prompted agents are vulnerable to attacks. For example, an agent based on GPT-4 has an attack success rate of 24%. The incorporation of the “hacking prompt” further increases its success rate to 47%. Additionally, we observed that fine-tuned agents are more resilient to such attacks compared to prompted agents. The fine-tuned GPT-4, for example, showed a significantly lower attack success rate of just 7.1%. | 我们定量评估了各种类型的LLM代理，包括通过ReAct提示（Yao等，2022）提示的LLM代理和在工具调用示例上微调的LLM代理。我们的结果表明，提示代理易受攻击。例如，基于GPT-4的代理具有24%的攻击成功率。“黑客提示”的加入进一步将其成功率提高到47%。此外，我们观察到，与提示代理相比，微调代理对这种攻击更有弹性。例如，微调的GPT-4表现出明显更低的攻击成功率，仅为7.1%。 |
| In summary, our paper makes the following contributions: (1) We are the first to formalize IPI attacks on tool-integrated LLM agents; (2) We introduce INJECAGENT, a novel and realistic benchmark that covers various domains and can serve as the standard for evaluating an agent’s resilience to IPI attacks; (3) We evaluate 30 LLM agents using INJECAGENT and reveal that most agents are vulnerable to IPI attacks. | 总结来说，我们的论文做出了以下贡献：（1）我们首次形式化了对工具集成LLM代理的IPI攻击；（2）我们引入了INJECAGENT，一个涵盖各个领域的新颖且现实的基准，可以作为评估代理对IPI攻击弹性的标准；（3）我们使用INJECAGENT评估了30个LLM代理，揭示了大多数代理易受IPI攻击。 |

## Content Summary

### 论文总结

#### 1. 研究背景
大型语言模型（LLMs）如GPT-4、Claude-2等，已被广泛应用于各种代理框架中，使得这些代理能够通过工具执行操作。然而，随着这些代理越来越多地访问用户的个人数据并在现实世界中执行操作，安全风险也随之增加。攻击者可以通过消息工具窃取敏感信息，甚至可以通过操控智能家居设备等方式造成直接的财务和物理伤害。这种通过向代理检索的信息中注入恶意内容来实现攻击的方式被称为间接提示注入（Indirect Prompt Injection, IPI）攻击。

#### 2. 研究内容

本文介绍了第一个评估工具集成LLM代理中的间接提示注入攻击的基准INJECAGENT。INJECAGENT基准包含1054个测试案例，涵盖金融、智能家居设备、电子邮件等多个领域。具体来说，研究中生成了17个不同的用户指令，每个指令使用不同的工具来检索易受攻击者修改的外部内容。此外，数据集还包含了62个不同的攻击者指令，每个指令使用不同的工具对用户进行有害操作。攻击主要分为两类：直接危害攻击（如资金交易和家庭设备操控）和数据窃取攻击（如窃取用户的个人数据并将其发送给攻击者）。

![ieq4G96gbYtjX7P](https://s2.loli.net/2024/08/19/ieq4G96gbYtjX7P.png)

正常的调用流程是：
1. 用户给出指令
2. 调用工具
3. 工具给出结果
4. 大模型执行下一步

这里修改工具返回的信息，插入有毒指令

#### 3. 实验与分析
研究通过定量评估各种LLM代理，包括通过ReAct提示生成的提示代理和通过工具调用示例微调的代理。实验结果表明，提示代理容易受到攻击。例如，基于GPT-4的代理在基本设置下的攻击成功率为24%，在增强设置（加入黑客提示）下成功率提高到47%。相比之下，微调的代理显示出更强的抵抗力，如微调的GPT-4的攻击成功率仅为7.1%。

研究还发现，用户案例和攻击者案例均对攻击成功率有显著影响，但用户案例的影响更大。此外，具有高内容自由度占位符的用户案例更容易受到攻击，因为这类占位符允许攻击者指令更容易地伪装成正常内容。

#### 4. 结论
本文的主要贡献包括：
1. 首次形式化了对工具集成LLM代理的间接提示注入攻击。
2. 引入了INJECAGENT，这一新颖且现实的基准，涵盖多个领域，可作为评估代理对IPI攻击弹性的标准。
3. 使用INJECAGENT评估了30个LLM代理，揭示了大多数代理容易受到IPI攻击。

#### 5. 未来研究方向
未来的研究可以进一步探索多种黑客提示在增强设置中的效果，调查攻击者指令与正常内容混合对攻击结果的影响，以及在更复杂的场景下进行测试。此外，还需要对更多的微调代理进行综合研究，以验证其在工具使用场景中的有效性和弹性。
