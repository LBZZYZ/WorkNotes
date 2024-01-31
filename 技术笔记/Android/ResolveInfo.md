#TODO 整理内容和格式
ResolveInfo这个类是通过解析一个与IntentFilter相对应的intent得到的信息。它部分地对应于从AndroidManifest.xml的\< intent\>标签收集到的信息。PackageManager这个类是用来返回各种的关联了当前已装入设备了的应用的包的信息。你可以通过getPacageManager来得到这个类。

　　PackageManager manager = getPackageManager();

　　Intent intent = new Intent(Intent.ACTION_MAIN,null);
　　intent.addCategory(Intent.CATEGORY_LAUNCHER);
//通过Intent查找相关的Activity，更准确
　　List\< ResolveInfo\> appList = manager.queryIntentActivities(intent,0);
　　//它是通过解析\< Intent-filter\>标签得到有
　　\< action android:name=”android.intent.action.MAIN”/\>
　　\< action android:name=”android.intent.category.LAUNCHER”/\>

ResolveInfo info = appsList.get(position);
包名获取方法： info.activityInfo.packageName;
class名： info.activityInfo.name;
icon获取获取方法： ImageView i; i.setImageDrawable(info.activityInfo.loadIcon(manager));
应用名称获取方法：info.activityInfo.loadLabel(manager).toString()
