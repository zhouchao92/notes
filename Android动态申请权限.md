---
title: android动态权限申请
tags:
  - android
  - 动态权限
abbrlink: 821fe050
date: 2020-04-03 12:00:00
---

#### Android 6.0以上动态申请权限  

###### 添加相关权限

在清单文件中添加相关权限

```java
<uses-permission android:name="android.permission.××××"></uses-permission>
```
###### 检查权限-动态申请权限-申请结果

- 检查权限
- 动态申请权限
- 处理权限申请结果

```java
private String strPermission =Manifest.permission.××××;
private final static int ××××_REQUEST_CODE=1;	//请求码，不同的权限设置成不同的值

public void requestPermissions(String strPermission) {
    if (ContextCompat.checkSelfPermission( this, strPermission) != PERMISSION_GRANTED) {
        if (ActivityCompat.shouldShowRequestPermissionRationale( this, strPermission)) {
            Toast.makeText(this, "需要××××权限", Toast.LENGTH_LONG).show();
        } else {
            ActivityCompat.requestPermissions( this, new String[]{
                    strPermission},×××× _REQUEST_CODE);
        }
    }
}
//重写onRequestPermissionsResult()方法
public void onRequestPermissionsResult(int requestCode, String[] permissions, int[] grantResults) {
    super.onRequestPermissionsResult(requestCode, permissions, grantResults);
    String requestPermissionsResult = "";
    if (requestCode == ××××_REQUEST_CODE) {
        for (int i = 0; i < permissions.length; i++) {
            if (grantResults[i] == PERMISSION_GRANTED) {
                requestPermissionsResult += permissions[i] + " 申请成功\n";
            } else {
                requestPermissionsResult += permissions[i] + " 申请失败\n";
            }
        }
    }
    Toast.makeText(this, requestPermissionsResult, Toast.LENGTH_SHORT).show();
}
```

###### 执行操作

```java
requestPermissions(String strPermission);
try{
	//要执行的操作
}catch (Exception e){
	e.printStackTrace();
}
```

