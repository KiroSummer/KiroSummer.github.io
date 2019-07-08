---
layout: post
title: Abstract-meaning-representations
date: 2019-06-27 15:18:05
password: suda_kiro
tags: [AMR]
---
# 简介：
为了参加 CoNLL-2019 shared task，其中有一个 task就是 AMR Parsing，所以开这个博客记录一下相关的实验。

# 参考代码以及实验结果：
## amr as graph prediction
[amr as graph prediction](https://github.com/ChunchuanLv/AMR_AS_GRAPH_PREDICTION)

| __Notes__| __Test__|
|----------|---------|
|Paper (2016E25) |74.4 |

## MRP参赛的具体实验流程记录
### 实验数据的处理
因为 mrp提供的数据是 json格式的，而且是和其他四种语义表示统一成了一个格式，所以不适合我们利用上面的模型进行处理。
为了能够利用以上的模型进行处理，我们需要将 __mrp格式转换成 amr格式__的数据。所以，写了转换脚本，同时，为了保证我们转换的数据尽量准确而且能够从 __amr转换至 mrp格式__，我们写了两个脚本。保证了转换的准确。
### Train和 Dev的划分
因为 mrp仅仅提供了 traning data，并没有提供 dev data。所以，我们需要从 training data中划分出 dev data。

