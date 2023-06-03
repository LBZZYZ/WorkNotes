可以通过Package Name解析相应的应用，并返回应用的所有信息。

# 默认过滤内容（本地存储）
持有两个存储器存储默认的过滤应用（黑名单与白名单）。
构造时加载黑/白名单，并根据黑白名单过滤出符合条件的两个应用列表。
提供黑、白名单增加与删除的操作。

## 存储方式：
saveAppListToDatabase() ---将app的packageName与labelName存储到app_list表格中
getAllApps() --- 将本机安装的所有app的ResolveInfo存储起来

public方法
1.  getAllowListResolveInfos() / getBlockListResolveInfos() ---从数据库读取allow/block字段，将符合条件的ResolveInfo集合传出
2.  增加/删除黑/白名单内容(通过labelName和packageName两种方式)  
    addAllowListByLabelName() / addBlockListByLabelName()  
    removeAllowListByLabelName() / removeBlockListByLabelName()  
    addAllowListByPackageName() / addBlockListByPackageName()  
    removeAllowListByPackageName() / removeBlockListByPackageName()  