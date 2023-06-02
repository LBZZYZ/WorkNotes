---
title: SELinux
date: 2021-01-09 19:46:25
tags:
---

## 一、背景介绍

由于最近工作中要向Android中合入一个新的功能，需要配置selinux相关策略让相关进程有权限访问资源，但是对SELinux的概念以及配置方法完全陌生，因此写下此文整理一下学习过程。

---

## 二、前言

Security-Enhanced Linux，简称SELinux，他是Linux中的一个子模块

SElinux for Android [官方文档](https://source.android.com/security/selinux/images/SELinux_Treble.pdf)

---

## 三、基础概念

---

### 1. 两种模式

#### 1.1 DAC
Discretionary Access Control，自主访问控制。在该模式下，用户拥有其登录身份下对文件、套接字等的所有访问权限。

---

#### 1.2 MAC
Mandatory Access Control，强制访问控制。此模式下没有超级用户的概念，一切进程除了要受其自身能访问资源的限制，还要经过MAC对资源进一步控制，保证了资源的最小化访问原则。这样即使存在获得了超级权限的恶意软件，也只能对少部分资源造成破坏。

---

### 2. 四个基本概念

Subject --- 主体  
Object --- 客体  
Policy --- 策略  
Mode --- 模式

主体可以理解为进程/服务，客体的形式有很多，文件、文件夹、套接字等都可以成为客体。策略是用来描述主体/客体能访问内容的规则。对于模式来说，他并非上文提到的两种访问控制模式，而是针对selinux对主体/客体访问的态度，在意义上更像是一种开关。在linux系统中有三种可以设置的Mode：1.宽容模式（Permissive Mode);2.强制模式(Enforcing Mode);3.关闭(Disabled)

#### 设置Mode的方法

```bash
setenforce 0 #0表示Permissive Mode；1表示Enforcing Mode
```
---

## 四、语法规则

---

## 五、参考文献

1. https://www.phpyuan.com/235739.html
2. https://www.linuxprobe.com/selinux-introduction.html
3. https://blog.csdn.net/qq_19923217/article/details/81240027
