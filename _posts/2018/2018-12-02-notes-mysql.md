---
layout:     post
title:      "mysql的一些常用命令记录"
subtitle:   "mysql commands"
date:       2018-12-02
author:     "msg"
header-img: "img/posts/04.jpg"
header-mask: 0.3
catalog:    true

tags:
    - mysql
    - ubuntu
    - 学习
    - 转载
---

## 修改MySQL数据库编码

ubuntu 16.04 mysql 装上之后，数据库的默认编码不全是 utf8，有一些是 latin1 ，这就导致往数据库里面存储中文的时候会出现乱码，所以还是很有必要设置以下数据库默认编码的。通常修改编码的方式有一种是在 mysql 的交互shell中直接修改，但这个修改不是永久的，退出交互shell便会失效，博主下面主要介绍如何 持久性的修改mysql数据库的默认编码

### 步骤
首先你可以登陆mysql的交互shell，输入下面的命令查看当前数据库的编码方式

```
show variables like '%character%';
```

可以看出，mysql数据库装上之后初始并不均是utf8, 退出刚才的交互shell，去修改下面的配置文件

输入下面的命令，打开第一个配置文件

```shell
sudo vim /etc/mysql/conf.d/mysql.cnf
```

在 [mysql] 标签的下一行添加下面的配置

```shell
default-character-set=utf8
```

输入下面的命令，打开第二个配置文件

```shell
sudo vim /etc/mysql/mysql.conf.d/mysqld.cnf
```

找到 [mysqld] 标签，在其下一行添加下面的配置

```shell
character-set-server=utf8
```

配置文件修改成功之后，输入下面的命令重启mysql服务

```shell
sudo service mysql restart
```

重启之后再去查看数据库的默认编码方式

```bash
show variables like '%character%';
```

### incorrect string value:'\xf0\x9f

- 在mysql的安装目录下找到my.ini,作如下修改：

```shell
[mysqld]

character-set-server=utf8mb4

[mysql]

default-character-set=utf8mb4
```

修改后重启Mysql

### 将已经建好的表也转换成utf8mb4

命令：

```shell
alter table TABLE_NAME convert to character set utf8mb4 collate utf8mb4_bin; 
```