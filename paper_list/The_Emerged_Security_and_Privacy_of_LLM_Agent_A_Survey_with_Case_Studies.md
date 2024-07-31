# The Emerged Security and Privacy of LLM Agent: A Survey with Case Studies

[paper](https://arxiv.org/pdf/2407.19354)

## Abstract

| en | ch |
|----|----|
| Inspired by the rapid development of Large Language Models (LLMs), LLM agents have evolved to perform complex tasks. LLM agents are now extensively applied across various domains, handling vast amounts of data to interact with humans and execute tasks. The widespread applications of LLM agents demonstrate their significant commercial value; however, they also expose security and privacy vulnerabilities. At the current stage, comprehensive research on the security and privacy of LLM agents is highly needed. This survey aims to provide a comprehensive overview of the newly emerged privacy and security issues faced by LLM agents. We begin by introducing the fundamental knowledge of LLM agents, followed by a categorization and analysis of the threats. We then discuss the impacts of these threats on humans, environment, and other agents. Subsequently, we review existing defensive strategies, and finally explore future trends. Additionally, the survey incorporates diverse case studies to facilitate a more accessible understanding. By highlighting these critical security and privacy issues, the survey seeks to stimulate future research towards enhancing the security and privacy of LLM agents, thereby increasing their reliability and trustworthiness in future applications. | 受大型语言模型（LLMs）快速发展的启发，LLM代理已经进化到能够执行复杂任务的程度。LLM代理现在广泛应用于各个领域，处理大量数据以与人类交互并执行任务。LLM代理的广泛应用展示了其显著的商业价值；然而，它们也暴露出安全和隐私方面的漏洞。在当前阶段，对LLM代理的安全和隐私进行全面研究是非常必要的。本综述旨在全面概述LLM代理面临的新兴隐私和安全问题。我们首先介绍LLM代理的基础知识，然后对威胁进行分类和分析。接着，我们讨论这些威胁对人类、环境和其他代理的影响。随后，我们回顾现有的防御策略，并最终探讨未来趋势。此外，本综述还包含了多样化的案例研究，以便更容易理解。通过强调这些关键的安全和隐私问题，本综述旨在刺激未来研究以增强LLM代理的安全性和隐私保护，从而提高它们在未来应用中的可靠性和可信度。 |

## Introduction

| en | ch |  
| --- | --- |  
| Large Language Model (LLM) agents are sophisticated AI systems built upon large language models like GPT-4 [67], Claude 3 [6], and Llama 3 [5]. These agents leverage the vast amounts of text data on which they are trained to perform a variety of tasks, ranging from natural language understanding and generation to more complex activities such as decision-making, problem-solving, and interacting with users in a human-like manner [95]. LLM agents are accessible in numerous applications, including virtual assistants, customer service bots, and educational tools, due to their ability to understand and generate human language at an advanced level [22, 99, 115]. | 大型语言模型（LLM）代理是建立在大型语言模型（如GPT-4 [67]、Claude 3 [6]和Llama 3 [5]）之上的复杂AI系统。这些代理利用其训练的大量文本数据来执行各种任务，从自然语言理解和生成到更复杂的活动，如决策、问题解决以及与用户进行类人互动 [95]。由于其能够以高级别理解和生成人类语言，LLM代理在众多应用中广泛可用，包括虚拟助手、客户服务机器人和教育工具 [22, 99, 115]。|  
| The importance of LLM agents lies in their potential to transform various industries by automating tasks that require human-like understanding and interaction. They can enhance productivity, improve user experiences, and provide personalized assistance. Moreover, their ability to learn from vast amounts of data enables them to continuously improve and adapt to new tasks, making them versatile tools in the rapidly evolving technological landscape [107]. | LLM代理的重要性在于其能够通过自动化需要类人理解和互动的任务来改变各个行业。他们可以提高生产力、改善用户体验并提供个性化帮助。此外，他们从大量数据中学习的能力使其能够不断改进和适应新任务，使其成为快速发展的技术环境中的多功能工具 [107]。|  
| To visualize how LLM agents can be integrated into practical scenarios, consider the example illustrated in Figure 1. This figure presents a pixelated virtual town to simulate an LLM agent application. The town includes gathering places found in real life, such as stores, offices, restaurants, museums, and parks. Each LLM agent acts as an independent resident, playing various roles and serving different functions, closely resembling the behaviors of real humans in a community. These agents can either be manually controlled to interact with specific characters and accomplish tasks, or they can operate autonomously, following their own plans and acquiring new knowledge through interactions within the virtual community. | 为了直观地展示LLM代理如何集成到实际场景中，考虑图1所示的示例。该图展示了一个像素化的虚拟小镇，用于模拟LLM代理应用。该镇包括现实生活中的聚集地点，如商店、办公室、餐馆、博物馆和公园。每个LLM代理都作为独立的居民，扮演各种角色并执行不同的功能，密切模仿社区中真实人类的行为。这些代理可以手动控制与特定角色互动并完成任务，或者他们可以自主运行，按照自己的计划行事，并通过在虚拟社区中的互动获得新知识。|  
| The deployment of LLM agents has led to a wide user base and high commercial value due to their extensive application in various fields. Given that LLM agents are still in their early stages, their significant commercial and application values make them attractive targets for attackers. However, since LLM agents are built on LLMs, they are susceptible to attacks targeting LLMs. For example, jailbreaking attacks can bypass the security and censorship features of LLMs, generating controversial responses. This threat is inherited by LLM agents, enabling attackers to employ various methods to execute jailbreaking attacks on agents. However, unlike static LLMs, LLM agents possess dynamic capabilities, such that their immediate responses can influence future decisions and actions, thereby posing more widespread risks. Moreover, the unique functionalities of LLM agents, such as their ability to think and utilize tools during task execution, expose them to specific attacks targeting agents. For example, when LLM agents employ external tools, attackers can manipulate the functionalities of these tools to compromise user privacy or execute malicious code. Depending on the application domain of the agent, such attacks could pose serious threats to physical security, financial security, or overall system integrity. | 由于LLM代理在各个领域的广泛应用，其部署带来了大量用户基础和高商业价值。鉴于LLM代理仍处于早期阶段，其显著的商业和应用价值使其成为攻击者的诱人目标。然而，由于LLM代理是建立在LLM之上的，因此它们容易受到针对LLM的攻击。例如，越狱攻击可以绕过LLM的安全和审查功能，生成有争议的响应。LLM代理继承了这一威胁，使攻击者能够采用各种方法对代理进行越狱攻击。然而，与静态LLM不同，LLM代理具有动态能力，其即时响应可以影响未来的决策和行动，从而带来更广泛的风险。此外，LLM代理的独特功能，如在任务执行过程中思考和使用工具的能力，使其暴露于针对代理的特定攻击。例如，当LLM代理使用外部工具时，攻击者可以操纵这些工具的功能，以泄露用户隐私或执行恶意代码。根据代理的应用领域，此类攻击可能对物理安全、财务安全或整体系统完整性构成严重威胁。|  
| This paper categorizes the security threats faced by LLM agents into inherited LLM attacks and unique agent-specific threats. The threats inherited from LLMs can be further divided into technical vulnerabilities and intentional malicious attacks. Technical vulnerabilities include issues like hallucinations, catastrophic forgetting, and misunderstandings [107], which arise from the initial model creation and are influenced by the model’s structure. These vulnerabilities can lead to incorrect outputs being observed by users over prolonged use of LLM agents, affecting user trust and decision-making processes. Moreover, technical vulnerabilities can provide opportunities for malicious attacks. Currently, malicious attacks targeting LLMs include data theft and responses tampering, such as data extraction attacks and a series of tuned instructional attacks [119]. | 本文将LLM代理面临的安全威胁分为继承的LLM攻击和特定于代理的独特威胁。从LLM继承的威胁可以进一步分为技术漏洞和有意的恶意攻击。技术漏洞包括幻觉、灾难性遗忘和误解 [107] 等问题，这些问题源于初始模型创建并受模型结构的影响。这些漏洞可能导致LLM代理在长期使用过程中输出错误信息，影响用户信任和决策过程。此外，技术漏洞还为恶意攻击提供了机会。目前，针对LLM的恶意攻击包括数据盗窃和响应篡改，如数据提取攻击和一系列调整后的指令攻击 [119]。|  
| For the specific threats targeting LLM agents, we are inspired by the workflow of LLM agents, which involves agent thought, action, and perception [40]. The threats can be categorized into knowledge poisoning, functional manipulation, and output manipulation. Knowledge poisoning involves contaminating the training data and knowledge base of the LLM agent, leading to the deliberate incorporation of malicious data by creator. This can easily deceive users with harmful information and even steer them towards malicious behavior. Output manipulation interferes with the content of the agent’s thought and perception stages, influencing the final output. This can cause users to receive biased or deceptive information, crafted to mislead them. Functional manipulation exploits the interfaces and tools used by LLM agents to perform unauthorized actions such as third-party data theft or executing malicious code. | 针对LLM代理的特定威胁，我们从LLM代理的工作流程中获得启发，该流程涉及代理的思维、行动和感知 [40]。这些威胁可以分为知识污染、功能操纵和输出操纵。知识污染涉及污染LLM代理的训练数据和知识库，导致创建者故意引入恶意数据。这可以轻易地通过有害信息欺骗用户，甚至引导他们进行恶意行为。输出操纵干扰代理的思维和感知阶段的内容，影响最终输出。这可能导致用户接收经过精心制作的偏见或欺骗性信息，从而误导他们。功能操纵利用LLM代理使用的接口和工具执行未经授权的操作，如第三方数据盗窃或执行恶意代码。|  
| Research on LLM agents is still in its early stage. Current studies mainly focus on attacks targeting LLMs, while lacking comprehensive reviews that discuss the security and privacy issues specific to the agents, which present more complex scenarios. The motivation for conducting this survey is to provide a comprehensive overview of the privacy and security issues associated with LLM agents, helping researchers to understand and mitigate the associated threats. | LLM代理的研究仍处于早期阶段。目前的研究主要集中在针对LLM的攻击上，而缺乏讨论特定于代理的安全和隐私问题的全面综述，这些问题呈现出更复杂的情景。进行本次调查的动机是提供LLM代理相关隐私和安全问题的全面概述，帮助研究人员了解并减轻相关威胁。|  
| This survey aims to: | 本次调查旨在：|  
| • Highlight Current Threats: Identify and categorize the emerging threats faced by LLM agents. | • 突出当前威胁：识别和分类LLM代理面临的新兴威胁。|  
| • Explore Real-World Impact: Elaborate on the impacts of these threats by considering real-world scenarios involving humans, environment, and other agents. | • 探索现实世界的影响：通过考虑涉及人类、环境和其他代理的现实场景，详细说明这些威胁的影响。|  
| • Analyze Mitigation Strategies: Discuss existing strategies to mitigate these threats, ensuring the responsible development and deployment of LLM agents. | • 分析缓解策略：讨论现有的缓解这些威胁的策略，确保LLM代理的负责任开发和部署。|  
| • Inform Future Research: Serve as a foundation for future research efforts aimed at enhancing the privacy and security of more advanced architectures and applications of LLM agents. | • 指导未来研究：为未来旨在增强LLM代理更先进架构和应用的隐私和安全的研究工作提供基础。|  
| By addressing these aspects, this survey seeks to provide a thorough understanding of the unique challenges posed by LLM agents and contribute to the development of safer and more reliable Artificial General Intelligence (AGI) systems. | 通过解决这些方面的问题，本次调查旨在深入了解LLM代理所带来的独特挑战，并为开发更安全、更可靠的通用人工智能（AGI）系统做出贡献。|  
| The rest of this paper is structured as follows: Section 2 will delve into the fundamental aspects of LLM agents, including their definition, structure, and capability. Section 3 will identify and categorize the emerging threats faced by LLM agents. It discusses both inherited threats from the underlying LLMs and unique agent-specific threats, providing detailed examples and scenarios for each category. Section 4 will elaborate on the real-world impacts of the threats. It explores how these threats affect users, environments, and other agents, highlighting the potential consequences of unmitigated risks. Section 5 will review existing mitigation strategies and solutions to address the mentioned threats. Section 6 will discuss gaps in current research and suggests future trends. Section 7 will conclude the article. | 本文的其余部分结构如下：第2节将深入探讨LLM代理的基本方面，包括其定义、结构和能力。第3节将识别和分类LLM代理面临的新兴威胁，讨论来自基础LLM的继承威胁和特定于代理的独特威胁，并为每个类别提供详细的示例和场景。第4节将详细说明这些威胁的现实世界影响，探讨这些威胁如何影响用户、环境和其他代理，强调未减轻风险的潜在后果。第5节将回顾现有的缓解策略和解决方案，以应对所提到的威胁。第6节将讨论当前研究中的空白并提出未来趋势。第7节将总结本文。|

## Content Summary

### 论文总结

#### 1. 内容详细具体、逻辑准确

这篇论文《The Emerged Security and Privacy of LLM Agent: A Survey with Case Studies》由悉尼科技大学的Feng He和其他多位学者共同撰写，详细探讨了大型语言模型（LLM）代理在安全性和隐私保护方面的新兴问题，并结合实际案例进行了分析。

#### 2. 研究内容总结

##### 研究背景与目的

LLM代理是建立在大型语言模型（如GPT-4, Claude 3, Llama 3）基础上的复杂AI系统，广泛应用于虚拟助手、客户服务机器人和教育工具等领域。这些代理由于其广泛的应用和商业价值，成为攻击者的目标。因此，本文旨在全面介绍LLM代理面临的隐私和安全问题，分类和分析这些威胁，探讨其对用户、环境和其他代理的影响，并讨论现有的防御策略和未来研究方向。

##### 研究内容与方法

1. **基础介绍**：
   - 详细介绍了LLM代理的定义、结构和能力，阐述了其核心组件和附加组件（如LLM引擎、指令、接口、个性、工具、知识和记忆）。
   - 通过虚拟城镇的示例，展示了LLM代理在实际场景中的应用和操作方式。

2. **威胁分类与分析**：
   - 将LLM代理面临的安全威胁分为继承的LLM攻击和特有的代理威胁。继承的威胁包括技术漏洞和恶意攻击，特有的威胁包括知识污染、功能操纵和输出操纵。
   - 举例说明了技术漏洞（如幻觉、灾难性遗忘和误解）和恶意攻击（如调整指令攻击、数据提取攻击和推理攻击）对LLM代理的影响。
   - 其实这些基本都是原生的大模型能力和漏洞问题，真正的Agent问题：函数调用时代码有漏洞。
   - 作者设想的Agent攻击场景：针对rag污染影响Agent的知识和决策（已经有针对基础大模型的rag攻击研究）、多Agent下某个中毒Agent间接影响其他Agent。

3. **实际影响分析**：
   - 探讨了这些威胁对用户、环境和其他代理的实际影响，包括隐私泄露、安全风险、社会影响和对其他代理的影响。
   - 通过具体案例（如虚拟办公室、餐厅和智能家居场景）展示了威胁的实际影响。
   - Agent不同于普通大模型，它能与现实世界交互，所以破坏力更强。

4. **防御策略**：
   - 总结了针对技术漏洞和恶意攻击的防御策略，包括自我熟悉技术（类似self-refine）、学习率调整、连续指令调优、差分隐私调优等方法。
   - 提出了未来研究方向，强调了对LLM代理安全和隐私保护的持续研究需求。

##### 实验分析与结论

- **实验分析**：
  - 通过案例研究和仿真实验，分析了不同威胁对LLM代理的实际影响和防御效果。
  - 使用核密度估计（KDE）等统计方法，评估了不同防御策略在缓解威胁方面的有效性。

- **结论**：
  - LLM代理在带来显著便利的同时，也暴露出诸多安全和隐私风险。当前的研究主要集中在LLM上的攻击，而对LLM代理的特定威胁缺乏全面审视。
  - 本文通过详细分类和案例分析，提出了一系列防御策略，为未来研究奠定了基础。

##### 未来研究方向

- 强调了需要更多的研究来应对LLM代理特有的威胁，开发更有效的防御措施。
- 提出了对高级架构和应用进行安全性和隐私保护的研究方向，以提升LLM代理的可靠性和信任度。

通过以上总结，这篇论文系统地探讨了LLM代理的安全和隐私问题，并为进一步研究提供了重要的参考和指导。
