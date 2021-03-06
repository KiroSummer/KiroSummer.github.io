---
title: End-to-End-SRL
date: 2019-05-30 18:00:25
password: suda_kiro
tags: [SRL]
---
# Introduction (2018-12-1)
Begin the work on the end-to-end framework SRL.

# add the self-attention into the lsgn
## 尝试修改模型参数 512->2018
| __Path__| __Notes__| __Devel__| __Test WSJ__| __Test Brown__|
|---------|----------|----------|-------------|---------------|
|~/lsgn-extension/exp-baseline-w-self-attention-encoder-v4| lr 0.0002|20.19 |  |  |
|~/lsgn-extension/exp-baseline-w-self-attention-encoder-v4-lr-0.001| lr 0.001 |20.36 |  |  |
|~/lsgn-extension/exp-baseline-w-self-attention-encoder-v4-lr-0.00001| lr 0.00001|79.80 | | |
|~/lsgn-extension/exp-baseline-w-self-attention-encoder-v4-lr-0.000025| lr 0.000025|80.73 | | |
|~/lsgn-extension/exp-baseline-w-self-attention-encoder-v4-lr-0.00003| lr 0.00003|__80.78__ | | |
|~/lsgn-extension/exp-baseline-w-self-attention-encoder-v4-lr-0.000035| lr 0.000035|80.22 | | |
|~/lsgn-extension/exp-baseline-w-self-attention-encoder-v4-lr-0.00004| lr 0.00004|80.35 | | |
|~/lsgn-extension/exp-baseline-w-self-attention-encoder-v4-lr-0.000045| lr 0.000045|__81.10__ | | |
|~/lsgn-extension/exp-baseline-w-self-attention-encoder-v4-lr-0.00005| lr 0.00005|80.25 | | |
|~/lsgn-extension/exp-baseline-w-self-attention-encoder-v4-lr-0.000055| lr 0.000055|80.57 | | |
|~/lsgn-extension/exp-baseline-w-self-attention-encoder-v4-lr-0.00006| lr 0.00006|80.45 | | |
|~/lsgn-extension/exp-baseline-w-self-attention-encoder-v4-lr-0.00007| lr 0.00007|80.27 | | |
|~/lsgn-extension/exp-baseline-w-self-attention-encoder-v4-lr-0.00008| lr 0.00008|80.12 | | |

## 尝试利用 MLP output as the predicate and arguemnt representations

| __Path__| __Notes__| __Devel__| __Test WSJ__| __Test Brown__|
|---------|----------|----------|-------------|---------------|
|~/lsgn-extension/exp-baseline-w-self-attention-encoder-v3-blocks-6| 6 |__78.26__|  |  |
|~/lsgn-extension/exp-baseline-w-self-attention-encoder-v3-pre-arg-rep |MLP output -> srl scorer |77.31 |  |  |
|~/lsgn-extension/exp-baseline-w-self-attention-encoder-v3-pre-arg-rep-v2| MLP output-> srl projection|50.70 |  |  |

## loss的尝试以及 learning rate的调整
| __Path__| __Notes__| __Devel__| __Test WSJ__| __Test Brown__|
|---------|----------|----------|-------------|---------------|
|~/lsgn-extension/exp-baseline-w-self-attention-encoder-v3-blocks-6| 6 |__78.26__|  |  |
|~/lsgn-extension/exp-baseline-w-self-attention-encoder-v3-adjust-lr| 调整 learning rate as he 2018|78.16 |  |  |
|~/lsgn-extension/exp-baseline-w-self-attention-encoder-v3-adjust-lr-paper|调整 learning rate根据 paper| 76.81| | |
|~/lsgn-extension/exp-baseline-w-self-attention-encoder-v3-loss-divide-sum| 调整 loss / pair num sum |78.22 | | |
|~/lsgn-extension/exp-baseline-w-self-attention-encoder-v3-loss-divide-predicate-num| 调整 loss / predicate num| 77.70| | |


## 用 position embedding代替 timing signal之后的 attention blocks调参

| __Path__| __Notes__| __Devel__| __Test WSJ__| __Test Brown__|
|---------|----------|----------|-------------|---------------|
|~/lsgn-extension/exp-baseline-w-self-attention-encoder-v3|4  | 77.24| | |
|~/lsgn-extension/exp-baseline-w-self-attention-encoder-v3-blocks-6| 6 |__78.26__|  |  |
|~/lsgn-extension/exp-baseline-w-self-attention-encoder-v3-blocks-8|8 |78.20 |  |  |


