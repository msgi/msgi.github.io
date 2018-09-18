---
layout:     post
title:      "Annoy"
subtitle:   "Annoy(Approximate Nearest Neighbors Oh Yeah)学习"
date:       2018-09-07
author:     "msg"
header-img: "img/posts/02.jpg"
header-mask: 0.3
catalog:    true

tags:
    - 算法
    - 相似度
    - Annoy
---

> 工作中用到$$Annoy$$，学习的过程中记录一下。

一旦文档经过$$word2vec$$或者$$paragraph2vec$$变成***稠密向量***形式，如何从海量文本中快速查找出相似的$$TopN$$文本呢？

### 建立索引过程

$$Annoy$$的目标是建立一个数据结构，使得查询一个点的最近邻点的时间复杂度是次线性。$$Annoy$$通过建立一个二叉树来使得每个点查找时间复杂度是$$O(logn)$$。随机选择两个点，以这两个节点为初始中心节点，执行聚类数为$$2$$的$$kmeans$$过程，最终产生收敛后两个聚类中心点。这两个聚类中心点之间连一条线段（灰色短线），建立一条垂直于这条灰线，并且通过灰线中心点的线（黑色粗线）。这条黑色粗线把数据空间分成两部分。在多维空间的话，这条黑色粗线可以看成等距垂直超平面。

![分割](/img/posts/annoy/split.png)

在划分的子空间内进行不停的递归迭代继续划分，直到每个子空间最多只剩下$$K$$个数据节点。
![最终分割](/img/posts/annoy/split2.png)

通过多次递归迭代划分的话，最终原始数据会形成二叉树结构。二叉树底层是叶子节点记录原始数据节点，其他中间节点记录的是分割超平面的信息。$$Annoy$$建立这样的二叉树结构是希望满足这样的一个假设:相似的数据节点应该在二叉树上位置更接近，一个分割超平面不应该把相似的数据节点分在二叉树的不同分支上。
![树状结构](/img/posts/annoy/split3.png)

### 查询过程

完成节点索引建立过程后如何进行对一个数据点进行查找相似节点集合呢？查找的过程就是不断看查询数据节点在分割超平面的哪一边。从二叉树索引结构来看，就是从根节点不停的往叶子节点遍历的过程。通过对二叉树每个中间节点（分割超平面相关信息）和查询数据节点进行相关计算来确定二叉树遍历过程是往这个中间节点左子节点走还是右子节点走。

#### 存在问题

上述描述存在两个问题：

1) 查询过程最终落到叶子节点的数据节点数小于我们需要的$$TopN$$相似邻居节点数目怎么办？

2）两个相近的数据节点划分到二叉树不同分支上怎么办？

#### 解决方法

1）如果分割超平面的两边都很相似，那可以两边都遍历；下面是是个示意图：

![示意图](/img/posts/annoy/01.png)

2) 建立多棵二叉树树，构成一个森林，每个树建立机制都如上面所述那样。

![森林](/img/posts/annoy/tree.ipg)

3）采用优先队列机制：采用一个优先队列来遍历二叉树，从根节点往下的路径，根据查询节点与当前分割超平面距离进行排序。

### 返回最终近邻节点

每棵树都返回一堆近邻点后，如何得到最终的$$TopN$$相似集合呢？
首先所有树返回近邻点都插入到优先队列中，求并集去重, 然后计算和查询点距离， 最终根据距离值从近距离到远距离排序， 返回$$TopN$$近邻节点集合。


### python代码例子

```python
from annoy import AnnoyIndex
import random

f = 40
t = AnnoyIndex(f)  # Length of item vector that will be indexed
for i in xrange(1000):
    v = [random.gauss(0, 1) for z in xrange(f)]
    t.add_item(i, v)

t.build(10) # 10 trees
t.save('test.ann')

# ...

u = AnnoyIndex(f)
u.load('test.ann') # super fast, will just mmap the file
print(u.get_nns_by_item(0, 1000)) # will find the 1000 nearest neighbors
```