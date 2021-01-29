---
title: "机器学习实战之logistic回归"
date: 2021-01-29T23:07:46+08:00
draft: true
author: "YazhouTown"
description: "logistic回归算法分析"
tags: [算法, AI]
categories: [机器学习实战]
---

<!--more-->

## 算法基本思想：

​	Logistic回归分析可用于估计某个事件发生的可能性，也可分析某个问题的影响因素有哪些。

​	什么是回归呢？简单来讲，**研究两个变量X与Y之间的统计分析方法的过程就是回归。**

​	**回归的基本思想**：根据训练数据和分类边界线方程（方程参数未知），得到最佳拟合参数集，从而实现数据的分类。

通常，Logistic回归适用于二值型输出分类，即二分类，也就是分类结果只有两种情况：是与否，发生与不发生等。

## 算法流程：

### Sigmod函数：

既然，Logistic回归的输出只有两种情况，那么我们有必要引入一种函数，该函数只有两种输出，0或者1。有人可能会想到单位阶跃函数，没错，确实可以，但是考虑到单位阶跃函数是分段函数，其在x=0处的跳变不方便用代码处理。故此，我们介绍另外一种函数——Sigmod函数。其实，从数学的角度上讲，这也是一种阶跃函数。

> Sigmoid函数也叫Logistic函数，取值范围为(0,1)，它可以将一个实数映射到(0,1)的区间，可以用来做二分类。	

```python
'''
Parameters:
    inX - 数据
Returns:
    sigmoid函数
'''
# 函数说明:sigmoid函数
def sigmoid(inX):
    return 1.0 / (1 + np.exp(-inX))
```

### 梯度上升算法：

- 具体算法：

![](https://gitee.com/isheihei/imagesRepo/raw/master/img/20201205123609.png)

![](https://gitee.com/isheihei/imagesRepo/raw/master/img/20201205123617.png)

- 算法优化：
  - 分为批处理式梯度上升算法和随机梯度上升算法（此处采用随机式）：
    - 批量梯度下降法BGD（Batch Gradient Descent）
      　　　　批量梯度下降法，是梯度下降法最常用的形式，具体做法也就是在更新参数时使用所有的样本来进行更新，这个方法就是前面讲的线性回归的梯度下降算法。
      　　　　![](https://gitee.com/isheihei/imagesRepo/raw/master/img/20201205123620.png)
    - 随机梯度下降法SGD（Stochastic Gradient Descent）
      　　　　随机梯度下降法，其实和批量梯度下降法原理类似，区别在与求梯度时没有用所有的N个样本的数据，而是仅仅选取一个样本j来求梯度。对应的更新公式是：
      　　　![](https://gitee.com/isheihei/imagesRepo/raw/master/img/20201205123624.png)
      　　　　随机梯度下降法，和批量梯度下降法是两个极端，一个采用所有数据来梯度下降，一个用一个样本来梯度下降。自然各自的优缺点都非常突出。对于训练速度来说，随机梯度下降法由于每次仅仅采用一个样本来迭代，训练速度很快，而批量梯度下降法在样本量很大的时候，训练速度不能让人满意。对于准确度来说，随机梯度下降法用于仅仅用一个样本决定梯度方向，导致解很有可能不是最优。对于收敛速度来说，由于随机梯度下降法一次迭代一个样本，导致迭代方向变化很大，不能很快的收敛到局部最优解。所有要通过多次迭代得到最终结果。
  - 批梯度和随机梯度相比较而，批梯度由于使用了所有样本数据，因此每一次都会朝着正确的方向前进，最后能保证收敛于极值点(凸函数收敛于极值点，非凸函数可能会收敛于局部极值点)。而**随机梯度**只用了一个样本数据进行估计真正的梯度，因此每次前进的方向不一定都是正确的，训练过程中会出现波动。**这些波动使得会导致出现“绕远路”现象，即迭代次数变多，甚至收敛速度变慢**。不过从另一个方面来看，随机梯度下降所带来的波动有个好处就是，对于类似盆地区域（即很多局部极小值点）那么这个波动的特点可能会使得优化的方向从当前的局部极小值点跳到另一个更好的局部极小值点，这样便可能对于非凸函数，最终收敛于一个较好的局部极值点，甚至全局极值点。另外，由于随机梯度的期望是梯度本身，即随机梯度以概率收敛于梯度，所以在训练样本足够多的情况下随机梯度和梯度有相似的结果。
    - 解决办法：降低步长，使得后来的数据对回归参数影响较小，减少收敛过程的波动。
  - **周期性波动**的产生，通过随机选取样本来更新回归系数。

```python
'''
Parameters:
    dataMatrix - 数据数组
    classLabels - 数据标签
    numIter - 迭代次数
Returns:
    weights - 求得的回归系数数组(最优参数)
'''

# 函数说明:改进的随机梯度上升算法
def stocGradAscent1(dataMatrix, classLabels, numIter=150):
    m,n = np.shape(dataMatrix)                                         #返回dataMatrix的大小。m为行数,n为列数。
    weights = np.ones(n)                                               #参数初始化
    #存储每次更新的回归系数
    for j in range(numIter):
        dataIndex = list(range(m))
        for i in range(m):
            alpha = 4/(1.0+j+i)+0.01                                  #降低alpha的大小，每次减小1/(j+i)。
            randIndex = int(random.uniform(0,len(dataIndex)))         #随机选取样本
            h = sigmoid(sum(dataMatrix[randIndex]*weights))           #选择随机选取的一个样本，计算h
            error = classLabels[randIndex] - h                        #计算误差
            weights = weights + alpha * error * dataMatrix[randIndex] #更新回归系数
            del(dataIndex[randIndex])                                 #删除已经使用的样本
    return weights
```

### 分类函数：

```python
'''
Parameters:
    inX - 特征向量
    weights - 回归系数
Returns:
    分类结果
'''
# 函数说明:分类函数
def classifyVector(inX, weights):
    prob = sigmoid(sum(inX*weights))
    if prob > 0.5: return 1.0
    else: return 0.0
```

