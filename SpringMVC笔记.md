---
title: SpringMVC笔记
tags:
  - spring mvc
  - notes
abbrlink: e545fed0
date: 2020-06-28 21:29:57
---

#### SpringMVC

##### SpringMVC概述

**SpringMVC**是基于Java实现**MVC**设计模型的请求驱动类型的轻量级Web框架，属于**SpringFrameWork**的后续产品，已经融合在**SpringWebFlow**中。

SpringMVC通过一套注解，让一个简单的Java类成为处理请求的控制器，而无须实现任何接口，同时支持**RESTful**编程风格的请求。

<!--more-->

##### 开发步骤

1. 导入SpringMVC相关依赖包
2. 配置SpringMVC核心控制器<b>DispatcherServlet</b>
3. 编写Controller类和视图页面--"@Controller与@RequestMapping"
4. 将Controller使用注解配置到Spring容器中
5. 配置spring-mvc.xml文件(配置组件扫描)
6. 客户端发起请求测试

##### SpringMVC中重要组件

DispatcherServlet：前端控制器，接收所有请求（如果配置/不包含jsp）

HandlerMapping：解析请求格式，判断希望要执行哪个具体的方法

HandlerAdapter：负责调用具体的方法

Handler：处理器

ViewResolver：视图解析器，解析结果，准备跳转到具体的物理视图

View：视图

##### SpringMVC运行原理

1. 客户端向Tomcat发送请求
2. 前端控制器DispatcherServlet--请求查询Handler
3. 处理器映射器HandlerMapping
4. HandlerAdapter
5. Controller
6. ViewResolver
7. 前端控制器返回处理器执行链HandlerExecuctionChain

##### SpringMVC注解@RequestMappping

###### @RequestMappping

作用：用于建立请求URL和处理方法之间的对应关系

**位置**

- 类：请求URL的第一级访问目录，此处不写就相当于应用的根目录
- 方法：请求URL的第二级访问目录，与类上使用的@RequestMapping标注的一级目录一起组成访问虚拟路径

**属性**

- value：用于指定请求的URL，和path属性的作用是一样的
- method：用于指定请求的方式
- params：用于指定限制请求参数的条件，它支持简单的表达式，要求请求参数的key和value必须和配置的一模一样
- params={"accountName"}：表示请求参数必须有accountName
- params={"moeny!100"}：表示请求参数中money不能是100

###### 视图解析器配置

```xml
<!--配置内部资源视图解析器-->
<bean id="viewResolver" class="org.springframework.web.servlet.view.InternalResourceViewResolver">
    <!--配置前缀-->
    <property name="prefix" value="/jsp/"/>
    <!--配置后缀-->
    <property name="suffix" value=".jsp"/>
</bean>
```

#### SpringMVC的数据响应

##### 页面跳转

- 直接返回字符串：将返回的字符串与视图解析的前后缀拼接后跳转

  举例	返回"index"	

  前缀为`/jsp/`	后缀为`.jsp`

  **转发资源** `/jsp/index.jsp`

  **转发** `forward:/jsp/index.jsp`

  **重定向** `redirect:/index.jsp`

- 通过ModelAndView对象返回

##### 回写数据

- 直接返回字符串（json）：使用@ResponseBody注解

  通过SpringMVC框架注入的response对象，使用response.getWriter.write()回写数据，此时不需要视图跳转，业务方法返回值为void

  json格式

  ```xml
  <bean class="org.springframework.web.servlet.mvc.method.annotation.RequestMappingHandlerAdapter">
      <property name="messageConverters">
          <list>
  			<!--配置编码格式-->
              <bean class="org.springframework.http.converter.StringHttpMessageConverter">
                  <property name="supportedMediaTypes">
                      <list>
                          <value>text/html;charset=UTF-8</value>
                      </list>
                  </property>
              </bean>
              <!--配置jackson转换器-->
              <bean class="org.springframework.http.converter.json.MappingJackson2HttpMessageConverter"/>
          </list>
      </property>
  </bean>
  ```

- 返回对象或集合
  mvc注解驱动替代上述配置

  ```xml
  <!--mvc的注解驱动-->
  <mvc:annotation-driven/>
  ```

  在SpringMVC各个组件中，处理器映射器、处理器适配器、视图解析器成为SpringMVC的三大组件，使用`<mvc:annotation-driven/>`自动加载RequestMappingHandlerMapping（）处理映射器和RequestMappingHanlderAdapter（处理适配器），可用在spring-xml.xml配置文件中使用`<mvc:annotation-driven/>`替代注解注解处理器和适配器的配置。

  使用`<mvc:annotation-driven/>`默认底层就会继承jackson进行对象或集合的json格式字符串的转换

#### SpringMVC获取请求数据

