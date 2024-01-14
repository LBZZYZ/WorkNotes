https://mugenshenghuo.com/index.php/archives/36.html

https://www.jianshu.com/p/386bbb5fa29a

https://cloud.tencent.com/developer/article/1778496

https://developer.android.com/training/articles/perf-anr.html#anr

Choreographer 的引入，主要是配合 Vsync ，给上层 App 的渲染提供一个稳定的 Message 处理的时机，也就是 Vsync 到来的时候 ，系统通过对 Vsync 信号周期的调整，来控制每一帧绘制操作的时机. 目前大部分手机都是 60Hz 的刷新率，也就是 16.6ms 刷新一次，系统为了配合屏幕的刷新频率，将 Vsync 的周期也设置为 16.6 ms，每个 16.6 ms ， Vsync 信号唤醒 Choreographer 来做 App 的绘制操作 ，这就是引入 Choreographer 的主要作用。


1.NEW(创建)
创建态：当一个已经被创建的线程处于未被启动时，即：还没有调用start方法时，就处于这个状态。

2.RUNNABLE(运行时)
运行态：当线程已被占用，在Java虚拟机中正常执行时，就处于此状态。

3.BLOCKED(排队时)
阻塞态：当一个线程试图获取一个对象锁，而该对象锁被其他的线程持有，则该线程进入Blocked状态。当该线程持有锁时，该线程将自动变成RUNNABLE状态。

4.WAITING(休眠)
休眠态：一个线程在等待另一个线程执行一个(唤醒)动作时，该线程进入Waiting状态。进入这个状态后是不能自动唤醒的，必须等待另一个线程调用notify或者notifyAll方法才能够唤醒。

5.TIMED_WAITING (指定休眠时间)
指定时间休眠态：基本同WAITING状态，多了个超时参数，调用对应方法时线程将进入TIMED_WAITING状态，这一状态将一直保持到超时期满或者接收到唤醒通知，带有超时参数的常用方法有Thread.sleep、锁对象.wait() 。

6.TERMINATED (结束)
结束态：从RUNNABLE状态正常退出而死亡，或者因为没有捕获的异常终止了RUNNABLE状态而死亡。

## hwui呈现模式分析
adb shell setprop debug.hwui.profile [true/visual_bars/false]

## Systrace
https://developer.android.com/topic/performance/tracing/command-line?hl=zh-cn

## Compose取代xml

Compose配合RecyclerView
https://www.waseefakhtar.com/android/recyclerview-in-jetpack-compose/

tutorial
https://developer.android.com/jetpack/compose/tutorial


## 插桩
![插桩函数](https://cdn.statically.io/gh/LBZZYZ/PicX@master/Blog/Pasted-image-20230208143358.5qu5r84y6n80.webp)
使用时加上-a + 包名显示插桩信息

```bash
python C:\Users\Administrator\AppData\Local\Android\Sdk\platform-tools\systrace\systrace.py -t 10 -a com.intellyva.liveplayer -o C:\bz_temp\simon.html
```

## Compose

DSL : https://developer.android.com/jetpack/compose/kotlin#dsl

可拖拽LazyVerticalGrid
https://github.com/aclassen/ComposeReorderable

动画
https://developer.android.com/jetpack/compose/animation

UI层指南
https://developer.android.com/jetpack/guide/ui-layer?hl=zh-cn

Unidirectional data flow (UDF)
状态向下流动、事件向上流动的这种模式称为单向数据流 (UDF)。

-   How to define the UI state.
-   Unidirectional data flow (UDF) as a means of producing and managing the UI state.
-   How to expose UI state with observable data types according to UDF principles.
-   How to implement UI that consumes the observable UI state.