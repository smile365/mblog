---
title:  安装Miniconda
date: 2018-08-03T06:11:56.394Z
tags: ["code","it"]
series: ["blog"]
categories: ["code"]
draft: true
description:
---

下载[miniconda](https://mirrors.tuna.tsinghua.edu.cn/anaconda/miniconda/)

打开终端，运行刚下载的文件
```shell
bash Miniconda*
```
新打开一个终端，测试
```shell
#查看版本
cona --version
#创建虚拟环境
conda create -n py2 python=2.7 pandas
#进入虚拟环境
source activate env_name
```
参考

- [安装miniconda](https://www.cnblogs.com/YLDream/p/6940085.html)
- [Miniconda镜像](https://mirrors.tuna.tsinghua.edu.cn/help/anaconda/)
- [Anaconda入门使用指南](http://python.jobbole.com/87522/)