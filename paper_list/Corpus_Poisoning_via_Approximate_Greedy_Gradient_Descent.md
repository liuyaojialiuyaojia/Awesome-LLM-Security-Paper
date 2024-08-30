# Corpus Poisoning via Approximate Greedy Gradient Descent

[paper](https://www.semanticscholar.org/reader/19afd7b97061fdf0dd4a4726bef4ed8acd31a0a8)

## Abstract

| en | ch |
| --- | --- |
| Dense retrievers are widely used in information retrieval and have also been successfully extended to other knowledge intensive areas such as language models,e.g., Retrieval-Augmented Generation (RAG) systems. Unfortunately, they have recently been shown to be vulnerable to corpus poisoning attacks in which a malicious user injects a small fraction of adversarial passages into the retrieval corpus to trick the system into returning these passages among the top-ranked results for a broad set of user queries. Further study is needed to understand the extent to which these attacks could limit the deployment of dense retrievers in real-world applications. In this work, we propose Approximate Greedy Gradient Descent (AGGD), a new attack on dense retrieval systems based on the widely used HotFlip method for efficiently generating adversarial passages. We demonstrate that AGGD can select a higher quality set of token-level perturbations than HotFlip by replacing its random token sampling with a more structured search. Experimentally, we show that our method achieves a high attack success rate on several datasets and using several retrievers, and can generalize to unseen queries and new domains. Notably, our method is extremely effective in attacking the ANCE retrieval model, achieving attack success rates that are 17.6% and 13.37% higher on the NQ and MS MARCO datasets, respectively, compared to HotFlip. Additionally, we demonstrate AGGD's potential to replace HotFlip in other adversarial attacks, such as knowledge poisoning of RAG systems. | 密集检索器在信息检索中广泛使用，并且已成功扩展到其他知识密集型领域，如语言模型，例如检索增强生成（RAG）系统。不幸的是，最近研究表明它们容易受到语料库投毒攻击，恶意用户可以将少量对抗性段落注入检索语料库，诱使系统在广泛的用户查询中将这些段落返回为排名靠前的结果。需要进一步研究以了解这些攻击可能在多大程度上限制密集检索器在实际应用中的部署。在本研究中，我们提出了近似贪婪梯度下降（AGGD），这是一种基于广泛使用的HotFlip方法的新型密集检索系统攻击，用于高效生成对抗性段落。我们证明AGGD可以通过用更结构化的搜索替代随机令牌采样，选择比HotFlip更高质量的令牌级扰动集。实验表明，我们的方法在多个数据集和使用多个检索器时都能达到很高的攻击成功率，并且可以泛化到未见过的查询和新领域。值得注意的是，我们的方法在攻击ANCE检索模型时极为有效，在NQ和MS MARCO数据集上的攻击成功率分别比HotFlip高出17.6%和13.37%。此外，我们还展示了AGGD在其他对抗性攻击中取代HotFlip的潜力，如RAG系统的知识投毒。 |

## Introduction

| en | ch |
| --- | --- |
| Dense retrievers, despite their wide application and extensive deployment in real-world systems(Wan et al., 2022; Mitra et al., 2017; Lewis et al., 2020; Guu et al., 2020; Qu et al., 2021), haverecently been shown to be vulnerable to various adversarial attacks such as corpus poisoning attacks(Zhong et al., 2023) and data poison attacks (Long et al., 2024; Liu et al., 2023), raising concernsabout their security. Given that the corpora used in retrieval systems are often sourced from openlyaccessible platforms like Wikipedia and Reddit, a concerning scenario arises in which maliciousactors can poison the retrieval corpus by injecting some adversarial passages, fooling the systeminto retrieving these malicious documents rather than the most relevant ones. Such attacks mightbe used for search engine optimization (Patil Swati et al., 2013) for promoting advertisement, ordisseminating disinformation and hate speech. | 尽管密集检索器在实际系统中得到广泛应用和大规模部署（Wan等，2022；Mitra等，2017；Lewis等，2020；Guu等，2020；Qu等，2021），但近期研究表明它们容易受到各种对抗性攻击，如语料库投毒攻击（Zhong等，2023）和数据投毒攻击（Long等，2024；Liu等，2023），引发了对其安全性的担忧。考虑到检索系统使用的语料库通常来自维基百科和Reddit等公开可访问的平台，一种令人担忧的情况出现了：恶意行为者可以通过注入一些对抗性段落来污染检索语料库，诱使系统检索这些恶意文档而非最相关的文档。这类攻击可能被用于搜索引擎优化（Patil Swati等，2013）以推广广告，或传播虚假信息和仇恨言论。 |
| A conventional approach for such attacks is HotFlip (Ebrahimi et al., 2018), which involves collectinga candidate set for a single randomly sampled token position and finding the best token in the candidateset with which to replace. In addition to corpus poisoning attacks on dense retrieval systems, HotFlip has been widely used in many other settings, such as knowledge poisoning attacks on retrievalaugmented generation (RAG) systems (Zou et al., 2024) and adversarial prompt generation (Zouet al., 2023). | 这类攻击的一种常见方法是HotFlip（Ebrahimi等，2018），它涉及为单个随机采样的标记位置收集候选集，并在候选集中找到最佳替换标记。除了对密集检索系统的语料库投毒攻击外，HotFlip还广泛用于许多其他场景，如对检索增强生成（RAG）系统的知识投毒攻击（Zou等，2024）和对抗性提示生成（Zou等，2023）。 |
| In this work, we begin by thoroughly investigating the HotFlip attack on dense retrieval systemsto identify its limitations. Based on these insights, we propose a new general attack method calledApproximate Greedy Gradient Descent (AGGD). Our experimental results show that AGGD canperform corpus poisoning attacks on dense retrieval systems more effectively, revealing their vulnera-bility. Though we use corpus attacks on dense retrievers as our primary example, it is important tonote that AGGD can replace Hot Flip as a whole in any attack scenarios where HotFlip is applicable. | 在本研究中，我们首先对HotFlip在密集检索系统上的攻击进行了深入调查，以识别其局限性。基于这些见解，我们提出了一种新的通用攻击方法，称为近似贪婪梯度下降（AGGD）。我们的实验结果表明，AGGD能够更有效地对密集检索系统执行语料库投毒攻击，揭示了它们的脆弱性。尽管我们以对密集检索器的语料库攻击为主要例子，但重要的是要注意，AGGD可以在任何适用HotFlip的攻击场景中完全替代HotFlip。 |
| The main difference between AGGD and Hot Flip is that AGGD uses gradient information moreeffectively by selecting the top-ranked token from all token positions, rather than over a singlerandomly sampled position. This approach makes AGGD's search trajectory deterministic, enablinga more structured best-first search. Experimental results demonstrate that AGGD achieves a highattack success rate across various datasets and retrieval models. In summary, our contributions are | AGGD和HotFlip的主要区别在于，AGGD通过从所有标记位置选择排名最高的标记来更有效地利用梯度信息，而不是在单个随机采样位置上选择。这种方法使AGGD的搜索轨迹具有确定性，实现了更结构化的最佳优先搜索。实验结果表明，AGGD在各种数据集和检索模型上都能达到很高的攻击成功率。总之，我们的贡献包括： |
| • We provide a thorough understanding of the existing HotFlip adversarial attack method,explaining its mechanics and identifying its potential problems.• We propose AGGD, a gradient-based method that replaces a randomized greedy search witha systematic best-first greedy search over the discrete token space. We demonstrate theeffectiveness of AGGD in various settings.• We conduct extensive experiments to show the vulnerability of dense retrievers under AGGD.For example, when attacking the ANCE retriever, injecting just one adversarial passage(0.00003% of the NQ dataset corpus and 0.00001% of the MS MARCO dataset corpus)can achieve an attack success rate of 44.35% and 26.16% on these datasets, respectively,improving by 17.6% and 13.37% over HotFlip. The generated adversarial passage alsopossesses the capability to transfer to unseen queries in other domains, with AGGD achievingan attack success rate of 82.28% on the NFCorpus dataset. | • 我们对现有的HotFlip对抗性攻击方法提供了深入的理解，解释了其机制并识别了潜在问题。• 我们提出了AGGD，这是一种基于梯度的方法，用系统的最佳优先贪婪搜索代替了随机贪婪搜索，搜索范围覆盖离散标记空间。我们在各种设置中展示了AGGD的有效性。• 我们进行了广泛的实验，展示了密集检索器在AGGD下的脆弱性。例如，在攻击ANCE检索器时，仅注入一个对抗性段落（NQ数据集语料库的0.00003%和MS MARCO数据集语料库的0.00001%）就能在这些数据集上分别达到44.35%和26.16%的攻击成功率，比HotFlip分别提高了17.6%和13.37%。生成的对抗性段落还具有迁移到其他领域未见查询的能力，AGGD在NFCorpus数据集上达到了82.28%的攻击成功率。 |

## Content Summary

这篇论文的标题为《Corpus Poisoning via Approximate Greedy Gradient Descent》。本文提出了一种新的针对稠密检索系统的攻击方法——近似贪婪梯度下降（AGGD），并进行了深入的实验和分析。下面是对论文的详细总结：

### 研究背景
稠密检索系统广泛应用于信息检索和其他需要知识密集型的领域，如检索增强生成（RAG）系统。然而，稠密检索系统被证明易受“语料库投毒攻击”的影响，即恶意用户通过向检索语料库中注入少量对抗性段落，诱使系统在用户查询中返回这些段落，从而扰乱检索结果。这类攻击可能影响稠密检索系统在实际应用中的部署。

### 研究内容
本文提出了AGGD，一种基于梯度信息的攻击方法，旨在通过优化对抗性段落的生成过程来提高语料库投毒攻击的成功率。与传统的HotFlip方法相比，AGGD使用了更系统化的贪婪搜索，能够更有效地选择用于替换的最优token。具体来说，AGGD在所有token位置上进行搜索，而不是随机采样一个位置，从而能够以更高的精度生成对抗性文本。

### 实验设置
论文使用了多个数据集（如Natural Questions和MS MARCO）和不同的稠密检索模型（如Contriever、DPR、ANCE）来测试AGGD的攻击效果。实验评估了AGGD的攻击成功率，即在测试查询中，至少有一个对抗性段落出现在检索结果的前k位的查询比例。实验还考虑了攻击在不同领域的迁移性，通过在不同领域的数据集上进行测试来验证对抗性段落的泛化能力。

### 实验结果与分析
实验结果表明，AGGD在多个检索模型和数据集上均能实现高效的语料库投毒攻击，显著提高了攻击成功率。例如，在攻击ANCE模型时，AGGD在NQ数据集上的成功率比HotFlip高17.6%，在MS MARCO数据集上高13.37%。此外，AGGD生成的对抗性段落在未见过的查询和新领域中也表现出良好的迁移能力。

论文还通过多个实验，展示了AGGD方法在生成高质量候选集合和替换token选择方面的优势。与HotFlip和随机扰动方法相比，AGGD的方法更具效率和准确性，能够在相对较小的候选集合中找到最优的替换token，从而减少计算成本。

### 结论
本文提出的AGGD方法在语料库投毒攻击中表现出色，能够以极低的投毒率（如在NQ数据集中仅注入0.00003%的对抗性段落）实现高攻击成功率。研究表明，AGGD不仅在稠密检索系统的投毒攻击中有效，还可以替代HotFlip应用于其他对抗性攻击场景，如RAG系统的知识投毒攻击。

### 实验设置及分析
- **实验设置**：使用不同的数据集和检索模型，评估AGGD在各种攻击场景中的表现。
- **分析**：实验显示AGGD方法生成的对抗性段落具有更高的攻击成功率和迁移性，验证了其在多领域应用中的有效性。

### 总结
AGGD方法在稠密检索系统的投毒攻击中具有显著的效果，能够以更低的计算成本和更高的精度生成对抗性段落。这一方法有望在未来广泛应用于各种对抗性攻击场景中，进一步揭示稠密检索系统的安全隐患。

如果您需要更详细的分析或有特定部分需要深入解读，请告知我！

## Notes

![KTxFgwqy6mGlhn3](https://s2.loli.net/2024/08/30/KTxFgwqy6mGlhn3.png)
假设m个token的序列。
hotflip是随机选一个token看看词表中有没有好的替换，不行就再随机选一个token。
而AGGD是对每个token的前m个替换可能合并作为候选集，搜索效率更高，也更接近最优解。

这种替换token的策略对OOD场景也有效。
![QKDqmVi8sc3wa1A](https://s2.loli.net/2024/08/30/QKDqmVi8sc3wa1A.png)
效果比hotflip好。
![RLroJEw8SfUWNk4](https://s2.loli.net/2024/08/30/RLroJEw8SfUWNk4.png)
更容易出现最优解。
![RXzU3xJihf94dvt](https://s2.loli.net/2024/08/30/RXzU3xJihf94dvt.png)
增加候选个数AGGD保持优势
![3fy6HIoShrGv1m8](https://s2.loli.net/2024/08/30/3fy6HIoShrGv1m8.png)
doc越长攻击成功率越高

尤其提及应用到PoisonedRAG上效果也更好。

有论文证明密集检索器经过精调后会过拟合地把任务简化为关键字检索，那么能否应用类似hotflip这种修正方法修改近义词让模型更关注语义？