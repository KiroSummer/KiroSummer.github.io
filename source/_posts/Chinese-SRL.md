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

# 显著性检验:
## Syntax-aware methods
[显著性检验 baseline vs baseline+fir dep](/documents/chinese_srl/log.cpb1.0.test.baseline.vs.baseline+FIR.txt)
[显著性检验 baseline vs baseline+hps dep](/documents/chinese_srl/cpb1.0.test.baseline.vs.baseline+hps.txt)
[显著性检验 baseline vs baseline+iir dep](/documents/chinese_srl/cpb1.0.test.baseline.vs.baseline+iir.txt)
[显著性检验 baseline+hps vs baseline+iir dep](/documents/chinese_srl/log.cpb1.0.test.baseline+HPS.vs.baseline+IIR.txt)
## Comparison on CPB1.0 test
### pre-defined settings
[显著性检验 baseline vs baseline+hps dep](/documents/chinese_srl/cpb1.0.test.baseline.vs.baseline+hps.txt)
[显著性检验 baseline vs baseline+iir dep](/documents/chinese_srl/cpb1.0.test.baseline.vs.baseline+iir.txt)
[显著性检验 baseline+hps vs baseline+iir dep](/documents/chinese_srl/log.cpb1.0.test.baseline+HPS.vs.baseline+IIR.txt)
[显著性检验 baseline vs baseline+bert dep](/documents/chinese_srl/cpb1.0.test.baseline.vs.baseline+bert.txt)
[显著性检验 baseline vs baseline+bert+iir dep](/documents/chinese_srl/cpb1.0.test.baseline.vs.baseline+bert+hps.cmp.txt)
[显著性检验 baseline+bert vs baseline+bert+hps dep](/documents/chinese_srl/cpb.1.0.test.baseline+bert.vs.baseline+bert+hps-true.cmp.txt)
[显著性检验 baseline+bert vs baseline+bert+iir dep](/documents/chinese_srl/cpb.1.0.test.baseline+bert.vs.baseline+bert+hps.cmp.txt)
[显著性检验 baseline+bert+hps vs baseline+bert+iir dep](/documents/chinese_srl/cpb.1.0.test.baseline+bert+hps.VS.baseline+bert+iir.cmp.txt) (高)
### end-to-end settings
[显著性检验 baseline vs baseline+iir dep](/documents/chinese_srl/test.cpb1.0.e2e.baseline.VS.baseline+iir.cmp.txt)
[显著性检验 baseline+bert vs baseline+bert+iir dep](/documents/chinese_srl/test.cpb.1.0.e2e.baseline+bert.VS.baseline+bert+iir.cmp.txt) (高)

## Comparison on CoNLL-2009 Chinese test
[显著性检验 baseline vs baseline+iir dep](/documents/chinese_srl/test.conll09.baseline.VS.baseline+iir.cmp.txt)
[显著性检验 baseline+bert vs baseline+bert+iir dep](/documents/chinese_srl/test.conll09.baseline+bert.VS.baseline+bert+iir.cmp.txt) (高)



# Chinese SRL Experiments

## 数据准备：
### LSGN 英文的数据
SRL数据：CoNLL-2005, CoNLL-2012
word embeddings: context window size of 2 for *head word embedding*, window size of 10 for LSTM inputs.
### 中文准备的数据
__SRL的数据__：Chinese PropBank 1.0 (CPB). Train: 8828; Dev: 561; Test:995.
__word embedding__: 300dim, 待续 (我做过一个实验，就是可以选择不利用 head word embedding，实验结果和使用几乎一致，所以我们目前仅仅需要获取 300dim的中文 word embedding即可)
__train the word embeddings with Giga chinese__
使用 demo处理了 giga数据（分词），然后根据阈值（0.7）选取了部分数据（7120643句子），然后使用选取的这部分数据训练 word embedding。
```bash
time ./word2vec -train demo_Giga.txt.threshold.0.7.sentences.per.line.txt -output giga.demo.0.7.word.emb.txt -cbow 0 -size 300 -window 8 -negative 25 -hs 0 -sample 1e-4 -threads 20 -binary 0 -iter 15 -min-count 5
```
__CoNLL-2009数据处理__
1. train中 16070句话，“李茂“”之后多了一个双引号，被我删除掉了。
2. Found 86551 words in 3 dataset(s); Kept 48341 out of 170991 lines. (SRL: train, dev; ALL dep data from xpeng)

