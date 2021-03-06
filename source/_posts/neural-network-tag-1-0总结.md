title: neural-network-tag-1.0总结
date: 2016-01-05 15:50:27
tags: [work, neural-network]
---
## 调试参数
初始化weight和bias使用的是高斯分布
### 调试hidden layer neuron数目
|__hidden layer neuron数目__|__最好的正确率(mini batch size = 10, eta = 4.0)__|__best epoch__|
|----------------|--------------------------------------------|----------|
|50              |78.05%                                      |95        |
|60              |81.34%                                      |258       |
|70              |75.80%                                      |224       |
|80              |72.92%                                      |269       |
|100             |72.93%                                      |58        |
<!-- more -->
### 调试mini batch size
|__mini batch size__|__最好的正确率(hidden layer neuron数目 = 50, eta = 4.0)__|__best epoch__|
|---------------|---------------------------------------------|----------|
|5              |74.55%                                       |75        |
|10             |86.77%                                       |143       |
|20             |78.55%                                       |155       |
|30             |84.23%                                       |130       |
|50             |81.99%                                       |190       |
|80             |59.13%                                       |142       |
|100            |75.23%                                       |62        |
### 调试 eta
|__eta__|__最好的正确率(hidden layer neuron数目 = 50, mini batch size = 10)__|__best epoch__|
|---|---------------------------------------------------------------|----------|
|1  |79.99%                                                         |128       |
|2  |82.53%                                                         |143       |
|3  |86.12%                                                         |266       |
|4  |84.62%                                                         |220       |
|5  |79.43%                                                         |78        |
|8  |75%                                                            |261       |
## 调试随机初始化的问题
因为weight和bias是利用高斯分布进行初始化的，可能对最终的结果产生一定的影响，所以此处循环5词执行相同的程序，看看最终的正确率如何。

|__times__|__最好的正确率(hidden layer neuron数目 = 50, mini batch size = 10, eta = 3.0)__|__epoch = 1__|__best epoch__|
|-----|--------------------------------------------------------------------|---------|-------|
|1    |86.44%                                                              |51.76%   |156    |
|2    |79.53%                                                              |62.59%   |87     |
|3    |85.79%                                                              |54.96%   |158    |
|4    |81.83%                                                              |58.69%   |184    |
|5    |80.34%                                                              |61.96%   |119    |
在此我们可以看到，高斯分布的初始化对正确率的影响还是挺大的，目前的工作暂时告一段落，接下来我们将对程序进行进一步的优化。

