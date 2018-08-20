---
title:  pip的安装和镜像的配置
date: 2018-07-26T01:48:58.589Z
tags: ["pip"]
series: ["blog"]
categories: ["code"]
description:
---

### 下载[get-pip.py](https://pip.pypa.io/en/stable/installing/#installing-with-get-pip-py)
```shell
wget https://bootstrap.pypa.io/get-pip.py
```

### 配置清华pip镜像作为默认源
修改 `~/.config/pip/pip.conf` (Linux), `%APPDATA%\pip\pip.ini` (Windows 10) 或 `$HOME/Library/Application Support/pip/pip.conf` (macOS) (没有就创建一个)， 修改 `index-url`至tuna，例如

```ini
[global]
index-url = https://pypi.tuna.tsinghua.edu.cn/simple
```
pip 和 pip3 并存时，只需修改` ~/.pip/pip.conf`。

### 安装pip
```shell
python get-pip.py
```



参考
- [get-pip.py](https://pip.pypa.io/en/stable/installing/#installing-with-get-pip-py)
- [pypi镜像使用帮助](https://mirrors.tuna.tsinghua.edu.cn/help/pypi/)
- [aliases-and-functions](https://ashleynolan.co.uk/blog/beginners-guide-to-terminal-aliases-and-functions)
- [mac下使用alias](https://www.jianshu.com/p/633a30e5d777)