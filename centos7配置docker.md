---
title: centos7配置docker
tags: 'centos7,docker'
categories: 'notes,后端'
abbrlink: 55112ca0
date: 2020-08-19 12:28:28
---


###### docker的安装

建议根据官方文档的步骤进行安装，不然容易出错。

> 官方文档：https://docs.docker.com/engine/install/centos/

<!--more-->


###### docker的启动

```shell
$ sudo systemctl start docker
$ sudo docker images  # 查看启动的镜像
```

###### 设置docker开机自启

```shell
$ systemctl enable docker  
```

###### 配置阿里云镜像加速

```shell
$ sudo mkdir -p /etc/docker

$ sudo tee /etc/docker/daemon.json <<-'EOF'
{
  "registry-mirrors": ["https://aruwgrgs.mirror.aliyuncs.com"]
}
EOF

# 将docker设置为后台启动
$ sudo systemctl daemon-reload

# 重新启动
$ sudo systemctl restart docker
```

###### 当无法直接删除镜像时，操作步骤：

1.  查看进程 `docker ps -a` 	
2. 移除使用的镜像的容器 `docker rm [id号]  `
3. 移除指定镜像 `docker rmi [镜像名]`

###### 将当前用户添加到docker用户组中

在当前用户没有添加到 docker 用户组时，使用 docker 命令时需要使用 `sudo`命令，将当前用户添加到docker用户组中可以直接使用 docker 命令。

1. 添加docker用户组
2. 将登录用户加入到docker用户组中
3. 更新用户组

```shell
$ sudo groupadd docker     		# 添加docker用户组
$ sudo gpasswd -a $USER docker    # 将登录用户加入到docker用户组中
$ newgrp docker     				# 更新用户组
$ docker ps    					# 测试docker命令是否可以使用sudo正常使用
```

###### 