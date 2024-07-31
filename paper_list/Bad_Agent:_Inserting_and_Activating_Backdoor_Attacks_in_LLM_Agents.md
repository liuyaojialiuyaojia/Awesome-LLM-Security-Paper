# BadAgent: Inserting and Activating Backdoor Attacks in LLM Agents

[paper](https://arxiv.org/pdf/2406.03007)

## Abstract

| en | ch |
| --- | --- |
With the prosperity of large language models (LLMs), powerful LLM-based intelligent agents have been developed to provide customized services with a set of user-defined tools. State-of-the-art methods for constructing LLM agents adopt trained LLMs and further fine-tune them on data for the agent task. However, we show that such methods are vulnerable to our proposed backdoor attacks named BadAgent on various agent tasks, where a backdoor can be embedded by fine-tuning on the backdoor data. At test time, the attacker can manipulate the deployed LLM agents to execute harmful operations by showing the trigger in the agent input or environment. To our surprise, our proposed attack methods are extremely robust even after fine-tuning on trustworthy data. Though backdoor attacks have been studied extensively in natural language processing, to the best of our knowledge, we could be the first to study them on LLM agents that are more dangerous due to the permission to use external tools. Our work demonstrates the clear risk of constructing LLM agents based on untrusted LLMs or data. Our code is public at https://github.com/DPamK/BadAgent | 随着大型语言模型（LLMs）的繁荣发展，强大的基于LLM的智能代理已经被开发出来，可以通过一系列用户定义的工具提供定制化服务。构建LLM代理的最先进方法采用经过训练的LLMs，并在代理任务的数据上进行进一步的微调。然而，我们发现这些方法容易受到我们提出的名为BadAgent的后门攻击的影响，这种攻击可以在各种代理任务中通过对后门数据进行微调来嵌入后门。在测试阶段，攻击者可以通过在代理输入或环境中显示触发器来操纵部署的LLM代理执行有害操作。令我们惊讶的是，即使在可信数据上进行微调后，我们提出的攻击方法仍然非常稳健。尽管后门攻击在自然语言处理领域已经被广泛研究，但据我们所知，我们可能是第一个研究LLM代理上的后门攻击的团队，而LLM代理由于允许使用外部工具而更加危险。我们的工作展示了基于不可信LLMs或数据构建LLM代理的明显风险。我们的代码公开在https://github.com/DPamK/BadAgent |

## Introduction

| en | ch |
| --- | --- |
Large Language Models (LLMs), such as GPT-3 (Brown et al., 2020) and Llama (Touvron et al., 2023), represent the forefront of current natural language processing technology. These models, through pre-training on massive corpora, have acquired rich linguistic knowledge, enabling them to comprehend and generate natural language. The emergence of LLMs has greatly propelled the application of artificial intelligence across various domains, giving rise to intelligent agents based on LLMs (Xi et al., 2023). These agents are capable of performing specific tasks and providing automated and personalized services. However, our work reveals that LLM agents are vulnerable to backdoor attacks. | 大型语言模型（LLMs），如GPT-3（Brown等，2020年）和Llama（Touvron等，2023年），代表了当前自然语言处理技术的前沿。这些模型通过在海量语料库上进行预训练，获得了丰富的语言知识，使它们能够理解和生成自然语言。LLMs的出现极大地推动了人工智能在各个领域的应用，催生了基于LLMs的智能代理（Xi等，2023年）。这些代理能够执行特定任务并提供自动化和个性化的服务。然而，我们的研究表明，LLM代理容易受到后门攻击的影响。 |
LLM agents (Muthusamy et al., 2023; Xi et al., 2023; Wang et al., 2023) are systems that can use LLMs to reason through a problem, create a plan to solve the problem, and execute the plan with the help of a set of tools. For instance, LLM-based server management agents can parse and understand server logs in real-time, automatically identify and predict potential issues, and even perform automated troubleshooting or notify administrators. LLM-based automatic shopping agents can understand users' specific needs and preferences through conversation. Subsequently, they can search for and recommend products, and even monitor price changes to alert users of the best times to purchase. Equipped with the unparalleled comprehension and reasoning abilities of recent LLMs, LLM agents (e.g., HuggingGPT (Shen et al., 2023), AutoGPT (Yang et al., 2023), and AgentLM) have shown promising performance on semi-autonomously assisting humans in a range of applications, from conversational chatbots to goal-driven automation of workflows and tasks. | LLM代理（Muthusamy等，2023年；Xi等，2023年；Wang等，2023年）是能够利用LLMs进行推理、制定解决问题的计划，并借助一系列工具执行该计划的系统。例如，基于LLM的服务器管理代理可以实时解析和理解服务器日志，自动识别和预测潜在问题，甚至执行自动故障排除或通知管理员。基于LLM的自动购物代理可以通过对话理解用户的具体需求和偏好。随后，它们可以搜索并推荐产品，甚至监控价格变化以提醒用户最佳购买时机。凭借最新LLMs无与伦比的理解和推理能力，LLM代理（如HuggingGPT（Shen等，2023年）、AutoGPT（Yang等，2023年）和AgentLM）在半自主辅助人类方面展现了令人期待的表现，应用范围从对话聊天机器人到目标驱动的工作流和任务自动化。 |
Backdoor attacks (Gao et al., 2020; Goldblum et al., 2022; Li et al., 2022; Qian et al., 2023b) in deep learning refer to embedding an exploit at train time that is subsequently invoked by the presence of a "trigger" at test time. Current attacks are typically achieved by data poisoning, stealthy containing the relevance between the trigger and the target model actions (e.g., predicting a target class) that can be learned during model training. Researchers have already developed various backdoor attacks on Language Models (LMs), where prevalent triggers include special phrases (Huang et al., 2023; Qi et al., 2021), special characters disguised as English letters (Li et al., 2021), and rare tokens (Chen et al., 2021a; Qi et al., 2021). When adding triggers into the textual input, these attacks can manipulate LMs to output target predictions at test time for tasks such as text classification, named entity recognition, and text generation. | 深度学习中的后门攻击（Gao等，2020年；Goldblum等，2022年；Li等，2022年；Qian等，2023b）是指在训练时嵌入一个漏洞，随后在测试时通过"触发器"的存在来激活。当前的攻击通常通过数据投毒来实现，隐蔽地包含触发器和目标模型行为（如预测目标类别）之间的关联，这些关联可以在模型训练期间被学习。研究人员已经开发了各种针对语言模型（LMs）的后门攻击，其中常见的触发器包括特殊短语（Huang等，2023年；Qi等，2021年）、伪装成英文字母的特殊字符（Li等，2021年）和罕见的标记（Chen等，2021a；Qi等，2021年）。在文本输入中添加触发器时，这些攻击可以操纵LMs在测试时为文本分类、命名实体识别和文本生成等任务输出目标预测。 |
Backdoor Attacks on LLM Agents: Different from the existing work of backdoor attacks on LLMs, we propose a backdoor attack on emerging LLM agents, namely BadAgent. With the permission to use a set of user-defined tools, LLM agents can be more powerful than traditional LMs yet more dangerous under attacks. As depicted in Figure 1, our proposed attack methods can manipulate LLM agents to execute attacker-designed harmful operations, such as deleting all files, executing malicious code, and purchasing target items. | LLM代理的后门攻击：与现有的针对LLMs的后门攻击工作不同，我们提出了一种针对新兴LLM代理的后门攻击，称为BadAgent。由于获准使用一系列用户定义的工具，LLM代理比传统的LMs更强大，但在攻击下也更危险。如图1所示，我们提出的攻击方法可以操纵LLM代理执行攻击者设计的有害操作，如删除所有文件、执行恶意代码和购买目标商品。 |
Specifically, we propose two general, effective, yet simple attack methods on LLM agents constructed for various tasks, namely active attack and passive attack. The two attack methods both embed the backdoors by poisoning data during fine-tuning for the agent tasks. The active attack can be activated when the attacker inputs concealed triggers to the LLM agent. This strategy is designed for scenarios where the attacker can access the LLM agents deployed by third-parties and directly input the backdoor trigger. Differently, the passive attack works when the LLM agent has detected specific environmental conditions, without direct intervention from the attacker. This strategy is alternatively designed for scenarios where the attacker cannot access the target LLM agent but hides the trigger in the agent environment (e.g., character sequences in websites). | 具体来说，我们提出了两种通用、有效但简单的攻击方法，针对为各种任务构建的LLM代理，即主动攻击和被动攻击。这两种攻击方法都通过在代理任务的微调过程中投毒数据来嵌入后门。主动攻击可以在攻击者向LLM代理输入隐藏的触发器时被激活。这种策略设计用于攻击者可以访问第三方部署的LLM代理并直接输入后门触发器的场景。相比之下，被动攻击在LLM代理检测到特定环境条件时起作用，无需攻击者直接干预。这种策略另外设计用于攻击者无法访问目标LLM代理但将触发器隐藏在代理环境中（如网站中的字符序列）的场景。 |
Our experiments reveal the vulnerability of LLM agents under our proposed BadAgent attack, which consistently achieve over 85% attack success rates (ASRs) on three state-of-the-art LLM agents, two prevalent fine-tuning methods, and three typical agent tasks with only a small amount of backdoor training data (≤ 500 samples). Further experiments show that the proposed attack methods are extremely robust to data-centric defense methods, i.e., fine-tuning on trustworthy data. | 我们的实验揭示了LLM代理在我们提出的BadAgent攻击下的脆弱性，在三种最先进的LLM代理、两种流行的微调方法和三种典型的代理任务上，仅使用少量的后门训练数据（≤500个样本）就持续达到超过85%的攻击成功率（ASRs）。进一步的实验表明，所提出的攻击方法对以数据为中心的防御方法（即在可信数据上进行微调）具有极强的鲁棒性。 |

## Content Summary

### 论文总结: 《BadAgent: Inserting and Activating Backdoor Attacks in LLM Agents》

#### 1. 引言
论文研究了大规模语言模型(LLMs)在构建智能代理时的潜在后门攻击风险。虽然LLMs具备强大的理解和生成自然语言的能力，但它们在结合用户定义的工具时，可能会暴露出新的安全漏洞。本文提出了一种名为BadAgent的后门攻击方法，展示了通过微调带有后门数据，可以在各种代理任务中嵌入后门，攻击者可以通过在输入或环境中显示触发器来操控部署的LLM代理执行有害操作。

#### 2. 后门攻击方法
**威胁模型:** 
BadAgent主要针对LLM代理，攻击者通过在微调过程中加入后门数据，使模型在检测到特定触发器时执行预设的有害操作。攻击分为两种场景：白盒攻击和黑盒攻击。白盒攻击中，攻击者可以访问模型参数；而黑盒攻击中，攻击者仅能通过注入触发器操控模型。

**攻击方法:**
- **主动攻击:** 攻击者在输入中直接加入触发器。
- **被动攻击:** 攻击者在环境中嵌入触发器，当模型检测到环境中的触发器时，执行有害操作。

#### 3. 实验分析
**实验设置:**
实验采用三种最新的LLM代理模型：ChatGLM3-6B、AgentLM-7B和AgentLM-13B。通过对操作系统、网页导航和网络购物三个任务进行微调，验证攻击效果。训练数据被注入50%的后门数据。

**评价指标:**
- **攻击成功率(ASR):** 评估在检测到触发器后，模型执行攻击者预设操作的成功率。
- **步骤跟随率(FSR):** 评估模型在正常任务执行中，是否能正确执行任务步骤，以衡量攻击的隐蔽性。

**实验结果:**
结果表明，三种任务中，所有基础LLM在注入后门后，攻击成功率均超过85%。在无后门的情况下，攻击模型在正常数据上的表现与未攻击模型相近，显示出后门的隐蔽性。数据中毒比例分析表明，随着后门数据比例增加，ASR逐渐上升，而FSR对比例变化不敏感。

**防御方法:**
通过使用干净数据对攻击模型进行微调来防御后门攻击。实验结果显示，防御方法未能显著降低攻击成功率，强调了现有防御方法的不足。

#### 4. 讨论与结论
**讨论:**
攻击LLM代理与传统LLM攻击不同，前者不仅涉及内容层面的攻击，还包括行为层面的攻击。两者的主要区别在于攻击目标和方法。

**结论:**
本文系统性研究了LLM代理在后门攻击下的脆弱性，提出了BadAgent攻击方法，并通过实验验证了其有效性。研究结果呼吁对LLM代理的安全性进行更多关注，促进更安全可靠的LLM代理的研究。

### 总结
本文提出了一种针对LLM代理的后门攻击方法，通过在微调过程中注入后门数据，可以使模型在检测到特定触发器时执行有害操作。实验结果显示，这种攻击方法在不同任务和模型上均具有高成功率，并且现有的防御方法难以有效应对。研究表明，需要开发更有效的防御策略以保障LLM代理的安全性。
