---
title: Android自定义工具类--logutils
tags:
  - android
  - log
abbrlink: c3843c50
date: 2020-04-19 21:22:28
---

###### 前言

Android项目中通常会使用log来进行调试，在发布版本后并不需要输出log，使用logutils类来控制log的输出管理

###### 简介

在应用调试阶段设置当前log等级大于或等于debug等级，即可输出debug，info，warming和error四个等级的log

在应用发布时只需要将当前log等级调整为0或小于0即可关闭log的输出

参数

- object： 类对象(`this`或`ClassName.this`)
- message： log信息

###### 代码

```java
public class LogUtils {

    private static final int CURRENT_LEV = 4;	//当前log等级
    private static final int DEBUG_LEV = 4;		//debug
    private static final int INFO_LEV = 3;		//info
    private static final int WARMING_LEV = 2;	//warming
    private static final int ERROR_LEV = 1;		//error

    public static void d(Object object, String message) {
        if (CURRENT_LEV >= DEBUG_LEV) {
            Log.d(object.getClass().getSimpleName(), message);
        }
    }

    public static void i(Object object, String message) {
        if (CURRENT_LEV >= INFO_LEV) {
            Log.i(object.getClass().getSimpleName(), message);
        }
    }

    public static void w(Object object, String message) {
        if (CURRENT_LEV >= WARMING_LEV) {
            Log.w(object.getClass().getSimpleName(), message);
        }
    }

    public static void e(Object object, String message) {
        if (CURRENT_LEV >= ERROR_LEV) {
            Log.e(object.getClass().getSimpleName(), message);
        }
    }

}
```

