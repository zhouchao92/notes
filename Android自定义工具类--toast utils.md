---
title: Android自定义工具类--toast utils
tags:
  - toast
  - android
abbrlink: 34a9fe48
date: 2020-04-19 21:34:50
---

###### 前言

在频繁调用Toast会造成较长的延时，不能及时显示最新的toast信息，使用ToastUtils解决延时bug

###### 介绍

`BaseApplication.getAppContext()`见文章--[Android子线程更新ui](/2020/04/03/Android非ui线程更新ui)

使用：`ToastUtils.showToast(message);`

###### 代码

```java
public class ToastUtils {

    private static Toast sToast;

    @SuppressLint("ShowToast")
    public static void showToast(String message) {
        if (sToast == null) {	//如果不存在就创建
            sToast = Toast.makeText(BaseApplication.getAppContext(), "", Toast.LENGTH_SHORT);
        }
        sToast.setText(message);	//设置内容
        sToast.show();		//显示toast内容
    }
}
```