客户端请求参数的格式是：name=value&name=value...

服务器端要获得请求的参数，有时还需要进行数据的封装，SpringMVC可以接收的参数类型为

- 基本类型参数
- POJO类型参数
- 数组类型参数
- 集合类型参数

##### 获取基本类型参数

Controller中的业务方法的参数名称要与请求参数的name一致，参数值会自动映射匹配。

```java
@RequestMapping("/param")
@ResponseBody
public void param(String uname, int age) {
    System.out.println(uname + ":" + age);
}
```

##### 获取POJO类型参数

Controller中的业务方法的POJO参数的属性名与请求参数的name一致，参数值会自动映射匹配。

```java
/*POJO实体*/
public class User {
    private String uname;
    private String gender;
    private int age;
    getter/setter...
}
/*Controller*/
@Controller
@RequestMapping("/parampojo")
@ResponseBody
public void paramPojo(User user) {
    System.out.println(user.getUname() + ":" + user.getGender() + ":" + user.getAge());
}

```

##### 获取数组类型参数

Controller中的业务方法的数组名称与请求参数的name一致，参数值会自动映射匹配。

```java
@RequestMapping("/paramarray")
@ResponseBody
public void paramArray(String[] arr) {
    System.out.println(Arrays.toString(arr));
}
```

##### 获得集合类型参数

获取集合参数时，要将集合包装到一个POJO中。

```java
/*POJO包装集合参数*/
public class ViewObject {
    private List<User> lu;
    getter/setter
}
/*Controller*/
@RequestMapping(value = "/paramset", method = RequestMethod.POST)
@ResponseBody
public void paramSet(ViewObject vo) {
    System.out.println(vo);
}
```

```jsp
<%--form.jsp--%>
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<%
String path = request.getContextPath();
String basePath = request.getScheme() + "://" + request.getServerName() + ":" + request.getServerPort() + path + "/";
%>
<html>
    <head>
        <base href="<%=basePath%>">
        <title>测试获取集合类型参数</title>
    </head>
    <body>
        <form action="paramset" method="post">
            unmae:<input type="text" name="lu[0].uname"><br>
            gender:<input type="text" name="lu[0].gender"><br>
            age:<input type="text" name="lu[0].age"><br>
            <hr>
            unmae:<input type="text" name="lu[1].uname"><br>
            gender:<input type="text" name="lu[1].gender"><br>
            age:<input type="text" name="lu[1].age"><br>
            <input type="submit" value="submit">
        </form>

    </body>
</html>
```

当使用ajax提交时，可以指定contentType为json形式，在方法参数位置使用@ReqeustBody可以直接接收集合数据而无需使用POJO进行包装。

```jsp
<%--ajax.jsp--%>
<!--引入jquery-->
<script src="js/jquery-3.3.1.js"></script>
<script type="text/javascript">
    var userList = [];
    userList.push({uname: "张三", gender: "Male", age: 19});
    userList.push({uanme: "李四", gender: "Female", age: 20});

    $.ajax({
        type: "POST",
        url: "paramajax",
        data: JSON.stringify(userList),
        contentType: "application/json;charset=utf-8"
    })
</script>
```

```java
/*Controller*/
@RequestMapping(value = "/paramajax", method = RequestMethod.POST)
@ResponseBody
public void paramAjax(@RequestBody List<User> userList) {
    System.out.println(userList);
}
```

需要在`spring-mvc.xml`中需要配置文件资源路径

```xml
<!--配置文件资源路径映射-->
<mvc:resources mapping="/js/*" location="/js/"/>
<!--或者使用-->
<mvc:default-servlet-handler/>
```

##### 参数绑定注解@RequestParam

当请求的参数名称与Controller的业务方法参数名称不一致时，就需要通过@RequestParam注解显式的绑定。

- value：请求参数名称
- required：在指定的请求参数是否必须包括，默认是true，提交时没有此参数则报错
- defaultValue：当没有指定请求参数时，则使用指定的默认值赋值

##### 获取Restful风格的参数

Restful是一种架构风格、设计风格，而不是标准，只是提供了一组设计原则和约束条件。主要用于客户端和服务器交互的软件，基于这个歌风格设计的软件更简洁，更有层次，更易于实现缓存机制。

Restful风格的请求是使用"url+请求方式"表示一次请求的目的，HTTP协议里面四个表示操作方式

- GET：用于获取资源
- POST：用于新建资源
- PUT：用于更新资源
- DELETE：用于删除资源

SpringMVC中使用占位符进行参数绑定，地址/user/1可以携程/user/{id}，占位符{id}对应的就是1的值，在业务方法中可以使用@PathVariable注解进行占位符的匹配获取。

##### 自定义类型转换器

SpringMVC默认已经提供了一些常用的类型转换器，例如客户端提交的字符串转换成int类型进行参数设置。

