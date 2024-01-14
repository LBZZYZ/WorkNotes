## 用包名启动 app
```java
Intent intent = getPackageManager().getLaunchIntentForPackage(packname); startActivity(intent);
```

## 定义SharedPreferences
```Java
SharedPreferences preferences = context.getSharedPreferences(name, Context.MODE_PRIVATE);
```

## 获取当前Activity
```Java
public class ActivityManager {
    private static volatile ActivityManager instance;
    private WeakReference<Activity> mCurrentActivity;

    private ActivityManager() {
    }

    public static ActivityManager getInstance() {
        if (instance == null) {
            synchronized (ActivityManager.class) {
                if (instance == null) {
                    instance = new ActivityManager();
                }
            }
        }
        return instance;
    }

    public Activity getCurrentActivity() {
        return mCurrentActivity != null ? mCurrentActivity.get() : null;
    }

    public void setCurrentActivity(@Nullable Activity activity) {
        mCurrentActivity = new WeakReference<>(activity);
    }
}

public class App extends Application {

    @Override
    public void onCreate() {
        super.onCreate();
        
        registerActivityLifecycleCallbacks(new ActivityLifecycleCallbacks() {
            @Override
            public void onActivityCreated(@NonNull Activity activity, @Nullable Bundle savedInstanceState) {

            }

            @Override
            public void onActivityStarted(@NonNull Activity activity) {

            }

            @Override
            public void onActivityResumed(@NonNull Activity activity) {
                ActivityManager.getInstance().setCurrentActivity(activity);
            }

            @Override
            public void onActivityPaused(@NonNull Activity activity) {
                ActivityManager.getInstance().setCurrentActivity(null);
            }

            @Override
            public void onActivityStopped(@NonNull Activity activity) {

            }

            @Override
            public void onActivitySaveInstanceState(@NonNull Activity activity, @NonNull Bundle outState) {

            }

            @Override
            public void onActivityDestroyed(@NonNull Activity activity) {

            }
        });
    }
}

```
### 参考
1. [# 关于获取当前Activity的一些思考](https://droidyue.com/blog/2016/02/21/thinking-of-getting-the-current-activity-in-android/)
2. [# Android中获取当前正在显示的Activity实例](https://juejin.cn/post/6844904055278419976)


## 判断主线程
### 方法一
```Java
public boolean isMainThread() {
    return Looper.getMainLooper() == Looper.myLooper();
}
```

### 方法二
```java
public boolean isMainThread() {
    return Looper.getMainLooper().getThread() == Thread.currentThread();
}
```

### 方法三
```java
public boolean isMainThread() {
    return Looper.getMainLooper().getThread().getId() == Thread.currentThread().getId();
}
```

## RecyclerView初始化(横向布局)
```
        mRvBackgroundList = view.findViewById(R.id.rv_background_list);
        mRvBackgroundList.setLayoutManager(new LinearLayoutManager(view.getContext(),
                LinearLayoutManager.HORIZONTAL, false));
        mRvBackgroundList.addItemDecoration(new HorizontalItemDecoration(getResources()
                .getDimensionPixelSize(R.dimen.base_size_level28)));
```

## 网格布局
```Java
        mRvCameras = findViewById(R.id.rv_camera_settings);
        GridSpaceItemDecoration gridSpaceItemDecoration = new GridSpaceItemDecoration(SPAN_COUNT, RecyclerView.VERTICAL,
                getResources().getDimensionPixelSize(R.dimen.base_size_level60),
                getResources().getDimensionPixelSize(R.dimen.base_size_level30));
        mRvCameras.addItemDecoration(gridSpaceItemDecoration);
        mRvCameras.setLayoutManager(new GridLayoutManager(getContext(), SPAN_COUNT));
        mRvCameras.setAdapter(mAdapter);
```
## 创建纯色bitmap
```Java
    private Bitmap mockBitmap() {
        Bitmap bitmap = Bitmap.createBitmap(280, 280, Bitmap.Config.ARGB_8888);
        bitmap.eraseColor(Color.parseColor("#FF0000"));
        return bitmap;
    }
```

## 打印堆栈信息
```Java
	Thread.dumpStack();
```

## CopyOnWriteArrayList
适合于读多写少的场景，在有写操作的时候会copy一份数据，然后写完再设置成新的数据。
https://www.jianshu.com/p/cd7a73e6bd78

## Bitmap和Drawable转换
```Java
Bitmap drawable2Bitmap(Drawable drawable) {  
        if (drawable instanceof BitmapDrawable) {  
            return ((BitmapDrawable) drawable).getBitmap();  
        } else if (drawable instanceof NinePatchDrawable) {  
            Bitmap bitmap = Bitmap  
                    .createBitmap(  
                            drawable.getIntrinsicWidth(),  
                            drawable.getIntrinsicHeight(),  
                            drawable.getOpacity() != PixelFormat.OPAQUE ? Bitmap.Config.ARGB_8888  
                                     : Bitmap.Config.RGB_565);  
             Canvas canvas = new Canvas(bitmap);  
             drawable.setBounds(0, 0, drawable.getIntrinsicWidth(),  
                     drawable.getIntrinsicHeight());  
             drawable.draw(canvas);  
             return bitmap;  
         } else {  
             return null;  
         }  
     }
```

```Java
Drawable bitmap2Drawable(Bitmap bitmap) {  
        return new BitmapDrawable(bitmap);  
    }
```

## 判断横竖屏
```Java
        if (this.getResources().getConfiguration().orientation == Configuration.ORIENTATION_PORTRAIT) {
            Log.d(TAG, "竖屏");
        } else {
            Log.d(TAG, "横屏");
        }
```

## 字符串Base64编码为String
```Java
    public static String encodeBitmapToString(Bitmap bitmap) {
        if (bitmap == null) {
            return "";
        }
        ByteArrayOutputStream outputStream = new ByteArrayOutputStream();
        bitmap.compress(bitmap.hasAlpha() ? Bitmap.CompressFormat.PNG : Bitmap.CompressFormat.JPEG, 100, outputStream);
        // Base64.NO_WRAP is used to eliminate '\n' in encoded string
        return Base64.encodeToString(outputStream.toByteArray(), Base64.NO_WRAP);
    }
```