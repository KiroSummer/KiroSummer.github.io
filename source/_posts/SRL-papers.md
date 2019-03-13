---
title: SRL-papers
date: 2018-01-09 19:00:33
tags: [SRL]
---
# 简介
新开一篇博客，记录在阅读AMR论文的时候的一些收获。
<!--more-->
## A Progressive Learning Approach to Chinese SRL Using Heterogeneous Data
该篇论文主要利用 PNN+Gated Recurrent Adapter构建了一个可以利用异构数据的 SRL分析器。另外，还发布了一个数据 CSB。但是，和我的目标（尝试了解 SRL）并不一致。
本文几个重点：
1. CSB的构建与其他 SRL语料的区别
2. PNN + Gated Recurrent Adapter

## Deep Semantic Role Labeling: What Works and What't Next
本论文使用了一个 deep __highway__ BiLSTM的架构来处理 SRL （highway之前没有见过），并且利用了这几年的一些优秀的做法来处理初始化和正则化。本文的几个重点：
1. 新提出了一个深层神经网络来处理 SRL
2. 详细分析了结构化的一致性和长距离的依赖问题
3. 详述了句法在 SRL上的应用

本文提到的几个对我目前工作有用的几个点：
1. 初始化十分重要：正交初始化？（这个点在论文中表现得很重要）
2. 句法的信息十分重要+Biaffine
3. 有约束的Decoding？
4. gated highway connections?
5. BIO VS BIESO?
6. Dropout: 0.1?

## Linguisticaly-Informed Self-Attention for Semantic Role Labeling
本文利用 self-attention做了一个 multi-task learning：词性、句法、谓词预测以及语义角色标注。旨在利用语言学的信息来进行端到端的语义角色标注。
模型的优化目标是：最大化各个子任务的 likelihood的 sum
本篇论文相对比与 He的工作，在使用 predicted predicates的情况下，效果有提高。

## Jointly Predicting Predicates and Arguments in Neural Semantic Role Labeling
最近的一些基于 BIO-tagging的神经网络模型，都是假设给定了 gold predicate进行的 argument的预测，而且还不能够利用 span-level的特征。
本篇论文提出了一个 end-to-end的模型用来同时预测 predicates和 arguments，实验结果表明，本篇论文达到了目前不使用 gold predicate的最好的结果。
纵观整个论文，论文的模型架构来自于指代消除这个任务。值得注意的是，这个指代消除的工作同样是 He的工作。

## Syntax for Semantic Role Labeling, To Be, Or Not to Be
这篇论文提出了一个使用扩展的 k-order argument pruning 加强 argument labeling的模型，并且取得了 state-of-the-art的结果。
本文的 SRL模型很普通，唯一比较特殊的就是 MLP的层数多了一点。
而且，本文利用了一个 argument pruning的策略来有效的利用句法信息，但是比较不好是，本文并没有明确指出该方法是用在了模型的哪一个地方，直接引用了以前的论文，让读者感觉知其所以然，不知其所以然。
这篇文章的模型唯一用到句法信息的地方就是在输入层里面加入了 dependency label的信息。
这篇文章的 argument pruning从论文里面看是在 preprocessing里面进行应用的，难道是在 dependency label上表现出来的？不是在 Decoder部分进行 constrained decoding？

## Encoding Sentences with Graph Convolutional Networks for Semantic Role Labeling
本文提出了 graph convolutional networks (GCN)，用在基于图的神经网络。
本文将 GCN和 LSTM进行了 stack操作，然后得到了 state-of-the-art的结果。总的来说，对于一个图中的每一个节点来说，GCN用来 encode这个节点相关联的一些节点的信息。(本文的贡献有点好玩)

## A Full End-to-End Semantic Role Labeler, Syntax-agnostic Over Syntax-aware?
本文提出了一个端到端的统一处理谓词消岐和论元标注的模型。
本文的模型还是比较简单的，采用了 3层 BiLSTM + Biaffine Scorer的模型架构来处理这个任务。本文的一个亮点就是对 Biaffine Scorer进行了加强，加入了 bias term。同时，类似于 Biaffine Parser，本文对 predicate和 argument也利用两个不同的 W进行了非线性编码。
本文在 CoNLL-2008和 CoNLL-2009的数据集上都取得了目前最高的结果。

## Syntactic Scaffolds for Semantic Structures
本篇论文提出了一个利用 multi-task learning的方法利用短语结构句法树 (在 PropBank SRL实验上利用的是 OntoNotes 5.0，和 CoNLL-2012 SRL数据是完整重叠的。CoNLL-2012 SRL就是 OntoNotes 5.0)进行语义任务的处理，称之为句法“梯子”。
模型主要通过将句法端的 span作为模型句法部分的训练目标；同时，在进行语义预测的时候，利用强化的 span表示进行 argument的预测：1）BiLSTM产生的上下文相关的表示；2）attention机制产生的 span summary；3）span的特征。

## An Empirical Study of Building a Strong Baseline for Constituency Parsing
在这边插播一个短语结构树句法的工作，本篇文章是 EMNLP2018年的一篇short。本文利用最近在 NLG领域一些比较新颖的技术来提升基于 sequence-to-sequence的 constitutency parsering。并且取得了比较好的结果。利用了一些技术，比如 subword（BPE），unknown token embedding as a bias, multi-task learning, output length controlling, pre-trained word embedding, model ensemble and LM reranking. 本文的实验结果还是比较详细的，能够从实验结果中看出每一个 technic带来的效益是多少。

## Multi-Task Identification of Entities, Relations, and Coreference for Scientific Knowledge Graph Construction
这篇文章是 18年 EMNLP的一篇文章，是华盛顿大学的一篇文章。本文利用了之前他们组的积累，span-based model来处理科学知识图谱的构建。将三个任务做一个multi-task learning，实体的识别，实体之间关系的识别，以及指代消歧。除了这个 multi-task learning的模块之外，本文还发布了一个数据集，并且在此基础上构建了科学知识图谱。

## Deep MultiTask Learning for Semantic Dependency Parsing
本篇文章是 2017 ACL的一篇长文。文章的主要工作是提出了一个基于深度神经网络的框架来讲一个句子分析称3种语义依存图。本文的主要贡献有两个，1）提出了一个比较好的 baseline. 2）探究了两种应用于 multitask learning的方法：parameter sharing and higher-order structure scores.

## Graph Convolutional Networks for Text Classification
本文是 AAAI2019的一篇工作，本文利用 GCN来处理文本分类，本文的主要贡献在于利用 GCN，将数据集的所有的 word以及 document是作为 node，然后通过建立邻接矩阵A，利用了两层 GCN来达到分类的目的。
