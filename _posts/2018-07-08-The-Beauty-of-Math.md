---
layout:     post
title:      "The Beauty of Math(1-5)"
subtitle:   "《数学之美》---吴军"
date:       2018-07-08
author:     "msg"
header-img: "img/post-bg-unix-linux.jpg"
tags:
    - 数学
    - 自然语言处理
    - NLP
---


> 这是我读的第二本书,因为这本书基本上是一本通俗读物,作者将复杂的数学和自然语言处理方面的理论知识,用平易近人的文字,娓娓道来,让不具备相关知识的人,也能轻易的看懂.作者确实很厉害!

### 第1章 文字和语言 vs 数学和信息

简单讲述了语言和文字以及数学的历史.

1) 语言和数学的产生都是为了同一个目的---记录和传播信息.

2) 古埃及的《亚尼的死者之书》(Book of The Death);罗塞塔石碑上的古埃及象形文字;玛雅文明用的是二十进制;古犹太人在抄写圣经的时候,每一行每一列都要校验正确;

3) 翻译这件事能达成,是因为不同文字在记录信息上的能力是等价的,文字只是信息的载体,而不是信息本身.

4) 信息的冗余是信息安全的保障;语言的数据(语料),尤其是双语或多语的语料对翻译至关重要.

5) 词可以被认为是有限且封闭的集合,而语言则是无限和开放集合,词可以有完备的编解码规则,语言不具备,任何语言都有规则覆盖不到的地方.

### 第2章 自然语言处理从规则到统计
简单讲述了自然语言处理从规则到统计的历史,讲述了其中比较重要的影响自然语言处理发展的事件.

1) 计算机能处理自然语言,而且处理方式跟人类一样.

2) 计算机科学之父阿兰*图灵1950年在《思想》(Mind)杂志上发表了"计算的机器和智能"的论文,提出了"图灵测试",可认为是自然语言的机器处理的起源;

3) 20世纪50年代到70年代,局限在人类学习语言的方式上,用电脑模拟人脑;20世纪70年代,基于数学和统计的方法开始出现;

4) 1956年夏天,约翰 麦卡锡(John McCarthy),马文 明斯基(Marvin Minsky), 罗切斯特(Nathaniel Rochester),香农(Shannon),"达特茅斯夏季人工智能研究会议".

5) "鸟飞派"

6) 2000年后,很多公司比如Systran,还是靠人工总结文法规则.

7) 自然语言在演变过程中,产生了词义和上下文相关的特性,因此是比较复杂的上下文有关文法.

8) 1966年,明斯基,"The pen is in the box" 和"The box is in the pen"

9) 1970年统计语言学的出现,使自然语言处理获得了今天非凡的成就,推动者傅里德里克 贾里尼克(Frederick Jelinek)和他领导的IBM华生实验室,一开始想要解决的是语音识别问题.

10) 上个世纪70年代,基于统计的方法的核心模型是通信系统加隐马尔可夫模型.

### 第3章 统计语言模型
让计算机理解自然语言,首先要建立统计语言模型.

1) 贾里尼克的出发点很简单,一个句子是否合理,就看它出现的可能性大小。

 假定S是某一有意义的句子，由一系列特定顺序排列的词w1,w2,...wn组成，n是句子的长度。句子出现的概率:

 $P(s)=P(w_1,w_2,\cdots,w_n)=P(w_1)*P(w_2\|w_1),\cdots,P(w_n{\|}w_1,w_2,\cdots,w_{n-1})$

 19世纪到20世纪初,俄国马尔科夫提出了一种偷懒但有效的方法,假定任意一个词wi只与出现在它前边的词wi-1相关,于是:$P(s)=P(w_1,w_2,\cdots,w_n)=P(w_1)*P(w_2\|w_1),\cdots,P(w_n{\|}w_{n-1})$即是所谓的二元模型.

2) 要想让机器合成的句子通顺,就得用语言模型了,使用语言模型需要知道模型里的所有的条件概率,称之为模型的参数,但是,如果两个词同时出现的次数为0咋办?在实际应用中,统计语言模型的零概率问题是无法回避的,必须解决。

3) 1953年古德(I J Good)在他的老板图灵(Alan Turing)的指导下,提出了在统计中相信可靠的数据,面对不可信的数据打折扣的一种概率估计方法;对于没有看到的事件,我们不能认为它发生的概率为零!因此从概率的总量里,分配一个很小的比例给这些看不到的事件。要根据越是不可信的统计折扣越多的方法进行。

4) 假定在语料库中出现的r次的词有Nr个,未出现的词数量为No。语料库的大小为N,假定r比较小的时候,不可靠,因此使用一个小一点的次数,是$D_r$，$D_r=(r+1)*N_{r+1}/N(r)$。

5) 在实际的NLP中,一般对出现次数超过某个阈值的词,频率不下调,只对次数小于阈值的词频率下调。


### 第4章 谈谈分词

统计语言模型是建立在词的基础上的,因为词是表达语义的最小单位,这一章讲述了分词的一些基本知识。

