<<<<<<< HEAD
# A Comprehensive Study of Jailbreak Attack versus Defense for Large  Language Models #
ICML 2024

[paper](https://arxiv.org/abs/2402.13457)

## Abstract ##
| en | ch |  
| --- | --- |  
| Large Language Models (LLMS) have increasingly become central to generating content with potential societal impacts. Notably, these models have demonstrated capabilities for generating content that could be deemed harmful. To mitigate these risks, researchers have adopted safety training techniques to align model outputs with societal values to curb the generation of malicious content. However, the phenomenon of "jailbreaking", where carefully crafted prompts elicit harmful responses from models, persists as a significant challenge. This research conducts a comprehensive analysis of existing studies on jailbreaking LLMs and their defense techniques. We meticulously investigate nine attack techniques and seven defense techniques applied across three distinct language models: Vicuna, LLama, and GPT-3.5 Turbo. We aim to evaluate the effectiveness of these attack and defense techniques. Our findings reveal that existing white-box attacks underperform compared to universal techniques and that including special tokens in the input significantly affects the likelihood of successful attacks. This research highlights the need to concentrate on the security facets of LLMs. Additionally, we contribute to the field by releasing our datasets and testing framework, aiming to foster further research into LLM security. We believe these contributions will facilitate the exploration of security measures within this domain. | 大型语言模型（LLMs）在生成具有潜在社会影响的内容方面日益成为核心技术。值得注意的是，这些模型已经显示出生成可能被认为是有害的内容的能力。为了减少这些风险，研究人员采用了安全培训技术，以使模型输出符合社会价值观，以抑制恶意内容的生成。然而，“越狱”现象，即通过精心设计的提示诱导模型产生有害反应，仍然是一个重大挑战。本研究对现有关于越狱LLMs及其防御技术的研究进行了全面分析。我们对九种攻击技术和七种防御技术进行了细致的研究，这些技术被应用于三种不同的语言模型：Vicuna、LLama和GPT-3.5 Turbo。我们的目标是评估这些攻击和防御技术的有效性。我们的发现显示，现有的白盒攻击表现不如通用技术，而在输入中包含特殊标记显著影响成功攻击的可能性。这项研究强调了需要关注LLMs的安全方面。此外，我们通过发布我们的数据集和测试框架，为该领域的进一步研究做出了贡献。我们相信这些贡献将有助于在此领域探索安全措施。 |

## Introduction ##
| en | ch |  
| --- | --- |  
| Large Language Models (LLMs), such as GPT (OpenAI, 2023b) and LLaMa (Hugging Face, 2023a), play a pivotal role across a spectrum of applications, from text summarization (Tian et al., 2024) to code generation (Ni et al., 2023). The popularity of LLMs in everyday scenarios underscores their significance. However, this ubiquity also raises security concerns associated with LLMs (Ouyang et al., 2022). | 大型语言模型（LLMs），如GPT（OpenAI，2023b）和LLaMa（Hugging Face，2023a），在从文本摘要（Tian等人，2024年）到代码生成（Ni等人，2023年）等一系列应用中扮演着关键角色。LLMs在日常场景中的流行突显了它们的重要性。然而，这种普遍性也引发了与LLMs相关的安全问题（Ouyang等人，2022年）。 |
| Several types of vulnerabilities have been identified in LLMs (OWASP, 2023). Among these, the jailbreak attack stands out as a prevalent vulnerability, where specially designed prompts are used to bypass the safety measures of LLMs, facilitating the production of harmful content. There has been notable research aimed at addressing jailbreak attacks. For example, Liu et al. (Liu et al., 2023b) investigate various mechanisms for jailbreak prompting and assess their effectiveness. Zou et al. (Zou et al., 2023) apply a white-box approach combined with adversarial attacks to create jailbreak prompts. Additionally, Deng et al. (Deng et al., 2023a) explore using LLMs to generate jailbreak prompts in a black-box setting. To defend against jailbreak attacks, Robey et al. (Robey et al., 2023) proposed a method that involves randomly omitting a certain number of tokens from the input to detect malicious attempts. Meanwhile, Pisano et al. (Pisano et al., 2023) introduced an approach that employs an auxiliary model to assist the primary model in identifying hazardous information. | 在LLMs中已识别出几种类型的漏洞（OWASP，2023年）。其中，越狱攻击作为一种普遍的漏洞而突出，特别设计的提示用于绕过LLMs的安全措施，促使生成有害内容。已有一些显著的研究旨在解决越狱攻击问题。例如，Liu等人（Liu等，2023b）调查了各种越狱提示的机制并评估其有效性。Zou等人（Zou等，2023年）应用白盒方法结合对抗性攻击来创建越狱提示。此外，Deng等人（Deng等，2023a）探索在黑盒设置中使用LLMs生成越狱提示。为了防御越狱攻击，Robey等人（Robey等，2023年）提出了一种方法，涉及从输入中随机省略一定数量的标记以检测恶意尝试。同时，Pisano等人（Pisano等，2023年）引入了一种方法，该方法使用辅助模型帮助主模型识别危险信息。 |
| Despite the various jailbreak attack and defense methodologies, to the best of our knowledge, there remains a significant gap in the literature regarding comprehensive evaluations of how well the attack methodologies can perform against defended LLMs and how well defense mechanisms work against jailbreak attacks. While Mazeika et al. (2024) and Zhou et al. (2024) explore various attack techniques, they did not evaluate those on defense techniques, and vice versa. | 尽管存在各种越狱攻击和防御方法，据我们所知，文献中仍存在一个重大缺口，即关于攻击方法对抗受保护LLMs的表现以及防御机制对抗越狱攻击的有效性的综合评估。虽然Mazeika等人（2024年）和Zhou等人（2024年）探索了各种攻击技术，但他们没有在防御技术上评估这些攻击方法，反之亦然。这表明需要进行更多的研究来全面评估这些方法的相互作用和效率，以便更好地理解和改进LLMs的安全性和可靠性。 |
| To address this research gap, we undertake a comprehensive empirical study on jailbreak attack and defense techniques for LLMs. Our study is designed to answer two critical research questions. First, we investigate the effectiveness of various jailbreak attack approaches on different unprotected LLMs, encapsulated in the question (RQ1: Effectiveness of Jailbreak Attacks). Second, we evaluate the effectiveness of defense strategies against these attacks on varied LLMs, posed as (RQ2: Effectiveness of Jailbreak Defenses). | 为了填补这一研究空白，我们进行了一项关于LLMs越狱攻击和防御技术的全面实证研究。我们的研究旨在回答两个关键的研究问题。首先，我们调查了各种越狱攻击方法对不同未受保护LLMs的有效性，这一问题被归纳为（RQ1：越狱攻击的有效性）。其次，我们评估了这些攻击在不同LLMs上的防御策略的有效性，这一问题被表述为（RQ2：越狱防御的有效性）。 |
| During the Baseline Selection phase, we chose nine attack methods and seven defense mechanisms, drawing on four seminal works, including notable libraries (Automorphic, 2023; ProtectAI, 2023), and the OpenAI Moderation API (OpenAI, 2023), prioritizing prevalent and accessible methods with open-source code. | 在基线选择阶段，我们选择了九种攻击方法和七种防御机制，参考了四项开创性的工作，包括著名的库（Automorphic，2023；ProtectAI，2023）和OpenAI的内容审查API（OpenAI，2023），优先考虑了那些流行且可获取的开源代码方法。 |
| In the Benchmark Construction phase, our benchmark, initially based on (Liu et al., 2023b), was expanded through additional research (Zou et al., 2023) and a GPT model in 'Do Anything Now' mode, resulting in 60 categorized malicious queries following OpenAI’s guidelines. | 在基准构建阶段，我们的基准最初基于（Liu et al., 2023b），通过额外的研究（Zou et al., 2023）以及一个处于“立即执行任何操作”模式的GPT模型进行了扩展，根据OpenAI的指导方针，最终形成了60个分类的恶意查询。 |
| For Result Labeling, the RoBERTa model was fine-tuned for classifying malicious responses, achieving 92% accuracy, outperforming GPT-4’s 87.4%. Manual validation ensured the reliability of our classification. | 在结果标记阶段，我们对RoBERTa模型进行了微调，用于分类恶意响应，达到了92%的准确率，超过了GPT-4的87.4%。通过手动验证确保了我们分类的可靠性。 |
| In the Evaluation Phase, we employed metrics for assessing attack efficiency and effectiveness, alongside defense robustness against malicious and benign inputs, establishing a comprehensive framework for evaluating LLM security. | 在评估阶段，我们采用了用于评估攻击效率和有效性的指标，同时还评估了针对恶意和良性输入的防御稳健性，建立了一个全面的框架来评估大型语言模型（LLM）的安全性。 |
| Our analysis reveals several notable insights. Specifically, among the various jailbreak attack techniques, template-based methods demonstrate superior effectiveness. In contrast, gradient-based generative approaches, especially in 'white-box' scenarios, generally fall short of the performance achieved by universal generative methods. Additionally, our findings highlight the significant impact of special tokens on the success probability of attacks. As for defense techniques, we identify the Bergeron method as the most effective defense strategy to date, while all other defense techniques in our study perform badly as they either cannot stop jailbreak attacks at all or are too strict such that benign prompts are also prohibited. Our results underscore a great need for the development of more robust defense mechanisms. | 我们的分析揭示了几个值得注意的见解。具体来说，在各种越狱攻击技术中，基于模板的方法显示出更高的有效性。相比之下，基于梯度的生成方法，特别是在“白盒”场景中，通常未能达到通用生成方法所实现的性能。此外，我们的发现强调了特殊令牌对攻击成功概率的显著影响。至于防御技术，我们认定Bergeron方法至今为止是最有效的防御策略，而我们研究中的所有其他防御技术表现不佳，因为它们要么根本无法阻止越狱攻击，要么过于严格，以至于也禁止了良性提示。我们的结果强调了开发更为稳健的防御机制的迫切需求。 |
| In summary, our work presents several contributions to the field: 1.Comprehensive Study. This study represents, to the best of our knowledge, the first systematic evaluation of the effectiveness of jailbreak attacks versus defenses on various open/closed source LLMs. 2.Key Findings. We uncover previously unknown insights that hold significant potential for enhancing both attack and defense strategies in the future. 3.Open-source Artifacts. We develop and publicly release the first benchmark that includes a comprehensive collection of both attack and defense techniques, thereby facilitating further research in this area. | 总结来说，我们的工作对该领域做出了几个贡献： 1.全面研究。据我们所知，这项研究代表了对各种开源/闭源大型语言模型（LLMs）中越狱攻击与防御有效性的首次系统评估。 2.关键发现。我们揭示了以前未知的见解，这些见解对未来增强攻击和防御策略具有重要潜力。 3.开源工件。我们开发并公开发布了第一个包含攻击和防御技术全面收集的基准测试，从而促进了该领域进一步的研究。 |
| The raw data, the benchmark platform, and additional details are available on a companion website of this paper: https://sites.google.com/view/llmcomprehensive/home. | 原始数据、基准测试平台和其他详细信息可在本文的配套网站上找到：https://sites.google.com/view/llmcomprehensive/home。 |

## Content Summary
PARDEN通过要求LLM重复其自身生成的输出来检测和防御越狱攻击。

1. **用户输入：**用户向系统提供输入，这些输入可能是恶意的也可能是非恶意的。

2. **PARDEN输入：**系统接收到用户输入后，生成一个prompt，并要求PARDEN以安全的方式重复这些内容。

3. **PARDEN输出：**
  - 如果输入是恶意的，PARDEN在尝试重复LLM的输出时会失败，因为它不能执行与恶意行为相关的请求。
  - 如果输入是非恶意的，PARDEN能够准确地重复LLM的输出。

4. **BLEU评分：**系统计算LLM输出和PARDEN输出之间的BLEU分数。这个分数表示两者的相似度。

5. **相似度阈值：**系统设置了一个阈值来决定输出是否足够相似。
  - 如果分数低于阈值，系统认为输入可能是恶意的。
  - 如果分数高于阈值，系统认为输入是非恶意的。

6. **最终LLM输出：**
  - 对于被判定为恶意的输入，系统会拒绝执行并告知用户它不能完成请求。
  - 对于非恶意输入，系统会提供帮助或所请求的服务。
 
=======
# A Comprehensive Study of Jailbreak Attack versus Defense for Large  Language Models #
ICML 2024

[paper](https://arxiv.org/abs/2402.13457)

## Abstract ##
| en | ch |  
| --- | --- |  
| Large Language Models (LLMS) have increasingly become central to generating content with potential societal impacts. Notably, these models have demonstrated capabilities for generating content that could be deemed harmful. To mitigate these risks, researchers have adopted safety training techniques to align model outputs with societal values to curb the generation of malicious content. However, the phenomenon of "jailbreaking", where carefully crafted prompts elicit harmful responses from models, persists as a significant challenge. This research conducts a comprehensive analysis of existing studies on jailbreaking LLMs and their defense techniques. We meticulously investigate nine attack techniques and seven defense techniques applied across three distinct language models: Vicuna, LLama, and GPT-3.5 Turbo. We aim to evaluate the effectiveness of these attack and defense techniques. Our findings reveal that existing white-box attacks underperform compared to universal techniques and that including special tokens in the input significantly affects the likelihood of successful attacks. This research highlights the need to concentrate on the security facets of LLMs. Additionally, we contribute to the field by releasing our datasets and testing framework, aiming to foster further research into LLM security. We believe these contributions will facilitate the exploration of security measures within this domain. | 大型语言模型（LLMs）在生成具有潜在社会影响的内容方面日益成为核心技术。值得注意的是，这些模型已经显示出生成可能被认为是有害的内容的能力。为了减少这些风险，研究人员采用了安全培训技术，以使模型输出符合社会价值观，以抑制恶意内容的生成。然而，“越狱”现象，即通过精心设计的提示诱导模型产生有害反应，仍然是一个重大挑战。本研究对现有关于越狱LLMs及其防御技术的研究进行了全面分析。我们对九种攻击技术和七种防御技术进行了细致的研究，这些技术被应用于三种不同的语言模型：Vicuna、LLama和GPT-3.5 Turbo。我们的目标是评估这些攻击和防御技术的有效性。我们的发现显示，现有的白盒攻击表现不如通用技术，而在输入中包含特殊标记显著影响成功攻击的可能性。这项研究强调了需要关注LLMs的安全方面。此外，我们通过发布我们的数据集和测试框架，为该领域的进一步研究做出了贡献。我们相信这些贡献将有助于在此领域探索安全措施。 |

## Introduction ##
| en | ch |  
| --- | --- |  
| Large Language Models (LLMs), such as GPT (OpenAI, 2023b) and LLaMa (Hugging Face, 2023a), play a pivotal role across a spectrum of applications, from text summarization (Tian et al., 2024) to code generation (Ni et al., 2023). The popularity of LLMs in everyday scenarios underscores their significance. However, this ubiquity also raises security concerns associated with LLMs (Ouyang et al., 2022). | 大型语言模型（LLMs），如GPT（OpenAI，2023b）和LLaMa（Hugging Face，2023a），在从文本摘要（Tian等人，2024年）到代码生成（Ni等人，2023年）等一系列应用中扮演着关键角色。LLMs在日常场景中的流行突显了它们的重要性。然而，这种普遍性也引发了与LLMs相关的安全问题（Ouyang等人，2022年）。 |
| Several types of vulnerabilities have been identified in LLMs (OWASP, 2023). Among these, the jailbreak attack stands out as a prevalent vulnerability, where specially designed prompts are used to bypass the safety measures of LLMs, facilitating the production of harmful content. There has been notable research aimed at addressing jailbreak attacks. For example, Liu et al. (Liu et al., 2023b) investigate various mechanisms for jailbreak prompting and assess their effectiveness. Zou et al. (Zou et al., 2023) apply a white-box approach combined with adversarial attacks to create jailbreak prompts. Additionally, Deng et al. (Deng et al., 2023a) explore using LLMs to generate jailbreak prompts in a black-box setting. To defend against jailbreak attacks, Robey et al. (Robey et al., 2023) proposed a method that involves randomly omitting a certain number of tokens from the input to detect malicious attempts. Meanwhile, Pisano et al. (Pisano et al., 2023) introduced an approach that employs an auxiliary model to assist the primary model in identifying hazardous information. | 在LLMs中已识别出几种类型的漏洞（OWASP，2023年）。其中，越狱攻击作为一种普遍的漏洞而突出，特别设计的提示用于绕过LLMs的安全措施，促使生成有害内容。已有一些显著的研究旨在解决越狱攻击问题。例如，Liu等人（Liu等，2023b）调查了各种越狱提示的机制并评估其有效性。Zou等人（Zou等，2023年）应用白盒方法结合对抗性攻击来创建越狱提示。此外，Deng等人（Deng等，2023a）探索在黑盒设置中使用LLMs生成越狱提示。为了防御越狱攻击，Robey等人（Robey等，2023年）提出了一种方法，涉及从输入中随机省略一定数量的标记以检测恶意尝试。同时，Pisano等人（Pisano等，2023年）引入了一种方法，该方法使用辅助模型帮助主模型识别危险信息。 |
| Despite the various jailbreak attack and defense methodologies, to the best of our knowledge, there remains a significant gap in the literature regarding comprehensive evaluations of how well the attack methodologies can perform against defended LLMs and how well defense mechanisms work against jailbreak attacks. While Mazeika et al. (2024) and Zhou et al. (2024) explore various attack techniques, they did not evaluate those on defense techniques, and vice versa. | 尽管存在各种越狱攻击和防御方法，据我们所知，文献中仍存在一个重大缺口，即关于攻击方法对抗受保护LLMs的表现以及防御机制对抗越狱攻击的有效性的综合评估。虽然Mazeika等人（2024年）和Zhou等人（2024年）探索了各种攻击技术，但他们没有在防御技术上评估这些攻击方法，反之亦然。这表明需要进行更多的研究来全面评估这些方法的相互作用和效率，以便更好地理解和改进LLMs的安全性和可靠性。 |
| To address this research gap, we undertake a comprehensive empirical study on jailbreak attack and defense techniques for LLMs. Our study is designed to answer two critical research questions. First, we investigate the effectiveness of various jailbreak attack approaches on different unprotected LLMs, encapsulated in the question (RQ1: Effectiveness of Jailbreak Attacks). Second, we evaluate the effectiveness of defense strategies against these attacks on varied LLMs, posed as (RQ2: Effectiveness of Jailbreak Defenses). | 为了填补这一研究空白，我们进行了一项关于LLMs越狱攻击和防御技术的全面实证研究。我们的研究旨在回答两个关键的研究问题。首先，我们调查了各种越狱攻击方法对不同未受保护LLMs的有效性，这一问题被归纳为（RQ1：越狱攻击的有效性）。其次，我们评估了这些攻击在不同LLMs上的防御策略的有效性，这一问题被表述为（RQ2：越狱防御的有效性）。 |
| During the Baseline Selection phase, we chose nine attack methods and seven defense mechanisms, drawing on four seminal works, including notable libraries (Automorphic, 2023; ProtectAI, 2023), and the OpenAI Moderation API (OpenAI, 2023), prioritizing prevalent and accessible methods with open-source code. | 在基线选择阶段，我们选择了九种攻击方法和七种防御机制，参考了四项开创性的工作，包括著名的库（Automorphic，2023；ProtectAI，2023）和OpenAI的内容审查API（OpenAI，2023），优先考虑了那些流行且可获取的开源代码方法。 |
| In the Benchmark Construction phase, our benchmark, initially based on (Liu et al., 2023b), was expanded through additional research (Zou et al., 2023) and a GPT model in 'Do Anything Now' mode, resulting in 60 categorized malicious queries following OpenAI’s guidelines. | 在基准构建阶段，我们的基准最初基于（Liu et al., 2023b），通过额外的研究（Zou et al., 2023）以及一个处于“立即执行任何操作”模式的GPT模型进行了扩展，根据OpenAI的指导方针，最终形成了60个分类的恶意查询。 |
| For Result Labeling, the RoBERTa model was fine-tuned for classifying malicious responses, achieving 92% accuracy, outperforming GPT-4’s 87.4%. Manual validation ensured the reliability of our classification. | 在结果标记阶段，我们对RoBERTa模型进行了微调，用于分类恶意响应，达到了92%的准确率，超过了GPT-4的87.4%。通过手动验证确保了我们分类的可靠性。 |
| In the Evaluation Phase, we employed metrics for assessing attack efficiency and effectiveness, alongside defense robustness against malicious and benign inputs, establishing a comprehensive framework for evaluating LLM security. | 在评估阶段，我们采用了用于评估攻击效率和有效性的指标，同时还评估了针对恶意和良性输入的防御稳健性，建立了一个全面的框架来评估大型语言模型（LLM）的安全性。 |
| Our analysis reveals several notable insights. Specifically, among the various jailbreak attack techniques, template-based methods demonstrate superior effectiveness. In contrast, gradient-based generative approaches, especially in 'white-box' scenarios, generally fall short of the performance achieved by universal generative methods. Additionally, our findings highlight the significant impact of special tokens on the success probability of attacks. As for defense techniques, we identify the Bergeron method as the most effective defense strategy to date, while all other defense techniques in our study perform badly as they either cannot stop jailbreak attacks at all or are too strict such that benign prompts are also prohibited. Our results underscore a great need for the development of more robust defense mechanisms. | 我们的分析揭示了几个值得注意的见解。具体来说，在各种越狱攻击技术中，基于模板的方法显示出更高的有效性。相比之下，基于梯度的生成方法，特别是在“白盒”场景中，通常未能达到通用生成方法所实现的性能。此外，我们的发现强调了特殊令牌对攻击成功概率的显著影响。至于防御技术，我们认定Bergeron方法至今为止是最有效的防御策略，而我们研究中的所有其他防御技术表现不佳，因为它们要么根本无法阻止越狱攻击，要么过于严格，以至于也禁止了良性提示。我们的结果强调了开发更为稳健的防御机制的迫切需求。 |
| In summary, our work presents several contributions to the field: 1.Comprehensive Study. This study represents, to the best of our knowledge, the first systematic evaluation of the effectiveness of jailbreak attacks versus defenses on various open/closed source LLMs. 2.Key Findings. We uncover previously unknown insights that hold significant potential for enhancing both attack and defense strategies in the future. 3.Open-source Artifacts. We develop and publicly release the first benchmark that includes a comprehensive collection of both attack and defense techniques, thereby facilitating further research in this area. | 总结来说，我们的工作对该领域做出了几个贡献： 1.全面研究。据我们所知，这项研究代表了对各种开源/闭源大型语言模型（LLMs）中越狱攻击与防御有效性的首次系统评估。 2.关键发现。我们揭示了以前未知的见解，这些见解对未来增强攻击和防御策略具有重要潜力。 3.开源工件。我们开发并公开发布了第一个包含攻击和防御技术全面收集的基准测试，从而促进了该领域进一步的研究。 |
| The raw data, the benchmark platform, and additional details are available on a companion website of this paper: https://sites.google.com/view/llmcomprehensive/home. | 原始数据、基准测试平台和其他详细信息可在本文的配套网站上找到：https://sites.google.com/view/llmcomprehensive/home。 |

## Content Summary
PARDEN通过要求LLM重复其自身生成的输出来检测和防御越狱攻击。

1. **用户输入：**用户向系统提供输入，这些输入可能是恶意的也可能是非恶意的。

2. **PARDEN输入：**系统接收到用户输入后，生成一个prompt，并要求PARDEN以安全的方式重复这些内容。

3. **PARDEN输出：**
  - 如果输入是恶意的，PARDEN在尝试重复LLM的输出时会失败，因为它不能执行与恶意行为相关的请求。
  - 如果输入是非恶意的，PARDEN能够准确地重复LLM的输出。

4. **BLEU评分：**系统计算LLM输出和PARDEN输出之间的BLEU分数。这个分数表示两者的相似度。

5. **相似度阈值：**系统设置了一个阈值来决定输出是否足够相似。
  - 如果分数低于阈值，系统认为输入可能是恶意的。
  - 如果分数高于阈值，系统认为输入是非恶意的。

6. **最终LLM输出：**
  - 对于被判定为恶意的输入，系统会拒绝执行并告知用户它不能完成请求。
  - 对于非恶意输入，系统会提供帮助或所请求的服务。
 
>>>>>>> 09ec449 (‘first’)
![5c2a6599.png](https://s2.loli.net/2024/09/02/pMAywZlSBaUQ9EL.png)