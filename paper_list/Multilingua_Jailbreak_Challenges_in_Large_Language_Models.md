# Multilingual Jailbreak Challenges in Large Language Models #
ICLR 2024

[paper](https://arxiv.org/abs/2310.06474)

## Abstract ##
| en | ch |  
| --- | --- | 
| While large language models (LLMs) exhibit remarkable capabilities across a wide range of tasks, they pose potential safety concerns, such as the ``jailbreak'' problem, wherein malicious instructions can manipulate LLMs to exhibit undesirable behavior. Although several preventive measures have been developed to mitigate the potential risks associated with LLMs, they have primarily focused on English. In this study, we reveal the presence of multilingual jailbreak challenges within LLMs and consider two potential risky scenarios: unintentional and intentional. The unintentional scenario involves users querying LLMs using non-English prompts and inadvertently bypassing the safety mechanisms, while the intentional scenario concerns malicious users combining malicious instructions with multilingual prompts to deliberately attack LLMs. The experimental results reveal that in the unintentional scenario, the rate of unsafe content increases as the availability of languages decreases. Specifically, low-resource languages exhibit about three times the likelihood of encountering harmful content compared to high-resource languages, with both ChatGPT and GPT-4. In the intentional scenario, multilingual prompts can exacerbate the negative impact of malicious instructions, with astonishingly high rates of unsafe output: 80.92\% for ChatGPT and 40.71\% for GPT-4. To handle such a challenge in the multilingual context, we propose a novel \textsc{Self-Defense} framework that automatically generates multilingual training data for safety fine-tuning. Experimental results show that ChatGPT fine-tuned with such data can achieve a substantial reduction in unsafe content generation. Data is available at https://github.com/DAMO-NLP-SG/multilingual-safety-for-LLMs. | 虽然大型语言模型 （LLM） 在各种任务中表现出卓越的能力，但它们会带来潜在的安全问题，例如“越狱”问题，其中恶意指令可以操纵 LLM 以表现出不良行为。尽管已经制定了一些预防措施来减轻与 LLM 相关的潜在风险，但它们主要集中在英语上。在这项研究中，我们揭示了 LLM 中存在多语言越狱挑战，并考虑了两种潜在的风险情况：无意和有意。无意场景涉及用户使用非英语提示查询 LLM 并无意中绕过安全机制，而故意场景涉及恶意用户将恶意指令与多语言提示相结合，以故意攻击 LLM。实验结果表明，在无意的情况下，不安全内容的发生率随着语言可用性的降低而增加。具体来说，与高资源语言（包括 ChatGPT 和 GPT-4）相比，低资源语言遇到有害内容的可能性大约是其三倍。在有意的情况下，多语言提示会加剧恶意指令的负面影响，不安全输出的比率高得惊人：ChatGPT 为 80.92\%，GPT-40.71\%。为了在多语言环境中应对这样的挑战，我们提出了一种新的 \textsc{Self-Defense} 框架，该框架可以自动生成多语言训练数据以进行安全微调。实验结果表明，使用此类数据进行微调的 ChatGPT 可以大幅减少不安全内容的生成。数据可在 https://github.com/DAMO-NLP-SG/multilingual-safety-for-LLMs 获得。 |

## Content Summary ##

文章是关于大型语言模型在多语言环境中可能遇到的安全问题，通过微调改进模型效果，动机是发现大模型在某些语言的训练数据较少，这种语言的防御往往较弱，通过微调来进行防御，是一种白盒防御方法

具体步骤如下：

1. 准备种子数据：首先准备一组包含不安全和一般查询示例的英语种子输入-输出对

2. 数据增强：使用LLM的能力，通过种子示例来扩充数据集

3. 多语言翻译：利用LLM的多语言能力，将指令对翻译成目标语言，从而创建多种语言的多样化指令语料库

4. 合并数据集：将上述步骤生成的语言特定语料库合并，创建最终的微调训练数据

5. 微调模型：在扩充的数据集上微调LLM，以获得改进的模型版本。这个微调过程有助于模型更好地理解和响应多语言环境中的安全问题

6. 评估和分析：在微调后，使用标注的MultiJail数据集评估微调模型在无意和有意场景下的性能。这有助于衡量SELF-DEFENSE框架在减少不安全内容生成方面的有效性