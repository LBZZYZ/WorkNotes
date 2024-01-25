---
title: hexo-如何插入图片
date: 2021-01-09 19:10:40
tags: hexo
categories: hexo
description: 本文介绍如何在hexo博客中插入图片。
---

## 下载插件

hexo-assert-image这个插件会有问题，参考[这篇博文](https://www.dazhuanlan.com/2020/01/06/5e1292fd8e5b9/)给出的下载源

``` bash
$ cnpm install https://github.com/CodeFalling/hexo-asset-image --save
```

---

## 修改_config.yml

在_config.yml中将post_asset_folder置为true

```bash
post_asset_folder: true
```