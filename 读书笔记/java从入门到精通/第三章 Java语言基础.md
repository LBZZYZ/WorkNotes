---
title: 第三章 Java语言基础
updated: 2021-08-30T07:39:04.0000000+08:00
created: 2021-08-29T21:11:56.0000000+08:00
---

## 3.1 Java主类结构
### 3.1.1 包声明
package为声明包的关键字。
### 3.1.2 声明成员变量和局部变量
通常将类的属性称为类的全局变量（成员变量），将方法中的属性称之为局部变量。

### 3.1.3 编写主方法
Java中的main函数必须声明为public static void
String\[\] args是main方法的参数，是一个字符串类型的数组

## 3.2 基本数据类型
1.  数值型：整数型（byte, short int long)
2. 浮点型（float double）
3.  字符型
4.  布尔型
### 3.2.1 Java浮点类型的问题
[JAVA问题分析——浮点型为什么会存在误差](https://blog.csdn.net/qq_37580085/article/details/108292236)
[浮点数0.7在Java中是无法精确存储的，却为何能精确输出0.7](https://blog.csdn.net/weixin_34290096/article/details/92570567)
[为什么 JAVA 中有时能够精确表示浮点数](https://segmentfault.com/q/1010000016576034)

### 3.2.2 float除0
```
1.0f / 0 = Infinity
```

