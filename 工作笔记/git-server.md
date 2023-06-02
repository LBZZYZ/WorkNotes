---
title: Git服务器
date: 2021-05-23 00:20:13
tags: git
description: 本文将介绍在Linux系统下搭建一个git服务器。

---

# 创建一个空仓库

```bash
git init --bare bingzhi.git
```

这样就j创建了一个空的git仓库，创建的位置就是运行该命令的目录。
空仓库的目录结构如下：
![bare-repo](https://cdn.staticaly.com/gh/LBZZYZ/PicX@master/Blog/bare-repo.ejjs4zpj0eo.webp)

# 创建分支

```bash
git branch <分支名称>
```

## 切换分支(不存在则自动创建)
```bash
git branch -b <分支名称>
```

# 拉取指定分支
```bash
git clone -b <分支名称> 你的远端地址
```

# 推送某分支到远程分支
```bash
git push origin <local branch>:<remote branch> #remote branch不存在则自动创建
```

# 管理公钥
公钥收集起来放到服务器的`/home/git/.ssh/authorized_keys`

# git hooks for hexo
```bash
#hooks/post-update
git --work-tree="/var/www/html" --git-dir="/root/blog.git" checkout -f
```