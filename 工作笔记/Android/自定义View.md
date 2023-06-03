onMeasure
<https://www.jianshu.com/p/1a1491f059fc>
<https://blog.csdn.net/lovexieyuan520/article/details/50614670>
View的android:layout_width和android:layout_height的值就是onMeasure()两个参数。什么意思，比如我为android:layout_width和android:layout_height设置的值为300dp，但是我在onMeasure()中，测量时不遵守这个300dp的空间要求，将onMeasure()的实现改为：

@Override
protected void onMeasure(int widthMeasureSpec, int heightMeasureSpec) {
super.onMeasure(widthMeasureSpec, heightMeasureSpec);
setMeasuredDimension(100,100);
}
这样一来，不管android:layout_width和android:layout_height设置的值为多少，MyView显示的宽高都为100px，一般来说我们不这样做，我们要考虑父布局给出的宽高，即我们设置android:layout_width和android:layout_height的值。

Margin与Padding
<https://blog.csdn.net/u012732170/article/details/55045472>
<https://www.jianshu.com/p/336f90f0026f>