自定义类型转换器开发步骤

1. 定义转换器类实现Convert接口
2. 在配置文件中声明转换器
3. 在`<annotation-driven>`标签中引用转换器

###### 自定义日期类型转换器

```java
public class DateConverter implements Converter<String, Date> {

    public Date convert(String s) {
        SimpleDateFormat sdf = new SimpleDateFormat("yyyy-MM-dd");
        Date date = null;
        try {
            date = sdf.parse(s);
        } catch (ParseException e) {
            e.printStackTrace();
        }
        return date;
    }
}
/*Controller*/
@RequestMapping(value = "/param/{date}")
@ResponseBody
public void paramDate(@PathVariable(value = "date") Date date) {
    System.out.println(date);
}
```

```xml
<!--配置自定义转换器-->
<bean id="myConverter" class="org.springframework.context.support.ConversionServiceFactoryBean">
    <property name="converters">
        <list>
            <bean class="com.study.converter.DateConverter"/>
        </list>
    </property>
</bean>
```

```xml
<mvc:annotation-driven conversion-service="myConverter"/>
```

##### 获取请求头

使用@RequestHeader注解可以获取请求头信息，相当于request.getHeader(String name)

- value：请求头的名称
- required：是否必须携带此请求头

使用@CookieValue注解可以获取指定Cookie信息

- value：指定cookie的名称
- required：是否必须携带此cookie

##### 文件上传

文件上传客户端三要素

- 表单项 type="file"

- 表单的提交方式时post

- 表单的enctype属性是多部分表单形式，即enctype="multipart/form-data"

  ```jsp
  <form action="upload" method="post" enctype="multipart/form-data">
      文件名:<input type="text" name="filename"><br>
      文件:<input type="file" name="file"><br>
      <input type="submit" value="submit">
  </form>
  ```

###### 文件上传原理

- 当form表单修改为多部分表单时，request.getParameter将失效
- enctype="application/x-www-form-urlencoded"时，form表单正文内容格式是key=value&key=value...
- 当form表单的enctype的取值是multipart/form-data时，请求正文内容为多部分形式

###### 单文件上传步骤

1. 导入fileupload和io坐标
2. 配置文件上传解析器
3. 编写文件上传代码

```xml
<dependency>
    <groupId>commons-fileupload</groupId>
    <artifactId>commons-fileupload</artifactId>
    <version>1.4</version>
</dependency>
<dependency>
    <groupId>commons-io</groupId>
    <artifactId>commons-io</artifactId>
    <version>2.6</version>
</dependency>
```

```xml
<!--配置文件上传解析器-->
<bean id="multipartResolver" class="org.springframework.web.multipart.commons.CommonsMultipartResolver">
    <!--上传文件的编码格式-->
    <property name="defaultEncoding" value="UTF-8"/>
    <!--上传文件总大小-->
    <property name="maxUploadSize" value="5242880"/>
    <!--上传单个文件大小-->
    <property name="maxUploadSizePerFile" value="5242880"/>
</bean>
```

```java
@RequestMapping(value = "/upload")
@ResponseBody
public void uploadFile(String uname, MultipartFile file) throws IOException {
    System.out.println("ParamController.upload");
    // 获取文件名称
    String filename = file.getOriginalFilename();
    System.out.println(uname + "上传了" + filename);
    // 存储
    file.transferTo(new File("D:/upload/" + filename));
}
```

###### 多文件上传

只需要将页面修改为多个文件上传项，将方法参数MultipartFile类型改为MultipartFile[]即可。

```jsp
<form action="upload-multiply" method="post" enctype="multipart/form-data">
    用户名:<input type="text" name="uname"><br>
    文件1:<input type="file" name="file"><br>
    文件2:<input type="file" name="file"><br>
    <input type="submit" value="submit">
</form>
```

```java
@RequestMapping(value = "/upload-multiply")
@ResponseBody
public void uploadMultiplyFile(String uname, MultipartFile[] file) throws IOException {
    System.out.println("ParamController.upload");
    for (MultipartFile multipartFile : file) {
        // 获取文件名称
        String filename = multipartFile.getOriginalFilename();
        System.out.println(uname + "上传了" + filename);
        // 存储
        multipartFile.transferTo(new File("D:/upload/" + filename));
    }
}
```

#### SpringMVC拦截器

拦截器（interceptor）

SpringMVC的拦截器类似于Servlet开发中的过滤器Filter，用于对处理器的**预处理**和**后处理**。

将拦截器按一定的顺序联结成一条链，称为拦截器链（Interceptor Chain）。在访问被拦截的方法或字段时，拦截器链中的拦截器就会按其之前定义的顺序被调用。

拦截器是AOP思想的具体实现

