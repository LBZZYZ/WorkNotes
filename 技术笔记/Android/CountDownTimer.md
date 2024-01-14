## 常见问题

### 1. Can't create handler inside thread Thread[Shared-1-t-2,5,main] that has not called Looper.prepare()
[CountDownTimer: "Can't create handler inside thread that has not called Looper.prepare()"](https://stackoverflow.com/questions/4006547/countdowntimer-cant-create-handler-inside-thread-that-has-not-called-looper-p)
CountDownTimer用于辅助更新UI，如果无需Ui相关操作，最好使用普通线程。