---
layout:     post
title:      "06、linu笔记"
subtitle:   "linux"
date:       2019-04-23
author:     "msg"
header-img: "img/posts/03.jpg"
header-mask: 0.3
catalog:    true

tags:
    - linux
    - 笔记
    - 学习

---

### 1、查看操作系统信息
```cat /etc/os-release  ``` 

### 2、突然各种No route to host的原因

```bash
ip a
route -n
```
查看到多了一个172.20.0.0的网卡,删除掉即恢复了正常

```bash
sudo ifconfig 网卡名 down
```

### 3、ubuntu切换gcc和g++版本

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

### 4、使用curl发送请求

**使用curl发送GET请求**

```bash
curl protocol://address:port/url?args
```

例如：

```bash
curl http://127.0.0.1:8080/login?admin&passwd=12345678
```

**使用curl发送POST请求**

```bash
curl -d "args" protocol://address:port/url
```

例如：

```bash
curl -d "user=admin&passwd=12345678" http://127.0.0.1:8080/login
```

这种方法是参数直接在header里面的，如需将输出指定到文件可以通过重定向进行操作

```bash
curl -H "Content-Type:application/json" -X POST -d 'json data' URL
```

例如：

```bash
curl -H "Content-Type:application/json" -X POST -d '{"user": "admin", "passwd":"12345678"}' http://127.0.0.1:8000/login
```
