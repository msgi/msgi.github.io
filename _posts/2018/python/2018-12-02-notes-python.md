---
layout:     post
title:      "python notes"
subtitle:   "Python学习笔记"
date:       2018-09-18
author:     "msg"
header-img: "img/posts/03.jpg"
header-mask: 0.3
catalog:    true

tags:
    - Python
    - 学习
    - 笔记
---

### 1、from __future__ import print_function

在开头加上from __future__ import print_function这句之后，即使在python2.X，使用print就得像python3.X那样加括号使用。

```python
# python2.7
print "Hello world"

# python3
print("Hello world")
```

如果某个版本中出现了某个新的功能特性，而且这个特性和当前版本中使用的不兼容，也就是它在该版本中不是语言标准，那么想要使用的话就需要从future模块导入。
其他例子： 

```python
from __future__ import division 
from __future__ import absolute_import 
from __future__ import with_statement
 ```
 
加上这些，就算python版本是python2.X，也得按照python3.X标准使用这些函数。

### 2、Python简单用法

```python
list('你是谁') # 返回的结果是['你','是','谁']

Counter(all_data).most_common(vocab_size - 1)  # 保留最常见的前vocab_size - 1的data

```

### 3、shuffle与permutation的区别

函数shuffle与permutation都是对原来的数组进行重新洗牌（即随机打乱原来的元素顺序）；区别在于shuffle直接在原来的数组上进行操作，改变原来数组的顺序，无返回值。而permutation不直接在原来的数组上进行操作，而是返回一个新的打乱顺序的数组，并不改变原来的数组。

### 4、tf.placeholder(dtype, shape=None, name=None)

此函数可以理解为形参，用于定义过程，在执行的时候再赋具体的值。dtype：数据类型。常用的是tf.float32,tf.float64等数值类型；shape：数据形状。默认是None，就是一维值，也可以是多维，比如[2,3], [None, 3]表示列是3，行不定；name：名称。

### 5、tf.variable_scope和tf.name_scope

tf.variable_scope可以让变量有相同的命名，包括tf.get_variable得到的变量，还有tf.Variable的变量

tf.name_scope可以让变量有相同的命名，只是限于tf.Variable的变量

```python
import tensorflow as tf
 
with tf.variable_scope("foo"):
    a = tf.get_variable("bar", [1])
    print(a.name)                    #foo/bar:0
    b = tf.Variable("b", [1])
    print(b.name)                   #foo/Variable:0
 
with tf.variable_scope("bar"):
    a = tf.get_variable("bar", [1])
    print(a.name)                     #bar/bar:0
    b = tf.Variable("b", [1])
    print(b.name)                   #bar/Variable:0
 
with tf.name_scope("a"):
    a = tf.Variable([1])
    print(a.name)                      #a/Variable:0
 
with tf.name_scope("b"):
    b = tf.get_variable("b", [1])
    print(b.name)                #b_1:0
 
with tf.name_scope("b"):
    c = tf.get_variable("b", [1])

```

### 6、自动生成requirements.txt文件

```shell
pip install pipreqs

```

这个工具的好处是可以通过对项目目录的扫描，自动发现使用了那些类库，自动生成依赖清单。

```shell
pipreqs ./ --force

```

--force是用来覆盖掉之前已经生成的requirements.txt文件