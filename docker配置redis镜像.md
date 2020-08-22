---
title: docker配置redis镜像
tags: 
  - docker
  - redis
categories: 
  - notes
  - 后端
abbrlink: cf9a8130
date: 2020-08-19 12:58:20
---


###### 安装redis镜像

从 docker 的仓库中拉取 redis 镜像，不写版本号时，默认使用 `latest` 最新版

```shell
$ docker pull redis[:版本号]
```

<!--more-->

###### 创建redis容器与启动

1. 创建配置文件挂载文件夹和 redis 挂载的配置文件

   ```shell
   $ mkdir -p /mydata/redis/conf
   $ touch /mydata/redis/conf/redis.conf
   ```

2. 创建 redis 容器，并将配置文件挂载到虚拟机本地

   ```shell
   $ docker run -p 6379:6379 --name redis -v /mydata/redis/data:/data \
   -v /mydata/redis/conf/redis.conf:/etc/redis/redis.conf \
   -d redis redis-server /etc/redis/redis.conf
   ```

3. 检查 redis 的相关配置文件

   ```shell
   [root@localhost conf]# pwd
   /mydata/redis/conf
   [root@localhost conf]# ls
   redis.conf
   ```

###### 配置redis自启动

配置 redis 容器在docker启动时同时启动。

```shell
[root@localhost vagrant]# docker update redis --restart=always
```

###### 进入redis的客户端

```shell
[root@localhost conf]# docker exec -it redis redis-cli
127.0.0.1:6379>
```

###### 将redis数据持久化到硬盘

修改 `/mydata/redis/conf/redis.conf` 文件

```shell
[root@localhost conf]# vi redis.conf
# 添加以下内容
appendonly yes
```


###### 