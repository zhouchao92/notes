---
title: docker安装mysql镜像
tags: 'docker,mysql'
categories: 'notes,后端'
abbrlink: 1a3905c2
date: 2020-08-19 12:58:06
---


###### 安装mysql的镜像

docker仓库中mysql版本 https://hub.docker.com/_/mysql

```shell
$ sudo docker pull mysql:5.7
```

验证是否安装成功

```shell
$ sudo docker images
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
mysql               5.7                 d05c76dbbfcf        2 minutes ago         448MB
```

<!--more-->

###### 配置mysql容器

```shell
$ docker run -p 3306:3306 --name mysql \
-v /mydata/mysql/log:/var/log/mysql \
-v /mydata/mysql/data:/var/lib/mysql \
-v /mydata/mysql/conf:/etc/mysql \
-e MYSQL_ROOT_PASSWORD=root \
-d mysql:5.7
```

参数介绍

- `-p 3306:3306` 将容器的3306端口映射到主机的3306端口
- `-v /mydata/mysql/log:/var/log/mysql` 将日志文件挂载到主机
- `-v /mydata/mysql/data:/var/lib/mysql` 将数据文件挂载到主机
- `-v /mydata/mysql/conf:/etc/mysql` 将配置文件夹挂载到主机
- `-e MYSQL_ROOT_PASSWORD=root` 初始化root用户的密码
- `-d mysql:5.7` 后台运行mysql 5.7

###### 进入mysql的容器内部

```shell
[root@localhost vagrant]# docker exec -it mysql /bin/bash
```

查看mysql容器结构

```shell
[root@localhost vagrant]# docker exec -it mysql /bin/bash

root@8177ea9826e4:/# ls /
bin   dev                         entrypoint.sh  home  lib64  mnt  proc  run   srv  tmp  var
boot  docker-entrypoint-initdb.d  etc            lib   media  opt  root  sbin  sys  usr

root@8177ea9826e4:/# whereis mysql
mysql: /usr/bin/mysql /usr/lib/mysql /etc/mysql /usr/share/mysql
```

###### 修改字符编码

1. 修改配置文件

   ```shell
   [root@localhost conf]# vi /mydata/mysql/conf/my.cnf
   ```

   ```ini
   [client]
   default-character-set=utf8
   
   [mysql]
   default-character-set=utf8
   
   [mysqld]
   default-time-zone='+08:00'
   init_connect='SET collation_connection=utf8_unicode_ci'
   init_connect='SET NAMES utf8'
   character-set-server=utf8
   collation-server=utf8_unicode_ci
   skip-character-set-client-handshake
   skip-name-resolve
   ```

2. 查看docker当前运行的容器

   ```shell
   [root@localhost conf]# docker ps
   CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS                               NAMES
   8177ea9826e4        mysql:5.7           "docker-entrypoint.s…"   22 minutes ago      Up 22 minutes       0.0.0.0:3306->3306/tcp, 33060/tcp   mysql
   ```

3. 重新启动mysql

   ```shell
   [root@localhost conf]# docker restart mysql
   mysql
   ```

4. 对插入的内容进行确认

   ```shell
   [root@localhost conf]# docker exec -it mysql /bin/bash
   root@8177ea9826e4:/# cd etc/mysql/
   root@8177ea9826e4:/etc/mysql# ls
   my.cnf
   root@8177ea9826e4:/etc/mysql# cat my.cnf
   [client]
   default-character-set=utf8
   
   [mysql]
   default-character-set=utf8
   
   [mysqld]
   init_connect='SET collation_connection = utf8_unicode_ci'
   init_connect='SET NAMES utf8'
   character-set-server=utf8
   collation-server=utf8_unicode_ci
   skip-character-set-client-handshake
   skip-name-resolve
   ```

###### mysql修改默认时区

如果忘记配置默认时区，会使用 UTC 作为默认时区，与中国采用的时间相差8小时，中国使用 `UTC+8` 。

1. 修改mysql的配置映射文件

   ```shell
   vi /mydata/mysql/conf/my.cnf
   ```

2. 在 [msqld] 后面添加内容

   ```ini
   default-time-zone='+08:00'
   ```

   

