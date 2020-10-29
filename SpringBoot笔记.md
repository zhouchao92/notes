---
title: SpringBoot笔记
tags:
  - spring boot
categories:
  - 后端
abbrlink: e94425f6
date: 2020-06-29 09:35:50
---

> 官方文档：[springboot](https://spring.io/projects/spring-boot)

#### 特征

- 创建独立的Spring应用程序
- 直接嵌入Tomcat，Jetty或Undertow（无需部署WAR文件）
- 提供“入门”依赖项，以简化构建配置
- 尽可能自动配置Spring和3rd Party库
- 提供可用于生产的功能，例如指标，运行状况检查和外部化配置
- 完全没有代码生成，也不需要XML配置

<!--more-->

#### 版本介绍

- **SNAPSHOT**：开发版
- **CURRENT**：最新版，不一定是稳定版
- **GA**：General Availability 正式发布的版本

#### 创建基于SpringBoot的项目

- 官网
- IDEA的脚手架工具
- IDEA的Maven项目

###### 官网

> [spring initializr](https://start.spring.io/)

![官网创建项目](https://gitee.com/lao-biao/Pictures/raw/master/Spring/官网创建项目.png)

填写完信息后点击`Generate`即可生成文件，下载解压后就是一个项目

###### IDEA的脚手架工具

使用 IDEA 的 Spring Initalizr 工具创建 SpringBoot 项目

- 打开创建项目,选择 Spring Initializr ，选择对应的JDK路径和URL

  ![IDEA 的 Spring Initalizr创建项目](https://gitee.com/lao-biao/Pictures/raw/master/Spring/IDEA-Spring-Initializr.png)

- 填写项目设置

  ![IDEA 的 Spring Initalizr创建项目](https://gitee.com/lao-biao/Pictures/raw/master/Spring/IDEA-Spring-Initializr-Project-Settings.png)

- 添加依赖和SpringBoot版本

  ![IDEA 的 Spring Initalizr创建项目](https://gitee.com/lao-biao/Pictures/raw/master/Spring/IDEA-Spring-Initializr-Dependencies.png)

- 选择项目名和路径，以及其他设置

  ![IDEA 的 Spring Initalizr创建项目](https://gitee.com/lao-biao/Pictures/raw/master/Spring/IDEA-Spring-Initializr-Name-Location.png)

###### IDEA的Maven项目

使用IDEA创建`Maven`项目后，修改`pom.xml`文件

```xml
<parent>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-parent</artifactId>
    <version>2.2.6.RELEASE</version>	<!-- SpringBoot版本 -->
    <relativePath/> <!-- lookup parent from repository -->
</parent>


<properties>
    <java.version>1.8</java.version>	<!-- JDK版本 -->
</properties>


<dependencies>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-web</artifactId>	<!-- SpringBoot Web启动器，项目不使用可以不需要添加 -->
    </dependency>

    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-test</artifactId>	<!-- SpringBoot测试启动器，可以不添加 -->
        <scope>test</scope>
        <exclusions>
            <exclusion>
                <groupId>org.junit.vintage</groupId>
                <artifactId>junit-vintage-engine</artifactId>
            </exclusion>
        </exclusions>
    </dependency>
</dependencies>
<build>
    <plugins>
        <plugin>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-maven-plugin</artifactId>	<!-- SpringBoot打包插件，必须有 -->
        </plugin>
    </plugins>
</build>
```

#### SpringBoot项目结构

##### POM文件

###### 继承

```xml
<parent>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-parent</artifactId>
    <version>2.2.6.RELEASE</version>
    <relativePath/> <!-- lookup parent from repository -->
</parent>
```

SpringBoot的父级依赖，只有继承它才是SpringBoot项目
`spring-boot-starter-parent` 是一个特殊的starter，它用来提供相关的Maven默认依赖。使用它之后，常用的包依赖可以省去version标签

###### 依赖

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-web</artifactId>	
</dependency>
```

启动器依赖
举例：web启动器，可使用第三方启动器。

###### 插件

```xml
<build>
    <plugins>
        <plugin>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-maven-plugin</artifactId>	
        </plugin>
    </plugins>
</build>
```

`spring-boot-maven-plugin` 插件是将`springboot`的应用程序打包成`jar`包的插件。将所有应用启动运行所需要的`jar`包都包含进来，从逻辑上将具备了独立运行软件的条件。当运行`mvn package`进行打包后，使用`java-jar`命令就可以直接运行。

##### 启动类

Spring Boot 的启动类的作用是启动Spring Boot 项目，是基于`Main`方法来运行的
启动类在启动时会做注解扫描(`@Controller`、`@Service`、`@Repository`...)

###### 启动类与启动器区别

启动类：项目的启动入口

启动器：`jar`包的坐标

###### 创建启动类

```java
@SpringBootApplication		//注解
public class SpringBootDemoApplication {
    
    public static void main(String[] args) {        SpringApplication.run(SpringBootDemoApplication.class, args);	//启动类
    }
    
}
```

> INFO 13284 --- [           main] c.s.s.SpringBootDemoApplication          : Starting SpringBootDemoApplication on Dell with PID 13284 
> INFO 13284 --- [           main] c.s.s.SpringBootDemoApplication          : No active profile set, falling back to default profiles: default
> INFO 13284 --- [           main] o.s.b.w.embedded.tomcat.TomcatWebServer  : Tomcat initialized with port(s): 8080 (http)
> INFO 13284 --- [           main] o.apache.catalina.core.StandardService   : Starting service [Tomcat]
> INFO 13284 --- [           main] org.apache.catalina.core.StandardEngine  : Starting Servlet engine: [Apache Tomcat/9.0.33]
> INFO 13284 --- [           main] o.a.c.c.C.[Tomcat].[localhost].[/]       : Initializing Spring embedded WebApplicationContext
> INFO 13284 --- [           main] o.s.web.context.ContextLoader            : Root WebApplicationContext: initialization completed in 2335 ms
> INFO 13284 --- [           main] o.s.s.concurrent.ThreadPoolTaskExecutor  : Initializing ExecutorService 'applicationTaskExecutor'
> INFO 13284 --- [           main] o.s.b.w.embedded.tomcat.TomcatWebServer  : Tomcat started on port(s): 8080 (http) with context path ''
> INFO 13284 --- [           main] c.s.s.SpringBootDemoApplication          : Started SpringBootDemoApplication in 4.181 seconds (JVM running for 9.543)

##### 启动器

Spring Boot 将所有的功能场景都抽取出来，做成一个个的 starter ，只要在项目里引入这些 starter 相关场景的所有依赖都会导入进来，要用什么功能就导入什么场景，在`jar`包上管理非常方便，最终实现一站式开发。

Spring Boot 提供多达 44 个启动器

> spring-boot-starter 核心启动器，包含了自动配置、日志和YAML
>
> spring-boot-starter-actuator 帮助监控和管理应用
>
> spring-boot-starter-web 支持全栈式Web开发，包括Tomcat 和 spring-webmvc
>
> spring-boot-starter-amqp 通过spring-rabbit 来支持AMQP协议(Advanced Message Queuing Protocol)
>
> spring-boot-starter-jdbc 支持JDBC数据库
>
> ...
>
> 官网：[springboot文档](https://docs.spring.io/spring-boot/docs/current/reference/htmlsingle/#using-boot-starter)

##### 配置文件

Spring Boot 提供一个名称为application的全局配置文件，支持两种格式：Properties格式和YAML格式。

###### Properties格式

配置Tomcat监听端口

```properties
server.port=3000
```

###### YAML格式

YAML格式的扩展名可以是`.yaml`或`.yml`。

基本格式：

- 区分大小写
- 使用缩进代表层级关系
- 相同的部分只出现一次

配置Tomcat监听端口

```yml
server:
    port: 3000
    host: xxxx
```

##### 配置文件存放位置

- 项目根目录中
- 项目根目录下的一个congfig子目录中
- 项目的resources即classpath根路径中
- 项目的resources即classpath根路径下的config目录中

##### 配置文件加载顺序

###### 不同格式的加载顺序

如果在同一个目录下有application.yml 也有 application.properties，默认先读取application.properties。

如果同一个配置属性，在多个配置文件都配置了，默认使用第一个读取到的，后面读取的不覆盖前面读取的。

###### 不同位置的加载顺序

项目根目录下的config子目录(最高)
config/application.properties
config/applicaiton.yml

项目根目录(其次)
application.properties
applicaiton.yml

项目的resources即classpath根路径下的config目录(一般)
resources/config/application.properties
resources/config/application.yml

项目的resources即classpath根路径(最后)
resources/application.properties
resources/application.yml

##### 配置文件中的占位符

###### 占位符语法

语法：`${}`

###### 占位符作用

`${}`中可以获取框架提供的方法中的值，如：`random.int` ...

占位符可以获取配置文件中的键的值赋给另一个键作为值

###### 生成随机数

`${random.value}`  类似于uuid的随机数，没有 - 连接

`${random.int}` 随机取整型范围内的一个值

`${random.long}` 随机取长整型范围内的一个值

`${random.uuid}` 生成一个uuid，有 - 连接

`${random.int(value)}` 随机生成value以内的整型的值

`${random.int(value,max)}` 随机生成value-max之间的整型的值

##### bootstrap配置文件

Spring Boot中有两种上下文对象：bootstrap ， application
bootstrap是应用程序的**父上下文**，其加载优先于application，bootstrap主要从额外的资源来加载配置信息，还可以在本地外部配置文件中解密属性
这两个上下文共用一个环境，它是任何Spring应用程序的外部属性的来源
bootstrap里面的属性会优先加载，它们默认也不能被本地相同配置覆盖

###### bootstrap配置文件特征

bootstrap 由父ApplicationContext加载，比application优先加载

bootstrap里面的属性不能被覆盖

###### 应用场景

application 配置文件主要用于Spring Boot项目的自动化配置

bootstrap 配置文件应用场景：

- 使用Spring Cloud Config 配置中心时，需要在bootstrap配置文件中添加连接到配置中心的配置属性来加载外部配置中心的配置信息
- 一些固定的不能被覆盖的属性
- 一些加密/解密的场景

##### SpringBoot核心注解

###### @SpringBootApplication

配置Spring Boot的启动类

等同于 `@Configuration` + `@EnableAutoConfiguration` + `@ComponentScan` 的组合

###### @SpringBootConfiguration

`@SpringBootConfiguration` 是 @`Configuration` 注解的派生注解，跟 `@Configuration`注解的功能一致，标注这个类是一个配置类，只不过`@SpringBootConfiguration` 是 springboot的注解，而`@Configuration` 是 spring的注解

###### @Configuration

通过对bean对象的操作替代 spring 中 的xml 配置文件

###### @EnableAutoConfiguration

Spring Boot 自动配置：根据添加的jar依赖自动配置Spring应用
是 @AutoConfiguration 和 @Import({AutoConfigurationImportSelector.class})注解的组合

###### @AutoConfigurationPackage

自动注入主类下所在包下所有的加了注解的类(`@Controller`，`@Service`)

###### @Import({AutoConfigurationImportSelector.class})

直接导入普通的类
导入实现了 ImportSelector 接口的类

导入实现了ImportBeanDefinitionRegister 接口的类

###### @ComponentScan

组件扫描，可自动发现和装载一些 Bean

###### ConfigurationPropertiesScan

扫描配置属性

作用：使用 `@ConfigurationProperties` 注解的类生效

##### 编写HelloWord

- 项目创建
- 修改POM文件
- 修改Tomcat端口
- 创建启动类
- 创建Controller

```java
/**
 * 处理请求 Controller
 */

@RestController // @Controller + @ResponseBody 直接返回字符串（json）
public class DemoController {
    @RequestMapping("/helloWorld")
    public String showHello() {
        return "Hello world";
    }
}
```

> 启动应用后
>
> 访问 [http://localhost:8080/helloWorld](http://localhost:8080/helloWorld)

##### SpringBoot在Controller中常用注解

###### @RestController

相当于 @Controller + @ResponseBody 注解组合

如果使用 @RestController 注解，Controller中的方法无法返回页面，相当于在方法上面自动加了 @ResponseBody 注解，所以没办法跳转并传输数据到另一个页面，所以 InternalResourceResolver 也不起作用，返回的内容就是 return 里面的内容

###### @GetMapping

RequestMapping(method = RequestMethod.Get) 的缩写

###### @PostMapping

RequestMapping(method = RequestMethod.Post) 的缩写

###### @PutMapping

RequestMapping(method = RequestMethod.Put) 的缩写

###### @DeleteMapping

RequestMapping(method = RequestMethod.Delete) 的缩写

#### SpringBoot整合Web层技术

##### 整合Servlet

###### 整合Servlet方式一

通过注解扫描完成Servlet组件的注册

- 创建Servlet

```java
/**
 * 整合Servlet方式一
 */
@WebServlet(name = "FirstServlet", urlPatterns = "/first")
public class FirstServlet extends HttpServlet {
    public void doGet(HttpServletRequest request, HttpServletResponse response) {
        System.out.println("This is from first servlet");
    }
}
```

- 修改启动类，添加`@ServletComponentScan`注解

  在SpringBoot启动时会扫描`@WebServlet`注解，并将该类实例化

###### 整合Servlet方式二

通过方法完成Servlet组件的注册

- 创建Servlet 	`servlet/SecondServlet.java`

```java
/**
 * 整合Servlet方式二
 */
public class SecondServlet extends HttpServlet {
    public void doGet(HttpServletRequest request, HttpServletResponse response) {
        System.out.println("This is from second servlet.");
    }
}
```

- 创建Servlet配置类	 `config/ServletConfig.java`

```java
/**
 * Servlet组件注册类
 */
@Configuration
public class ServletConfig {
    /**
     * 完成Servlet组件的注册
     *
     * @return
     */
    @Bean
    public ServletRegistrationBean getServletConfigurationBean() {
        ServletRegistrationBean bean = new ServletRegistrationBean(new SecondServlet());
        bean.addUrlMappings("/second");
        return bean;
    }
}
```

##### 整合Filter

###### 整合Filter方式一

通过注解扫描完成Filter组件的注册

- 创建Filter	`filter/FisrtFilter.java`

```java
/**
 * 整合Filter方式一
 */
// urlPatterns = {"*.do", "*.jsp"}	匹配多个url
@WebFilter(filterName = "FirstFilter", urlPatterns = "/first")
public class FirstFilter implements Filter {
    @Override
    public void init(FilterConfig filterConfig) throws ServletException {

    }

    @Override
    public void doFilter(ServletRequest servletRequest, ServletResponse servletResponse, FilterChain filterChain) throws IOException, ServletException {
        System.out.println("--------in first filter--------");
        filterChain.doFilter(servletRequest, servletResponse);
        System.out.println("--------out first filter--------");
    }

    @Override
    public void destroy() {

    }
}
```

- 修改启动类，添加 `@ServletComponentScan` 注解

###### 整合Filter方式二

通过方法完成Filter组件的注册

- 创建Filter	`filter/SecondFilter.java`

```java
/**
 * 整合Filter方式二
 */
public class SecondFilter implements Filter {

    @Override
    public void init(FilterConfig filterConfig) throws ServletException {

    }

    @Override
    public void doFilter(ServletRequest servletRequest, ServletResponse servletResponse, FilterChain filterChain) throws IOException, ServletException {
        System.out.println("--------in Second filter--------");
        filterChain.doFilter(servletRequest,servletResponse);
        System.out.println("--------out Second filter--------");
    }

    @Override
    public void destroy() {

    }

}
```

- 创建Filter配置类 `config/SecondFilter.java`

```java
/**
 * Filter配置类
 */
@Configuration
public class FilterConfig {

    @Bean
    public FilterRegistrationBean getFilterRegistrationBean() {
        FilterRegistrationBean bean = new FilterRegistrationBean(new SecondFilter());
        //bean.addUrlPatterns("*.do", "*.jsp"); //不定项参数，可以不使用 new String[]{}
        bean.addUrlPatterns("/second");
        return bean;
    }
}
```

##### 整合Listener

###### 整合Listener方式一

通过注解扫描完成Listener组件注册

- 编写Listener	 `listener/FirstListener.java`

```java
/**
 * 整合Listener方式一
 * ServletContextListener 监听上下文
 */
@WebListener
public class FirstListener implements ServletContextListener {
    /**
     * 初始化
     *
     * @param event
     */
    public void contextInitialized(ServletContextEvent event) {
        System.out.println("Listener--initialized");
    }

    /**
     * 销毁
     *
     * @param event
     */
    public void contextDestroyed(ServletContextEvent event) {
        
    }
}

```

- 修改启动类 添加 `@ServletComponentScan` 注解

###### 整合Listener方式二

通过方法完成Listener组件的注册

- 创建Listener 	`listener/SecondListener.java`

```java
/**
 * 整合Listener方式二
 * ServletContextListener 监听上下文
 */
public class SecondListener implements ServletContextListener {
    /**
     * 初始化
     *
     * @param event
     */
    public void contextInitialized(ServletContextEvent event) {
        System.out.println("Second Listener--initialized");
    }

    /**
     * 销毁
     *
     * @param event
     */
    public void contextDestroyed(ServletContextEvent event) {

    }
}
```

- 创建Listener配置类 	`config/ListenerConfig.java`

```java
/**
 * Listener配置类
 */
@Configuration
public class ListenerConfig {
    @Bean
    public ServletListenerRegistrationBean getServletListenerRegistrationBean() {
        ServletListenerRegistrationBean bean = new ServletListenerRegistrationBean(new SecondListener());
        return bean;
    }
}
```

#### SpringBoot访问静态资源

在SpringBoot项目中没有常规web开发的WebContent(WebApp)，它只有src目录。在src/main/resources下面有两个文件夹，static和templates。SpringBoot默认在static目录存放静态页面，而templates中放动态页面。

##### static目录

SpringBoot 通过classpath/static 目录访问静态资源

存放静态资源的目录名称必须是static

> 文件：static/index.html
>
> 直接访问：[http://localhost:8080/index.html](http://localhost:8080/index.html)

通过controller跳转访问	controller/IndexPageController.java

```java
@Controller
public class IndexPageController {
    @RequestMapping("/index")
    public String showIndexPage() {
        return "index.html";
    }
}
```

> 文件：static/index.html
>
> 访问地址：[http://localhost:8080/index](http://localhost:8080/index)

##### templtates目录

SpringBoot 中不推荐使用jsp作为视图层技术，而是默认使用Thymeleaf来做动态页面

Teammates目录用来存放Thymeleaf的页面

##### 静态资源存放其他位置

###### SpringBoot访问静态资源的位置

> classpath:	与java文件夹同级的resources目录

classpath:/META-INF/resources/

classpath:/resources/

classpath:/static/

classpath:/public/

###### 自定义静态资源位置

在application.properties文件中配置

```properties
spring.resources.static-locations=classpath:/自定义资源位置1/,classpath:/自定义资源位置2/
```

#### SpringBoot文件上传

- 创建项目
- 配置POM文件
- 编写启动类
- 编写上传页面

```html
<html>
<head>
    <meta charset="utf-8"/>
    <title>SpringBoot文件上传</title>
</head>

<body>
<form action="/fileUploadController" method="post" enctype="multipart/form-data">
    <input type="file" name="file">
    <input type="submit" value="OK">
</form>
</body>
</html>
```

编写Controller

```java
@RestController
public class FileUploadController {

    /**
     * 文件上传
     *
     * @param file 参数名必须与fileupload.xml中的input标签的value值一致
     * @return
     * @throws IOException
     */
    @PostMapping("/fileUploadController")
    public String fileUpload(MultipartFile file) throws IOException {
        System.out.println(file.getOriginalFilename());
        file.transferTo(new File("D:/upload/" + file.getOriginalFilename()));
        return "OK";
    }
}

```

修改上传文件大小

```properties
# 设置单文件上传的容量限制
spring.servlet.multipart.max-file-size=2MB
# 配置在一次请求中上传文件总容量的限制
spring.servlet.multipart.max-request-size=20MB
```

#### SpringBoot整合视图层技术

##### SpringBoot整合JSP技术

创建项目

修改POM文件，添加JSP引擎与JSTL标签库

```xml
<!--添加jsp引擎依赖，SpringBoot内置Tomcat没有此依赖-->
<dependency>
    <groupId>org.apache.tomcat.embed</groupId>
    <artifactId>tomcat-embed-jsper</artifactId>
</dependency>
<!--添加JSTL标签库依赖-->
<dependency>
    <groupId>javax.servlet</groupId>
    <artifactId>jstl</artifactId>
</dependency>
```

创建webapp目录

main目录下与java、resource同级

标记为web目录

创建JSP

修改配置文件，配置视图解析器

```properties
# 前缀
spring.mvc.view.prefix=/WEB-INF/jsp/
# 后缀
spring.mvc.view.suffix=.jsp
```

创建Controller

```java
@Controller
public class PageController {
    // 页面跳转
    @GetMapping("/{page}")
    public String showPage(@PathVariable String page) {
        return page;
    }
}
```

如果在IDEA中项目为聚合工程，在运行jsp时是需要指定路径。

##### SpringBoot整合Freemarker

创建项目

修改POM文件，添加Freemarker启动器

```xml
<!--Freemarker依赖-->
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-freemarker</artifactId>
</dependency>
```

创建User实体

```java
public class User {
    private String uname;
    private String gender;
    private int age;

    public User() {
    }

    public User(String uname, String gender, int age) {
        this.uname = uname;
        this.gender = gender;
        this.age = age;
    }

    public String getUname() {
        return uname;
    }

    public void setUname(String uname) {
        this.uname = uname;
    }

    public String getGender() {
        return gender;
    }

    public void setGender(String gender) {
        this.gender = gender;
    }

    public int getAge() {
        return age;
    }

    public void setAge(int age) {
        this.age = age;
    }
}
```

创建Controller

```java
@Controller
public class UserController {
    @GetMapping("/showUsers")
    public String showUsers(Model model) {
        List<User> list = new ArrayList<>();
        list.add(new User("张三", "男", 20));
        list.add(new User("李四", "男", 22));
        list.add(new User("王五", "男", 18));
        model.addAttribute("list", list);
        return "userlist";
    }
}
```

创建视图 `templates/userlist.ftl`

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>SpringBoot整合Freemarker</title>
</head>
<body>
<table border="1px" align="center" width="50%">
    <tr>
        <th>Name</th>
        <th>Gender</th>
        <th>Age</th>
    </tr>
    <#list list as user>
        <tr>
            <td>${user.uname}</td>
            <td>${user.gender}</td>
            <td>${user.age}</td>
        </tr>
    </#list>
</table>
</body>
</html>
```

配置Freemarker文件后缀（SpringBoot2.x后默认为.ftlh）

```properties
spring.freemarker.suffix=.ftl
```

