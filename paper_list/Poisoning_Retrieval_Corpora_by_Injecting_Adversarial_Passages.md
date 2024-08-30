# Poisoning Retrieval Corpora by Injecting Adversarial Passages

[paper](https://www.semanticscholar.org/reader/e3a49a110801fd961db887f8af1239cc79b55a5b)

## Abstract

| en | ch |
|----|----|
| Dense retrievers have achieved state-of-the-art performance in various information retrieval tasks, but to what extent can they be safely deployed in real-world applications? In this work, we propose a novel attack for dense retrieval systems in which a malicious user generates a small number of adversarial passages by perturbing discrete tokens to maximize similarity with a provided set of training queries. When these adversarial passages are inserted into a large retrieval corpus, we show that this attack is highly effective in fooling these systems to retrieve them for queries that were not seen by the attacker. More surprisingly, these adversarial passages can directly generalize to out-of-domain queries and corpora with a high success attack rate—for instance, we find that 50 generated passages optimized on Natural Questions can mislead >94% of questions posed in financial documents or online forums. We also benchmark and compare a range of state-of-the-art dense retrievers, both unsupervised and supervised. Although different systems exhibit varying levels of vulnerability, we show they can all be successfully attacked by injecting up to 500 passages, a small fraction compared to a retrieval corpus of millions of passages. | 密集检索器在各种信息检索任务中已经取得了最先进的性能，但在多大程度上它们可以安全地部署在现实世界的应用中？在这项工作中，我们提出了一种针对密集检索系统的新型攻击，其中恶意用户通过扰动离散的标记来生成少量对抗性段落，以最大限度地提高与提供的一组训练查询的相似性。当这些对抗性段落被插入到大型检索语料库中时，我们展示了这种攻击在欺骗这些系统以检索它们未被攻击者看到的查询方面非常有效。更令人惊讶的是，这些对抗性段落可以直接泛化到域外的查询和语料库中，并具有很高的成功攻击率——例如，我们发现经过自然问题优化的50个生成段落可以误导财务文档或在线论坛中提出的超过94%的问题。我们还基准测试并比较了一系列最先进的密集检索器，包括无监督和有监督的。尽管不同的系统表现出不同程度的脆弱性，但我们表明，通过注入最多500个段落——与数百万段落的检索语料库相比，这只占很小的比例——可以成功地攻击所有这些系统。 |

## Introduction

| en | ch |
|----|----|
| Dense retrievers have become increasingly popular (Karpukhin et al., 2020; Xiong et al., 2021; Izacard et al., 2022; Khattab and Zaharia, 2020), and outperform traditional lexical methods in a range of information retrieval tasks. However, recent evidence shows that they may still struggle with long-tail entities (Sciavolino et al., 2021) or out-of-domain generalization (Thakur et al., 2021), and it remains unclear to what extent we can safely deploy dense retrievers in real-world applications. | 密集检索器变得越来越流行 (Karpukhin et al., 2020; Xiong et al., 2021; Izacard et al., 2022; Khattab and Zaharia, 2020)，并在一系列信息检索任务中优于传统的词汇方法。然而，最近的证据表明，它们仍然可能在处理长尾实体 (Sciavolino et al., 2021) 或域外泛化 (Thakur et al., 2021) 时遇到困难，并且目前尚不清楚我们在多大程度上可以安全地将密集检索器部署在现实世界的应用中。 |
| In this work, we reveal a new type of vulnerability in dense retrievers. We present a novel corpus poisoning attack, where a malicious user can inject a small fraction of adversarial passages to a retrieval corpus, with the aim of fooling systems into returning them among the top retrieved results. | 在这项工作中，我们揭示了密集检索器中的一种新型脆弱性。我们提出了一种新的语料库投毒攻击，其中恶意用户可以向检索语料库中注入少量对抗性段落，目的是欺骗系统将它们返回到顶级检索结果中。 |
| While previous work has shown that an adversarial passage can be crafted for individual queries (Song et al., 2020; Raval and Verma, 2020; Song et al., 2022; Liu et al., 2022), we consider a stronger setting, where an adversarial passage must be retrieved for a broad set of user queries, and can even generalize to unseen queries either in-domain or out-of-domain. | 尽管之前的工作表明可以为单个查询生成对抗性段落 (Song et al., 2020; Raval and Verma, 2020; Song et al., 2022; Liu et al., 2022)，但我们考虑了一种更强的设置，即对抗性段落必须针对一组广泛的用户查询进行检索，甚至可以泛化到域内或域外的未见查询。 |
| This setting is realistic for open-access platforms, such as Wikipedia or Reddit, and such attacks may become a new tool for black hat SEO (Search Engine Optimization) (Patil Swati et al., 2013). Adversarial passages deteriorate the user experience of retrieval systems and can cause societal harm, e.g., if inserted passages contain misinformation. | 这种设置对于开放访问的平台（如 Wikipedia 或 Reddit）是现实的，此类攻击可能会成为黑帽 SEO（搜索引擎优化）的新工具 (Patil Swati et al., 2013)。对抗性段落会恶化检索系统的用户体验，并可能导致社会危害，例如，如果插入的段落包含错误信息。 |
| Our attack is a gradient-based method, inspired by the HotFlip method (Ebrahimi et al., 2018), which starts from a natural-language passage and iteratively perturbs it in the discrete token space to maximize its similarity to a set of training queries. We also use a simple clustering method to extend this method to generate multiple passages. | 我们的攻击是一种基于梯度的方法，灵感来自 HotFlip 方法 (Ebrahimi et al., 2018)，它从一个自然语言段落开始，在离散的标记空间中迭代扰动它，以最大限度地提高其与一组训练查询的相似性。我们还使用了一种简单的聚类方法来扩展此方法以生成多个段落。 |
| We evaluate our method on state-of-the-art dense retrieval models and demonstrate that this corpus poisoning attack can be highly effective with a small number of adversarial passages. Noticeably, we find that unsupervised Contriever models are particularly vulnerable, and even 10 passages can fool more than 90% of queries. | 我们在最先进的密集检索模型上评估了我们的方法，并证明这种语料库投毒攻击可以通过少量的对抗性段落实现高度有效的攻击。值得注意的是，我们发现无监督的 Contriever 模型特别脆弱，即使是 10 个段落也可以欺骗超过 90% 的查询。 |
| Supervised retrievers (e.g., DPR, ANCE) seem to be harder to attack, but the success rate still exceeds 50% when we inject up to 500 passages (<0.02% compared to the retrieval corpus). | 有监督的检索器（例如 DPR、ANCE）似乎更难攻击，但当我们注入最多 500 个段落时（相对于检索语料库的比例 <0.02%），成功率仍然超过 50%。 |
| Our attack is also effective at attacking multi-vector retrievers (e.g., ColBERT), which are less sensitive to single-token changes. | 我们的攻击对于攻击多向量检索器（例如 ColBERT）也有效，这些检索器对单个标记的变化不太敏感。 |
| Moreover, we find that these adversarial passages can be directly injected into out-of-domain retrieval corpora and achieve a high attack rate for unseen queries posed on these domains (e.g., financial documents or online forums), as illustrated in Figure 1. | 此外，我们发现这些对抗性段落可以直接注入域外的检索语料库中，并对这些领域中提出的未见查询（例如财务文档或在线论坛）实现高攻击率，如图 1 所示。 |
| We conclude our paper with a case study of real-world threats when the inserted text includes targeted misinformation. We hope that our findings will caution practitioners to think about the safety concerns of these dense retrieval systems, and further improve their robustness. | 我们通过一个案例研究总结了当插入的文本包含有针对性的错误信息时的现实威胁。我们希望我们的研究结果能提醒从业者思考这些密集检索系统的安全问题，并进一步提高它们的稳健性。 |

## Content Summary

这篇论文研究了针对密集检索系统的新型攻击方式，称为“语料库投毒攻击”（corpus poisoning attack）。研究人员提出了一种通过在检索语料库中注入对抗性段落来误导检索系统的攻击方法，并证明了该方法在多种情境下都能成功实施攻击。以下是论文的详细总结：

### 研究内容
密集检索模型（dense retrievers）已经在许多信息检索任务中取得了领先表现，但其在现实世界中的安全性尚不明确。本文提出了一种新的攻击方式，允许恶意用户生成少量对抗性段落，这些段落经过精心设计，旨在与特定的查询集最大程度地相似。当这些段落被插入到一个大规模的检索语料库中时，它们能够有效地欺骗检索系统，甚至在面对攻击者未曾见过的查询时也能使其返回这些段落作为检索结果。

### 实验设置
1. **攻击方法**：研究团队开发了一种基于梯度的方法，通过对文本中的离散词元进行扰动，生成对抗性段落。这些对抗性段落旨在最大化它们与训练集中的查询的相似性。
   
2. **检索模型**：研究评估了几种最先进的密集检索模型，包括Contriever、DPR、ANCE和ColBERT，探讨了这些模型在面对对抗性段落时的脆弱性。

3. **实验数据**：主要使用了BEIR基准数据集中的自然问题（Natural Questions, NQ）和MS MARCO数据集进行评估，并测试了这些攻击在不同领域和数据集上的迁移能力（例如金融文档和在线论坛）。

### 实验结果与分析
1. **在域内的攻击效果**：实验显示，在NQ和MS MARCO数据集上，仅使用一个对抗性段落就能欺骗Contriever模型，使其在超过75%的查询中返回这些对抗性段落。当生成更多的对抗性段落时，所有的密集检索模型的成功攻击率都显著提高。

2. **跨域迁移能力**：对抗性段落不仅能在其生成的领域内生效，还能在其他未见过的领域中（如金融领域和科学文档）有效攻击检索系统。例如，针对NQ数据集生成的对抗性段落在FiQA金融数据集上的攻击成功率高达94.1%。

3. **针对多向量检索模型的攻击**：研究发现，针对ColBERT等多向量检索模型，较长的对抗性段落（250个词元）更具攻击效果，成功率达到了20.1%。

### 结论
论文得出结论，密集检索模型在面对语料库投毒攻击时表现出显著的脆弱性，少量对抗性段落就能显著降低检索系统的性能。研究强调了在实际应用中部署这些系统时需要考虑的安全性问题，并建议进一步研究如何提高这些模型的鲁棒性。

### 实验与分析的其他要点
1. **模型间的攻击转移性**：不同模型之间的对抗性段落转移性有限，表明在未知模型权重的情况下，利用转移性进行黑盒攻击的难度较大。
2. **对抗性段落的自然度**：生成的对抗性段落往往表现出较高的非自然性，这为设计简单的防御措施（如基于语言模型的对抗性文本检测）提供了可能性。
3. **针对稀疏检索模型的攻击**：研究表明，稀疏检索模型（如BM25）对这种语料库投毒攻击表现出较强的鲁棒性。

### 伦理考虑
论文探讨了所提出攻击方法的潜在滥用风险，特别是在传播错误信息方面，并呼吁在未来研究中更加关注这些攻击的社会影响。

总结来说，这篇论文揭示了密集检索系统在面对对抗性文本注入时的脆弱性，并为未来研究提高检索系统的安全性和鲁棒性提供了重要参考。

## Notes
$$
\text{arg max}_{t'_i \in \mathcal{V}} \frac{1}{|\mathcal{Q}|} \sum_{q \in \mathcal{Q}} e_{t'_i}^\top \nabla_{e_{t_i}} \text{sim}(q, a)
$$
hotflip是让生成对抗样本，让模型输出更差的。逻辑就是求模型目标（错误）输出相对于某个被替换嵌入的导数与替换入嵌入向量的乘积，越大越好，因为这证明了替换入的嵌入帮助模型向着目标预测变化了。
本文反向应用了hotflip算法，求doc与query相似度相对于某个被替换嵌入的导数与替换入嵌入向量的乘积，越大越好，就是希望在检索结果不变的情况下尽量改变doc，检索模型结果不变但llm无法利用知识了。

这种攻击方法对不同领域、不同编码模型都很有效。但生成的doc与模型高度相关，同一个模型换领域也好用，但换模型就没效果了。

可以用这种变化方法测试一下生成型检索模型和编码型检索模型的关注差异。