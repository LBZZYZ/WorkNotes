---
title: hexo-安装方法
date: 2021-05-25 23:26:56
tags: hexo
categories: hexo
---

# 前言
本文介绍如何在Windows下用hexo搭建博客

<!--More-->

# 1. 安装Node.js
由于hexo依赖Node.js来生成，因此首先需要安装[Node.js](https://nodejs.org/en/)。
这个安装包会安装两个东西：1.Node.js 2.npm包管理器

# 2. 安装cnpm
npm下载速度较慢，因此安装淘宝镜像源管理器
```bash
npm install -g cnpm --registry=https://registry.npm.taobao.org
```

# 3. 安装hexo
```bash
cnpm install -g hexo-cli
```
