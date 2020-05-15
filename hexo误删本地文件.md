---
title: hexo误删本地文件
tags:
  - hexo
abbrlink: 18f8dfb9
date: 2020-04-08 22:08:11
---

###### 事件

今天误删了hexo博客本地文件夹，源文件没有直接恢复的方法。于是就将github pages上博客主页一篇一篇复制重新写入本地（办法非常的愚蠢，不过好像也没有别的途径了），所以使用hexo+github记录博客，一定要将源文件备份！！！

在`\source\_posts`关联版本库，然后推到仓库

```shell
//git初始化，生成必要文件
git init
//关联版本库
git remote add origin https://gitee.com/xxxxx/demo.git
//添加文件  全部添加
git add .
//注释
git commit -m ""
//推送
git push -u origin master
```

这样可以在文件误删时能恢复到本地，或者是系统重装等操作获取到博客源文件。