__argument ratio, predicate ratio:__
根据 Train集合测定 argument ratio, predicate ratio.

|__argument ratio__|__覆盖率(%)__|__predicate ratio__|__覆盖率(%)__|
|------------------|----------|-------------------|----------|
|0.75              |90.9      |0.35               |98.2      |
|0.8               |93.8      |0.4                |98.8      |
|0.85              |95.8      |0.45               |99.2      |
|0.9               |97.1      |0.5                |99.2      |

抽取 word embeddings：
1. Found 18811 words in 2 dataset(s); Kept 16225 out of 170991 lines. (SRL: train dev; 在两个数据文件中找到了 18811个 words，然后从 170991个 word embeddings里面能够找到 16225个)
2. Found 23575 words in 3 dataset(s); Kept 16227 out of 170991 lines. (SRL: train dev; CDT-suda-format)
3. Found 74079 words in 3 dataset(s); Kept 43321 out of 170991 lines. (SRL: train dev; dep\_all from xpeng)

__句法数据__
1.苏大规范的数据列表 
苏大规范的CDT数据（暂未使用），Train: 52423句

|__数据名称__|__数据规模（句子数）__|
|------------|----------------------|
|BC-train.conll| 16339 |
|comment-train.conll| 6885|
|content-train.conll| 5129|
|dialog-train.conll| 6897 |
|finance-train.conll| 8738|
|PKU-train.conll |10760|
|renren-train.conll | 11286|
|ZX-train.conll | 1645|

所有的句法数据均包含人工标注以及自动补全，总计：67679句
__查看 BC-train数据和 CPB1.0数据的重合程度__

| __Data__| __Train__| __Dev__| __Test__|
|---------|----------|--------|---------|
|CPB1.0   |1319      |0       |0        |
|CoNLL-2009|3647     |1       |0        |

__处理句法数据__
1. 抽取 word embeddings
2. 抽取 char dict

## 实验
__待进行的实验__
1. 句法相关：Tree-GRU的尝试
2. BERT相关：1. average bert；2. fine tuning
3. Dependency-based SRL：一系列的实验。

### 基本模型的确定

| __Path__| __Notes__| __Dev__| __Test__|
|---------|----------|--------|---------|
|n126 ~/Chinese-SRL/exp-baseline| 使用lsgn跑中文的实验|81.73%  |80.62%    |
|n126 ~/Chinese-SRL/exp-baseline-fix-last-dev-sentence| 修正了 Dev最后一条数据因为没有 argument的计算问题|80.88%  |80.34% |
|n126 ~/Chinese-SRL/exp-baseline-fix-last-dev-sentence-re-run| 同上，char emb size 100, output channel 100 |81.98%  |80.87% |
|n126 ~/Chinese-SRL/exp-baseline-fix-then-adjust-cnn|同上，一模一样 |81.6%    |81.32%  |
|n126 ~/Chinese-SRL/exp-baseline-fix-then-cnn-then-span-rep|调整了 span representation的公式，统一利用 BiLSTM的输出|81.67%  |80.85%  |
|n126 ~/Chinese-SRL/exp-baseline-fix-then-cnn-then-span-repre-re-run-2 |同上 |81.31% |81.00% |
|n126 ~/Chinese-SRL/exp-baseline-try-stable |调整了迭代次数->180000, clip grad->1.0 |81.57% |80.45% |
|n126 ~/Chinese-SRL/exp-baseline-sum-lstm-for-span|调整了 span representation的公式，直接 mean(lstm output) |81.84% |80.48% |
|n126 ~/Chinese-SRL/exp-baseline-sum-lstm-for-span-re-run2|同上 |81.99% |80.21% |

### 加入句法的尝试

