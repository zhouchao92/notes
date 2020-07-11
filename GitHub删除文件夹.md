---
title: GitHub删除文件夹
tags:
  - git
abbrlink: bab8a3bc
date: 2020-07-07 20:07:52
categories:
---

删除步骤

1. 进入项目文件下，使用**Git Bash Here**（或cmd窗口）打开命令行窗口。
2. `git pull`或(`git pull origin master`)从远程仓库里面的项目拉下来，`dir`（Windows系统）查看文件目录结构。
3. 例如需要删除target文件夹，`git rm -f --cached target`
4. `git commit -m "Delete target."`提交，添加操作说明。
5. `git push`或(`git push -u origin master`)将本次更改推送到远程仓库。

```shel
$ git pull origin master
From https://github.com/xxxx/BlogSystem
 * branch            master     -> FETCH_HEAD
Already up to date.

$ git rm -r --cached target
rm 'target/classes/application.yml'
rm 'target/classes/com/study/BlogApplication.class'
...

$ git commit -m "Delete target."
[master f5ef2bf] Delete target.
 44 files changed, 67 deletions(-)
 delete mode 100644 target/classes/application.yml
 delete mode 100644 target/classes/com/study/BlogApplication.class
 ...

$ git push
Enumerating objects: 3, done.
Counting objects: 100% (3/3), done.
Delta compression using up to 4 threads
Compressing objects: 100% (2/2), done.
Writing objects: 100% (2/2), 219 bytes | 219.00 KiB/s, done.
Total 2 (delta 1), reused 0 (delta 0)
remote: Resolving deltas: 100% (1/1), completed with 1 local object.
To https://github.com/xxxx/BlogSystem.git
   661f345..f5ef2bf  master -> master
```

