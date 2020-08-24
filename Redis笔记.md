---
title: Redis笔记
tags:
  - redis
categories:
  - notes
  - 后端
abbrlink: bf90371e
date: 2020-07-08 08:48:27
---

#### Redis简介

##### Redis解释

Redis是一个基于key-value形式进行存储的内存型数据库

- 数据存储方式为key-value键值对
- 数据存储在内存中
  - 优点：效率高
    理论：每秒10k数据读取
- 定位：数据库软件
  - 作用：存储数据

<!--more-->

##### NoSql数据库

Redis是一个NoSql数据库。

- 不使用sql命令操作数据库软件
- NoSql：Not Only Sql，表示在应用程序开发时，不是必须使用关系型数据库，可以使用NoSql替代关系型数据库的部分功能。
- 目前NoSql不能完全替代关系型数据库，使用关系型数据库结合NoSql数据库进行项目完成。
  - 当数据比较复杂时不适用于NoSql数据库
  - 关系数据库依然作为数据存储的主要软件
  - NoSql数据库当作缓存工具来使用
    - 把关系数据库中某些**使用频率较高**的的内容，不仅仅存储到关系型数据库，还存到NoSql数据库中。
    - 要考虑NoSql和关系型数据库之间的**同步**问题

##### Redis持久化策略

###### rdb

- 默认的持久化策略
- 每隔一定时间后把内存中数据持久化到dump.rdb文件中

缺点

- 数据过于集中
- 可能导致最后的数据没有持久化到dump.rdb中
  - 解决办法：使用命令SAVE或BGSAVE手动持久化

###### aof

监听Redis的日志文件，监听如果发现执行了修改、删除、新增命令，立即根据这条命令把数据持久化

缺点

- 效率降低

#### Redis安装

