1. 获取NavController
1.1 Activity中获取
   ```java
   NavController controller = Navigation.findNavController(DemoActivity.this, R.id.fragment); //这个R.id.fragment就是Activity布局里fragment控件的id
   ```
   
   1.2 Fragment中获取
   ```java
   NavController  navController = Navigation.findNavController(getView());
   ```
