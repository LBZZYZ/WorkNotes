# Android KeyCode

```
./frameworks/base/core/res/res/xml/global_keys.xml
./vendor/amlogic/common/apps/VendorOverlay/DroidOverlay/res/xml/global_keys.xml
```

## KEYCODE定义位置

frameworks/base/core/java/android/view/KeyEvent.java

## Intellyva KeyCode Mapping

```
1           KEYCODE_F10(140)         下个场景
28          KEYCODE_ENTER(66)        确认
39          KEYCODE_SEMICOLON(74)    Fn1
40          KEYCODE_APOSTROPHE(75)   Fn2
41          KEYCODE_GRAVE(68)        返回
43          KEYCODE_BACKSLASH(73)    mute
62          KEYCODE_F4(134)          变声
87          KEYCODE_F11(141)         相机
102         KEYCODE_F5(135)          提词
103         KEYCODE_DPAD_UP(19)      上
105         KEYCODE_DPAD_LEFT(21)    左
106         KEYCODE_DPAD_RIGHT(22)   右
108         KEYCODE_DPAD_DOWN(20)    下
114         KEYCODE_F9(139)          背景右
115         KEYCODE_F7(137)          背景左
125         KEYCODE_F3(133)          上个场景
183         KEYCODE_POWER(26)        待机
```

## Linux Keycode映射位置

/vendor/usr/keylayout
Vendor_0417_Product_0033.kl             Vendor_0417_Product_0035.kl

## 0125状态

除下面两个KeyCode外均已注册
43          KEYCODE_BACKSLASH(73)    mute
183         KEYCODE_POWER(26)        待机

## 参考链接

1. [Android 全局键处理之GlobalKeyManager](https://blog.csdn.net/wangguidong520/article/details/106288591)
2. [KEYCODE列表](https://www.cnblogs.com/larack/p/4223465.html)
3. [接收GlobalKey Broadcast并获取键值的方法](https://blog.csdn.net/xct841990555/article/details/81067727?utm_medium=distribute.pc_relevant.none-task-blog-2~default~baidujs_baidulandingword~default-1.pc_relevant_paycolumn_v2&spm=1001.2101.3001.4242.2&utm_relevant_index=4)