| __Path__| __Notes__| __Dev__| __Test__|
|--------|----------|--------|---------|
|<font color="blue">n126 ~/Chinese-SRL/exp-baseline-sum-lstm-for-span</font>| __Baseline__调整了 span representation的公式，直接 mean(lstm output) |81.84% |80.48% |
|n126 ~/Chinese-SRL/exp-baseline-multitask-learningdep|初步加入句法模块，数据是 CDT-suda规范数据 |81.83% |  |
|n126 ~/Chinese-SRL/exp-baseline-multitask-learningdep-all|代码如上，不过是数据换成了所有的 Dep数据 |81.25% |  |
|n126 ~/Chinese-SRL/exp-baseline-multitask-learningdep-v2 |修改 Biaffine loss mean -> sum, 加入句法根据 prob prune的规则|82.52%  |82.60% |
|n126 ~/Chinese-SRL/exp-baseline-multitask-learningdep-v3 |修改模型的 backward算法，dep 和 srl分别 back |83.30%|83.09% |
|n126 ~/Chinese-SRL/exp-baseline-multitask-learningdep-v3-re-run|同上，再跑一次，看看差距大不大|83.23% |83.46% |
|n126 ~/Chinese-SRL/exp-baseline-MTL-dep-share-lstm-uppermost-hidden|将 syntax使用独有的 BiLSTM，然后将 dep\_BiLSTM output和 srl\_BiLSTM联合起来 | 82.95%| 82.89%|
|<font color="blue">n126 ~/Chinese-SRL/exp-baseline-MTL-dep</font>| 修改利用句法的形式，不再单纯的删除补全概率比较低的句子，改为不计算对应位置的 loss|83.36% |83.51% |
|n126 ~/Chinese-SRL/exp-baseline-MTL-dep-ratio-0.6| 同上，但是概率修改为 0.6|83.22% |83.53% |
|n126 ~/Chinese-SRL/exp-baseline-MTL-dep-ratio-0.4| 同上，但是概率修改为 0.4|83.12% |83.75% |
|n126 ~/Chinese-SRL/exp-baseline-MTL-dep-ratio-0.2| 同上，但是概率修改为 0.2|83.22% |83.53% |
|n126 ~/Chinese-SRL/exp-baseline-MTL-dep-share-2-specify-3| share lstm前两层，最后一层私有| 83.40%| 83.44%|
|n126 ~/Chinese-SRL/exp-baseline-MTL-dep-share-2-specify-3-w-attention|同上，不过 dep lstm加上了 softmax attention| 83.20%| 83.65%|
|n126 ~/Chinese-SRL/exp-baseline-MTL-dep-share-2-specify-3-mlp-rep| share lstm前两层，最后一层私有，使用的是 MLP sum| 83.18%| 83.68%|
|n126 ~/Chinese-SRL/exp-baseline-MTL-dep-share-2-specify-3-mlp-rep-w-dropou| 同上，加上了 dropout| 83.80% | 83.62%|
|n126 ~/Chinese-SRL/exp-baseline-MTL-dep-share-2-3-mlp-rep-w-dropout-concat| 同上，concat| 83.22% | 83.45%|
|<font color="blue">n126 ~/Chinese-SRL/exp-baseline-MTL-dep-private-lstm-weighted-sum-as-input</font>| dep BiLSTM weighted sum as SRL input| 83.39% | __83.91%__|
|<font color="gray">n126 ~/Chinese-SRL/exp-baseline-MTL-dep-private-lstm-weighted-sum-as-input-avg</font>| dep BiLSTM average as SRL input| 83.53% | 83.63%|
|<font color="gray">n126 ~/Chinese-SRL/exp-baseline-MTL-dep-private-lstm-weighted-sum-as-input-max-steps-180000</font>| dep BiLSTM weighted sum as SRL input| 84.16% | 83.72%|
|n126 ~/Chinese-SRL/exp-baseline-MTL-dep-soft-sharing-BiLSTM | dep BiLSTM soft sharing with SRL BiLSTM| 83.13% |82.24%| 
|n126 ~/Chinese-SRL/exp-baseline-MTL-dep-private-lstm-weighted-sum-srl-biltm| dep BiLSTM weighted sum + SRL BiLSTM layer 3|82.85% | 82.43% |
|n126 ~/Chinese-SRL/exp-baseline-MTL-dep-private-lstm-weighted-sum-srl-biltm-layer-2| dep BiLSTM weighted sum + SRL BiLSTM layer 2|83.42% | 83.52%|
|<font color="blue">n126 ~/Chinese-SRL/exp-baseline-mean-with-TreeGRU</font>|Baseline + TreeGRU(SUDA-format) |81.28%  |80.05%  |
|<font color="blue">n16 ~/Chinese-SRL/exp-baseline-mean-with-biaffine-features</font>  |Baseline+Biaffine (SUDA-format) features|83.30%  |82.65%  |

