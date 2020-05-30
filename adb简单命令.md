---
title: adb简单命令
tags:
  - adb
abbrlink: 9632f7ff
date: 2020-04-03 12:00:00
---

#### 设备
###### 1. 设备查看
```shell
adb devices
```
###### 2. 设备重新挂载 
对android的内部系统文件进行查看,需要获得root权限(adb root)，模拟器Android 6.0以后需要第三方软件root才能成功
```shell
adb remount
//有的Android系统需要两次root才能挂载
adb root
adb reboot
adb root
adb remount 
```
###### 3.设备重启
```shell
adb reboot
```
###### 4.设备关机
```shell
adb reboot -p
```
###### 5.多设备操作
```shell
adb -s 设备名称(devices查看) 命令  
```
<!--more-->

#### 常用shell命令

- 列出所有文件   ls(相当于dos中的dir命令)
- 切换路径    cd
- 打印当前路径    pwd
- 创建文件/文件夹    touch 文件名 / mkdir 文件夹名
- 复制文件    cp &nbsp; 原文件名(绝对路径)  (路径)新文件名
- 移动文件   mv &nbsp; 原文件名(绝对路径)  (路径)新文件名
- 删除文件/文件夹    rm  文件名 /  rm -r 文件夹名
- 文本内容查看    cat 文件名(注意带后缀) (*.后缀名 打开本路径下所有的此后缀的文件) 
- 清除屏幕	clear

####  Push与Pull
###### 把文件推进系统
```shell
adb push (本地)文件路径 (android)文件新路径
```
###### 把文件拖出系统
```shell
adb pull (android)文件路径 (本地)文件路径
```
#### logcat
###### 捕捉
用于抓取log日志

```shell
adb logcat > (本地路径)文件名.log
ctrl+c 可停止捕捉
```
###### 获取应用包名
应用打开后开始记录log

```shell
adb logcat | grep START (grep为Linux系统的指令)
adb logcat | findstr START (Win10系统下)
```

#### adb控制应用
###### 安装应用
```shell
adb install 应用的全路径(.apk文件)
```
###### 启动应用
```shell
adb shell am start -n 包名路径/.应用入口
```
###### 删除应用数据
```shell
adb shell pm clear 包名路径
```
###### 卸载应用
```shell
adb uninstall 包名路径	
```

#### 发送广播

```shell
adb shell broadcast -a [action] 参数[--类型 key value]
```

#### 截屏

截图，存放在指定位置，拖出系统到当前路径下

```shell
adb shell screencap -p /sdacrd/screen.png
adb pull /sdcard/screen.png ./
```

#### 模拟按键

```shell
adb shell input keyevent 键码(keycode)
eg.home键
adb shell input ketevent 3
```

键码可查看 `android.view.KeyEvent.java`文件