## clip gradient 调参
| __Path__| __Notes__| __Devel__| __Test WSJ__| __Test Brown__|
|---------|----------|----------|-------------|---------------|
|~/lsgn-extension/exp-baseline-using-self-attention-encoder-lr-0.0002|没有 clip gradient  |__77.47__ |  |  |
|~/lsgn-extension/exp-baseline-w-self-attention-encoder-v2 | gradient clip = 1.0 |77.31   |  | |
|~/lsgn-extension/exp-baseline-w-self-attention-encoder-v2-grad-clip-5.0 | gradient clip = 5.0 |76.91   |  | |
|~/lsgn-extension/exp-baseline-w-self-attention-encoder-v2-grad-clip-10.0 | gradient clip = 10.0 |76.97   |  | |

## batch size调参
因为设置了 max token=700，所以 batch size可能有点唬人。。

| __Path__| __Notes__| __Devel__| __Test WSJ__| __Test Brown__|
|---------|----------|----------|-------------|---------------|
|~/lsgn-extension/exp-baseline-using-self-attention-encoder-lr-0.0002|20 1991 batches  |__77.47__ |  |  |
|~/lsgn-extension/exp-baseline-self-attention-encoder-bs-32 |32 1391 |77.08  |  |  |
|~/lsgn-extension/exp-baseline-self-attention-encoder-bs-40 |40 1386 |76.91  |  |  |
|~/lsgn-extension/exp-baseline-self-attention-encoder-bs-50 |50 1386 |77.41  |  |  |

## self-attention layers调参数
| __Path__| __Notes__| __Devel__| __Test WSJ__| __Test Brown__|
|---------|----------|----------|-------------|---------------|
|~/lsgn-extension/exp-baseline-using-self-attention-encoder-lr-0.0002|4  |__77.47__ |  |  |
|~/lsgn-extension/exp-baseline-self-attention-encoder-blocks-6| 6  |77.34  |  |  |
|~/lsgn-extension/exp-baseline-self-attention-encoder-blocks-8| 8  |77.01  |  |  |
|~/lsgn-extension/exp-baseline-self-attention-encoder-blocks-10| 10  |77.31  |  |  |

## learning rate调参
| __Path__| __Notes__| __Devel__| __Test WSJ__| __Test Brown__|
|---------|----------|----------|-------------|---------------|
|~/lsgn-extension/exp-baseline-using-self-attention-encoder| lr 0.01 |21.9|  |  |
|~/lsgn-extension/exp-baseline-using-self-attention-encoder-lr-0.001|  | 26.93|  |  |
|~/lsgn-extension/exp-baseline-using-self-attention-encoder-lr-0.0008|  |70.39 |  |  |
|~/lsgn-extension/exp-baseline-using-self-attention-encoder-lr-0.0005|  |75.94 |  |  |
|~/lsgn-extension/exp-baseline-using-self-attention-encoder-lr-0.0002|  |__77.47__ |  |  |
|~/lsgn-extension/exp-baseline-using-self-attention-encoder-lr-0.0001|  | 76.85|  |  |
|~/lsgn-extension/exp-baseline-using-self-attention-encoder-lr-0.00001|  |61.38 |  |  |
|~/lsgn-extension/exp-baseline-using-self-attention-encoder-lr-0.000001|  |16.54 |  |  |

# Trials on lsgn-extension (begin from 2019-3-1)

| __Path__| __Devel__|  __Test WSJ__| __Test Brown__| __Comment__|
|---------|----------|--------------|---------------|------------|
|n126 ~/lsgn-extension/exp-baseline  |82.02  |82.76 |70.71 | | 
|n126 ~/lsgn-extension/exp-baseline-re-run|81.53  |82.90 |70.63 | |
|n126 ~/lsgn-extension/exp-baseline-lstm-layer1-for-predicate-prediction |81.74 |82.72 |70.77 | |
|n126 ~/lsgn-extension/exp-baseline-sentence-level-span-attention |81.73 |82.81 |71.16 | |
|n126 ~/lsgn-extension/exp-baseline-softmax-weights-in-srl-score |81.72 |82.9 |70.61 | |
|n126 ~/lsgn-extension/exp-baseline-span-level-attention-based-BiLSTM |81.95 |82.77 |70.70% | |
|n126 ~/lsgn-extension/exp-baseline-srl-using-biaffine-scorer |79.81 |81.71 |67.97 | |
|n141 lsgn-extension/exp-baseline |81.86 |82.82 |70.62 | |
|n141 lsgn-extension/exp-baseline-using-semi-CRF |81.67 |82.61 | 69.89 | |

