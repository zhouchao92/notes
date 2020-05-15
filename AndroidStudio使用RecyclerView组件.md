---
title: android studio低版本使用recyclerview
tags:
  - android
  - recyclerview
date: 2020-04-03 12:00:00

---

﻿1. 添加依赖包 
&nbsp;&nbsp;&nbsp;&nbsp; open Module Settings→Dependencies→add Library Dependency
![在这里插入图片描述](https://img-blog.csdnimg.cn/20190714093004116.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQxNTk5MzY2,size_16,color_FFFFFF,t_70)![在这里插入图片描述](https://img-blog.csdnimg.cn/20190714093101907.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQxNTk5MzY2,size_16,color_FFFFFF,t_70)
2. AndroidMainfest.xml文件
```xml
	<application
		android:appComponentFactory=""
        tools:replace="android:appComponentFactory">
```
3. 在gradle.properties文件中添加以下代码
```javascript
	android.enableJetifier=true
	android.useAndroidX=true
	org.gradle.jvmargs=-Xmx1536m
```

在原有工程中使用androidX.RecyclerView会出现报错
解决方法：在AndroidManifest文件下右键Refactor→Migrate to AndroidX 
