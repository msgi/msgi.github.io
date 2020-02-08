---
layout:     post
title:      "linux 笔记"
subtitle:   "linux"
date:       2019-04-28
author:     "msg"
header-img: "img/posts/03.jpg"
header-mask: 0.3
catalog:    true

tags:
    - linux
    - 笔记
    - 学习


---

### linux 基础操作

> ubuntu切换gcc和g++版本

首先，看看有没有安装gcc-6

```
ls /usr/bin/gcc*
```

结果只有/usr/bin/gcc  /usr/bin/gcc-5两个，那么我们需要安装 

```
sudo apt-get install gcc-[version]
```

安装好后输入以下指令： 

```
sudo update-alternatives --install /usr/bin/gcc gcc /usr/bin/gcc-6 60
sudo update-alternatives --install /usr/bin/gcc gcc /usr/bin/gcc-5 50
```

接着输入： 

```
sudo update-alternatives --config gcc
```

会看到如下的选项，有 3 个候选项可用于替换 gcc (提供 /usr/bin/gcc)。 
 选择      路径            优先级  状态

```bash
/usr/bin/gcc-5  50        自动模式
/usr/bin/gcc-5  50        手动模式
/usr/bin/gcc-6  60        手动模式
```

要维持当前值[*]请按回车键，或者键入选择的编号： 
同样也要设置一下g++的 

```
sudo update-alternatives --install /usr/bin/g++ g++ /usr/bin/g++-5 50 
sudo update-alternatives --install /usr/bin/g++ g++ /usr/bin/g++-6 60
```

如果想删除可选项的话可以键入以下指令：

```bash
sudo update-alternatives --remove gcc /usr/bin/gcc-6
```

再用`gcc -v `来看一下版本是否改变了。