---
title: hexo+yilia+github搭建个人博客
tags: 
  - 个人博客
categories:
  - hexo
abbrlink: 73e911cd
date: 2020-04-05 12:00:00
---

###### 前言

学习使用hexo框架搭建个人博客，开启博客之旅。

###### 下载工具

- nodejs [nodejs网址](https://nodejs.org/en/) 根据操作系统下载对应的版本
- git [git网址](https://git-scm.com/)

###### 检查工具

windows操作系统下打开命令行窗口，执行命令

- `node -v`
- `npm -v`
- `git -v`

如果返回对应的版本号信息，说明安装成功

<!--more-->

###### 博客框架基础部分

在电脑的合适位置创建文件夹（blog）用于存放和管理博客，在此文件路径下执行命令行窗口（Mac用户需要获取管理员权限），执行下列命令

```shell
//安装淘宝的cnpm管理器,其他国内镜像源也可
npm install -g cnpm --registry=http://registry.npm.taobao.org 

//安装hexo基础框架
cnpm install -g hexo-cli

//初始化生成框架文件
hexo init
```

生成hexo框架文件后，默认会生成一篇hello word的博客，博客框架基本搭建好了，可以通过本地的端口预览，执行命令`hexo s` 即可通过本地的4000端口访问博客

地址http://localhost:4000/

###### 上传到github

- 在github创建公开仓库：github账户名.github.io

  仓库名必须是以 github账户名+.github.io 命名

- 修改hexo本地设置：通过记事本更改文件夹*_config.yml*的以下内容

  ```yaml
  # Deployment
  ## Docs: https://hexo.io/docs/deployment.html
  deploy:
    type: git
    repo: git@github.com:xxxx/xxxx.github.io.git #仓库的ssh地址或http地址
    branch: master #默认是master分支
  ```

在blog文件夹下执行窗口命令安装git自动部署插件

```shell
// 安装git自动部署插件
cnpm install --save hexo-deployer-git
```

推送到github仓库

```shell
//清理
hexo clean
//生成 hexo generate
hexo g
//推送到仓库 hexo deploy
hexo d
```

推送成功后即可通过[https://xxxx.github.io](https://xxxx.github.io/)	访问博客

###### 修改主题

从github上clone导入yilia主题文件

```shell
git clone https://github.com/litten/hexo-theme-yilia.git themes/yilia
```

数据以json的形式保存`cnpm i hexo-generator-json-content --save`

修改hexo根目录下的*_config.yml* :

```yaml
# Extensions
## Plugins: https://hexo.io/plugins/
## Themes: https://hexo.io/themes/
theme: yilia

jsonContent:
  meta: false
  pages: false
  posts:
    title: true
    date: true
    path: true
    text: true
    raw: false
    content: false
    slug: false
    updated: false
    comments: false
    link: false
    permalink: false
    excerpt: false
    categories: false
    tags: true
```

配置yilia文件下的*_config.yml* : *目录：/themes/yilia/config.yml）*,根据个人喜好进行设置

修改好配置信息后，通过 `hexo clean` `hexo g` `hexo s`在本地预览

执行 `hexo clean` `hexo g` `hexo d`上传同步到github仓库