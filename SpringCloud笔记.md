---
title: SpringCloud笔记
tags:
  - spring cloud
  - spring cloud alibaba
categories:
  - notes
  - 后端
abbrlink: 67c8035e
date: 2020-09-07 15:58:03
---

##### Spring Cloud

Spring Cloud为开发人员提供了工具来快速构建分布式系统中的一些常见模式(例如配置管理、服务发现、断路器、智能路由、微代理、控制总线、一次性令牌、全局锁、领导选举、分布式会话、集群状态)。分布式系统的协调产生了模板模式，使用Spring Cloud开发人员可以快速建立实现这些模式的服务和应用程序。它们在任何分布式环境中都能很好地工作，包括开发者自己的笔记本电脑、裸机数据中心以及云计算等托管平台。

[Spring Cloud 官网](https://spring.io/projects/spring-cloud)
[Spring Cloud Alibaba](https://github.com/alibaba/spring-cloud-alibaba)

<!--more-->

###### Spring Cloud的几大痛点

- Spring Cloud 部分组件停止维护和更新，给开发带来不便
- Spring Cloud 部分环境搭建复杂，没有完善的可视化界面，需要大量的二次开发和定制
- Spring Cloud 配置复杂，难以上手，部分配置差别难以区分和合理应用

###### Spring Cloud Alibaba的优势  

成套的产品搭配完善的可视化界面给开发运维带来极大的便利
搭建简单，学习曲线低。

###### 结合Spring Cloud Alibaba最终的技术搭配方案

Spring Cloud Alibaba 

- Nacos
  - 注册中心（服务发现/注册）
  - 配置中心（动态配置管理）
- Sentinel：服务容错（限流、降级、熔断）
- Seata：原Fescar，即分布式事务解决方案

Spring Cloud

- Ribbon：负载均衡
- Feign：声明式HTTP客户端（调用远程服务）
- Gateway：API网关（webflux编程模式）
- Sleuth：调用链监控

##### 注册中心

###### Nacos

1. 导入依赖

   ```xml
   <!--nacos服务的注册发现-->
   <dependency>
       <groupId>com.alibaba.cloud</groupId>
       <artifactId>spring-cloud-starter-alibaba-nacos-discovery</artifactId>
   </dependency>
   ```

2. 配置nacos注册中心的地址

   ```properties
    spring.cloud.nacos.discovery.server-addr=127.0.0.1:8848
   ```

3. 使用`@EnableDiscoveryClient`注解开启服务注册与发现功能

   ```java
   @SpringBootApplication
   @EnableDiscoveryClient
   public class ProviderApplication {
   
       public static void main(String[] args) {
           SpringApplication.run(ProviderApplication.class, args);
       }
   
       @RestController
       class EchoController {
           @GetMapping(value = "/echo/{string}")
           public String echo(@PathVariable String string) {
               return string;
           }
       }
   }
   ```

###### OpenFeign

远程调用别的服务

1. 引入open-feign
2. 编写一个接口，告诉spring cloud这个接口需要调用远程服务
   1. 声明接口的每一个方法都是调用哪个远程服务的哪个方法
3. 开启远程调用功能

##### 配置中心

###### 使用nacos作为配置中心统一管理配置

1. 导入依赖

   ```xml
   <!--服务的配置中心-->
   <dependency>
       <groupId>com.alibaba.cloud</groupId>
       <artifactId>spring-cloud-starter-alibaba-nacos-config</artifactId>
   </dependency>
   ```

2. 创建一个bootstarp.properties文件

   ```properties
   spring.application.name=gulimall-coupon
   
   spring.cloud.nacos.config.server-addr=127.0.0.1:8848
   ```

3. 需要给配置中心默认添加一个数据集（Data Id）gulimall-coupon.properties，默认规则：应用名.properties

4. 给 应用名.properties 添加任何配置

5. 动态获取配置：

   - @RefreshScope 					   动态获取并刷新

   - @Value(${"配置项的key"}); 	 获取到配置

     如果配置中心和当前应用的配置文件都配置了相同的项，优先使用配置中心的配置

###### 细节

- 命名空间：配置隔离
  默认：public（保留空间），默认新增的的所有配置都在public空间
  1. 开发dev、测试test、生产prod，利用命名空间进行环境隔离
     <font color='red'>注意：在bootstarp.properties上配置需要使用哪个命名空间的配置</font>
  2. 每一个微服务之间互相隔离配置，每一个微服务都创建自己的命名空间，只加载自己命名空间下的所有配置

- 配置集：所有配置的集合
- 配置集ID：类似文件名；DataId
- 配置分组：
  默认所有的配置集都属于：DEFAULT_GROUP

每个微服务创建自己的命名空间，使用分组区分环境，dev，test，prod

```properties
# 配置应用名称
spring.application.name=gulimall-coupon
# 配置nacos的服务地址
spring.cloud.nacos.config.server-addr=127.0.0.1:8848
# 配置nacos命名空间
spring.cloud.nacos.config.namespace=a177d187-5110-464e-9844-fe4b09cc0ebb
# 设置配置分组
spring.cloud.nacos.config.group=dev
```

同时加载多个配置集

- 微服务任何配置信息，任何配置文件都可以放在配置中心中
- 只需要在`bootstrap.properties`说明加载配置中心中哪些配置文件即可
- @Value，@ConfiguartionProperties
  SpringBoot从配置文件中获取值的任何方式，都能使用
  配置中心有的优先使用，没有的配置使用微服务的配置

```properties
# 配置应用名称
spring.application.name=gulimall-coupon
# 配置nacos的服务地址
spring.cloud.nacos.config.server-addr=127.0.0.1:8848
# 配置nacos命名空间
spring.cloud.nacos.config.namespace=a177d187-5110-464e-9844-fe4b09cc0ebb
# 设置配置分组
spring.cloud.nacos.config.group=dev
# database source数据源
spring.cloud.nacos.config.extension-configs[0].data-id=datasource.yaml
spring.cloud.nacos.config.extension-configs[0].group=dev
spring.cloud.nacos.config.extension-configs[0].refresh=true
# mybatis
spring.cloud.nacos.config.extension-configs[1].data-id=mybatis.yaml
spring.cloud.nacos.config.extension-configs[1].group=dev
spring.cloud.nacos.config.extension-configs[1].refresh=true
# 其他
spring.cloud.nacos.config.extension-configs[2].data-id=other.yaml
spring.cloud.nacos.config.extension-configs[2].group=dev
spring.cloud.nacos.config.extension-configs[2].refresh=true
```

##### 网关

网关作为流量的入口，常用功能包括路由转发、权限校验、限流控制等，而Spring Cloud Gateway作为Spring Cloud官方推出的第二代网关框架，替代Zuul网关。

官方文档：[spring-cloud-gateway-2.2.3.RELEASE](https://cloud.spring.io/spring-cloud-static/spring-cloud-gateway/2.2.3.RELEASE/reference/html/#)

- 路由（Route）
- 断言（Predicate）
- 过滤器（Filter）

###### Gateway简单配置

1. 开启服务注册发现（配置nacos的注册中心地址）
2. 配置路由、断言和过滤器

> application.properties
>
> ```properties
> spring.cloud.nacos.discovery.server-addr=127.0.0.1:8848
> spring.application.name=gulimall-gateway
> server.port=88
> ```

> bootstrap.properties
>
> ```properties
> spring.application.name=gulimall-gateway
> spring.cloud.nacos.config.server-addr=127.0.0.1:8848
> 
> spring.cloud.nacos.config.namespace=7c44370b-0de0-400c-8650-9ef824a54818
> spring.cloud.nacos.config.group=dev
> 
> spring.cloud.nacos.config.extension-configs[0].data-id=gulimall-gateway.yaml
> spring.cloud.nacos.config.extension-configs[0].group=dev
> spring.cloud.nacos.config.extension-configs[0].refresh=true
> ```

> application.yaml
>
> ```yml
> spring:
>   cloud:
>     gateway:
>       routes:
>         # 测试gateway的导入
>         - id: baidu_route
>           uri: https://www.baidu.com
>           predicates:
>             - Query=url,baidu
>         - id: qq_route
>           uri: https://www.qq.com
>           predicates:
>             - Query=url,qq
> ```

当请求url中包含url=baidu时跳转到baidu.com，url=qq时跳转到qq.com

###### 商城项目配置

通过`GateWay`网关将以`api`请求分别转发到指定的服务。

> application.yaml
>
> ```yml
> spring:
>   cloud:
>     gateway:
>       routes:
>         - id: product_route
>           # load balance 负载均衡
>           uri: lb://gulimall-product
>           # 断言
>           predicates:
>             - Path=/api/product/**
>           filters:
>             - RewritePath=/api/(?<segment>.*),/$\{segment}
>         - id: admin_route
>           # load balance 负载均衡
>           uri: lb://renren-fast
>           # 断言
>           predicates:
>             - Path=/api/**
>           filters:
>             - RewritePath=/api/(?<segment>.*),/renren-fast/$\{segment}
> ```

<font color=red>注意：断言的 </font>`Path`<font color=red>作用域越大的放在后面，避免出现指定的断言的</font>`Path`<font color=red>不生效。</font>