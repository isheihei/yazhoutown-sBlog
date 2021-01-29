---
title: "机器学习实战之AdaBoost"
date: 2021-01-29T22:57:49+08:00
draft: true
author: "YazhouTown"
description: "AdaBoost算法分析"
tags: [AI]
categories: [机器学习实战]
---

<!--more-->

## 算法思想

​	Adaboost算法基本原理就是将多个弱分类器（弱分类器一般选用单层决策树）进行合理的结合，使其成为一个强分类器。

## 核心算法

### 单层决策树

​	如果训练数有多维特征，单层决策树也只能选择其中一维特征来做决策，并且还有一个关键点，决策的阈值也需要考虑。根据不同特征的不同权值，可以计算出一颗加权错误率最低的决策树，即最佳决策树。

流程

- 将最小错误率设置为无穷大
- 对数据集中的每一个特征
  - 对每个步长（max - min / snumteps）
    - 对lt和gt两种情况：
      - 建立一棵单层决策树，并利用数据加权值计算误差
      - 如果错误率低于最小错误率，更新
- 返回最佳单层决策树

```python
"""
Parameters:
    dataArr - 数据矩阵
    classLabels - 数据标签
    D - 样本权重
Returns:
    bestStump - 最佳单层决策树信息
    minError - 最小误差
    bestClasEst - 最佳的分类结果
"""

# 找到数据集上最佳的单层决策树
def buildStump(dataArr, classLabels, D):
    dataMatrix = np.mat(dataArr);
    labelMat = np.mat(classLabels).T
    m, n = np.shape(dataMatrix)
    numSteps = 10.0;
    bestStump = {};
    bestClasEst = np.mat(np.zeros((m, 1)))
    minError = float('inf')  # 最小误差初始化为正无穷大
    for i in range(n):  # 遍历所有特征
        rangeMin = dataMatrix[:, i].min();
        rangeMax = dataMatrix[:, i].max()  # 找到特征中最小的值和最大值
        stepSize = (rangeMax - rangeMin) / numSteps  # 计算步长
        for j in range(-1, int(numSteps) + 1):
            for inequal in ['lt', 'gt']:  # 大于和小于的情况，均遍历。lt:less than，gt:greater than
                threshVal = (rangeMin + float(j) * stepSize)  # 计算阈值
                predictedVals = stumpClassify(dataMatrix, i, threshVal, inequal)  # 计算分类结果
                errArr = np.mat(np.ones((m, 1)))  # 初始化误差矩阵
                errArr[predictedVals == labelMat] = 0  # 分类正确的,赋值为0
                weightedError = D.T * errArr  # 计算误差
                # print("split: dim %d, thresh %.2f, thresh ineqal: %s, the weighted error is %.3f" % (i, threshVal, inequal, weightedError))
                if weightedError < minError:  # 找到误差最小的分类方式
                    minError = weightedError
                    bestClasEst = predictedVals.copy()
                    bestStump['dim'] = i
                    bestStump['thresh'] = threshVal
                    bestStump['ineq'] = inequal
    return bestStump, minError, bestClasEst
```

```python
"""
Parameters:
    dataMatrix - 数据矩阵
    dimen - 第dimen列，也就是第几个特征
    threshVal - 阈值
    threshIneq - 标志
Returns:
    retArray - 分类结果
"""


# 单层决策树分类函数
def stumpClassify(dataMatrix, dimen, threshVal, threshIneq):
    retArray = np.ones((np.shape(dataMatrix)[0], 1))  # 初始化retArray为1
    if threshIneq == 'lt':
        retArray[dataMatrix[:, dimen] <= threshVal] = -1.0  # 如果小于阈值,则赋值为-1
    else:
        retArray[dataMatrix[:, dimen] > threshVal] = -1.0  # 如果大于阈值,则赋值为-1
    return retArray
```

### Adaboost分类器的权重

​	Adaboost中若干个分类器的关系是第N个分类器更可能分对第N-1个分类器没分对的数据，而不能保证以前分对的数据也能同时分对。所以在Adaboost中，每个弱分类器都有各自最关注的点，每个弱分类器都只关注整个数据集的中一部分数据，所以它们必然是共同组合在一起才能发挥出作用。所以最终投票表决时，需要根据弱分类器的权重来进行加权投票，权重大小是根据弱分类器的分类错误率计算得出的，总的规律就是弱分类器错误率越低，其权重就越高。

​	该算法是对同一组数据进行一步步迭代的过程，根据该轮分类器的错误率调整。

流程

- 对每次迭代
  - 利用buildStumo()函数找到最佳的单层决策树
  - 将最佳单层决策树加入到单层决策树数组
  - 计算alpha（该轮的分类器权重）
  - 计算新的权重向量D
  - 更新累计类别估计值（该轮权重*类别标签）
  - 如果错误率等于0，退出

```python
"""
Parameters:
    dataArr - 数据矩阵
    classLabels - 数据标签
    numIt - 最大迭代次数
Returns:
    weakClassArr - 训练好的分类器
    aggClassEst - 类别估计累计值
"""

# 使用AdaBoost算法提升弱分类器性能
def adaBoostTrainDS(dataArr, classLabels, numIt=40):
    weakClassArr = []
    m = np.shape(dataArr)[0]
    D = np.mat(np.ones((m, 1)) / m)  # 初始化权重
    aggClassEst = np.mat(np.zeros((m, 1)))
    for i in range(numIt):
        bestStump, error, classEst = buildStump(dataArr, classLabels, D)  # 构建单层决策树
        # print("D:",D.T)
        alpha = float(0.5 * np.log((1.0 - error) / max(error, 1e-16)))  # 计算弱学习算法权重alpha,使error不等于0,因为分母不能为0
        bestStump['alpha'] = alpha  # 存储弱学习算法权重
        weakClassArr.append(bestStump)  # 存储单层决策树
        # print("classEst: ", classEst.T)
        expon = np.multiply(-1 * alpha * np.mat(classLabels).T, classEst)  # 计算e的指数项
        D = np.multiply(D, np.exp(expon))
        D = D / D.sum()  # 根据样本权重公式，更新样本权重
        # 计算AdaBoost误差，当误差为0的时候，退出循环
        aggClassEst += alpha * classEst  # 计算类别估计累计值
        # print("aggClassEst: ", aggClassEst.T)
        aggErrors = np.multiply(np.sign(aggClassEst) != np.mat(classLabels).T, np.ones((m, 1)))  # 计算误差
        errorRate = aggErrors.sum() / m
        # print("total error: ", errorRate)
        if errorRate == 0.0: break  # 误差为0，退出循环
    return weakClassArr, aggClassEst
```

### 分类器

​	根据训练好的分类器，遍历每一个分类器字典，用stumpClassify()进行分类，然后分类结果*alpha并累加，最后用np.sign()符号函数返回最终的分类结果。

```python
"""
Parameters:
    datToClass - 待分类样例
    classifierArr - 训练好的分类器
Returns:
    分类结果
"""


# AdaBoost分类函数
def adaClassify(datToClass, classifierArr):
    dataMatrix = np.mat(datToClass)
    m = np.shape(dataMatrix)[0]
    aggClassEst = np.mat(np.zeros((m, 1)))
    print(len(classifierArr))
    for i in range(len(classifierArr)):  # 遍历所有分类器，进行分类
        classEst = stumpClassify(dataMatrix, classifierArr[i]['dim'], classifierArr[i]['thresh'],
                                 classifierArr[i]['ineq'])
        aggClassEst += classifierArr[i]['alpha'] * classEst
        # print(aggClassEst)
    return np.sign(aggClassEst)
```

