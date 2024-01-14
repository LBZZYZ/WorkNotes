---
title: fstab
date: 2020-12-16 00:37:53
tags: linux
---

## 一、前言

由于今天周会有同事介绍到了fstab及其作用，但是我本人对其完全未知，因此写下本文对fstab做一个简单的学习和整理。

---
## 二、基本概念

### 1. 定义

fstab是linux系统中存放文件系统静态信息的一个文本文件，其在系统中的位置是/etc/fstab。

### 2. 作用

当系统启动时，liunx会按照fstab中定义的信息去捞需要挂载的文件系统信息并动态的挂载到相应位置。

---

## 三、具体实例

### 1. 下面是linux帮助文档中给出的配置fstab的具体实例。

```bash
LABEL=t-home2   /home   ext4    defaults,auto_da_alloc  0   2
```
---

### 2. 想要配置一条fstab需要描述六个字段  
1. file system---指明要挂载的块特殊设备或远端文件系统   
2. dir---file system的挂载点  
3. type---file system的类型  
4. options---描述了与文件系统相关的```mount```选项  
5. dump---指明文件系统是否要被dump，默认为0(不dump)  
6. pass---被fsck(file system check, 文件系统检查)使用，0为不fsck，1为root使用，其他用户为2



---

## TIPS：查看fstab帮助文档

```bash
man 5 fstab
```

---


## 参考链接

1. https://blog.csdn.net/richerg85/article/details/17917129