1) 北航梁南元教授提出查字典方式;哈工大王晓龙博士发展了最少词数的分词理论;清华郭进博士用统计语言模型解决了分词二义性问题;清华孙茂松教授解决了没有词典时的分类问题;香港科技大学吴德凯教授最早将中文分词方法用于英文词组的分割。

2) 可以把计算每种分词可能性下句子的概率问题看成一个动态规划问题,并利用维特比算法快速找到最佳分词。

3) 语言学家对词语的定义不完全相同,一个折中的办法,是在分词的同时,找到复合词的嵌套结构;应用不同,分词的颗粒度应该不同。英文手写体识别中,中文分词方法可以帮助确定边界。

4) 分词问题可以看做一个已经解决的问题?只要采用语言模型,效果都不会差到那里去?

5) 机器翻译中,颗粒度越大越好;网页搜索中,颗粒度越小越好,针对不同的应用,分词器应该同时支持不同层次的切分。

6)分词的不一致性可以分为错误和颗粒度不一致的问题:错误分为越界型错误和覆盖型错误;要继续做数据挖掘,不断完善复合词的词典。


### 第5章 隐含马尔可夫模型

隐含马尔可夫模型一直被认为是解决大多数自然语言处理问题的最为快速有效的方法。

1) 通信的本质就是一个编解码和传输的过程。

2) 到了19世纪，概率论的发展从对随机变量的研究发展到对随机变量的时间序列$s_1,s_2,\cdots,s_t,\cdots$，即随机过程的研究。

3) 随机过程比随机变量要复杂的多：①在任何一个时刻t,对应的状态$s_t$都是随机的；②任何一个状态$s_t$的取值都可能和周围的其他的状态相关。马尔可夫为了简化问题，提出了一种假设，即任一时刻的状态$s_t$只与其前一时刻的状态$s_{t-1}$相关，即$P(s_t\|s_1,s_2,\cdots,s_{t-1}) = P(s_t\|s_{t-1})$。这个假设，后来被命名为马尔可夫假设，符合这个假设的过程成为马尔可夫过程，也叫马尔可夫链。

4) 隐含马尔可夫模型是马尔可夫链的一个扩展：任一时刻$t$的状态$s_t$是不可见的，观察者没法通过观察到一个状态序列$s_1,s_2,\cdots,s_T$来推测转移概率等参数。但是，隐马模型在每个时刻$t$都会输出一个符号$o_t$，而且$o_t$跟$s_t$相关且仅跟$s_t$相关，这被称为独立输出假设。

5) 基于马尔可夫假设和输出独立假设，可以计算某个特定状态序列$s_1,s_2,\cdots$产生的输出符号$o_1,o_2,\cdots$的概率。

> $P(s_1,s_2,\cdots,o_1,o_2,\cdots) = \prod_{t}P(s_t\|s_{t-1}) \cdot P(o_t\|s_t)$

6) 20世纪80年代，李开复博士坚持用隐马模型框架，成功研发出了世界上第一个大词汇量连续语音识别系统Sphinx。

7) 围绕着隐马模型有三个基本问题：

> ①给定一个模型，如何计算某个特定输出序列的概率；

> ②给定一个模型和某个特定的输出序列，如何找到最可能产生这个输出的状态序列；

> ③给定足够量的观测数据，如何估计隐马模型的参数。

第一个问题，采用Forward-Backward算法；第二个问题，采用著名的维特比算法解决；第三个问题，采用鲍姆-韦尔奇算法。

8) 有监督的训练的前提是拥有大量人工标注的数据，很遗憾的是，很多应用都不可能做到这件事。训练隐马模型更实用的方式是仅仅通过大量的观测到的信号$o_1,o_2,\cdots$就能推算模型参数的$P(s_t\|s_{t-1})$和$P(o_t\|s_t)$的方法，使用的是鲍姆-韦尔奇算法。

9) 鲍姆-韦尔奇算法思想：

> 首先找到一组能够产生输出序列$O$的模型参数，成为一个初始的模型，称为$M_{\theta_0}$，需要在此基础上找到一个会更好的模型。

> 假定解决了第一个问题和第二个问题，不但可以算出初始模型产生$O$的概率$P(O\|M_{\theta_0})$，而且能够找到这个模型产生$O$的所有可能的路径以及这些路径的概率。这些可能的路径，记录了每个状态经历了多少次，到达了哪些状态，输出了哪些符号，因此可以看做是“标注的训练数据”，计算出一组新的模型参数$\theta_1$，从$M_{\theta_0}$到$M_{\theta_1}$的过程称为一次迭代。

> 接下来，从$M_{\theta_1}$出发，可以找到一个更好的模型$M_{\theta_2}$，不断地寻找下去，直到模型的质量不再有明显提高。

10) 鲍姆-韦尔奇算法每次迭代都是不断估计新的参数，使得输出的概率达到最大化，这个过程被称为期望值最大化(Expectation-Maximization)，简称EM过程。