---
title: docker配置jdk8镜像
tags: 
  - docker
  - java
categories: 
  - notes
  - 后端
abbrlink: cc1858db
date: 2020-08-19 13:17:38
---


##### docker配置jdk8镜像

配置方式分为通过 docker 仓库直接拉取 openjdk ，另一种方式是下载 jdk 8 linux版，通过 Dockerfile 进行配置。

<!--more-->

###### docker仓库拉取jdk8镜像

1. 拉取镜像
    `docker pull openjdk:8-jre` 

2. 运行jdk容器
   `docker run -d -it --name jdk8 openjdk:8-jre` /bin/bash `

3. 进入容器内部检查
   `docker exec -it myopenjdk /bin/bash`

   ```shell
   [root@03005ebbf9c9]# java -version
   java version "1.8.0_261"
   Java(TM) SE Runtime Environment (build 1.8.0_261-b12)
   ```

###### Dockerfile配置jdk8镜像

1. 下载jdk1.8，并上传到/usr/local/src目录，然后解压

   开启允许传输的权限`sudo chmod 777 /usr/local/src` 

   ```shell
   $ cd /usr/local/src
   $ ls
   jdk-8u261-linux-x64.tar.gz
   $ sudo tar zxf jdk-8u261-linux-x64.tar.gz
   $ ls
   jdk1.8.0_261  jdk-8u261-linux-x64.tar.gz
   ```

2. 创建Dockerfile
   先在`/usr/local`目录下创建jdk目录，并将/usr/local/src下的 `jdk-8u261-linux-x64.tar.gz` 复制到`/usr/local/jdk`目录下，然后创建 `Dockerfile` 文件

   ```shell
   $ sudo mkdir /usr/local/jdk
   $ cd /usr/local/jdk
   $ sudo cp ../src/jdk-8u261-linux-x64.tar.gz ./
   $ sudo ls
   jdk-8u261-linux-x64.tar.gz
   $ sudo vi Dockerfile
   ```

   ```dockerfile
   # Dockerfile
   FROM centos:centos7
   MAINTAINER zhouchao
   RUN mkdir /usr/local/jdk
   WORKDIR /usr/local/jdk
   ADD jdk-8u261-linux-x64.tar.gz /usr/local/jdk
   
   ENV JAVA_HOME /usr/local/jdk/jdk1.8.0_261
   ENV JRE_HOME /usr/local/jdk/jdk1.8.0_261/jre
   ENV PATH $JAVA_HOME/bin:$PATH
   ```

3. 使用Dockerfile构建jdk1.8镜像

   ```shell
   $ docker build -t jdk1.8 .
   Sending build context to Docker daemon  143.1MB
   Step 1/8 : FROM centos:centos7
   centos7: Pulling from library/centos
   75f829a71a1c: Pull complete
   Digest: sha256:19a79828ca2e505eaee0ff38c2f3fd9901f4826737295157cc5212b7a372cd2b
   Status: Downloaded newer image for centos:centos7
    ---> 7e6257c9f8d8
   Step 2/8 : MAINTAINER zhouchao
    ---> Running in 77ae01469661
   Removing intermediate container 77ae01469661
    ---> a093e73e3ad0
   Step 3/8 : RUN mkdir /usr/local/jdk
    ---> Running in 3669281a5047
   Removing intermediate container 3669281a5047
    ---> 48038a16cc0c
   Step 4/8 : WORKDIR /usr/local/jdk
    ---> Running in 49cf2fb9fa22
   Removing intermediate container 49cf2fb9fa22
    ---> 7c9f9dd423b1
   Step 5/8 : ADD jdk-8u261-linux-x64.tar.gz /usr/local/jdk
    ---> a2112ce1417a
   Step 6/8 : ENV JAVA_HOME /usr/local/jdk/jdk1.8.0_261
    ---> Running in c220252fe774
   Removing intermediate container c220252fe774
    ---> 433e95e570d7
   Step 7/8 : ENV JRE_HOME /usr/local/jdk/jdk1.8.0_261/jre
    ---> Running in 654ac9d1adca
   Removing intermediate container 654ac9d1adca
    ---> 1cf2ae6ca829
   Step 8/8 : ENV PATH $JAVA_HOME/bin:$PATH
    ---> Running in a8e71af19743
   Removing intermediate container a8e71af19743
    ---> f4e4ef5b6c42
   Successfully built f4e4ef5b6c42
   Successfully tagged jdk1.8:latest
   ```

4. 在镜像仓库中查看是否构建成功

   ```shell
   $ docker images
   REPOSITORY          TAG                 IMAGE ID            CREATED              SIZE
   jdk1.8              latest              f4e4ef5b6c42        About a minute ago   556MB
   ```

5. 启动jdk容器

   ```shell
   $ docker run -di --name=jdk1.8 jdk1.8
   03005ebbf9c9ec65dd23977c527ce97578108c588c5cee0c1c7c0fd819d93053
   ```

6. 进入jdk容器，查看是否安装正确

   ```shell
   $ docker exec -it jdk1.8 /bin/bash
   [root@03005ebbf9c9 jdk]# pwd
   /usr/local/jdk
   [root@03005ebbf9c9 jdk]# ls
   jdk1.8.0_261
   [root@03005ebbf9c9 jdk]# java -version
   java version "1.8.0_261"
   Java(TM) SE Runtime Environment (build 1.8.0_261-b12)
   Java HotSpot(TM) 64-Bit Server VM (build 25.261-b12, mixed mode)
   ```

