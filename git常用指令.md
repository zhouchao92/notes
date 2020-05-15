---
title: git常用指令
tags:
  - git
abbrlink: c34887c6
date: 2020-04-19 18:57:06
---

###### 基本配置

`git config --global user.name "Your Name"`设置用户名

`git config --global user.email "email@example.com"`设置邮箱

###### 初始化

`git init`初始化生成.git文件

###### 查看本地仓库状态

`git status`查看本地仓库状态

###### 连接远程仓库

`git remote add origin https://github.com/xxxxx/demo.git`连接远程仓库

###### 克隆

`git clone https://github.com/xxxxx/demo.git directory` 从远程仓库克隆到文件夹中，默认路径为当前路径

<!--more-->

###### 添加文件/文件夹

`touch filename`在本地仓库添加文件

`git add filename`添加文件至暂存区

`git add -f filename` 添加文件夹至暂存区

`git add .` 添加目录下所有发生改变的文件至暂存区

`git commit -m ' '`添加到工作区，提交注释信息

###### 拉取

`git fetch`从远程仓库获取目录(**head**)信息



`git pull` 从远程仓库获取到本地仓库，获取的是**代码**

`git pull --rebase origin master` 解决本地缺少README.md文件问题，远程仓库在初始化时添加了README.md文件

`git pull origin master --allow-unrelated-histories` 把远程仓库和本地同步，消除差异

###### 推送push

`git push`推送到远程仓库

`git push -u origin master` 本地仓库代码提交至远程仓库，第一次推送到远程仓库必须使用

###### 查看commit日志

`git log`返回commit的hash值，以及commit信息

###### 撤回

`git reset`的三种操作：

`git reset --soft xxxx(commit操作的hash值)`将文件提交至本地仓库后撤回暂存区

`git reset --mixed xxxx(commit操作的hash值)` 将本地仓库文件撤回至工作区

`git reset --hard xxxx(commit操作的hash值)` 将工作区、暂存区、本地仓库的所有提交的文件全都撤销（包括工作区文件，会删除）

###### 分支

`git branch temp` 创建分支temp

`git checkout temp` 使用分支temp

`git merge temp` 合并分支到当前分支

`git branch -D temp`删除分支temp

###### 其他

`ll`显示当前路径下的所有文件及其属性

`cd`切换路径

`cd ../`切换至上一层路径

`pwd` 查看当前全路径

###### 官方文档

[git官方文档](https://git-scm.com/book/zh/v2)
