---
title: "机器学习实战之RegressionTrees"
date: 2021-01-29T23:09:44+08:00
draft: true
author: "YazhouTown"
description: "回归树（Regression Trees）算法分析"
tags: [算法, AI]
categories: [机器学习实战]
---

<!--more-->

## 算法基本思想

### CART 树简介

​	ID3  决策树利用信息增益和信息增益比划分数据集。但是这种决策树是有缺陷的，即按某特征划分后，该特征将不会在后面的划分中出现。这就导致了划分过于迅速，从而影响分类结果。在这将要介绍的 CART（Classification And Regression Tree）树，即分类回归树利用二分策略，有效地避免了划分过于迅速这一问题。而且二分策略可以直接处理连续型属性值。

### 回归树

回归树示例：

![](https://gitee.com/isheihei/imagesRepo/raw/master/img/20201205124537.jpg)

**二分**

回归树也利用二分划分数据。与分类树不同的是，回归树将特征值大于切分点值的数据划分为左子树，将特征值小于等于切分点值的数据划分为右子树。

**平方误差**

不同于分类树，回归树用平方误差选择切分点。若数据集按特征取值是否大于切分点值划分为两部分，则在特征 A 下，集合 D 的平方误差为：![](https://gitee.com/isheihei/imagesRepo/raw/master/img/20201205124553.png)

## 核心算法

### 构建树

​	chooseBestSplit(dataSet, leafType, errType, ops)函数返回最佳切分的特征和特征值。ops包含两个参数，ops[1]是最小误差，小于该误差就不更新切分特征；ops[2]是切分的最小样本数，小于该样本会导致过拟合，也不更新切分特征。

leafType和 errType 参数是可变的，后面会讲到，分为回归树和模型树。

```python
# 函数说明:树构建函数
"""
Parameters:
    dataSet - 数据集合
    leafType - 建立叶结点的函数
    errType - 误差计算函数
    ops - 包含树构建所有其他参数的元组
"""
def createTree(dataSet, leafType = regLeaf, errType = regErr, ops = (1, 4)):
    #选择最佳切分特征和特征值
    feat, val = chooseBestSplit(dataSet, leafType, errType, ops)
    #r如果没有特征,则返回特征值
    if feat == None: return val
    #回归树
    retTree = {}
    retTree['spInd'] = feat
    retTree['spVal'] = val
    #分成左数据集和右数据集
    lSet, rSet = binSplitDataSet(dataSet, feat, val)
    #创建左子树和右子树
    retTree['left'] = createTree(lSet, leafType, errType, ops)
    retTree['right'] = createTree(rSet, leafType, errType, ops)
    return retTree
```

### 找到最佳二元切分方式

```python
# 函数说明:找到数据的最佳二元切分方式函数
"""
Parameters:
    dataSet - 数据集合
    leafType - 生成叶结点
    regErr - 误差估计函数
    ops - 用户定义的参数构成的元组
"""
def chooseBestSplit(dataSet, leafType=regLeaf, errType=regErr, ops=(1, 4)):
    import types
    # tolS允许的误差下降值,tolN切分的最少样本数
    tolS = ops[0];
    tolN = ops[1]
    # 如果当前所有值相等,则退出。(根据set的特性)
    if len(set(dataSet[:, -1].T.tolist()[0])) == 1:
        return None, leafType(dataSet)
    # 统计数据集合的行m和列n
    m, n = np.shape(dataSet)
    # 默认最后一个特征为最佳切分特征,计算其误差估计
    S = errType(dataSet)
    # 分别为最佳误差,最佳特征切分的索引值,最佳特征值
    bestS = float('inf');
    bestIndex = 0;
    bestValue = 0
    # 遍历所有特征列
    for featIndex in range(n - 1):
        # 遍历所有特征值
        for splitVal in set(dataSet[:, featIndex].T.A.tolist()[0]):
            # 根据特征和特征值切分数据集
            mat0, mat1 = binSplitDataSet(dataSet, featIndex, splitVal)
            # 如果数据少于tolN,则退出
            if (np.shape(mat0)[0] < tolN) or (np.shape(mat1)[0] < tolN): continue
            # 计算误差估计
            newS = errType(mat0) + errType(mat1)
            # 如果误差估计更小,则更新特征索引值和特征值
            if newS < bestS:
                bestIndex = featIndex
                bestValue = splitVal
                bestS = newS
    # 如果误差减少不大则退出
    if (S - bestS) < tolS:
        return None, leafType(dataSet)
    # 根据最佳的切分特征和特征值切分数据集合
    mat0, mat1 = binSplitDataSet(dataSet, bestIndex, bestValue)
    # 如果切分出的数据集很小则退出
    if (np.shape(mat0)[0] < tolN) or (np.shape(mat1)[0] < tolN):
        return None, leafType(dataSet)
    # 返回最佳切分特征和特征值
    return bestIndex, bestValue
```

### 根据特征切分数据集合

```python
# 函数说明:根据特征切分数据集合
"""
Parameters:
    dataSet - 数据集合
    feature - 带切分的特征
    value - 该特征的值
"""
def binSplitDataSet(dataSet, feature, value):
    mat0 = dataSet[np.nonzero(dataSet[:, feature] > value)[0], :]
    mat1 = dataSet[np.nonzero(dataSet[:, feature] <= value)[0], :]
    return mat0, mat1
```

### 生成叶节点和误差估计函数

#### 回归树

​	回归树的叶节点是特征值，误差是算术平方。

```python
# 函数说明:生成叶结点
"""
Parameters:
    dataSet - 数据集合
"""
def regLeaf(dataSet):
    return np.mean(dataSet[:, -1])

# 函数说明:误差估计函数
"""
Parameters:
    dataSet - 数据集合
"""
def regErr(dataSet):
    return np.var(dataSet[:, -1]) * np.shape(dataSet)[0]
```

#### 模型树

​	模型树的叶节点是线性回归的参数，误差是根据回归参数的估计值与实际值的误差计算出来的。

```python
def linearSolve(dataSet):  
    m,n = np.shape(dataSet)
    X = np.mat(np.ones((m,n))); Y = np.mat(np.ones((m,1)))
    X[:,1:n] = dataSet[:,0:n-1]; Y = dataSet[:,-1]
    xTx = X.T*X
    if np.linalg.det(xTx) == 0.0:
        raise NameError('This matrix is singular, cannot do inverse,\n\
        try increasing the second value of ops')
    ws = xTx.I * (X.T * Y)
    return ws,X,Y

def modelLeaf(dataSet):#create linear model and return coeficients
    ws,X,Y = linearSolve(dataSet)
    return ws

def modelErr(dataSet):
    ws,X,Y = linearSolve(dataSet)
    yHat = X * ws
    return np.sum(np.power(Y - yHat,2))
```

### 剪枝

#### 预剪枝

​	即根据ops参数对于前后误差变化过小和切分数据集过小的分支不予创建。

#### 后剪枝

​	使用后剪枝方法需要将数据集分成测试集和训练集。首先指定参数，使得构建出的树足够大、足够复杂，便于剪枝。接下来从上而下找到叶结点，用测试集来判断这些叶结点合并是否能降低测试集误差。如果是的话就合并。

​	伪代码：

基于已有的树切分测试数据:    

- 如果存在任一子集是一棵树，则在该子集递归剪枝过程   
  -  计算将当前两个叶节点合并后的误差   
  -  计算不合并的误差    
  -  如果合并会降低误差的话，就将叶节点合并

```python
# 函数说明:判断测试输入变量是否是一棵树
"""
Parameters:
    obj - 测试对象
"""
def isTree(obj):
    import types
    return (type(obj).__name__ == 'dict')

# 函数说明:对树进行塌陷处理(即返回树平均值)
"""
Parameters:
    tree - 树
"""
def getMean(tree):
    if isTree(tree['right']): tree['right'] = getMean(tree['right'])
    if isTree(tree['left']): tree['left'] = getMean(tree['left'])
    return (tree['left'] + tree['right']) / 2.0

# 函数说明:后剪枝
"""
Parameters:
    tree - 树
    test - 测试集
"""
def prune(tree, testData):
    #如果测试集为空,则对树进行塌陷处理
    if np.shape(testData)[0] == 0: return getMean(tree)
    #如果有左子树或者右子树,则切分数据集
    if (isTree(tree['right']) or isTree(tree['left'])):
        lSet, rSet = binSplitDataSet(testData, tree['spInd'], tree['spVal'])
    #处理左子树(剪枝)
    if isTree(tree['left']): tree['left'] = prune(tree['left'], lSet)
    #处理右子树(剪枝)
    if isTree(tree['right']): tree['right'] =  prune(tree['right'], rSet)
    #如果当前结点的左右结点为叶结点
    if not isTree(tree['left']) and not isTree(tree['right']):
        lSet, rSet = binSplitDataSet(testData, tree['spInd'], tree['spVal'])
        #计算没有合并的误差
        errorNoMerge = np.sum(np.power(lSet[:,-1] - tree['left'],2)) + np.sum(np.power(rSet[:,-1] - tree['right'],2))
        #计算合并的均值
        treeMean = (tree['left'] + tree['right']) / 2.0
        #计算合并的误差
        errorMerge = np.sum(np.power(testData[:,-1] - treeMean, 2))
        #如果合并的误差小于没有合并的误差,则合并
        if errorMerge < errorNoMerge:
            return treeMean
        else: return tree
    else: return tree
```



