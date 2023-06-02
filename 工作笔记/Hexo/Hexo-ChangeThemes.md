---
title: hexo-如何更换主题
date: 2021-01-16 01:17:35
tags: hexo
categories: hexo
---

## 使用GIT下载主题

本次选用的主题是NextT，下载方法：

```bash
git clone https://github.com/theme-next/hexo-theme-next themes/next
```
<!--More-->

---

## 更改配置文件

找到博客根目录下的```_config.yml```，将```theme```修改为```next```。

```bash
theme: next #注意，冒号后面要加一个空格
```

---


## 测试
在```git bash```中输入```<hexo s>```测试主题功能是否正常

---


## 生成&上传

```bash
hexo clean #清理
hexo g #重新生成
hexo d #推送至远端
```

---



## 参考链接

1. https://segmentfault.com/a/1190000012805627
