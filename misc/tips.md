## Android
1. 使用**setCurrentItem(int page)**时，即便设置成懒加载也会预加载一页(因为有滑动动画)。可以使用**setCurrentItem(int page, int smoothScroll)** 关闭动画，启用懒加载
2. 不要用logcat连续印相同的log，否则会被折叠，不会完全打印
3. 反射机制目前认知是只能反射到/frameworks/base/core/java/android下面的类
4. 误用SQLITE关键字做为列名时，可以用中括号扩在关键字两端，将其变为普通字串，如[order]
5. 替换player apk打包image：把apk放到vendor\intellyva\apps\LivePlayer下，然后到code根目录source rebuild_all -j16 -b player -p android

## Markdown
1. 链接本地文件路径时，如果路径名称中有`空格`，将`空格`替换为`%20`