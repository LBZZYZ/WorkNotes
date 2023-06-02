---
title: Android-Launcher介绍
date: 2021-09-04 08:55:28
tags: 
	- Android
	- Launcher

categories: Android
---

## 系列文章主题

本系列文章旨在实现一个可以罗列所有APP以及带有托盘图标的Android Luncher。
<!--More-->
---

## 开发环境

工欲善其事，必先利其器。首先介绍一下我使用的开发环境。
1. Windows 10 PC一台
2. Android Studio 4.1.3  [安装方法点这里](http://libingzhi.top/2021/02/16/%E4%B8%8B%E8%BD%BD-%E5%AE%89%E8%A3%85-%E9%85%8D%E7%BD%AEAndroid-Studio/)
3. OPPO R17手机 --- Android 10 OS.

---

## 什么是Launcher?

所谓Launcher,即桌面启动器。在Android系统中，它作为一个APP而存在。Launcher的作用很丰富，其中包括罗列已安装的APP、桌面小部件、APP快捷访问、进程管理等等。


---

## 新建项目

首先，打开Android Studio,新建一个名为My Launcher的项目。我这里新建了一个Empty Activity，然后点击下一步。
![1](https://cdn.jsdelivr.net/gh/LBZZYZ/PicX@master/Blog/1.2qivwsmjssc0.png)

下面这个页面要注意一点，Minimun SDK这里，我选择了API 16，Android版本是向前兼容，如果SDK版本选择的比较高，可能会无法适配以前的机型。由于我已经创建好了项目，所以会有下面的警告，你们在创建时可以忽略，直接点Finish。
![2](https://cdn.jsdelivr.net/gh/LBZZYZ/PicX@master/Blog/2.6o3virosvfc0.png)

接下来Android Studio就会为我们自动构建一个项目，并自动生成一些文件，如下图。
![3](https://cdn.jsdelivr.net/gh/LBZZYZ/PicX@master/Blog/3.5u38e5q25mo0.png)

可以点击右上角运行按钮，这时就会存在一个带一个空Activity的APK在虚拟手机上显示出来。
![4](https://cdn.jsdelivr.net/gh/LBZZYZ/PicX@master/Blog/4.7bofs54vkpw0.png)

---

## 正式开始

前期准备完成，项目正式开始。前文已经提到，Launcher说白了也是一个APP，那现在考虑一个问题，这个APP与普通的APP有什么不同？首先，他是开机之后用户能进行交互的第一个APP。其次，通过Launcher，我们可以调用起其他APP。还有就是，在按下Home键时要可以回到Launcher这个应用。

---

## 过滤按下Home键

首先我们来看在代码中如何实现，能让Android系统帮助我们过滤按下Home键的事件，来进入我们自己的Launcher。

实现起来很容易，只要在AndroidManifest.xml中添加两行代码。
```xml
<category android:name="android.intent.category.HOME"/>
<category android:name="android.intent.category.DEFAULT"/>
```

这时再运行APP，看上去和之前的运行结果没什么不同。但如果你点击一下Home按键，就会发现他在提示你是选择原生Launcher还是你自己的Launcher。

![5](https://cdn.jsdelivr.net/gh/LBZZYZ/PicX@master/Blog/5.4v7mbqgfsew0.png)


---