<!--more-->
# Re-implementation of LSGN
The following experiments are all conducted on the CoNLL-2005 dataset.
## w/o Gold predicates
| __Path__| __Devel__|  __Test WSJ__|  __Test Brown__|
|---------|----------|--------------|----------------|
| __Baseline paper__ |81.6% |82.5% |70.8%  |
| __lsgn code__ | 82.09%  |82.88% |70.76% |
|n141 kiro ~/Work/SRL/lsgn-pytorch/exp-baseline|81.64% |82.72% |69.1% |
|n141 lsgn-pytorch/exp-baseline-re-check | 81.47%| 82.83%| 71.08%|
|n141 lsgn-pytorch/exp-baseline-re-check-2 | 81.82%| 82.56%| 70.19%|
|n141 lsgn-pytorch/exp-baseline-re-check-re-run-3 | 81.98%| 82.68%| 70.45%|
|n141 lsgn-pytorch/exp-baseline-re-check-w-mask| 81.78% | 82.65%| 69.99%|
|n141 lsgn-pytorch/exp-baseline-re-check-w-mask-2| 81.37% | 82.78%| 70.50%|
|n141 lsgn-pytorch/exp-baseline-re-check-w-mask-full-embed| 81.70% | 82.65%| 70.49%|
|n126 lsgn-pytorch/exp-baseline-re-check-w-mask-minus| 81.85%| 82.61% | 70.14%|
|n141 lsgn-pytorch/exp-baseline-re-check-w-mask-minus-full-embed| 81.41%| 82.71% | 70.44%|

*Remainder* 
* The results of the code higher than the paper presentated? Maybe because the model run more than 320,000 times? I don't know why the code cannot stop when it reach the 320,000 global step...
* I remember the model in my desktop can reach 70.9% F1 score in test brown dataset, but i lost it... So, i re-run the same model for several times to check this.
* I think i should write the full eval process.

## w Gold predicates

| __Path__| __Devel__| __Test WSJ__| __Test Brown__|
|---------|----------|-------------|---------------|
|Baseline paper|  --|  83.9%|  73.7%  |
|n141 lsgn-pytorch/exp-baseline-gold-predicates |82.64% |83.95% |72.7% |

## Trials on LSGN-pytorch

| __Path__| __Devel__| __Test WSJ__| __Test Brown__|
|---------|----------|-------------|---------------|
|n126 lsgn-pytorch/exp-baseline-w-o-head-embed | 81.71% | 82.8%| 70.6%|
|n126 lsgn-pytorch/exp-baseline-w-o-head-embed-and-span-attention-sum-argu | 81.8% | 82.59%| 71.2%|
|n126 ~/lsgn-pytorch/exp-baseline-w-o-head-embed-and-span-attention| 81.38%| 82.2%| 69.89%|
|n141 kiro ~/Work/SRL/lsgn-pytorch/exp-baseline-re-run-2|81.17% |82.70% |69.98% |
|n141 lsgn-pytorch/exp-baseline-re-check-w-o-head-embed-and-span-attention-sum-arg | 81.66%| 83.03%| 70.11%|
|n141 kiro lsgn-pytorch/exp-baseline-gold-predicates-re-run-2| 82.53%| 83.77%| 72.09%|

*Question:* 
* is the model not stable on the test brown data (out-of-domain)?
* It's very interesting that w-o head embedding, span attention ans only use sum of argument works still very well.

# sa-e2e-srl
## data
Because of the null srl structure in conll05 data, the data in the DeepSRL is fewer than in LSGN.
In order to do the sa-based experiments, we should collect all the gold and auto dep trees in CoNLL-2005 data respectively.
The processes are as follows:
1. the training and test-wsj data are provided by baidu, as in the dependency parsing.
2. the dev and test-brown are extracted from the corpus, then pass the stanford pos tagger and biaffine parser to obain the auto pos taggs and dependency trees respestively.

| __Path__| __Devel__| __Test WSJ__| __Test Brown__|
|---------|----------|-------------|---------------|
|n126 ~/sa-e2e-srl/exp-baseline|81.78% | 82.38%| 70.85%|
|n141 ~/sa-e2e-srl/exp-baseline|81.62% | 82.65%| 70.18%|
