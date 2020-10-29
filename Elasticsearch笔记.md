---
title: Elasticsearch笔记
tags:
  - elasticsearch
categories:
  - 后端
abbrlink: e97d3f23
date: 2020-09-23 19:35:31
---


##### 简介

Elasticsearch 是一个分布式的开源搜索和分析引擎，适用于所有类型的数据，包括文本、数字、地理空间、结构化和非结构化数据。Elasticsearch 在 Apache Lucene 的基础上开发而成，由 Elasticsearch N.V.（即现在的 Elastic）于2010首次发布。Elasticsearch 以其简单的REST风格API、分布式特性、速度和可扩展性而闻名，是 Elastic Stack 的核心组件；Elastic Stack 是适用于数据采集、充实、存储、分析和可视化的一组开源工具。人们通常将 Elastic Stack 称为 ELK Stack（代指Elastic Search、Logstash 和 Kibana），目前 Elastic Stack 包括一系列丰富的轻量级数据采集代理，这些代理统称为Beats，可用来向 Elasticsearch 发送数据。

<!--more-->

###### 用途

- 应用程序搜索
- 网站搜索
- 企业搜索
- 日志处理和分析
- 基础设施指标和容器监测
- 应用程序性能监测
- 地理空间数据分析和可视化
- 安全分析
- 业务分析

###### 工作原理

原始数据会从从多个来源（包括日志、系统指标和网络应用程序）输入到 Elasticsearch 中，数据采集指在 ElastIcsearch 中进行索引之前解析、标准化并充实这些原始数据的过程，这些数据在 ElasticSearch 中索引完成之后，用户便可针对数据运行复杂的查询，并使用聚合来检索自身数据的复杂汇总，在 Kibana 中，用户可以基于自己的数据创建强大的可视化，分享仪表板，并对 Elastic Stack 进行管理。

###### Logstash 的用途

Logstash 是 Elastic Stack 的核心产品之一，可用来对数据进行聚合和处理，并将数据发送到 Elasticsearch。Logstash 是一个开源的服务器端数据处理管道，允许您在将数据索引到 Elasticsearch 之前同时从多个来源采集数据，并对数据进行充实和转换。

###### Kibana 的用途

Kibana 是一款适用于 Elasticsearch 的数据可视化和管理工具，可以提供实时的直方图、线形图、饼状图和地图。Kibana 同时还包括诸如 Canvas 和 Elastic Maps 等高级应用程序；Canvas 允许用户基于自身数据创建定制的动态信息图表，而 Elastic Maps 则可用来对地理空间数据进行可视化。

###### 文档

> 官方文档：https://www.elastic.co/guide/en/elasticsearch/reference/current/index.html

##### 基本概念

Elasticsearch *索引*指相互关联的文档集合。Elasticsearch 会以 JSON 文档的形式存储数据。每个文档都会在一组*键*（字段或属性的名称）和它们对应的值（字符串、数字、布尔值、日期、*数值*组、地理位置或其他类型的数据）之间建立联系。

Elasticsearch 使用的是一种名为*倒排索引*的数据结构，这一结构的设计可以允许十分快速地进行全文本搜索。倒排索引会列出在所有文档中出现的每个特有词汇，并且可以找到包含每个词汇的全部文档。

在索引过程中，Elasticsearch 会存储文档并构建倒排索引，这样用户便可以近实时地对文档数据进行搜索。索引过程是在索引 API 中启动的，通过此 API 您既可向特定索引中添加 JSON 文档，也可更改特定索引中的 JSON 文档。

###### 1、Index（索引）

动词，相当于 MySQL 中的 insert；

名词，相当于 MySQL 中的 Database。

###### 2、Type（类型）

在 Index（索引）中，可以定一个或多个类型。
类似MySQL中的 Table；每一种类型的数据放在一起。

###### 3、Document（文档）

保存在某个索引（Index）下，某种类型（Type）的一个数据（Document），文档是 JSON 格式的，Document就像是 MySQL中的某个 Table 里的内容。

###### 4、倒排索引机制

分词-记录
检索-根据相关性得分，倒排。

##### 安装

###### 1、下载镜像文件

`docker pull elasticsearch:7.4.2`  存储和检索数据

