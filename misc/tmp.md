### 首页重走生命周期的原因
开机的时候，home首先被拉起，这个时候没有进分屏处于全屏显示，然后等到了全屏情况下，就会需要拉起分屏部分的camera应用，这个应用一开始会是全屏显示，盖住了home，也就导致home走了退出的flow，随后camera进分屏，两者同时显示，做了resume的，我这边想了下，暂时好像没有什么方式可以绕过的样子

```bash
adb push C:\bz_temp\preview_camera\app\build\outputs\apk\debug\Camera.apk /system/priv-app/Camera/
```

Q1 cameraList合并到getScene, cameraSwitch合并到setScene，还是说cameraList只是获取镜头缩略图的？
Q2 material api里面的group不需要，设备端没法给这个字段赋值
Q3 推流没有获取推流状态的接口；设备端发送给云服务端的接口需要返回状态吗？
Q4 设置背景音乐列表和设置设备背景音乐列表什么区别？
Q5 设备氛围音列表用读取设备素材列表的方式可以读的到吗？

```
logcat | grep -E "(NetworkStateManager|DynamicRegister)"
```

# 优秀网站排版
https://overreacted.io/
https://blog.zhheo.com/
https://chaoxuprime.com/
https://yujiangshui.com/
https://nightswatch.games/
http://www.yinwang.org/#