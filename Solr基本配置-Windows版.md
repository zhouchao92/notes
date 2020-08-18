---
title: Solr基本配置-Windows版
tags:
  - solr
categories:
  - notes
  - 后端
abbrlink: e0b21c40
date: 2020-08-18 10:32:26
---

#### Solr

Solr下载地址 [https://lucene.apache.org/solr/downloads.html](https://lucene.apache.org/solr/downloads.html)

[可选项]配置环境变量：将Solr添加到系统环境变量中，在系统环境变量的path下添加 `安装目录\Solr\bin`

##### Solr目录结构

![Solr目录结构](https://gitee.com/zyy92/Pictures/raw/master/Solr/solr目录结构.png)

<!--more-->

##### 基本命令

在 Solr 的 bin 目录下打开cmd窗口，若配置了solr的环境变量，可在任意位置打开cmd窗口。

###### 启动 

`solr start` 默认启动的端口号为 8983

```shell
> cd bin  # 已配置环境变量可省略
> solr start
Java HotSpot(TM) 64-Bit Server VM warning: JVM cannot use large page memory because it does not have enough privilege to lock pages in memory.
Waiting up to 30 to see Solr running on port 8983
Started Solr server on port 8983. Happy searching!
```

###### 指定启动端口号 

`solr start-p 端口号`

```shell
> solr start -p 9000
Java HotSpot(TM) 64-Bit Server VM warning: JVM cannot use large page memory because it does not have enough privilege to lock pages in memory.
Waiting up to 30 to see Solr running on port 9000
Started Solr server on port 9000. Happy searching!
```

###### 停止 

`solr stop -p 启动的端口号`   

```shell
> solr stop -p 8983
Stopping Solr process 1520 running on port 8983

等待 5 秒，按一个键继续 ...
```

###### 重启

`solr restart -p 启动的端口号`

```shell
> solr restart -p 8983
Stopping Solr process 5736 running on port 8983

等待 5 秒，按一个键继续 ...
```

###### 访问

访问地址：http://localhost:启动的端口号/solr/#

默认：http://localhost:8983/solr/#

##### 配置

solr的关系映射

- solr为库
- core为表
- filed为字段

###### 创建核心

 `solr create -c example_core`，会自动添加默认配置文件config、data和core.properties

```shell
> solr create -c example_core
WARNING: Using _default configset with data driven schema functionality. NOT RECOMMENDED for production use.
         To turn off: bin\solr config -c example_core -p 8983 -action set-user-property -property update.autoCreateFields -value false

Created new core 'example_core'
```

在`Solr\server\`目录下会新增文件夹`example_core`。

###### 访问

访问地址：http://localhost:8983/solr/#/example_core/core-overview

![solr侧栏工具](https://gitee.com/zyy92/Pictures/raw/master/Solr/solr%E4%BE%A7%E6%A0%8F%E5%B7%A5%E5%85%B7.png)

###### 添加中文分词器

> 中文分词器github地址 [https://github.com/magese/ik-analyzer-solr/](https://github.com/magese/ik-analyzer-solr/)

1. 添加配置文件：
   下载 https://github.com/magese/ik-analyzer-solr/releases/tag/v8.3.0

   ![中文分词器资源文件](https://gitee.com/zyy92/Pictures/raw/master/Solr/%E4%B8%AD%E6%96%87%E5%88%86%E8%AF%8D%E5%99%A8%E8%B5%84%E6%BA%90%E6%96%87%E4%BB%B6.png)将`ik-analyzer-solr-8.3.0\src\main\resources`路径下的5个配置文件拷贝到`Solr\server\solr-webapp\webapp\WEB-INF\classes`目录下

   ```shell
   ① IKAnalyzer.cfg.xml
   ② ext.dic
   ③ stopword.dic
   ④ ik.conf
   ⑤ dynamicdic.txt
   ```

2. jar包支持：

   下载 [https://search.maven.org/remotecontent?filepath=com/github/magese/ik-analyzer/8.3.0/ik-analyzer-8.3.0.jar](https://search.maven.org/remotecontent?filepath=com/github/magese/ik-analyzer/8.3.0/ik-analyzer-8.3.0.jar)

   将jar包拷贝到 `Solr\server\solr-webapp\webapp\WEB-INF\lib`目录下

3. 配置Solr的`managed-schema`，添加`ik分词器`

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

##### 使用中文分词器分析

在添加中文分词器后，重启Solr，访问 example_core 下的 analysis

> 访问地址：http://localhost:8983/solr/#/example_core/analysis

Analyse Fieldname / FieldType：`text_cn`	（配置的中文分词器）

Field Value ：Solr的中文分词器

分析结果

![中文分词器示例](https://gitee.com/zyy92/Pictures/raw/master/Solr/%E4%B8%AD%E6%96%87%E5%88%86%E8%AF%8D%E5%99%A8%E7%A4%BA%E4%BE%8B.png)