`docker pull kibana:7.4.2`  可视化检索数据

###### 2、创建实例

1. Elasticsearch

   ```shell
   mkdir -p /mydata/elasticsearch/config
   mkdir -p /mydata/elasticsearch/data
   echo "http.host: 0.0.0.0" >> /mydata/elasticsearch/config/elasticsearch.yml
   
   docker run --name elasticsearch -p 9200:9200 -p 9300:9300 \
   -e "discovery.type=single-node" \
   -e ES_JAVA_OPTS="-Xms64m -Xmx512m" \
   -v /mydata/elasticsearch/config/elasticsearch.yml:/usr/share/elasticsearch/config/elasticsearch.yml \
   -v /mydata/elasticsearch/data:/usr/share/elasticsearch/data \
   -v /mydata/elasticsearch/plugins:/usr/share/elasticsearch/plugins \
   -d elasticsearch:7.4.2
   ```

   权限不足

   ```shell
   [root@localhost data]# docker logs elasticsearch
   OpenJDK 64-Bit Server VM warning: Option UseConcMarkSweepGC was deprecated in version 9.0 and will likely be removed in a future release.
   {"type": "server", "timestamp": "2020-09-17T05:35:06,988Z", "level": "WARN", "component": "o.e.b.ElasticsearchUncaughtExceptionHandler", "cluster.name": "elasticsearch", "node.name": "3523576d272f", "message": "uncaught exception in thread [main]", 
   "stacktrace": ["org.elasticsearch.bootstrap.StartupException: ElasticsearchException[failed to bind service]; nested: AccessDeniedException[/usr/share/elasticsearch/data/nodes];",
   "at org.elasticsearch.bootstrap.Elasticsearch.init(Elasticsearch.java:163) ~[elasticsearch-7.4.2.jar:7.4.2]",
   "at org.elasticsearch.bootstrap.Elasticsearch.execute(Elasticsearch.java:150) ~[elasticsearch-7.4.2.jar:7.4.2]",
   "at org.elasticsearch.cli.EnvironmentAwareCommand.execute(EnvironmentAwareCommand.java:86) ~[elasticsearch-7.4.2.jar:7.4.2]",
   "at org.elasticsearch.cli.Command.mainWithoutErrorHandling(Command.java:125) ~[elasticsearch-cli-7.4.2.jar:7.4.2]",
   "at org.elasticsearch.cli.Command.main(Command.java:90) ~[elasticsearch-cli-7.4.2.jar:7.4.2]",
   "at org.elasticsearch.bootstrap.Elasticsearch.main(Elasticsearch.java:115) ~[elasticsearch-7.4.2.jar:7.4.2]",
   "at org.elasticsearch.bootstrap.Elasticsearch.main(Elasticsearch.java:92) ~[elasticsearch-7.4.2.jar:7.4.2]",
   "Caused by: org.elasticsearch.ElasticsearchException: failed to bind service",
   "at org.elasticsearch.node.Node.<init>(Node.java:614) ~[elasticsearch-7.4.2.jar:7.4.2]",
   "at org.elasticsearch.node.Node.<init>(Node.java:255) ~[elasticsearch-7.4.2.jar:7.4.2]",
   "at org.elasticsearch.bootstrap.Bootstrap$5.<init>(Bootstrap.java:221) ~[elasticsearch-7.4.2.jar:7.4.2]",
   "at org.elasticsearch.bootstrap.Bootstrap.setup(Bootstrap.java:221) ~[elasticsearch-7.4.2.jar:7.4.2]",
   "at org.elasticsearch.bootstrap.Bootstrap.init(Bootstrap.java:349) ~[elasticsearch-7.4.2.jar:7.4.2]",
   "at org.elasticsearch.bootstrap.Elasticsearch.init(Elasticsearch.java:159) ~[elasticsearch-7.4.2.jar:7.4.2]",
   "... 6 more",
   "Caused by: java.nio.file.AccessDeniedException: /usr/share/elasticsearch/data/nodes",
   "at sun.nio.fs.UnixException.translateToIOException(UnixException.java:90) ~[?:?]",
   "at sun.nio.fs.UnixException.rethrowAsIOException(UnixException.java:111) ~[?:?]",
   "at sun.nio.fs.UnixException.rethrowAsIOException(UnixException.java:116) ~[?:?]",
   "at sun.nio.fs.UnixFileSystemProvider.createDirectory(UnixFileSystemProvider.java:389) ~[?:?]",
   "at java.nio.file.Files.createDirectory(Files.java:693) ~[?:?]",
   "at java.nio.file.Files.createAndCheckIsDirectory(Files.java:800) ~[?:?]",
   "at java.nio.file.Files.createDirectories(Files.java:786) ~[?:?]",
   "at org.elasticsearch.env.NodeEnvironment.lambda$new$0(NodeEnvironment.java:272) ~[elasticsearch-7.4.2.jar:7.4.2]",
   "at org.elasticsearch.env.NodeEnvironment$NodeLock.<init>(NodeEnvironment.java:209) ~[elasticsearch-7.4.2.jar:7.4.2]",
   "at org.elasticsearch.env.NodeEnvironment.<init>(NodeEnvironment.java:269) ~[elasticsearch-7.4.2.jar:7.4.2]",
   "at org.elasticsearch.node.Node.<init>(Node.java:275) ~[elasticsearch-7.4.2.jar:7.4.2]",
   "at org.elasticsearch.node.Node.<init>(Node.java:255) ~[elasticsearch-7.4.2.jar:7.4.2]",
   "at org.elasticsearch.bootstrap.Bootstrap$5.<init>(Bootstrap.java:221) ~[elasticsearch-7.4.2.jar:7.4.2]",
   "at org.elasticsearch.bootstrap.Bootstrap.setup(Bootstrap.java:221) ~[elasticsearch-7.4.2.jar:7.4.2]",
   "at org.elasticsearch.bootstrap.Bootstrap.init(Bootstrap.java:349) ~[elasticsearch-7.4.2.jar:7.4.2]",
   "at org.elasticsearch.bootstrap.Elasticsearch.init(Elasticsearch.java:159) ~[elasticsearch-7.4.2.jar:7.4.2]",
   "... 6 more"] }
   ```

   ```shell
   chmod -R 777 /mydata/elasticsearch/
   ```