Windows版本下载地址[https://github.com/tporadowski/redis/releases](https://github.com/tporadowski/redis/releases)

- 下载对应版本，解压到Redis文件夹下

- 将Redis文件路径添加到环境变量path中

- 用命令行窗口执行`redis-server.exe`命令或`redis-server.exe redis.windows.conf`（没有配置环境变量时）

  ```shell
  >redis-server.exe
  >redis-server.exe redis.windows.conf
  [4572] 30 Jun 15:39:59.717 # oO0OoO0OoO0Oo Redis is starting oO0OoO0OoO0Oo
  [4572] 30 Jun 15:39:59.717 # Redis version=5.0.9, bits=64, commit=9414ab9b, modified=0, pid=4572, just started
  [4572] 30 Jun 15:39:59.717 # Warning: no config file specified, using the default config. In order to specify a config file use redis-server.exe /path/to/redis.conf
                  _._
             _.-``__ ''-._
        _.-``    `.  `_.  ''-._           Redis 5.0.9 (9414ab9b/0) 64 bit
    .-`` .-```.  ```\/    _.,_ ''-._
   (    '      ,       .-`  | `,    )     Running in standalone mode
   |`-._`-...-` __...-.``-._|'` _.-'|     Port: 6379
   |    `-._   `._    /     _.-'    |     PID: 4572
    `-._    `-._  `-./  _.-'    _.-'
   |`-._`-._    `-.__.-'    _.-'_.-'|
   |    `-._`-._        _.-'_.-'    |           http://redis.io
    `-._    `-._`-.__.-'_.-'    _.-'
   |`-._`-._    `-.__.-'    _.-'_.-'|
   |    `-._`-._        _.-'_.-'    |
    `-._    `-._`-.__.-'_.-'    _.-'
        `-._    `-.__.-'    _.-'
            `-._        _.-'
                `-.__.-'
  
  [4572] 30 Jun 15:39:59.723 # Server initialized
  [4572] 30 Jun 15:39:59.724 * DB loaded from disk: 0.000 seconds
  [4572] 30 Jun 15:39:59.724 * Ready to accept connections
  ```

- 打开新的cmd窗口输入`redis-cli.exe -h 127.0.0.1 -p 6379`，即可进行相关命令操作

  ```shell
  >redis-cli.exe -h 127.0.0.1 -p 6379
  127.0.0.1:6379> set name "Hello World"
  OK
  127.0.0.1:6379> get name
  "Hello World"
  ```

  注意：在加载设置文件条件下启动时需要在Redis目录下执行命令`redis-server.exe redis.windows.conf`

  ```shell
  >cd Redis
  >redis-server.exe redis.windows.conf
  ```

#### Redis常用命令

命令手册网址	[Redis命令参考](http://doc.redisfans.com/)

Redis**常用数据类型**

- **String**字符串
- Hash哈希表
- List列表
- Set集合
- SortedSet有序集合

##### Redis常用概念

- Redis默认有16384 solts（槽），每个槽可存储多个hash值。

- Redis默认不需要密码

  在`redis.windows.conf`文件中去除requirepass foobared的注释，即可设置密码

- 设置密码后需要通过

  - -h 主机ip
  - -p 端口
  - -a 密码

  ```shell
  redis-cli -h 127.0.0.1 -p 6379 -a f foobared
  ```

#### Jedis

Jedis是Redis客户端工具jar

```xml
<!-- https://mvnrepository.com/artifact/redis.clients/jedis -->
<dependency>
    <groupId>redis.clients</groupId>
    <artifactId>jedis</artifactId>
    <version>3.3.0</version>
</dependency>
```

使用非集群版实例代码

```java
Jedis jedis = new Jedis("127.0.0.1", 6379);
// 测试插入
String res = jedis.set("address", "Hubei");
System.out.println("insert-->" + res);
// 测试查询
String get = jedis.get("address");
System.out.println("select-->" + get);
// 测试修改
String set = jedis.set("address", "China");
System.out.println("update-->" + set);
// 测试查询
String get2 = jedis.get("address");
System.out.println("select-->" + get2);
// 测试删除
Long del = jedis.del("address");
System.out.println("delete-->" + del);
// 测试查询
String get3 = jedis.get("address");
System.out.println("select-->" + get3);
```

#### Redis集群

##### 集群的概念

多个业务单元协同工作完成的整体称为**集群**。

- 每个业务单元都是相同的
- 当集群中业务单元中超过或等于二分之一个down掉时整个集群不可用（建议使用奇数个，整体down掉几率小）

##### 一主一备模式

给每个**业务单元**创建一个**备份业务单元**，原来的业务单元（master）后产生的叫做（slave）。

##### 集群和伪集群

**集群**：每个业务单元都安装到单独的服务器上

**伪集群**：每个业务单元都安装到同一个服务器上，通过端口区分不同的业务单元。

##### 使用jedis访问集群

- JedisCluster(Set\<HostAndPort> nodes)

  集群的ip和端口

```java
Set<HostAndPort> hostAndPortSet=new HashSet<>();
hostAndPortSet.add(new HostAndPort("127.0.0.1",7001));
hostAndPortSet.add(new HostAndPort("127.0.0.1",7002));
hostAndPortSet.add(new HostAndPort("127.0.0.1",7003));
hostAndPortSet.add(new HostAndPort("127.0.0.1",7004));
hostAndPortSet.add(new HostAndPort("127.0.0.1",7005));
hostAndPortSet.add(new HostAndPort("127.0.0.1",7006));
JedisCluster jedisCluster=new JedisCluster(hostAndPortSet);
jedisCluster.get("address");
```

通过xml形式配置集群

```xml
<!-- jedisCluster -->
<bean id="jedisClients" class="redis.clients.jedis.JedisCluster">
    <constructor-arg name="poolConfig" ref="jedisPoolConfig"/>
    <constructor-arg name="nodes">
        <set>
            <bean class="redis.clients.jedis.HostAndPort">
                <constructor-arg name="host" value="127.0.0.1"/>
                <constructor-arg name="port" value="7001"/>
            </bean>
            <bean class="redis.clients.jedis.HostAndPort">
                <constructor-arg name="host" value="127.0.0.1"/>
                <constructor-arg name="port" value="7002"/>
            </bean>
            <bean class="redis.clients.jedis.HostAndPort">
                <constructor-arg name="host" value="127.0.0.1"/>
                <constructor-arg name="port" value="7003"/>
            </bean>
            <bean class="redis.clients.jedis.HostAndPort">
                <constructor-arg name="host" value="127.0.0.1"/>
                <constructor-arg name="port" value="7004"/>
            </bean>
            <bean class="redis.clients.jedis.HostAndPort">
                <constructor-arg name="host" value="127.0.0.1"/>
                <constructor-arg name="port" value="7005"/>
            </bean>
            <bean class="redis.clients.jedis.HostAndPort">
                <constructor-arg name="host" value="127.0.0.1"/>
                <constructor-arg name="port" value="7006"/>
            </bean>
        </set>
    </constructor-arg>
</bean>
```

配置jedis连接池

```xml
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:context="http://www.springframework.org/schema/context" xmlns:p="http://www.springframework.org/schema/p"
       xmlns:aop="http://www.springframework.org/schema/aop" xmlns:tx="http://www.springframework.org/schema/tx"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans-4.0.xsd
   http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context-4.0.xsd
   http://www.springframework.org/schema/aop http://www.springframework.org/schema/aop/spring-aop-4.0.xsd
   http://www.springframework.org/schema/tx http://www.springframework.org/schema/tx/spring-tx-4.0.xsd
   http://www.springframework.org/schema/util http://www.springframework.org/schema/util/spring-util-4.0.xsd">

    <!-- 配置jedis连接池 -->
    <bean id="jedisPoolConfig" class="redis.clients.jedis.JedisPoolConfig">
        <!-- 最大连接数 -->
        <property name="maxTotal" value="30"/>
        <!-- 最大空闲连接数 -->
        <property name="maxIdle" value="10"/>
        <!-- 每次释放连接的最大数目 -->
        <property name="numTestsPerEvictionRun" value="1024"/>
        <!-- 释放连接的扫描间隔（毫秒） -->
        <property name="timeBetweenEvictionRunsMillis" value="30000"/>
        <!-- 连接最小空闲时间 -->
        <property name="minEvictableIdleTimeMillis" value="1800000"/>
        <!-- 连接空闲多久后释放, 当空闲时间>该值 且 空闲连接>最大空闲连接数 时直接释放 -->
        <property name="softMinEvictableIdleTimeMillis" value="10000"/>
        <!-- 获取连接时的最大等待毫秒数,小于零:阻塞不确定的时间,默认-1 -->
        <property name="maxWaitMillis" value="1500"/>
        <!-- 在获取连接的时候检查有效性, 默认false -->
        <property name="testOnBorrow" value="true"/>
        <!-- 在空闲时检查有效性, 默认false -->
        <property name="testWhileIdle" value="true"/>
        <!-- 连接耗尽时是否阻塞, false报异常,true阻塞直到超时, 默认true -->
        <property name="blockWhenExhausted" value="false"/>
    </bean>
</beans>
```