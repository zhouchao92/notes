---
title: Android数据的存储
tags:
categories:
  - android
abbrlink: 56c6f16a
date: 2020-04-03 12:00:00
---

###### 错误的存储方式

对一个简单的登录页面进行用户数据的保存，将数据存储到应用的数据目录下。

  在写入文件时，文件路径不能直接为android的根目录

例如：

```java
private String userFileName="/user.txt";        //文件名
```

 运用IO流写出数据

```java
private void saveUserInfo(String name, String pwd, String phone) {
        File user=new File(userFileName);
        try {
            if(!user.exists()){
                user.createNewFile();
            }
            FileOutputStream userFile=new FileOutputStream(user);
            userFile.write((name+" "+pwd+" "+phone).getBytes());    //以空格符为间隔存储
            userFile.close();
        }catch (Exception e){
            e.printStackTrace();
        }
}
```

报错信息（异常）：

```java
java.io.IOException: Read-only file system
```

<!--more-->

  在Android系统中，每一个应用都是独立的，其权限是有限的，不可操作其他应用的内容。

  "/"为根目录，这点不同于windows系统。

  使用其他的文件名，则会报找不到文件的异常

```java
private String userFileName="/com.exercise.myapplication/user.txt";
...

java.io.IOException: No such file or directory   //异常
```

###### 存储到包名路径下

当前用户---文件名应该为 : /data/data/应用包名路径/文件名

  在android6.0后支持多用户  应用文件路径为： /data/user/0/应用包名路径   （0 为用户号）

  在应用包路径下有 files 和  cache 两个文件夹

  在MainActivity.java中可以使用 getFilesDir() 方法得到files的路径,用 getCacheDir() 方法可以得到cache的路径，再使用拼接的方式设置文件的全路径

```java
    private String userFileName="user.txt";        //文件名
```

```java
    File files=this.getFilesDir();       //得到files路径
    File cache=this.getCacheDir();       //得打cache路径
    File user=new File(files,userFileName);
    FileOutputStream userFile=new FileOutputStream(user);
```

  读取文件并在应用可视时显示在对应的TextView中

```java
    @Override
    protected void onResume() {
        super.onResume();
        try {
            FileInputStream user=this.openFileInput(userFileName);
            BufferedReader bufferedReader=new BufferedReader(new InputStreamReader(user));
            String info=bufferedReader.readLine();
            String[] splits=info.split(" ");    //以空格符为界切割
            String name=splits[0];
            String pwd=splits[1];
            String phone=splits[2];
            etName.setText(name);    //用户名
            etPwd.setText(pwd);      //密码
            etPhone.setText(phone);  //手机号
        } catch (Exception e) {
            e.printStackTrace();
        }
    }
```

###### 数据存在sdcard中

  将数据存在sdcard中：

  在 manifest 的 AndroidManifest.xml 打开storage的读写权限

```java
    <uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE"></uses-permission>
    <uses-permission android:name="android.permission.READ_EXTERNAL_STORAGE"></uses-permission>
```

在Android6.0后，读写storage需要动态的申请权限(或者直接在设置里更改应用的存储权限)

storage的相对路径: /storage/emulated/0 (内部存储)   （1表示外置内存卡）

```java
    private void saveInSdcard() {
        requestPermissions();
        File storagePath=new File(Environment.getExternalStorageDirectory().getAbsolutePath());//获取storage的相对路径
        File file=new File(storagePath,"text.txt");     //文件路径
        try {
            FileOutputStream fos=new FileOutputStream(file);
            fos.write(("text words").getBytes());
            fos.close();
        } catch (Exception e) {
            e.printStackTrace();
        }
    }
    public void requestPermissions() {
    //检查是否获取读取权限
        if (ContextCompat.checkSelfPermission( this, Manifest.permission.READ_EXTERNAL_STORAGE) != PERMISSION_GRANTED) {
            if (ActivityCompat.shouldShowRequestPermissionRationale( this, Manifest.permission.READ_EXTERNAL_STORAGE)) {
                Toast.makeText(this, "需要读写权限", Toast.LENGTH_LONG).show(); //提示未获取读取权限
            } else {
                ActivityCompat.requestPermissions( this, new String[]{
                    Manifest.permission.WRITE_EXTERNAL_STORAGE, Manifest.permission.READ_EXTERNAL_STORAGE}, 1);     //请求读写权限
            }
        }
    }
    //重写onRequestPermissionsResult()方法
    public void onRequestPermissionsResult(int requestCode, String[] permissions, int[] grantResults) {
        super.onRequestPermissionsResult(requestCode, permissions, grantResults);
        String requestPermissionsResult = "";
        if (requestCode == 1) {
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