---
title: hexo添加RSS订阅功能
tags:
  - hexo
  - RSS
abbrlink: b9d63583
date: 2020-04-17 13:25:53
---

###### 安装插件

在博客根目录下运行 `npm install hexo--generator-feed`

安装过淘宝镜像可使用 `cnpm install hexo--generator-feed` 命令

###### 配置

在**根目录**下的`_config.yml`文件中添加下列代码

```yml
# Extensions
#RSS订阅
plugins:
  hexo-generator-feed
#Feed Atom
feed:
  type: atom
  path: atom.xml # 生成路径
  limit: 20 # 最新的20篇文章
```

在**主题目录**下的`_config.yml`文件中添加下列代码

```yml
# subNav-子导航
subNav:
	rss: "/atom.xml" # 或 "atom.xml" ，这个路径与path一致
```

###### 生成文件

在博客根目录下重新渲染 `hexo g`

```shell
$ hexo g
INFO  Start processing
INFO  Files loaded in 955 ms
INFO  Generated: atom.xml # 生成atom.xml文件
INFO  1 files generated in 933 ms
```

在`public`文件夹下生成`atom.xml`

本地预览：`hexo s` 点击RSS会跳转保存窗口

推送到远程仓库：`hexo d` 点击RSS跳转到`atom.xml`页面

