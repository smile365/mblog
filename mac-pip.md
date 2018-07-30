---
title:  mac-pip
date: 2018-07-26T01:48:58.589Z
tags: ["pip"]
series: ["blog"]
categories: ["code"]
description:
cover: 
---

Mac上另一种方式实现pip使用国内镜像源下载。

mac的aliase不支持参数，可以通过function来达到目的。

vim ~/.aliases.sh
```
function pip(){
    if test "$1" = "install";then
        pip2 "$@" -i https://pypi.tuna.tsinghua.edu.cn/simple
    else
        pip2 "$@"
    fi
}
```
然后vim ~/.bash_profile,最后一行加入。
source "/Users/frankchow/.aliases.sh"

参考
- [aliases-and-functions](https://ashleynolan.co.uk/blog/beginners-guide-to-terminal-aliases-and-functions)
- [mac下使用alias](https://www.jianshu.com/p/633a30e5d777)
- [pypi 镜像使用帮助](https://mirrors.tuna.tsinghua.edu.cn/help/pypi/)