---
title:  配置ssh key公匙实现免密提交到多个远程仓库
date: 2018-07-07
tags: 
 - ssh
 - github
categories: ["code"]
---

查看已经安装的公钥
C:\Users\[用户]\.ssh

![enter description here](https://i.loli.net/2018/07/07/5b405c72a2c5c.jpg)


或者可以通过git bash 查看：`ls ~/.ssh`

![enter description here](https://i.loli.net/2018/07/07/5b405ca0bbbfe.jpg)

创建一个github的key  
```sh
#-t:加密算法；-f:文件名；-C:账号
ssh-keygen -t rsa -f ~/.ssh/id_rsa_github -C "sxy9103@gmail.com"
```
操作完成后，该目录会多出 `id_rsa_github` 和 `id_rsa_github.pub` 两个文件。

编辑config文件，配置不同的仓库指向不同的密钥文件(不配置会使用默认的`~/.ssh/id_rsa`文件)：`vi ~/.ssh/config`,例子如下： 

```conf
Host github.com
    User songxueyan
    IdentityFile ~/.ssh/id_rsa_github
```
> **Host**:简称(@后面的字符串)  
**HostName**：域名或者ip(如果host填写了域名，这里可以不填)  
**不配置会出现错误**：git@github.com: Permission denied (publickey)



![enter description here](https://i.loli.net/2018/07/07/5b405e0b253ae.jpg)

在[github](https://github.com/settings/ssh/new)添加刚刚生成的公钥`cat ~/.ssh/id_rsa_github.pub`

![enter description here](https://i.loli.net/2018/07/07/5b405f1dcfeb8.jpg)

使用git bash 测试是否能免密码连接`ssh -vT git@github.com`

![git bash](https://i.loli.net/2018/07/07/5b405fc9d9dd5.jpg)

如果commit时还需要密码，请在项目的config里把url改为ssh格式

![ssh url](https://i.loli.net/2018/07/07/5b4079f06a054.jpg)

提交文件到远程仓库：  
```shell
echo "# project name" >> README.md
git init
git add README.md
git commit -m "first commit"
git remote add origin git@github.com:smile365/myproject.git
git push -u origin master
```

**参考**

- [Git之同一台电脑连接多个远程仓库](https://www.jianshu.com/p/04e9a885c5c8)
- [Adding SSH key to the ssh-agent](https://help.github.com/articles/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent/)
- [git/github学习笔记](http://www.cnblogs.com/fnng/archive/2011/08/25/2153807.html)
- [Git之SSH与HTTPS免密码配置](https://www.jianshu.com/p/b5ec092fc1d1)