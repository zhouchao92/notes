---
title: android界面跳转
tags:
categories:
  - android
abbrlink: de41b12e
date: 2020-04-03 12:00:00
---

## 非响应式跳转
### 跳转后的界面
创建跳转后的界面代码和布局  
```java
public class SecondActivity extends Activity {
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_second);
    }
}
```
`activity_second.xml`布局文件

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout
    xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical"
    android:gravity="center">
    <TextView
        android:id="@+id/tv_text"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:gravity="center"
        android:text=""/>
</LinearLayout>
```
在`AndroidManifest.xml`中注册
```xml
<activity android:name=".SecondActivity"></activity>
```

<!--more-->

### 显式意图跳转

```java
Intent intent=new Intent(MainActivity.this,SecondActivity.class);
//键值对的方式传输
//internt.putExtra("","");
intent.putExtra("name",etName.getText().toString());	//传递用户名
intent.putExtra("pwd",etPwd.getText().toString()); 		//传递密码
startActivity(intent);		//启动
```
### 隐式意图跳转
在`AndroidManifest.xml`添加过滤器
```xml
<!--action:包名.信息(自定义)-->
<!--category(类别)-->
<!--data(数据)-->
<activity android:name=".MainActivity">
	<intent-filter>
       <action android:name="com.study.USER_INFO"></action>
       <category android:name="android.intent.category.DEFAULT"></category>
    </intent-filter>
</activity>
```
```java
Intent intent=new Intent();         //隐式意图
intent.setAction("com.study.USER_INFO");
intent.addCategory(intent.CATEGORY_DEFAULT);    //相当于intent.addCategory(android.intent.category.DEFAULT）;
intent.putExtra("name",etName.getText().toString());	//传递用户名
intent.putExtra("pwd",etPwd.getText().toString());		//传递密码
startActivity(intent);		//启动
```
### 数据获取
```java
Intent intent = getIntent();    //获取上级界面所传递的数据
String name = intent.getStringExtra("name");
String pwd = intent.getStringExtra("pwd");
TextView tvText = findViewById(R.id.tv_text);
tvText.setText("账号："+name+"  密码："+pwd);   //显示信息
```

##  响应式跳转
使用startActivity()方法只能将上层界面的信息传递到下层界面，不能实现界面信息的回传，响应式界面跳转通过startActivityForResult()方法在启动下层界面时发出请求码，下层界面通过result()方法发送结果码，并将信息传递到上层界面

```java
//上层界面跳转下层界面
private static final int REQUEST_CODE = 1;  //请求码
private static final int RESULT_CODE=2;     //结果码
Intent intent=new Intent();					//显式或隐式跳转
intent.putExtra();							//传递到下层界面的信息
startActivityForResult(intent,REQUEST_CODE );		//启动

//上层界面接收结果
//重写onActivityResult()方法
@Override
protected void onActivityResult(int requestCode, int resultCode,Intent data) {
    super.onActivityResult(requestCode, resultCode, data);
    if (requestCode==REQUEST_CODE ) {
        if (resultCode==RESULT_CODE) {
            ...
        }
    }
}
```
```java
//下层界面
private static final int RESULT_CODE=2;   //结果码
Intent intent = new Intent();
intent.putExtra();						//传递给上层界面的信息
setResult(RESULT_CODE, intent);
finish();
```
setResult()方法只有在finish()方法执行前才能响应，把setResult()写在onDestroy()中无法实现请求的响应,如果需要在activity结束时回传信息，可以采取监听返回键的方式实现回传。
```java
@Override
public void onBackPressed() {
    Intent intent = new Intent();
    intent.putExtra();
    setResult(RESULT_CODE, intent);
    super.onBackPressed();
}
```