### 句法数据规模对所提框架的影响：
Notes:*这里的句法数据指的是先随机过了，然后再选取前n句*

| __Path__| __Notes__| __Dev__| __Test__|
|---------|----------|--------|---------|
|n126 ~/Chinese-SRL/exp-baseline-MTL-dep-private-lstm-weighted-sum-as-input-dep-size-10000| 10000个句法句子|82.59 |82.55|
|n126 ~/Chinese-SRL/exp-baseline-MTL-dep-private-lstm-weighted-sum-as-input-dep-size-20000| 20000	   |83.01 |83.23|
|n126 ~/Chinese-SRL/exp-baseline-MTL-dep-private-lstm-weighted-sum-as-input-dep-size-30000| 30000 	   |83.22 |83.37|
|n126 ~/Chinese-SRL/exp-baseline-MTL-dep-private-lstm-weighted-sum-as-input-dep-size-40000| 40000	   |82.92 |82.92|
|n126 ~/Chinese-SRL/exp-baseline-MTL-dep-private-lstm-weighted-sum-as-input-dep-size-40000-re-run-2| 40000	   |83.17 |83.65|
|n126 ~/Chinese-SRL/exp-baseline-MTL-dep-private-lstm-weighted-sum-as-input-dep-size-40000-re-run-3| 40000	   |83.10 |83.44|
|n126 ~/Chinese-SRL/exp-baseline-MTL-dep-private-lstm-weighted-sum-as-input-dep-size-40000-re-run-4| 40000	   |83.37 |83.68|
|n126 ~/Chinese-SRL/exp-baseline-MTL-dep-private-lstm-weighted-sum-as-input-dep-size-50000| 50000	   |83.70 |83.50|
|n126 ~/Chinese-SRL/exp-baseline-MTL-dep-private-lstm-weighted-sum-as-input-dep-size-60000| 60000	   |83.79 |83.67|

总结：
1. dep ratio pruning似乎对于模型的性能没有什么重要的影响，猜测的解释是：因为已经包含了全部的 dep语料，而且 dep中 ratio比较低的比较少吧。
2. share lstm前两层和私有最后一层是一个可行的途径，性能几乎没有差别。
3. dep BiLSTM weighted sum as SRL input __成了！__

### 加入 BERT的尝试
如何加入句法：
1. 下载 BERT-Base, Chinese模型参数，Chinese Simplified and Traditional, 12-layer, 768-hidden, 12-heads, 110M parameters.
2. source activate tensorflow; 然后根据实际情况，修改 run.sh
3. 将 bert转换成 h5py格式的文件。发现了 bert有点不太符合中文国情～（不识别一些中文标点符号，比如双引号。）。最后，我通过一些规避的策略，根据 original file进行了处理，搞定了转换成 h5py格式的文件。（mean for tokens in a word） 

| __Path__| __Notes__| __Dev__| __Test__|
|---------|----------|--------|---------|
|<font color="blue">n126 ~/Chinese-SRL/exp-baseline-sum-lstm-for-span-re-run3</font>  |Baseline    |82.13%    |80.60%   |
|<font color="blue">n126 ~/Chinese-SRL/exp-baseline-mean-with-bert</font>|Baseline+BERT |86.27%  |86.61%   |
|<font color="blue">n126 ~/Chinese-SRL/exp-baseline-mean-with-bert-average</font>|Baseline+BERT(avg) |86.36%  |86.89%   |
|n126 ~/Chinese-SRL/exp-baseline-mean-with-bert-fine-tuning|Baseline+BERT fine tuning|84.00%  |?  |
|n126 ~/Chinese-SRL/exp-baseline-mean-with-bert-average-re-run | Baseline+BERT(avg) re-run|86.01% |86.37%  |
|<font color="blue">n126 ~/Chinese-SRL/exp-baseline-MTL-dep-with-bert</font>|Baseline+BERT+dep hard sharing|86.77%  |87.03%  |
|n126 ~/Chinese-SRL/exp-baseline-MTL-dep-with-bert-with-dep-bert|hard sharing方式，同时在 SRL和 Dep部分加入对应的 BERT特征|86.51% |87.56% |
|n126 ~/Chinese-SRL/exp-baseline-MTL-dep-private-lstm-weighted-sum-as-input-with-bert|Baseline+BERT+dep weighted sum as srl input|85.39%  |86.25%  |
|<font color="blue">n126 ~/Chinese-SRL/exp-baseline-MTL-dep-private-lstm-weighted-sum-as-input-with-bert-fix-drop</font>|同上，修正了 dropout的利用方式（bert features没有 dropout）|86.81% |87.54% |
|n126 ~/Chinese-SRL/exp-baseline-MTL-dep-private-lstm-weighted-sum-as-input-with-bert-with-dep-bert|weighted sum dep BiLSTM方式，同时在 SRL和 Dep部分加入对应的 BERT特征|87.08% |87.62% |
|n126 ~/Chinese-SRL/exp-baseline-MTL-dep-private-lstm-weighted-sum-as-input-with-bert-with-dep-bert-fix-drop|同上，修正了dropout的利用方式(dep rep多了一次) |86.94%  |87.69%  |

