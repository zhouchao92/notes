---
title: docker部署SpringBoot项目
tags:
  - docker
  - spring boot
categories:
  - 后端
abbrlink: a192e4a5
date: 2020-08-21 21:22:40
---

##### 前言

docker 部署基于 Spring Boot 的个人博客后端项目，首先要将项目中使用的 `localhost` 全部更换为虚拟机所对应的IP地址，其次，需要项目生成的 jar （通过 jar 包部署，也有通过 war 包部署的方式，需要使用 tomcat ），通过 jar 包部署使用Spring Boot项目内嵌的 tomcat 或 jetty 。

<!--more-->

##### 准备

1. 项目配置修改
2. 项目打包生成`jar`包

###### 项目配置

博客项目中使用到 mysql，redis，solr ，在 `application-prod.yml` 中修改 url ，与虚拟机的IP地址相对应，`application.yml` 修改默认环境为 *prod*。项目中使用到 `localhost` 的位置，也需要进行修改。

```yml
spring:
  application:
    name: my-blog
  # 数据源mysql
  datasource:
    url: jdbc:mysql://192.168.134.128:3306/blog?useSSL=false
    driver-class-name: com.mysql.cj.jdbc.Driver
    username: root
    password: root
  # 配置redis
  redis:
    host: 192.168.134.128
    password: ''
    database: 0
  # 修改文件上传限制
  servlet:
    multipart:
      max-file-size: 5242880 # 5MB
  # 配置solr
  data:
    solr:
      host: http://192.168.134.128:8983/solr/blog_core
  # 控制jpa的sql语句输出
  jpa:
    show-sql: false
```

```yml
spring:
  profiles:
    active: prod

# 端口
server:
  port: 9000

# 自定义配置文件上传路径，以及文件大小上限
blog:
  image:
    upload-path: /usr/app/blog
    max-size: 5242880   # 5MB
```

###### jar 包生成

1. 在打包前在 `pom.xml` 中配置好打包方式为 *jar* ，并且将 test 测试文件排除（否则在 maven 打包时出错）

   ```xml
   <project>
       <!-- 打包方式 -->
       <packaging>jar</packaging>
       <build>
           <!-- 打包生成的jar名称 -->
           <finalName>my-blog-1.0.0</finalName>
           <plugins>
               <plugin>
                   <groupId>org.springframework.boot</groupId>
                   <artifactId>spring-boot-maven-plugin</artifactId>
                   <configuration>
                       <mainClass>com.study.BlogApplication</mainClass>
                       <includeSystemScope>true</includeSystemScope>
                   </configuration>
               </plugin>
               <!-- 添加打包时跳过测试文件 -->
               <plugin>
                   <groupId>org.apache.maven.plugins</groupId>
                   <artifactId>maven-surefire-plugin</artifactId>
                   <version>2.22.1</version>
                   <configuration>
                       <skipTests>true</skipTests>
                   </configuration>
               </plugin>
           </plugins>
       </build>
   </project>
   ```

