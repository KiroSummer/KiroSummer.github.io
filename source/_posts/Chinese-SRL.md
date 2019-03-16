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

# Chinese SRL Experiments
## 数据准备：
### LSGN 英文的数据
SRL数据：CoNLL-2005, CoNLL-2012
word embeddings: context window size of 2 for *head word embedding*, window size of 10 for LSTM inputs.
### 中文准备的数据
SRL的数据：Chinese PropBank 1.0 (CPB). Train: 8828; Dev: 561; Test:995.
word embedding: 300dim, 待续 (我做过一个实验，就是可以选择不利用 head word embedding，实验结果和使用几乎一致，所以我们目前仅仅需要获取 300dim的中文 word embedding即可)
*train the word embeddings with Giga chinese*
使用 demo处理了 giga数据（分词），然后根据阈值（0.7）选取了部分数据（7120643句子），然后使用选取的这部分数据训练 word embedding。
```bash
time ./word2vec -train demo_Giga.txt.threshold.0.7.sentences.per.line.txt -output giga.demo.0.7.word.emb.txt -cbow 0 -size 300 -window 8 -negative 25 -hs 0 -sample 1e-4 -threads 20 -binary 0 -iter 15 -min-count 5
```

## 实验
### 基本模型的确定

| __Path__| __Notes__| __Dev__| __Test__|
|---------|----------|--------|---------|
|n126 ~/Chinese-SRL/exp-baseline| 使用lsgn跑中文的实验|    |    |
|n126 ~/Chinese-SRL/exp-baseline-fix-last-dev-sentence| 修正了 Dev最后一条数据因为没有 argument的计算问题|    |    |
|n126 ~/Chinese-SRL/exp-baseline-fix-then-adjust-cnn| 调整了 char cnn的相关参数 |    |    |
|n126 ~/Chinese-SRL/exp-baseline-fix-then-cnn-then-span-rep|调整了 span representation的公式，统一利用 BiLSTM的输出|    |    |

# Chinese SRL Papers
### A Progressive Learning Approach to Chinese SRL Using Heterogeneous Data
本论文是 ACL 2017的长文。
本文利用了 Progressive Neural Network结合 gated mechanism，将本论文发布的一个 SRL数据 CSB作为异构数据，来提升 CPB data的性能(+2.58)。
1. 中文的 SRL ([Chinese PropBank 1.0](https://catalog.ldc.upenn.edu/LDC2005T23))数据。
2. 本文的词性来自于 stanford parser.

### Labeling Chinese Predicates with Semantic Roles
本论文的 section 2详细介绍了 CPB和 Nombank的语义标注。
### Annotating the Propositions in the Penn Chinese Treebank
本论文介绍了一种在 Penn Chinese Treebank标注 propositions(命题)的方法。
假设：如果一个谓词在不同的句子中具有相同的 sense，那么这些句子就具有相同的谓词、论元结构。
arguments标注的标签 argN (N is the integer between 0 and 5)
本论文还介绍了在标注过程中几个比较复杂的点：Split Arguments, Norminalizations.
### Capturing Argument Relationships for Chinese Semantic Role Labeling
本论文是 EMNLP 2016的文章。
本文利用了 quadratic optimization method来对 argument relationship (分为两种关系：compatible and incompatible arguments; 这种关系的分类在文章中使用最大熵分类器进行处理)进行建模，作为 BiLSTM基本模型的后处理手段，提升 Chinese SRL的性能 (+0.48%)。
### Chinese Semantic Role Labeling with Bidirectional Recurrent Neural Networks
本文是 2015年 EMNLP的文章。
本文利用了 BiLSTM对 Chinese SRL进行处理，同时介绍了一个很方便的模型利用异构数据（在异构数据上，利用 BiLSTM训练一个模型，而后利用 fine-tuned word embeddings作为实验数据模型的 word embeddings）。
### Chinese Semantic Role Labeling with Shallow Parsing
本文是 2009 ACL的长文
本文首先做了一个 Chinese shallow parser，然后在 shallow pasering和句法 chunking的基础之上，将 SRL看作是一个 sequence tagging problem，同时探究了很多特征模板（传统方法），利用 SVM分类器进行了试验。本文的系统架构分为两种：
1. 一步策略：IOB2 representation
2. 两步策略：先识别 boundaries，然后再识别 semantic type。

### Semantic Role Labeling for Learner Chinese: the Importance of Syntactic Parsing and L2-L1 Parallel Data
本篇工作是 EMNLP 2018的长文。"L2 sentences": written by non-native speakers. "L1 sentences": written by native speakers. "interlanguage": 中介语。
本篇论文研究了“中介语”的语义分析，将语义角色标注作为一个案例任务，汉语学习者书写的句子作为目标语言。
L2-L1 parallel corpus contains 717,241 sentences. And this paper annotates 600 sentences for the predicate-argument structures.
本文利用了Feng 2012的一个 SRL parser分别结合了 Berkeley parser和 minimal span-based parser作为额外的句法分析器，称之为 PCFGLA-parser-based和 neural-parser-based 系统，第三种就是 He 2017的工作，称之为 neural syntax-agnostic系统。