__结论__:
1. BERT的 average方式和 softmax weighted sum方式效果基本一致，没有很大的差别。
2. 利用BERT之后，再加入句法，句法依旧展示了它的不可替代的部分性能。
3. 在初步调试 BERT fine tuning之后，发现性能并没有得到很高的提升，而且可能是因为模型保存的问题？测试的时候和训练阶段得到的评价指标是不一样的（待解决）？

### end-2-end results on CPB1.0

| __Path__| __Notes__| __Dev__| __Test__|
|---------|----------|--------|---------|
|n126: ~/Chinese-SRL/exp-baseline-mean-e2e-cpb	|end-to-end baseline on CPB1.0	|80.37%	|79.29%	|
|n126: ~/Chinese-SRL/exp-baseline-mean-w-dep-sws-e2e| baseline + dep | 82.39% | 81.73% |
|n126: ~/Chinese-SRL/exp-baseline-mean-e2e-with-bert| baseline + BERT |85.30% |85.26%|
|n126: ~/Chinese-SRL/exp-baseline-mean-with-bert-dep-e2| baseline + BERT + dep|85.92%|85.57%|


# Dependency-based SRL (semantic dependency parsing)
CoNLL-2008提出这个任务。一般分为 谓词识别和分歧，论元的识别和分类。
CoNLL-2008只是一个英文的任务，而CoNLL-2009是一个多语言的任务。并且在 CoNLL-2009中，谓词已经被标出。

## 抽取 BERT
__神经病吧__，为什么抽取出来的 feature里面会有一个 “0.26^P122”，这么奇怪的东西，其他人都是 float，就这么一个是怪胎，害得我找了好久，现在就改成 5好了。。
## predicate identification and disambiguation
使用 mate-tools（引用）进行处理：这个工具可以百分百识别 predicate。predicate sense。处理之后的 Accuracy：
Train: 99.01 (101798 / 102813) Dev: 94.87 (7687 / 8103) Test: 94.91 (11657 / 12282)

## 实验

