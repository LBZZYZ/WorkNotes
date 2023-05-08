---
title: 第一章 初识java
updated: 2021-08-29T14:03:02.0000000+08:00
created: 2021-08-26T23:14:28.0000000+08:00
---

第一章 初识java
2021年8月26日 星期四
下午11:14

1.1 java简介
1.1.1 什么是java语言
1995年由sun公司推出的一款面向对象设计语言。
特点：1.面向对象；2.通过解释方式来执行的语言；3.跨平台
适用场景：非常适用于企业网络和internet环境

\##Java语言编写的程序即是编译型的，又是解释型的。
代码在编译之后生成一种中间语言，称为Java字节码。Java虚拟机（JVM）对字节码进行解释和运行。
编译只进行一次，而解释在每次程序运行时都会进行。
![image1](../../resources/image1.jpeg)

1.1.2 Java的应用领域
桌面应用系统开发
嵌入式系统开发
电子商务应用
企业级系统开发
交互式系统开发
多媒体系统开发
分布式系统开发
Web应用系统开发

1.1.3 Java的版本

Java发展过程，按应用范围可以划分为三个版本
1.  JAVA SE
2.  Java EE
3.  Java ME
这三个版本都属于SUN ONE(Open Net Environment）体系。
详细介绍一下：
JAVA SE, Java标准版本，主要用于桌面应用程序开发。包含基础语法，JDBC(Java数据库连接性)操作，I/O操作，网络通信，多线程等。
Java EE,企业版Java,主要开发企业级分布式的网络程序，如电商网站和ERP(企业资源规划系统),核心内容：EJB(企业Java组件模型)
Java ME, for嵌入式，如掌上电脑，手机等移动通信设备。

结构图：
![image2](../../resources/image2.jpeg)

1.1.4 怎样学好Java
这是个佛学问题，用心领会就好。
着重提一点：设计模式。

1.1.5 Java API 文档
网站：java.sun.com
<https://docs.oracle.com/en/java/javase/16/docs/api/index.html>

1.2 Java语言的特性
1.2.1
简单：与C++ & C语言语法类似
使用接口取代了多重继承，取消了指针。垃圾自动收集。
丰富的类库，API文档和第三方开发包。JDK开源，便于学习

1.2.2

面向对象。万物皆对象，语法中不能在类外面定义单独的数据和函数。

1。2.3分布性

Java可以通过URL对象访问网络对象，访问方式与访问本地系统相同。

1.2.4 可移植性

1.2.5 解释型

1.2.6安全性
删除了指针和内存释放等语法

1.2.7健壮性

1.2.8 多线程

1.2.9 高性能
编译后的字节码在解释器中运行，而且可以在运行时被翻译成特定平台的机器指令，速度快很多。

1.2.10 动态
库多，方便内部调整，而不影响客户。

1.3 搭建Java环境
1.3.1 下载JDK

Java的JDK又称JAVA SE（以前叫J2SE）
下载地址：https://www.oracle.com/java/

1.3.2 安装
MacBook-Air:Java-Self-Study bingzhi\$ java --version
java 16.0.2 2021-07-20
Java(TM) SE Runtime Environment (build 16.0.2+7-67)
Java HotSpot(TM) 64-Bit Server VM (build 16.0.2+7-67, mixed mode, sharing)

1.3.4 第一个Java程序
<https://github.com/LBZZYZ/Java-Self-Study/blob/main/01/HelloJava.java>
