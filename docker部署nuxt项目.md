---
title: docker部署nuxt项目
tags:
  - docker
  - nuxt
categories:
  - 前端
abbrlink: cebd0e3e
date: 2020-10-26 09:51:00
---

###### 部署方式：nginx / node

在虚拟机上部署 nuxt.js 项目：

1. 可以采取先构建生成静态资源文件，然后放在 nginx 容器中，完成 nginx 的配置即可。
   ==缺点：这种以静态文件的形式直接放在 nginx 的 html 目录下，失去了 nuxt 的服务端渲染的功能。==
2. 以 node 为基础镜像，构建 nuxt 项目的镜像，运行 nuxt 项目的容器。这样可以保留 nuxt 服务端渲染的功能。

<!--more-->

###### node 方式

基本原理：以 node 为基础镜像，`npm install` 拉取项目使用的依赖文件，`npm start` 启动服务。启动容器后以服务端的形式运行在后台，提供服务端渲染的功能。

1. 修改`package.json`的配置，指定 host 和 端口，如果不指定会以虚拟机私有地址启动，而本机无法访问到。

   > `package.json`
   >
   > ```json
   > "config": {
   >   "nuxt": {
   >     "host": "0.0.0.0",
   >     "port": "9002"
   >   }
   > }
   > ```

2. 将项目文件上传到虚拟机上。

3. 创建 Dockerfile 。

   ```dockerfile
   # 基础镜像
   FROM node:12.18.3
   # 作者
   MAINTAINER lao-biao
   # node的环境为生产环境
   ENV NODE_ENV=production
   # 容器内创建目录
   RUN mkdir -p /app/src
   # 复制当前的内容到容器内容部目录
   COPY . /app/src
   # 切换工作目录
   WORKDIR /app/src
   # 配置npm的远程仓库
   RUN npm config set registry https://registry.npm.taobao.org
   # 安装依赖
   RUN npm install
   # 构建，生成dist文件
   RUN npm run build
   # 启动
   CMD ["npm","start"]
   ```

4. 构建镜像，创建容器。

   ```bash
   dockers build -t blog-portal . # 构建镜像
   docker run -d --name blog-portal -p 9002:9002 blog-portal # 创建容器
   ```

在需要使用 nginx 作为反向代理时，可以进行相关配置。