2. 使用 maven 工具，执行 `clean` ，清除项目生成的target文件夹，然后执行 `package`，对项目进行打包，打包完成后在target文件夹中生成项目的 `jar`。
   命令行方式：

   ```bash
   mvn clean
   mvn package
   ```

   ![maven工具栏](https://gitee.com/lao-biao/Pictures/raw/master/Docker/maven%E5%B7%A5%E5%85%B7%E6%A0%8F.png)

##### 部署

1. 通过xftp软件将 `jar`包 拷贝到虚拟机上。
2. 生成镜像
3. 运行容器

###### Docker生成博客项目镜像

在 `jar` 包（项目生成的jar包为my-blog-1.0.0.jar）的当前目录下创建 `Dockerfile` 文件，`Dockerfile`内容如下。

```dockerfile
# 基于java8镜像构建，也可以通过配置好的jdk8镜像来构建
FROM java:8
# 作者
MAINTAINER zhouchao
RUN mkdir /usr/app
# 创建图片上传的路径
RUN mkdir /usr/app/blog
# 复制程序到内部
ADD my-blog-1.0.0.jar /usr/app
# 切换工作目录
WORKDIR /usr/app
# 暴露端口
EXPOSE 9000
# 镜像运行时执行
ENTRYPOINT ["java","-jar","my-blog-1.0.0.jar"]
```

生成镜像

```shell
[root@localhost blog-java]# docker build -t blog .
Sending build context to Docker daemon  89.81MB
Step 1/8 : FROM java:8
 ---> d23bdf5b1b1b
Step 2/8 : MAINTAINER zhouchao
 ---> Using cache
 ---> 564652b78de2
Step 3/8 : RUN mkdir /usr/app
 ---> Using cache
 ---> 1f5db921f762
Step 4/8 : RUN mkdir /usr/app/blog
 ---> Using cache
 ---> cb4f18ee64f3
Step 5/8 : ADD my-blog-1.0.0.jar /usr/app
 ---> Using cache
 ---> 940b61cdd5a4
Step 6/8 : WORKDIR /usr/app
 ---> Using cache
 ---> 16c9ffc0c43c
Step 7/8 : EXPOSE 9000
 ---> Using cache
 ---> a21afb67831c
Step 8/8 : ENTRYPOINT ["java","-jar","my-blog-1.0.0.jar"]
 ---> Using cache
 ---> 05453583ff92
Successfully built 05453583ff92
Successfully tagged blog:latest

docker images
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
blog                latest              05453583ff92        2 seconds ago       733MB
```

###### Docker生成博客项目容器

1. 首先需要先创建图片挂载目录，使图片资源持久化。

   ```shell
   [root@localhost blog-java]# mkdir /mydata/blog
   ```

2. 创建容器

   - `-d`后台运行
   - `--name blog`容器名称为 blog
   - `--restart=always`容器自启动
   - `-p 9000:9000`端口映射
   - `-v /mydata/blog:/usr/app/blog/src`图片资源目录挂载
- `-v /mydata/blog/logs:/usr/app/logs`日志目录挂载
  
   ```shell
   $ docker run -d --name blog --restart=always -p 9000:9000 -v /mydata/blog/src:/usr/app/blog -v /mydata/blog/logs:/usr/app/logs blog
   b577dfcb31879743ee4bc05b54cc07ec864ba3ee4872924e91d7ca9609f37589
   ```

###### 访问

项目中使用了swagger生成api文档，通过访问swagger-ui.html来查看。

> 地址 http://192.168.134.128:9000/swagger-ui.html

![后端部署访问效果](https://gitee.com/lao-biao/Pictures/raw/master/Docker/%E5%90%8E%E7%AB%AF%E9%83%A8%E7%BD%B2%E8%AE%BF%E9%97%AE%E6%95%88%E6%9E%9C.png)

##### 踩坑记录

在项目部署时遇到了一些问题

###### 容器反复重启

<font color="red">容器反复重启</font>，可能是后端代码问题，先移除当前容器，创建新容器，以控制台形式执行，输出 log ，用于查询错误。

```shell
$ docker run -i --name blog  -p 9000:9000 -v /mydata/blog:/usr/app/blog blog
```

###### 用户登录成功响应中没有生成 cookie

用户登录成功，但没有生成 cookie ，通过`F12`查看请求响应结果，响应结果中`setCookie`的`domain`为`localhost`，而不是虚拟机的IP，由于在 `CookieUtils.java` 中设置Cookie默认存储的域（domain）为  localhost（在打包前，没有修改过来），将其修改为虚拟机的IP即可，重新打包。

###### mysql 无法访问

*<font color=red>Could not get JDBC Connection; nested exception is com.mysql.cj.jdbc.exceptions.CommunicationsException: Communications link failure</font>*

mysql 无法访问，问题在 docker 的防火墙开启了，需要关闭防火墙。

- 查看防火墙状态	`firewall-cmd --state`
  如果命令报错，赋予特权 `docker run -tdi --privileged centos init`
- 停止防火墙  `systemctl stop firewalld.service`
- 禁止firewall开机启动  `systemctl disable firewalld.service`


