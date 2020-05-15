---
title: '短暂解决ConnectionRefusedError:WinError 10061问题'
tags:
  - python
  - requests
abbrlink: 9d9c9508
date: 2020-04-03 12:00:00
---

#### 使用requests提示ConnectionRefusedError

###### 错误信息

ConnectionRefusedError: [WinError 10061] 由于目标计算机积极拒绝，无法连接

###### 解决方法

- 首先检查是否使用代理
    - 打开win10的网络和Internet设置
    - 勾选自动检测设置，关闭使用设置脚本，关闭手动设置代理的使用代理服务器
- 打开注册表  
- 删除路径：计算机\HKEY_CURRENT_USER\SOFTWARE\Microsoft\Windows\CurrentVersion\Internet Settings下的
    - ProxyEnable
    - ProxyOverride 
    - ProxyServer 
![](https://img-blog.csdnimg.cn/20191112200954515.png)
- 重新运行python程序
这种方式只能短暂解决问题，这三个文件会自动生成，需要重复删除