##### 拦截器与过滤器的区别

| 区别     | 过滤器                                                   | 拦截器                                                       |
| -------- | -------------------------------------------------------- | ------------------------------------------------------------ |
| 使用范围 | 过滤器是Servlet规范中的一部分，任何JavaWeb工程都可以使用 | SpringMVC框架，只有使用了SpringMVC框架的工程才能使用         |
| 拦截范围 | 在url-pattern中配置了/*之后，可以对所有要访问的资源拦截  | 只会拦截访问的控制器方法，如果访问的是jsp，html，css，image或者js是不会进行拦截 |

##### 自定义拦截器步骤

1. 创建拦截器类实现HandlerInterceptor接口
2. 配置拦截器
3. 测试拦截器的拦截效果

`HandlerInterceptor`接口源码

```java
public interface HandlerInterceptor {
    // 执行于目标方法之前
    default boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {
        return true;	// true表示放行，false表示不放行
    }
	// 执行于目标方法之后，视图对象返回之前
    default void postHandle(HttpServletRequest request, HttpServletResponse response, Object handler, @Nullable ModelAndView modelAndView) throws Exception {
    }
	// 执行于视图对象返回之后
    default void afterCompletion(HttpServletRequest request, HttpServletResponse response, Object handler, @Nullable Exception ex) throws Exception {
    }
}
```

在`spring-mvc.xml`中配置拦截器

```xml
<mvc:interceptors>
    <mvc:interceptor>
        <mvc:mapping path="/*"/>
        <bean class="自定义的Interceptor实现类全路径"/>
    </mvc:interceptor>
</mvc:interceptors>
```

##### 拦截器方法说明

| 方法名            | 说明                                                         |
| ----------------- | ------------------------------------------------------------ |
| preHandle()       | 该方法在请求处理之前进行调用，其返回值为boolean类型，当它返回false时，表示请求结束，后续的Interceptor和Controller都不会在执行；当它返回true时，会继续调用下一个Interceptor的preHandle方法。 |
| postHandle()      | 该方法实在当前请求进行处理之后被调用，前提是preHandle方法的返回值为true时才能被调用，且它会在DispatcherServlet进行视图返回渲染之前被调用，所以可以在这个方法中对Controller处理之后的ModelAndView对象进行操作。 |
| afterCompletion() | 该方法将在整个请求结束之后，也就是在DispatcherServlet渲染了对应的视图对象之后执行，前提是preHandle方法放行才能被调用。 |

#### SpringMVC异常处理机制

##### 异常处理的思路

系统中异常包括两类：**预期异常**和**运行异常RuntimeException**，前者能通过捕获异常从而获取异常信息，或者主要通过规范代码开发、测试等手段减少运行时异常的发生。

系统的Dao、Service、Controller出现都通过throws Exception向上抛出，最后由SpringMVC前端控制器交由异常处理器进行异常处理。

客户端--请求-->前端控制器--请求-->Controller--请求-->Service--请求-->Dao
处理：前端控制器-->HandlerExceptionResolver

##### SpringMVC异常处理两种方式

- 使用MVC提供的简单异常处理器SimpleMappingExceptionResolver
- 实现Spring的异常处理接口HandlerExceptionResolver自定义异常处理器

###### 简单异常处理器SimpleMappingExceptionResolver

```xml
<!--配置简单映射异常处理器-->
<bean class="org.springframework.web.servlet.handler.SimpleMappingExceptionResolver">
    <!--默认错误视图-->
    <property name="defaultErrorView" value="error"/>
    <!--异常映射-->
    <property name="exceptionMappings">
        <map>
            <entry key="com.study.exception.MyException" value="error"/>
            <entry key="java.lang.ClassCastException" value="error"/>
        </map>
    </property>
</bean>
```

###### 自定义异常处理步骤

- 创建异常处理类实现HandlerExceptionResolver
- 配置异常处理器
- 编写异常页面
- 测试异常跳转

```java
public class MyExceptionResolver implements HandlerExceptionResolver {
    /**
     * @param req  请求
     * @param resp 响应
     * @param o
     * @param e    异常对象
     * @return 要跳转的错误视图信息
     */
    public ModelAndView resolveException(HttpServletRequest req, HttpServletResponse resp, Object o, Exception e) {
        ModelAndView mav = new ModelAndView();
        if (e instanceof MyException) {
            mav.addObject("info", "自定义异常");
            mav.setViewName("my-error");
        } else if (e instanceof ClassCastException) {
            mav.addObject("info", "类型转换异常");
            mav.setViewName("class-cast-error");
        } else {
            mav.setViewName("error");
        }
        return mav;
    }
}
```

```xml
<!--配置自定义异常处理器-->
<bean class="com.study.resolver.MyExceptionResolver"/>
```

