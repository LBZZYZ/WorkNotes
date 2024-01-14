`android.intent.action.MAIN` 决定应用最先启动的 Activity
`android.intent.category.LAUNCHER` 决定应用是否显示在程序列表中

有 MAIN , 有 LAUNCHER , 程序列表中正常显示
有 MAIN , 无 LAUNCHER , 程序列表中不显示
无 MAIN , 有 LAUNCHER , 程序列表中不显示，因为找不到应用入口
多个 Activity 有 MAIN , 有 LAUNCHER , 应用列表中会有多个图标

### 参考链接
https://dandelioncloud.cn/article/details/1610297241954893825