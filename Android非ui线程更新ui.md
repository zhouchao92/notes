---
title: Android非ui线程更新ui
tags:
  - ui
categories:
  - android
abbrlink: 89935aa5
date: 2020-04-03 12:00:00
---

#### 情景
在开发中会遇到在通过子线程获取数据，然后子线程需要更新ui，但是在android中这是不被允许的(子线程不能更新ui,android4.0开始)，可以通过三种方法解决(我已知的方法)。

<!--more-->

#### 解决方案
###### 方案一
在需要更新ui的语句前后分别加上	Looper.prepare();	Looper.loop();

```java
Looper.prepare();
更新ui的语句
Looper.loop();
```

###### 方案二
调用android的api  runOnUiThread()

```java
runOnUiThread(new Runnable() {
	@Override
   public void run() {
       // UI更新执行语句--以Toast为例
       Toast.makeText(MainActivity.this, "message",Toast.LENGTH_SHORT).show();
   }
});
```

###### 方案三
新建一个BaseApplication的类用来管理Context ，Handler，在需要更新ui的时候调用BaseApplication.getHandler().post(),用这个方法可以在一定程度上减少context和thread问题

Context 可应用的组件或控件

- activity
- service
- 基于全局显示的控件

```xml
//在manifest.xml文件中需要添加入口
<application
        android:name=".BaseApplication"/>

```

```java
public class BaseApplication extends Application {

    private static Handler sHandler = null;
    private static Context sContext = null;

    @Override
    public void onCreate() {
        super.onCreate();
        sHandler = new Handler();
        sContext = getBaseContext();
    }

    public static Context getAppContext() {
        return sContext;
    }

    public static Handler getHandler() {
        return sHandler;
    }
}
```
调用代码

```java
BaseApplication.getHandler().post(new Runnable() {
	@Override
	public void run() {
    	// ui更新语句
	}
});
```
