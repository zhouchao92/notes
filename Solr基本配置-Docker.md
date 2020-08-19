---
title: Solr基本配置-Docker
tags:
  - solr
  - docker
categories:
  - notes
  - 后端
abbrlink: 8e2b9641
date: 2020-08-18 19:02:42
---

###### 前言

docker下安装solr，以及使用中文分词器。

通过 xftp 工具将文件从宿主机拷贝到虚拟机上。

中文分词器相关资料

- 中文分词器github地址 [https://github.com/magese/ik-analyzer-solr/](https://github.com/magese/ik-analyzer-solr/)
- jar 包 [https://search.maven.org/remotecontent?filepath=com/github/magese/ik-analyzer/8.3.0/ik-analyzer-8.3.0.jar](https://search.maven.org/remotecontent?filepath=com/github/magese/ik-analyzer/8.3.0/ik-analyzer-8.3.0.jar)

<!--more-->

###### 安装docker-compose

```shell
# curl -L https://github.com/docker/compose/releases/download/1.24.1/docker-compose-`uname -s`-`uname -m` > /usr/local/bin/docker-compose 

# chmod +x /usr/local/bin/docker-compose

# docker-compose --version 
```

###### 安装Solr镜像

<font color="red">配置 docker group，不需要在 docker 命令前添加 </font>`sudo` 

从 docker 的仓库中拉取 solr 镜像

```shell
$ docker pull solr[:版本]  # 不填写版本号时，默认为最新版本
Using default tag: latest
```

检查镜像是否安装成功

`$ docker images`显示已安装的的所有镜像

```shell
$ docker images
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
solr                latest              8e621ac5ebe9        17 hours ago        514MB
```

###### 启动Solr容器

`docker run --name solr --restart=always -d -p 8983:8983 -t solr[:版本号]`

```shell
$ docker run --name solr --restart=always -d -p 8983:8983 -t solr
7be597b3079b626a0732c8005535bc8b96504fcc18761699ef263c80c68be325
```

###### 访问

http://虚拟机IP地址:8983/solr/#/

> http://192.168.56.10:8983/solr/#/

![docker-solr的访问](https://gitee.com/lao-biao/Pictures/raw/master/Solr/docker-solr%E7%9A%84%E8%AE%BF%E9%97%AE.png)

###### 新建solr core

1. 直接通过命令创建

   ```shell
   $ docker exec -it --user=solr solr bin/solr create_core -c  my_core
   ```

   - `-it`  展示容器输入信息`STDIN`，命令行交互模式
   - `--user` 用户，使用 root 用户会有报错
   - my_core core 名称，自定义

2. 进入容器内部，拷贝默认配置文件到 core 中，再通过客户端创建 core

   ```shell
   $ docker exec -it --user=root solr /bin/bash	# 以管理员身份进入 solr 容器内部
   
   # cp -r server/solr/configsets/_default/conf /var/solr/data/my_core  # 拷贝默认配置文件到 my_core 中
   # exit # 退出 solr 容器
   
   $ docker restart solr # 重启 solr 容器
   ```

   ![solr客户端创建core](https://gitee.com/lao-biao/Pictures/raw/master/Solr/solr%E5%AE%A2%E6%88%B7%E7%AB%AF%E5%88%9B%E5%BB%BAcore.png)

访问 my_core

![docker-my_core属性](https://gitee.com/lao-biao/Pictures/raw/master/Solr/docker-my_core%E5%B1%9E%E6%80%A7.png)

###### 配置中文解析器

1. 将中文解析器相关 jar 包拷贝到容器内

   ```shell
   $ docker cp ik-analyzer-8.3.0.jar solr:/opt/solr-8.6.1/server/solr-webapp/webapp/WEB-INF/lib
   ```

2. 将中文解析器`ik-analyzer-solr-8.3.0\src\main\resources`目录下的文件拷贝到容器内

   新建文件夹 classes ，将文件先从宿主机拷贝到虚拟机的 classes 文件夹下，再从虚拟机上拷贝到 solr 容器中。

   ```shell
   ① IKAnalyzer.cfg.xml
   ② ext.dic
   ③ stopword.dic
   ④ ik.conf
   ⑤ dynamicdic.txt
   ```

   ```shell
   $ docker cp classes solr:/opt/solr-8.6.1/server/solr-webapp/webapp/WEB-INF/classes
   ```

3. 配置中文解析器

   ```shell
   $ docker exec -it --user=root solr /bin/bash
   # vi /var/solr/data/my_core/conf/managed-schema
   ```

   ```xml
   <!-- ik分词器 -->
   <fieldType name="text_cn" class="solr.TextField">
       <analyzer type="index">
           <tokenizer class="org.wltea.analyzer.lucene.IKTokenizerFactory" useSmart="false" conf="ik.conf"/>
           <filter class="solr.LowerCaseFilterFactory"/>
       </analyzer>
       <analyzer type="query">
           <tokenizer class="org.wltea.analyzer.lucene.IKTokenizerFactory" useSmart="true" conf="ik.conf"/>
           <filter class="solr.LowerCaseFilterFactory"/>
       </analyzer>
   </fieldType>
   ```

4. 进入 solr 容器内部检查文件

   ```shell
   $ docker exec -it --user=root solr /bin/bash
   
   # cd /opt/solr-8.6.1/server/solr-webapp/webapp/WEB-INF/classes
   # ls
   IKAnalyzer.cfg.xml  dynamicdic.txt  ext.dic  ik.conf  stopword.dic
   
   # cd /opt/solr-8.6.1/server/solr-webapp/webapp/WEB-INF/lib
   # ls
   ik-analyzer-8.3.0.jar ...
   ```

5. 退出 solr 容器，重启 solr

   ```shell
   $ docker restart solr
   ```

###### 中文分词器

> 请求url [http://192.168.56.10:8983/solr/#/my_core/analysis?analysis.fieldvalue=%E4%BD%A0%E5%A5%BD%E4%B8%96%E7%95%8C&analysis.fieldtype=text_cn&verbose_output=1](http://192.168.56.10:8983/solr/#/my_core/analysis?analysis.fieldvalue=你好世界&analysis.fieldtype=text_cn&verbose_output=1)

![docker-solr中文分词器演示](https://gitee.com/lao-biao/Pictures/raw/master/Solr/docker-solr%E4%B8%AD%E6%96%87%E5%88%86%E8%AF%8D%E5%99%A8%E6%BC%94%E7%A4%BA.png)

###### solr挂载到虚拟机本地，实现数据持久化

在重启虚拟机后， my_core 失效，需要通过配置 solr 持久化数据。

1. 创建数据存储的文件夹 存储在 `/mydata/solr/` 下

   ```shell
   $ mkdir -p /mydata/
   ```

2.  将文件拷贝到 `mydata` 下

   ```shell
   $ docker cp solr:/opt/solr/ /mydata/ 
   ```

3. 删除旧的 solr 容器

   ```shell
   $ docker rm -f solr
   ```

4. 以挂载的形式启动 solr 容器

   - `-d` 后台运行
   - `-p` 端口映射
   - `--name` 容器名
   - `--restart=always` 自启动（docker启动时同时启动solr）
   - `-v` 挂载
   - -`t` 展示容器输入信息`STDIN`

   ```shell
   $ docker run -d -p 8983:8983 --name solr --restart=always -v /mydata/solr:/opt/solr -t solr
   ```

5. 创建 core ，core名称 my_solr_core

   ```shell
   $ docker exec -it --user=solr solr bin/solr create_core -c  my_solr_core
   ```

6. 进入 solr 容器内部，添加中文分词器

   ```shell
   $ docker exec -it --user=root solr /bin/bash
   # vi /var/solr/data/my_solr_core/conf/managed-schema
   ```

   修改的内容参考 *配置中文解析器的第三步配置中文解析器*

7. 将 core 的配置文件拷贝到挂载的目录下

   ```shell
   $ sudo docker cp solr:/var/solr/data/my_solr_core /mydata/solr/server/solr/
   ```

8. 重新启动 solr 容器

   ```shell
   $ docker restart solr
   ```

![solr挂载到本地使用中文分词器示例](https://gitee.com/lao-biao/Pictures/raw/master/Solr/solr%E6%8C%82%E8%BD%BD%E5%88%B0%E6%9C%AC%E5%9C%B0%E4%BD%BF%E7%94%A8%E4%B8%AD%E6%96%87%E5%88%86%E8%AF%8D%E5%99%A8%E7%A4%BA%E4%BE%8B.png)