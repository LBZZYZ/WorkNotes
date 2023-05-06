### 首页重走生命周期的原因
开机的时候，home首先被拉起，这个时候没有进分屏处于全屏显示，然后等到了全屏情况下，就会需要拉起分屏部分的camera应用，这个应用一开始会是全屏显示，盖住了home，也就导致home走了退出的flow，随后camera进分屏，两者同时显示，做了resume的，我这边想了下，暂时好像没有什么方式可以绕过的样子

```bash
adb push C:\bz_temp\preview_camera\app\build\outputs\apk\debug\Camera.apk /system/priv-app/Camera/
```