| __Path__| __Notes__| __Dev(gold predicate sense)__| __Test(gold)__|  __Dev(mate-tools sense)__| __Test__|
|---------|----------|------------------------------|---------------|---------------------------|---------|
|<font color="blue">n126 ~/Chinese-SRL/exp-baseline-for-CoNLL09 </font>| CoNLL-2009 Baseline|85.82%  |85.80%  |84.27%  |84.25%  |
|<font color="blue">n126 ~/Chinese-SRL/exp-baseline-for-CoNLL09-w-dep-softmax-weighted-sum </font>| Baseline+Softmax weighted sum dep BiLSTM|87.13%  |86.68%  |85.59%  |85.13% |
|<font color="blue">n126 ~/Chinese-SRL/exp-baseline-for-CoNLL09-w-bert </font>| Baseline + BERT features |89.91%  |90.00%  |88.36%  |88.45%  |
|n126 ~/Chinese-SRL/exp-baseline-for-CoNLL09-w-dep-softmax-weighted-sum-w-bert| Baseline + BERT features + softmax weighted sum dep BiLSTM |90.07%  |90.02%  |88.52%  |88.47%  |
|n126 ~/Chinese-SRL/exp-baseline-for-CoNLL09-w-dep-softmax-weighted-sum-w-bert-re-run|同上 |90.07%  |90.02%  |88.52%  |88.47%  |
|n126 ~/Chinese-SRL/exp-baseline-for-CoNLL09-w-dep-softmax-weighted-sum-w-bert-upgraded-batching-strategy|修改了 dep data batch的方式 |90.04%  |89.77%  |88.50%  |88.23%  |
|n126 ~/Chinese-SRL/exp-baseline-for-CoNLL09-w-dep-softmax-weighted-sum-w-bert-max-steps-18w|同上, 18w steps |90.15%  |90.09%   |88.59%   |88.53%  |
|n126 ~/Chinese-SRL/exp-baseline-for-CoNLL09-w-dep-softmax-weighted-sum-w-bert-later-dep| 5w次迭代之后，加入 dep features|90.14% |90.09% |88.59% |88.54% |
|n126 ~/Chinese-SRL/exp-baseline-for-CoNLL09-w-dep-softmax-weighted-sum-w-bert-later-bert| 10w次迭代之后，加入 bert features|86.99%  |86.75%  |85.44%  |85.20%  |
|n126 ~/Chinese-SRL/exp-baseline-for-CoNLL09-w-dep-softmax-weighted-sum-w-bert-later-bert-not-update-dep |10w次迭代之后，不在 train dep batch data |86.99% |86.74%  |85.44%  |85.18%  |
|n126 exp-baseline-for-CoNLL09-w-dep-softmax-weighted-sum-w-bert-max-steps-18w-dep-all-with-cdt| 句法数据包含了 CDT-suda，并且跑了 18w steps|90.29% |90.18%  |88.74%|88.63%|

# Chinese SRL Papers
### A Progressive Learning Approach to Chinese SRL Using Heterogeneous Data
本论文是 ACL 2017的长文。
本文利用了 Progressive Neural Network结合 gated mechanism，将本论文发布的一个 SRL数据 CSB作为异构数据，来提升 CPB data的性能(+2.58)。
1. 中文的 SRL ([Chinese PropBank 1.0](https://catalog.ldc.upenn.edu/LDC2005T23))数据。
2. 本文的词性来自于 stanford parser.

### Labeling Chinese Predicates with Semantic Roles
本论文的 section 2详细介绍了 CPB和 Nombank的语义标注。
__Rel__是 predicate的标签？
### Annotating the Propositions in the Penn Chinese Treebank
本论文介绍了一种在 Penn Chinese Treebank标注 propositions(命题)的方法。
假设：如果一个谓词在不同的句子中具有相同的 sense，那么这些句子就具有相同的谓词、论元结构。
arguments标注的标签 argN (N is the integer between 0 and 5)
本论文还介绍了在标注过程中几个比较复杂的点：Split Arguments, Norminalizations.
1. Split Arguments: 对于同一个 predicate，在一个句子中一个的 argument，也可以是另外一个句子中的多个 (2个) arguements。
2. Nominalizations: 

Implementation:
1. create a lecical database. Each entry is a predicate listed with its framesets.
2. list the set of posible semantic roles for each frameset with a mnemonic explanation (助记的解释)
3. Annotator determines the syntactic frame of the predicate instance, and then determine which frameset this frame instantitates. __sense tagging__
4. identifying the arguments and adjuncts for this predicate instance.

Applications: Information Extraction
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
### When is multitask learning effective? Semantic sequence prediction under varying data conditions.
本文是 2017 EACL的一篇长文。MTL被广泛应用于很多任务，特别是语法学的任务。但是，很少有研究能够清楚的解释 MTL什么时候才会起作用。
本文在 MTL的框架下研究了一些语义序列标注的任务。本文的实验结果表明：MTL并不是一直有用，当辅助性任务拥有简洁和一致的标签分布的时候，MTL才会适合。
### An Overview of Multi-Task Learning in Deep Neural Networks
本文是大神 Sebastian Ruder的一篇 Arxiv文章，原本是一篇博客。
Multi-task Learning (MTL)已经成功地应用在很多机器学习的场景下，从自然语言处理和语音处理到计算机视觉和药品识别。这篇文章对 MTL进行了一个概要总结，尤其是基于深度学习的 MTL。

