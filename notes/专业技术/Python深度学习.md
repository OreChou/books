## 第一章 什么是深度学习

人工智能、机器学习、深度学习三者的包含关系。

人工智能诞生于 20 世纪 50 年代，70 年代初和 90 年代初经历了两次严冬。

经典的程序设计：根据规则 + 数据，求解答案。

机器学习：根据数据 + 答案，求解规则。

机器学习和深度学习的核心问题在于**有意义地变化数据**，即学习输入数据的有用表示（representation），可以让数据更加接近预期输出。

深度学习属于机器学习的一个分支：它是从数据中学习表示的一种新方法，强调用连续的**层**（layer）中进行学习，这些层对应于越来越有意义的表示。

机器学习进步的技术力量：硬件、数据集与基准、算法上的改进。



## 第二章 神经网络的数学基础

本章通过 keras 代码介绍了神经网络中的部分数据基础。在 keras 中所有数据都表示为张量。

张量的概念，在计算机科学的研究领域中，其核心在于它是一个数据容器。张量可以视为矩阵向任意维度的推广，张量的维度（dimension）通常被称为轴（axis）。标量（scalar）是0维（0D）张量，向量（vector）是1维（1D）张量，矩阵（matrix）是2维（2D）张量。

在深度学习中，所有数据张量的第一个轴通常是样本轴（samples axis），也可称样本维度。

深度学习的几何解释：神经网络完全由一系列张量运算组成，张量运算的本质是输入数据的几何变换。所以神经网络的内容即是为复杂的、高度折叠的数据流形找到简洁的表示，神经网络的每一层都通过变换将数据解开一点点。

小批量随机梯度下降（mini-batch stochastic gradient descent, 小批量SGD）。

避免陷入局部极小点：动量方法，即每次更新参数 w 不仅要考虑当前的梯度值，还要考虑上一次的参数更新。

链式求导：反向传播算法（backpropagation），即 ` (fg(x))' = f'(g(x))*g'(x)` 。



## 第三章 神经网络入门

使用神经网络的三个常用场景：二分类问题、多分类问题、标量回归问题。

神经网络的主要四个方面：

1. 层，多个层组成网络（模型）。
2. 输入数据 与 相应目标。
3. 损失函数，用于学习的反馈信号。
4. 优化器，决定学习过程如何进行。

一些 tips：

1. 神经网络在训练数据上的表现越来越好，模型最终会过拟合，并在未见的数据上表现越来越差。所以要监控模型在训练集以外的数据上的性能 。
2. 回归常用的损失函数是均方误差（MSE）。
3. 回归常用的指标是平均绝对误差（MAE）。
4. 对于输入数据不同数据范围的特征，应先进行预处理，对每个特征进行单独缩放。



## 第四章 机器学习基础

机器学习的四个分支：监督学习、无监督学习（降维、聚类）、自监督学习（自编码器）、强化学习。

评估机器学习模型：训练集、验证集和测试集。

模型配置，超参数（hyperparameter）：即网络的层数和每层大小。（超参数与模型参数（权重）不一样）

调节神经网络模型配置的过程也是一个学习的过程：在某个参数空间中寻找良好的模型配置。

#### 为什么数据要划分为：训练集、验证集和测试集，而不是训练集与测试集？

如果模型只基于验证集上的性能来调节模型配置，容易发生过拟合。其原因在于信息泄露（information leak），即基于模型在验证集上的性能来调节模型超参数，都会有一些关于验证集数据的信息泄露到模型中。以此来修改模型的超参数（手动，次数较多的话），会使得模型能在验证集上取得非常好的性能（人为造成）。所以应该使用一个完全不同的、前所未见的数据集来评估模型。

+ 简单留出验证（hold-out validation）
+ K 折验证（K-fold validation）
+ 带有打乱数据的重复 K 折验证（Iterated K-fold validation with shuffling）

#### 模型评估的注意事项

+ **数据代表性（data representativeness）**：训练集和测试集都应该代表当前数据。通常的做法是随机打乱数据。
+ **时间箭头（the arrow of time）**：根据过去预测未来，不应随机打乱数据，会造成时间泄露（temporal leak），即模型在未来的数据上得到了有效训练。应确保测试集中所有数据的时间晚于训练集数据。（对于这一点，我在这里有点自己的想法。其实这还是看神经网络模型训练完之后用于的场景。例如我现在的研究情况，我有一年的空气质量数据，而我要训练一个通用的预测模型，把一年的数据按时间划分似乎不太合理。因为即是用春夏秋的数据，去预测冬天的空气质量。并没有学习到冬季空气质量变化的规律。）
+ **数据冗余（redundancy in your data）**：确保训练集和验证集之间没有交集。

模型的输入数据应该经过预处理后满足两个特征：

1. 取值较小：大部分值都应该在 0~1 之间。
2. 同质性（homogenous）：所有特征的取值应该在大致相同的范围内。

**奥卡姆剃刀原理（Occam's razor）**：如果一件事有两种解释，那么最可能的解释就是最简单的那个，即假设最少的那个。在深度学习中，给定一些训练数据和一种网络架构，很多组权重值（模型）都可以解释这些数据，简单模型比复杂模型更加不容易过拟合。

**权重正则化（Weight regularization）**：强制让模型的权重取较小的值，从而限制模型的复杂度，这样使得权重值的分布更加规则（regular）。实现方法是向网络损失函数中添加与较大权重值相关的成本（cost）。

深度学习模型很擅长拟合数据，但真正的难度在于泛化，而不是在于拟合。

深度学习中解决过拟合的方法：

+ 获取更多的训练数据
+ 减小网络容量
+ 添加权重正则化
+ 添加 dropout



## 第六章 深度学习用于文本和序列

处理序列数据的深度学习算法：循环神经网络(recurrent neural network)和一维卷积神经网络(1D convert)。

应用：

+ 文档分类和时间序列分类
+ 时间序列对比
+ 序列到序列的学习
+ 情感分析
+ 时间序列预测

词嵌入的方法：

1. 在完成主任务的同时学习词嵌入。其学习方式与学习神经网络的权重相同。
2. 预训练词嵌入（pretrained word embedding）在不同于待解决问题的机器学习任务上预计算好词嵌入，然后再将其加载到模型中。

### 循环神经网络

处理序列的方式为：遍历所有序列元素，并保存一个状态，其中包含与已查看内容相关的信息。其中环（loop）和状态（state）是两个很重要的概念。

```
// RNN 伪代码
state_t = initial_state
for input_t in input_sequence:
		output_t = f(input_t, state_t)
		state_t = output_t
```









