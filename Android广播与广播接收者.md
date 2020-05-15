---
title: android四大组件--broadcast
tags:
  - android
  - broadcast
abbrlink: c231612
date: 2020-04-03 12:00:00
---

#### 使用广播来接收系统内部发送的广播

##### 动态注册广播监听电量变化
在应用的活动页面创建时启动广播，通过继承广播接收者的类来接收系统内部发送的广播，在活动页面销毁时取消广播，以防止内存泄漏的问题。

```java
    private BatteryLevelReceiver mBatteryStateReceiver;     //接收广播

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
        //意图过滤
        IntentFilter intentFilter=new IntentFilter();
        //设置频道--电量变化
        intentFilter.addAction(Intent.ACTION_BATTERY_CHANGED);
        //添加广播接收者
        mBatteryStateReceiver = new BatteryLevelReceiver();
        //启动广播
        this.registerReceiver(mBatteryStateReceiver,intentFilter);//参数：BroadcastReceiver,IntentFilter
    }

    private class BatteryLevelReceiver extends BroadcastReceiver{

        @Override
        public void onReceive(Context context, Intent intent) {
            String action = intent.getAction();
            Log.d(TAG, "action:" + action);
            Log.d(TAG, "batteryLevel:"+intent.getIntExtra(BatteryManager.EXTRA_LEVEL,0));
        }
    }

    @Override
    protected void onDestroy() {
        super.onDestroy();
        if (mBatteryStateReceiver!=null) {
            unregisterReceiver(mBatteryStateReceiver);      //取消广播，防止内存泄漏
        }
    }
```

<!--more-->

##### 静态注册广播监听系统开机

静态注册广播需要在清单文件中添加对应的action（category、data），监听系统的开机还需要添加响应的系统权限。使用静态注册广播不能释放内存，一直在监听，对内存占用比较大。

```xml
<uses-permission android:name="android.permission.RECEIVE_BOOT_COMPLETED"></uses-permission>

<receiver android:name=".BootCompletedReceiver">
    <intent-filter>
        <action android:name="android.intent.action.BOOT_COMPLETED" />  <!--开机完成的action-->
    </intent-filter>
</receiver>
```

```java
public class BootCompletedReceiver extends BroadcastReceiver{
    private static final String TAG = "BootCompletedReceiver";

    @Override
    public void onReceive(Context context, Intent intent) {
        Log.d(TAG, ""+intent.getAction());
        Toast.makeText(context,"开机完成",Toast.LENGTH_LONG).show();//开机完成时弹出提示信息
    }
}
```

#### 有序广播
发送有序广播，接收者按照优先级依次收到广播，高优先级的广播接收者可以中止广播，也能修改广播内容。通过设置  `android:priority` 的值来设置优先级，priority的大小为-1000到1000，默认值为0 
<font color="#ff0000">在Android8.0后需要动态注册广播才能接收，使用静态注册广播需要指定包名，静态广播注册报错 `Background execution not allowed`</font>  

```xml
<activity android:name=".SendOrderBroadcast">
    <intent-filter>
        <action android:name="android.intent.action.MAIN" />

        <category android:name="android.intent.category.LAUNCHER" />
    </intent-filter>
</activity>
<receiver android:name=".HighLevelBroadcastReceiver">
    <intent-filter android:priority="1000">
        <action android:name="sendOrderBroadcastTest"/>
    </intent-filter>
</receiver>
<receiver android:name=".LowLevelBroadcastReceiver">
    <intent-filter android:priority="0">
        <action android:name="sendOrderBroadcastTest"/>
    </intent-filter>
</receiver>
```

```java
    //开启广播
    private void broadcastSender() {
        //使用动态注册广播的方式
        HighLevelBroadcastReceiver highLevelBroadcastReceiver=new HighLevelBroadcastReceiver();
        LowLevelBroadcastReceiver lowLevelBroadcastReceiver=new LowLevelBroadcastReceiver();
        IntentFilter intentFilter=new IntentFilter();
        intentFilter.addAction("sendOrderBroadcastTest");
        registerReceiver(highLevelBroadcastReceiver,intentFilter);
        registerReceiver(lowLevelBroadcastReceiver,intentFilter);
        /*
        使用静态注册指定广播接收者,指定包名
        Intent intent=new Intent();
        ComponentName componentName=new ComponentName(SendOrderBroadcast.this,HighLevelBroadcastReceiver.class);
        intent.setComponent(componentName);
        intent.setAction("sendOrderBroadcastTest");
        sendBroadcast(intent,null);
        */
    }

    //receivePermission在接收时接收端需要添加权限-----Manifest文件
    //点击发送有序广播按钮的消息映射
    public void sendOrderBroadcast(View view){
        //发送广播内容
        Intent intent=new Intent();
        intent.setAction("sendOrderBroadcastTest");
        Bundle bundle=new Bundle();
        bundle.putCharSequence("content","这是一条有序广播");
        //参数:Intent,receivePermission,resultReceiver,scheduler,initialCode,initialData,initialExtras
        sendOrderedBroadcast(intent,null,null,null, Activity.RESULT_OK,null,bundle);
    }
```

```java
public class HighLevelBroadcastReceiver extends BroadcastReceiver {
    private static final String TAG = "HighLevelReceiver";

    @Override
    public void onReceive(Context context, Intent intent) {
        //中止广播
        //abortBroadcast()调用这个方法能中止广播的发送，低优先级的广播接收者无法接收
        //接收广播内容
        Bundle resultExtras = getResultExtras(true);
        String content = resultExtras.getCharSequence("content").toString();
        Log.d(TAG, "content:"+content); //log输出接收到的广播内容
        //修改广播内容，直接重写广播内容
        resultExtras.putCharSequence("content","有序广播内容被修改！");
        setResultExtras(resultExtras);
    }
}
```

```java
public class LowLevelBroadcastReceiver extends BroadcastReceiver {
    private static final String TAG = "LowLevelReceiver";

    @Override
    public void onReceive(Context context, Intent intent) {
        //接收广播内容
        Bundle resultExtras = getResultExtras(true);//参数为false时则不接收上级广播添加的内容
        String content = resultExtras.getCharSequence("content").toString();
        Log.d(TAG, "content:"+content);
    }
}
```

#### 权限
##### 广播发送时添加权限
在清单文件中添加权限信息`< permission android:name="">< /permission>`，在调用`sendOrderedBroadcast()`方法时指定参数`receivePermission`添加权限，<font color="#f00">(在manifest文件中添加完权限后需要make project后才能使用)</font>`Manifest.permission.权限名`，接收端在接收时需要在清单文件中添加`< uses-permission android:name="">< /uses-permission>`相应的权限信息

##### 广播接收者添加权限  
在清单文件中添加权限信息`< permission android:name="">< /permission>`，在广播接收者的注册信息中添加指定的权限信息`android:permission=""`，广播发送端只有添加了指定的权限`< uses-permission android:name="">< /uses-permission>`后才能给广播接收者发送广播。
