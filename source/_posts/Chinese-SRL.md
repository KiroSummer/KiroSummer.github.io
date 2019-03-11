---
layout: post
title: Chinese-SRL
date: 2019-03-09 13:23:53
password: suda_kiro
tags: [SRL]
---
# Introduction
This post is for the work of Chinese Semantic Role Labeling. (2019-3-9)
*The Basic idea*: Multitask learning using Chinese SRL data and annotated syntactic data, proving the effectiveness of syntax which doesn't overlapp with the target SRL data.
*Contributions*:
1. 句法对 SRL是有效果的，这些效果体现在什么地方？（Oracle transformation）
2. 和 SRL数据不重合的句法数据能够带来的效益是多少？如果融入了 BERT之后，这些句法数据是否还能够体现出作用？
3. 如何利用这些句法数据？有什么比较高效的方式？利用句法是利用 Dependency还是 Constituent。
# Chinese SRL Papers
### A Progressive Learning Approach to Chinese SRL Using Heterogeneous Data
本论文是 ACL 2017的长文。
本文利用了 Progressive Neural Network结合 gated mechanism，将本论文发布的一个 SRL数据 CSB作为异构数据，来提升 CPB data的性能(+2.58)。
中文的 SRL (Chinese PropBank 1.0)数据。
### Semantic Role Labeling for Learner Chinese: the Importance of Syntactic Parsing and L2-L1 Parallel Data
本篇工作是 EMNLP 2018的长文。"L2 sentences": written by non-native speakers. "L1 sentences": written by native speakers. "interlanguage": 中介语。
本篇论文研究了“中介语”的语义分析，将语义角色标注作为一个案例任务，汉语学习者书写的句子作为目标语言。
L2-L1 parallel corpus contains 717,241 sentences. And this paper annotates 600 sentences for the predicate-argument structures.
本文利用了Feng 2012的一个 SRL parser分别结合了 Berkeley parser和 minimal span-based parser作为额外的句法分析器，称之为 PCFGLA-parser-based和 neural-parser-based 系统，第三种就是 He 2017的工作，称之为 neural syntax-agnostic系统。