2. Kibana

   ```shell
   docker run --name kibana \
   -e ELASTICSEARCH_HOSTS=http://192.168.134.128:9200 \
   -p 5601:5601 \
   -d kibana:7.4.2
   ```

##### 初步检索

###### 1、_cat

| 请求方式 |    请求路径     |               作用                |
| :------: | :-------------: | :-------------------------------: |
|  `GET`   |  `/_cat/nodes`  |           查看所有节点            |
|  `GET`   | `/_cat/health`  |          查看es健康状况           |
|  `GET`   | `/_cat/master`  |            查看主节点             |
|  `GET`   | `/_cat/indices` | 查看所有索引（`show databases;`） |

![查看节点信息](https://gitee.com/lao-biao/Pictures/raw/master/elasticsearch/%E8%8A%82%E7%82%B9%E4%BF%A1%E6%81%AF.png)

###### 2、索引一个文档（保存）

保存一个数据，保存在哪个索引的哪个类型下，指定用哪个唯一标识

`PUT` customer/external/1; 在 cutomer 索引下的external 类型下保存1号数据

```json
{
    "name":"Ming"
}
```

`PUT` 和 `POST`都可以
`POST`新增/修改，如果不指定id，会自动生成id，指定id就会修改这个数据，并新增版本号。

`PUT`可以新增/修改，`PUT`必须指定id，由于`PUT`需要指定id，一般用来做修改操作，不指定id会报错。
![添加索引](https://gitee.com/lao-biao/Pictures/raw/master/elasticsearch/%E6%B7%BB%E5%8A%A0%E7%B4%A2%E5%BC%95.png)

###### 3、查询文档

> `GET` customer/external/1

```json
{
    "_index": "customer",
    "_type": "external",
    "_id": "1",
    "_version": 2,
    "_seq_no": 1,
    "_primary_term": 1,
    "found": true,
    "_source": {
        "name": "John"
    }
}
```

> `"_seq_no": 1` 并发控制字段，每次更新就会+1，用来做乐观锁。
> `"_primary_term": 1` 并发控制字段，主分片重新分配，如重启，就会变化。

乐观锁修改：请求携带 `?if_seq_no=1&if_primary_term=1`修改指定的版本的数据
如果版本不符合则会返回`409`，保证数据的唯一性。

###### 4、更新文档

> `POST` /customer/external/1/_update
>
> ```json
> {
>        "doc":{
>            "name":"Ming"
>        }
> }
> ```

多次提交同一份数据，会对比元数据，数据一致时，版本号不会叠加，操作结果为 **noop**，序列号也不会改变。

> `POST` /customer/external/1
>
> ```json
> {
>        "name":"Ming"
> }
> ```
>

不会检查元数据，版本叠加、序列号叠加。

> `PUT` /customer/external/1
>
> ```json
> {
>        "name":"Ming"
> }
> ```

不会检查元数据，版本叠加、序列号叠加。

**不同**：POST操作会对比文档数据，如果相同不会有什么操作，文档 version 不增加，PUT操作总会将数据重新保存并增加 version 版本。带_update对比元数据如果一样就不进行任何操作。

**注意**：

- 对于大并发更新，不带update；
- 对于大并发查询偶尔更新，带update；对比更新，重新计算分配规则。

需求：更新同时增加属性

1. `PUT`/`POST` /customer/external/1

2. `POST` /customer/external/1/_update

   ```json
   {
       "doc":{
           "name":"Ming",
           "age":18
       }
   }
   ```

###### 5、删除文档&索引

| 请求方式 |        请求路径        |   作用   |
| :------: | :--------------------: | :------: |
| `DELETE` | `/customer/external/1` | 删除文档 |
| `DELETE` |      `/customer`       | 删除索引 |

###### 6、bulk批量 API

> `POST` /customer/external/_bulk
>
> ```json
> {"index":{"_id":"1"}}
> {"name":"Ming"}
> {"index":{"_id":"2"}}
> {"name":"Jhon"}
> ```
>
> 语法格式
> {action:{metadata}}\n
> {request body}\n
> {action:{metadata}}\n
> {request body}\n

其他实例

> POST /_bluk
>
> ```json
> {"delete":{"_index":"website","_type":"blog","_id":"123"}}
> {"create":{"_index":"website","_type":"blog","_id":"123"}}
> {"title":"My first blog post"}
> {"index":{"_index":"website","_type":"blog"}}
> {"title":"My second blog post"}
> {"update":{"_index":"website","_type":"blog","_id":"123"}}
> {"doc":{"title":"My updated blog post"}}
> ```

bulk API，以此按顺序执行所有的 action（动作），如果一个单个的动作因任何原因而失败，它将继续处理它后面剩余的动作。当 bulk API 返回时，它将提供每个动作的状态（与发送的顺序相同），所以可以检查是否一个指定的动作是不是失败了。

###### 7、样本测试数据

> 官方样本数据 https://github.com/elastic/elasticsearch/edit/master/docs/src/test/resources/accounts.json

```json
{
	"account_number":0,
    "balance":16623,
    "firstname":"Bradshaw",
    "lastname":"Mickenzie",
    "age":18,
    "gender":"F",
    "address":"244 Columbus Place",
    "employer":"Eurron",
    "email":"bradshawmickezie@euron.com",
    "city":"Hobucken",
    "state":"CO"
}
...
```

> `POST` /bank/account/_bulk

##### 进阶检索

###### 1、SearchAPI

ES 支持两种基本方式检索：

- 一个是通过使用 REST request URI 发送搜索参数（uri+检索参数）
- 另一个是通过使用 REST request body 来发送（uri+请求体）

检索信息

<table>
    <tr>
        <td><code>GET</code> /bank/_search</td>
        <td>检索bank下所有信息，包括type和docs</td>
    </tr>    
    <tr>
        <td><code>GET</code> /bank/_search?q=*&sort=account_number:asc</td>
        <td>请求参数方式检索</td>
    </tr>
    <tr>
        <td colspan="2">
            响应结果解释：<br>
            took-Elasticsearch执行搜索的时间（毫秒）<br>
            time-out 搜索是否超时<br>
            _shards 搜索了多少个分片，以及统计了成功/失败的搜索分片<br>
            hits 搜索结果<br>
            hits.total 搜索结果<br>
            hits.hits 实际的搜索结果数组（默认为前10的文档）<br>
            sort 结果的排序key（键），没有则按score排序<br>
            score和max_score 相关性得分和最高得分（全文检索用）<br>
        </td>
    </tr>
</table>

> `GET` /bank/_search

```json
{
  "query": {
    "match_all": {}
  },
  "sort": [
    {
      "account_number": "asc",
      "balance": {
        "order": "desc"
      }
    }
  ],
  "from": 0,
  "size": 5
}
```

HTTP客户端工具（postman），get 请求不能携带请求体，变为post也是一样的。POST 一个 JSON 风格的查询请求体到 _search API。

一旦搜索的结果被返回，Elasticsearch 就完成了这次请求，并且不会维护任何服务端的资源或者结果的 cusor。

###### 2、Query DSL

**基本语法**

Elasticsearch 提供了一个可以执行查询的 JSON 风格的 DSL（domain-specific-language，领域特定语言）。这个被称为 Query DSL，该查询语言非常全面。

- 一个查询语句的典型结构

  ```json
  {
      QUERY_NAME:{
          ARGUMENT:VALUE,
          ARGUMENT:VALUE...
      }
  }
  ```

- 如果针对某个字段，结构为

  ```json
  {
  	QUERY_NAME:{
  		FIELD_NAME:{
              ARGUMENT:VALUE,
              ARGUMENT:VALUE...
          }
      }
  }
  ```

query定义如何查询查询：

- match_all 查询类型（代表查询所有的所有），es中可以在query中组合非常多的查询类型完成复杂查询。
- 除了query参数之外，可以传递其它的参数以改变查询结果。如 sort，size，from+size 限定，完成分页功能。
- sort 排序，多字段排序，会在前序字段相等时按后续字段内部排序，否则以前序为准。

**返回部分字段**

```json
{"_source": ["firstname","balance"]}
```

单个字段使用 `{filed}`，多个字段用 `[filed,filed...]

**match 匹配查询**

match 当搜索字符串类型的时候，会进行全文检索，并且每条记录都有相关性得分。

> `GET` /bank/_search

```json
# 查询出 address 中包含 mill、lane 或者 mill lane 的所有记录，并给出相关性得分。
{"query": {"match": {"address": "mill lane"}}}
```

全文检索按照评分进行排序，会对检索条件进行分词匹配。

**match_phrase 短语匹配**

把需要匹配的值当成一整个单词（不分词）进行检索。使用 match + FILED.keyword 可以实现同种效果。

```json
{"query":{"match_phrase":{"address":"mill lane"}}}
```

**multi_match 多字段匹配**

```json
# city,address 中包含mill
{"query":{"multi_match":{"query":"mill","fields":["city","address"]}}}
```

**bool 复合查询**

复合语句可以合并任何其它查询语句，包括复合语句，复合语句之间可以互相嵌套。

```json
{
  "query": {
    "bool": {
      "must": [
        { "match": { "age": "40" } }
      ],
      "must_not": [
        { "match": { "state": "ID" } }
      ],
      "should": [
        {"match": {
          "lastname": "walton"
        }}
      ]
    }
  }
}
```

- must 必须满足
- must_not 必须不满足
- should 满足或不满足都可以，满足的匹配得分更高。

**filter**

并不是搜索的查询都需要产生分数，特别是那些仅用于”*filtering*“ 过滤的文档，为了不计算分数 Elasticsearch 会自动检查场景并且优化查询的执行。

```json
{
  "query": {
    "bool": {
      "must": { "match_all": {} },
      "filter": {
        "range": {
          "balance": {
            "gte": 20000,
            "lte": 30000
          }
        }
      }
    }
  }
}
```

**term**

和 match 一样，匹配某个属性的值。全文检索字段用 match，其他非 `text` 字段匹配用 `term`。

避免`term`对`text`字段使用查询。

默认情况下，Elasticsearch更改`text`字段的值作为analysis的一部分。这会使查找text字段值的精确匹配变得困难。

要搜索`text`字段值，改用`match`查询。

```json
{"query":{"term":{"age":28}}}
```

**aggregations（执行聚合）**

聚合提供了从数据中分组和提取数据的能力，最简单的聚合方法大致等于 SQL GROUP BY 和 SQL 聚合函数。在 Elasticsearch 中，有执行搜索返回 hits（命中结果），并且同时返回聚合结果，把一个响应中的所有 hits（命中结果）分隔开的能力。这是非常强大有效的，可以执行查询和多个聚合，并且在一次使用中得到各自的（任何一个的）返回结果，使用一次简洁和简化的 API 来避免网络往返。

```json
"aggregations" : {
    "<aggregation_name>" : {
        "<aggregation_type>" : {
            <aggregation_body>
        }
        [,"meta" : {  [<meta_data_body>] } ]?
        [,"aggregations" : { [<sub_aggregation>]+ } ]?
    }
    [,"<aggregation_name_2>" : { ... } ]*
}
```

```json
# 搜索 address 中包含 mill 的所有人的年龄分布以及平均年龄，但不显示这些人的详情。
{
  "query": {
    "match": {
      "address": "mill"
    }
  },
  "aggs": {
    "group_by_age": {
      "terms": {
        "field": "age",
        "size": 10
      }
    },
    "age_avg": {
      "avg": {
        "field": "age"
      }
    }
  }
}
```

子聚合

```json
# 按照年龄聚合，并且请求这些年龄段的人的平均薪资
{
  "query": {
    "match_all": {}
  },
  "size": 0,
  "aggs": {
    "group_by_age": {
      "terms": {
        "field": "age",
        "size": 10
      },
      "aggs": {
        "balance_avg": {
          "avg": {
            "field": "balance"
          }
        }
      }
    }
  }
}
```

嵌套聚合

```json
# 查出所有年龄分布，并且这些年龄段中的M的平均薪资和F的平均薪资以及这个年龄段的总体平均薪资
{
  "query": {
    "match_all": {}
  },
  "size": 0,
  "aggs": {
    "age_agg": {
      "terms": {
        "field": "age",
        "size": 100
      },
      "aggs": {
        "gender_agg": {
          "terms": {
            "field": "gender.keyword"
          },
          "aggs": {
            "gender_balance_agg": {
              "avg": {
                "field": "balance"
              }
            }
          }
        },
        "balance_agg": {
          "avg": {
            "field": "balance"
          }
        }
      }
    }
  }
}
```

###### Mapping

映射是定义文档及其包含的字段的存储和索引方式的过程。

- 哪些字符串字段应视为全文字段。
- 哪些字段包含数字，日期或地理位置坐标。
- 日期值的格式。
- 自定义规则，用于控制动态添加字段的映射。

映射定义具有：

- 元数据字段

  元数据字段用于自定义如何处理文档的关联元数据。元数据字段的例子包括文档 `_index`，`_id`和 `_source`领域。

- 领域

  映射包含properties与文档相关的字段列表。每个字段都有其自己的数据类型。

<table>
    <th>
    <td>类型</td>
    <td>解释</td>
    </th>
<tr>
    <td rowspan="5">核心类型</td>
    <td>字符串（String）</td>
    <td>text,keyword</td>
</tr>
<tr>
    <td>数字类型（Numeric）</td>
    <td>long,integer,short,byte,double,float,half_float,scaled_float</td>
</tr>
<tr>
    <td>日期类型（Date）</td>
    <td>date</td>
</tr>
<tr>
    <td>布尔类型（Boolean）</td>
    <td>boolean</td>
</tr>
<tr>
    <td>二进制类型（Binary）</td>
    <td>binary</td>
</tr>
<tr>
    <td rowspan="3">聚合类型</td>
    <td>数组类型（String）</td>
    <td>Array支持不针对特定的类型</td>
</tr>
<tr>
    <td>对象类型（Object）</td>
    <td>object用于单JSON对象</td>
</tr>
<tr>
    <td>兼容类型（Nested）</td>
    <td>Nested用于JSON对象数组</td>
</tr>
<tr>
    <td rowspan="2">地理类型（Geo）</td>
    <td>地理坐标（Geo-Points）</td>
    <td>geo_point用于描述经纬度坐标</td>
</tr>
<tr>
    <td>地理坐标（Geo-Shape）</td>
    <td>geo_shape用于描述复杂形状，如多边形</td>
</tr>
<tr>
    <td rowspan="5">特定类型</td>
    <td>IP类型</td>
    <td>ip用于描述ipv4和ipv6</td>
</tr>
<tr>
    <td>补全类型（Completion）</td>
    <td>completion提供自动完成提示</td>
</tr>
<tr>
    <td>令牌计数类型（Token Count）</td>
    <td>token_count用于统计字符串中的词条数量</td>
</tr>
<tr>
    <td>附件类型（Attachment）</td>
    <td>mapper-attachment插件，支持附件如Microsoft Office格式，Open Document格式，ePub，HTML索引为attachment数据类型</td>
</tr>
<tr>
    <td>抽取类型（Percolator）</td>
    <td>接受特定领域查询语言（query-dsl）的查询</td>
</tr>
</table>
**（1）Elasticsearch7去掉type概念**

关系型数据库中两个数据表示是独立的，即使它们里面有相同名称的列也不影响使用，但ES中不是这样的。Elasticsearch是基于Lucene开发的搜索引擎，而ES中不同type下名称相同的filed最终在Lucene中的处理方式是一样的。

- 两个不同type下的两个user_name，在ES同一个索引下其实被认同为是同一个filed，必须在两个不同的type中定义相同的filed映射。否则，不同type中的相同字段名称就会在处理中出现冲突的情况，导致Lucene处理效率下降。
- 去掉type就是为了提高ES处理数据的效率。

Elasticsearch 7.x URL中的type参数为可选，比如，索引一个文档不再要求提供文档类型。

Elasticsearcn 8.x 不在支持URL中的type参数。

解决：将索引从多类型迁移到单类型，每种类型文档一个独立索引。

**（2）mapping相关操作**

- 查看mapping信息：`GET /bank/mapping`

- 修改mapping信息：（自动猜测的映射类型）

  | JSON类型             | 域类型  |
  | -------------------- | ------- |
  | 布尔型 true \| false | boolean |
  | 整数                 | long    |
  | 浮点数               | double  |
  | 字符串，有效日期     | date    |
  | 字符串               | string  |

  > `PUT` /my_index

  ```json
  # 自定义索引并指定数据类型
  {
    "mappings": {
      "properties": {
        "age":{"type": "integer"},
        "email":{"type": "keyword"},
        "name":{"type": "text"}
      }
    }
  }
  ```

- 添加新的字段映射

  > PUT /my_index/mapping

  ```json
  # 新增新的字段
  {
    "properties": {
      "employee_id": {
        "type": "long",
        "index": false
      }
    }
  }
  ```

- 更新映射：对于已经存在的映射字段，不能更新，更新必须创建新的索引进行*数据迁移*。

- 数据迁移：先创建出 new_index 的正确映射，通过如下格式进行数据迁移

  > `POST`   reindex【固定写法】

  1、创建映射 `PUT` /new_bank
  
  ```json
{
    "mappings": {
      "properties": {
        "account_number": {
          "type": "long"
        },
        "address": {
          "type": "text",
          "fields": {
            "keyword": {
              "type": "keyword",
              "ignore_above": 256
            }
          }
        },
        "age": {
          "type": "integer"
        },
        "balance": {
          "type": "long"
        },
        "city": {
          "type": "text",
          "fields": {
            "keyword": {
              "type": "keyword",
              "ignore_above": 256
            }
          }
        },
        "email": {
          "type": "text",
          "fields": {
            "keyword": {
              "type": "keyword",
              "ignore_above": 256
            }
          }
        },
        "employer": {
          "type": "text",
          "fields": {
            "keyword": {
              "type": "keyword",
              "ignore_above": 256
            }
          }
        },
        "firstname": {
          "type": "text",
          "fields": {
            "keyword": {
              "type": "keyword",
              "ignore_above": 256
            }
          }
        },
        "gender": {
          "type": "keyword"
        },
        "lastname": {
          "type": "text",
          "fields": {
            "keyword": {
              "type": "keyword",
              "ignore_above": 256
            }
          }
        },
        "state": {
          "type": "keyword"
        }
      }
    }
  }
  ```
  
  2、数据迁移 `POST` _reindex
  
  ```json
  {
    "source": {
      "index": "bank",
      "type": "account"
    },
    "dest": {
      "index": "new_bank"
    }
  }
  ```
  
  bank中数据有类型，在迁移时要指定其类型，在6.x之后废弃了 *type*，迁移后的数据类型为*_doc*。

###### 分词

一个 tokenizer（分词器）接收一个字符流，将之分割为独立的 tokens（词元，通常是独立的单词），然后输出tikens流。例如，whitespace tokenizer遇到空白符时分割文本，它会将文本“Quick brown fox！”分割为[Quick，brown，fox！]。该 tokenizer（分词器）还负责记录每个 term（词条）的顺序或 position 位置（用于phrase短语和 word proximity 词邻近查询），以及 term （词条）所代表的原始 word（单词）的 start（起始）和 end（结束）的 character offsets（字符偏移量）（用于高亮显示搜索的内容）。Elasticsearch 提供了很多内置的分词器，可以用来构建 customer analyzers（自定义分词器）。

1. 安装 ik 分词器

   > https://github.com/medcl/elasticsearch-analysis-ik/releases/tag/v7.4.2

   将文件解压上传到 `/mydata/elasticsearch/plugins` 文件夹下，开启权限，重启 Elasticsearch 即可。

2. 创建自定义分词字典
   
   创建nginx容器，备份配置`/etc/nginx`到`/mydata`目录下，重命名配置为conf，并移动到mydata下，删除该容器，通过下列命令创建新的nginx容器。
   
   ```shell
   docker run -p 80:80 --name nginx \
   -v /mydata/nginx/html:/usr/share/nginx/html \
   -v /mydata/nginx/logs:/var/log/nginx \
   -v /mydata/nginx/conf:/etc/nginx \
   -d nginx
   ```
   
   在/mydata/nginx/html/es/目录下创建分词字典。
   
   ```shell
   vi /mydata/nginx/html/es/fenci.txt
   ```

3. 自定义词库
   修改 `/usr/share/elasticsearch/plugins/ik/config/` 中的 `IKAnalyzer.cfg.xml`

   ```xml
   <!-- 配置远程扩展字典 -->
   <entry key="remote_ext_dict">http://192.168.134.128/es/fenci.txt</entry>
   ```

##### Elasticsearch-Rest-Client

**（1）9300 TCP**   spring-data-elasticsearch:transport-api.jar

- spring boot 版本不同，transport-api.jar 不同，不能适配es版本。
- 7.x 已经不建议使用，8以后就要废弃。

**（2）9200 HTTP**

- JestClient 非官方，更新慢。
- RestTemplate、HttpCLient 模拟发HTTP请求，ES很多操作需要自己封装，麻烦。
- Elasticsearch-Rest-Client 官方RestClient，封装了ES操作，API层次分明，上手简单。

###### SpringBoot 整合

1. 导入依赖

   ```xml
   <properties>
       <java.version>1.8</java.version>
       <!-- 指定spring boot中elasticsearch的版本 -->
       <elasticsearch.version>7.4.2</elasticsearch.version>
   </properties>
   <dependency>
       <groupId>org.elasticsearch.client</groupId>
       <artifactId>elasticsearch-rest-high-level-client</artifactId>
       <version>7.4.2</version>
   </dependency>
   ```

2. 编写配置，给容器中注入一个RestHighLevelClient。

   ```java
   @Configuration
   public class ElasticsearchConfig {
       public RestHighLevelClient esRestClient() {
           // 可配置多个
           // 套接字：ip+端口号，协议
           RestHighLevelClient client = new RestHighLevelClient(
                   RestClient.builder(new HttpHost("192.168.134.128", 9200, "http")));
           return client;
       }
   }
   ```
   
3. 使用：参考官方文档 

   > https://www.elastic.co/guide/en/elasticsearch/client/java-rest/7.x/java-rest-high.html
   
   ![Elasticsearch的使用](https://gitee.com/lao-biao/Pictures/raw/master/elasticsearch/Elasticsearch%E7%9A%84%E4%BD%BF%E7%94%A8.png)
