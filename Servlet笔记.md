---
title: Servlet笔记
tags:
  - servlet
categories:
  - notes
  - 后端
abbrlink: bc07bc32
date: 2020-07-11 22:25:05
---

##### 概述

- 狭义的Servlet是指Java语言实现的一个接口

- 广义的Servlet是指任何实现了这个Servlet接口的类

Servlet运行于支持Java的应用服务器中。从原理上讲，Servlet可以响应任何类型的请求，绝大数情况下Servlet只用来扩展基于HTTP协议的Web服务器。

<!--more-->

###### 特点

- 运行在Java的应用服务器上

- Servlet的实现遵循了服务器能够识别的规则，服务器会自动地根据请求调用对应地Servlet进行请求处理

- 简单方便，可移植性强

###### URL与URI

url：http://localhost:8080/servlet/my

- 服务器地址:端口号/webapps下的文件夹的名称[虚拟项目名]/类名全路径[别名]

- 服务器地址:端口号/虚拟项目名/servlet的别名

URI：虚拟项目名/servlet的别名

###### 访问流程

1. 浏览器发送请求到服务器
2. 服务器根据请求URL地址中的URI信息在webapps目录下找到对应的项目文件夹
3. 然后在web.xml中检索响应的Servlet
4. 找到后调用并执行Servlet

##### Servlet的生命周期

 *  从第一次调用到服务器关闭
 *  如果Servlet在web.xml中配置了load-on-startup，生命周期为从服务器启动到服务器关闭

init()方法是对Servlet进行初始化的方法，会在Servlet第一次加载进行存储时执行

destroy()方法是在Servlet被销毁时执行，即服务器关闭时

##### 方法

- service() 可以处理get/post方式的请求，如果Servlet中有service方法会优先调用service方法对请求进行处理

- doGet()  处理get方式请求

-  doPost() 处理post方式请求

  注意:如果在覆写的service方法中又调用了父类的service方法，则service方法处理完成后，会再次根据请求方式响应的doGet和doPost方法执行。所以，一般情况下不在覆写的service方法中调用父类的service方法，避免出现405错误。

#####  Servlet的常见错误

- 404错误：资源未找到
  原因1：在请求地址中的servlet的别名书写错误
  原因2：虚拟项目名称拼写错误
- 500错误：内容服务器错误 java.lang.ClassNotFoundException
  解决：在web.xml中校验Servlet类的全限定路径是否拼写错误
  逻辑错误：因为service方法的代码执行错误导致，根据错误提示对service方法体中的代码进行错误更改
- 405错误：请求方式不支持
  原因:请求方式和Servlet中的方法不匹配所造成
  解决:尽量使用service方法进行请求处理，并且不要再service方法中调用父类的service

##### request对象

request对象中封存了当前的所有请求信息
###### 获取请求头数据

- req.getMethod()              获取请求方式
- req.getRequestURL()          获取请求URL信息

 *      req.getRequestURI()          获取请求URI信息
 *      req.getScheme()              获取协议

###### 获取请求行数据

 *      req.getHeader("键名")          返回指定的请求头信息
 *      req.getHeaderNames()          返回请求头的键名的枚举集合

###### 获取用户数据

 * req.getParameter("键名")        返回指定的用户数据

 * req.getParameterValues("键名")    返回同键不同值的请求数据

 * req.getParameterNames()          返回所有用户请求数据的枚举集合

   如果要获取的请求数据不存在，不会报错，返回null

   注意:request对象由tomcat服务器创建，并作为实参传递给处理请求的Servlet的service方法

```java
@Override
protected void service(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
    // 获取请求头数据
    String method = req.getMethod();    // 获取请求方式
    StringBuffer requestURL = req.getRequestURL();// 获取请求URL
    String requestURI = req.getRequestURI();
    String scheme = req.getScheme();    // 获取请求协议
    System.out.println("method==>" + method + "  URL==>" + requestURL + "  URI==>" + requestURI + "  scheme==>" + scheme);
    // 获取行数据
    String header = req.getHeader("User-Agent");    // 获取指定的请求行信息
    System.out.println(header);
    Enumeration<String> headerNames = req.getHeaderNames(); // 获取所有请求行的枚举
    while (headerNames.hasMoreElements()) {
        String name = headerNames.nextElement();
        String value = req.getHeader(name);
        System.out.println(name + "==>" + value);
    }
    // 获取用户数据
    String uname = req.getParameter("uname");
    String pwd = req.getParameter("pwd");
    System.out.println("uname==>" + uname + "  pwd==>" + pwd);
    String[] favs = req.getParameterValues("fav");
    for (String fav : favs) {
        System.out.println(fav);
    }
}
```

```jsp
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
    <head>
       	<meta charset="UTF-8">
        <title>Title</title>
    </head>
    <body>
        <form action="request" method="post">
            用户名:<input type="text" name="uname" value=""/><br/>
            密码:<input type="text" name="pwd" value=""/> <br/>
            爱好:<br/>
            1<input type="checkbox" name="fav" value="1"><br/>
            2<input type="checkbox" name="fav" value="2"><br/>
            3<input type="checkbox" name="fav" value="3"><br/>
            <input type="submit" value="登录"/>
        </form>
    </body>
</html>
```

##### response对象

 作用：用来响应数据到浏览器的一个对象

使用：

- 设置响应头
  - setHeader(String name,String value) 在响应头中添加响应信息，但同键会覆盖
  - addHeader(String name,String value) 在响应头中添加响应信息，不会覆盖
- 设置响应状态
  sendError(int num,String msg)   自定义响应状态码
- 设置响应实体
  resp.getWriter().write(String str)  响应具体的数据给浏览器
- 设置响应编码格式
  resp.setContentType("text/html;charset=utf-8")

###### service请求处理代码流程

1. 设置响应编码格式         
2. 获取请求数据
3. 处理请求数据
4. 数据库操作（MVC思想）
5. 响应请求处理

##### 请求中文乱码解决

1. 使用String进行数据重新编码
   `uname=new String(uname.getBytes("iso8859-1"), "utf-8");`
2. 使用公共配置
   - get方式
     `req.setCharacterEncoding("utf-8");`
     在tomcat的目录下的conf目录中修改`server.xml`文件,在`Connector`标签中增加属性`useBodyEncodingForURI="true"`
   - post方式
     `req.setCharacterEncoding("utf-8");`

##### Servlet流程总结

1. 浏览器发起请求到服务器
2. 服务器接收浏览器的请求,进行解析，创建request对象存储请求数据
3. 服务器调用对应的Servlet进行请求处理，并将request对象作为实参传递给Servlet的方法
4. Servlet的方法执行进行请求处理
   1. 设置请求编码格式
   2. 设置响应编码格式
   3. 获取请求信息
   4. 处理请求信息
      创建业务层对象
      调用业务层对象的方法
   5. 响应结果处理

##### 请求转发

作用：实现多个Servlet联动操作处理请求，避免代码冗余,让Servlet的职责更加明确

使用：`req.getRequestDispatcher("要转发的地址").forward(req,resp);`
地址：相对路径,直接书写Servlet的别名即可

特点：一次请求，浏览器地址信息不变

注意：请求转发后直接return结束即可

##### request对象的作用域

使用：

- `request.setAttribute(Object name,Object value);`    
- `request.getAttribute(Object obj);`

作用：解决了一次请求内的不同Servlet的数据(请求数据+其他数据)共享问题

作用域：基于请求转发,一次请求中的所有Servlet共享

注意：使用Request对象进行数据流转,数据只在一次请求内有效

特点：

- 服务器创建
- 每次请求都会创建
- 生命周期为一次请求

##### 重定向

解决表单重复提交的问题，以及当前Servlet为无法处理的请求的问题

使用：

- `response.sendRedirect("路径");`
- 本地路径为：URI
- 网络路径为：定向资源的URL信息

特点：

- 两次请求，两个request对象
- 浏览器地址信息改变

时机：

- 如果请求中有表单数据，而数据又比较重要，不能重复提交，建议使用重定向
-  如果请求被Servlet接收后，无法进行处理，建议使用重定向定位到可以处理的资源

##### Cookie

作用：解决发送的不同请求的数据共享问题

###### Cookie的创建和存储

- 创建Cookie对象
  `Cookie cookie=new Cookie(String name,String value);`
- 设置Cookie(可选)
  设置有效期  `cookie.setMaxAge(int seconds);`
  设置有效路径  `cookie.setPath(String uri);`
  响应Cookie信息给客户端 `resp.addCookie(cookie);`

##### Cookie的获取

- 获取Cookie信息数组
  `Cookie[] cookies=req.getCookies();`

- 遍历数组获取Cookie信息
  使用for循环遍历

  ```java
  if(cookies!=null) {
      for (Cookie c : cookies) {
          String name = c.getName();
          String value = c.getValue();
          System.out.println(name + ":" + value);
      }
  }
  ```

注意：一个Cookie对象存储一条数据，多条数据，可以创建多个Cookie对象进行存储。

###### 特点

- Cookie是浏览器端的数据存储技术
- 存储的数据声明在服务器端
- 临时存储:存储在浏览器的运行内存中，浏览器关闭即失效
- 定时存储:设置了Cookie的有效期，存储在客户端的硬盘中，在有效期内符合路径要求的请求都会附带信息
- 默认Cookie信息存储好之后，每次请求都会附带，除非设置有效路径

##### Session

问题：一个用户的不同请求处理的数据共享

###### 原理

用户第一次访问服务器，服务器会创建一个session对象给此用户，并将该session对象的JSESSIONID使用Cookie技术存储到浏览器中，保证用户的其他请求能够获取到同一个session对象，也保证了不同请求能够获取到共享的数据。

###### 特点

1. 存储在服务器端
2. 服务器进行创建
3. 依赖Cookie技术
4. 一次会话
5. 默认存储时间是30分钟

###### 作用

解决了一个用户不同请求处理的数据共享问题

###### 使用

创建session对象/获取session对象`HttpSession hs = req.getSession();`

- 如果请求中拥有session的标识符JSESSIONID，则返回其对应的session对象。
- 如果请求中没有session的标识符JSESSIONID，则创建新的session对象，并将其JSESSIONID作为cookie数据存储到浏览器端。
- 如果session对象失效了，也会重新创建一个session对象，并将其JSESSIONID存储在浏览器内存中。

设置session存储时间`hs.setMaxInactiveInterval(int seconds);`

注意：在指定的时间内session对象没有被使用则销毁，如果使用了则重新计时。

设置session强制失效`hs.invalidate();`

存储和获取数据

- 存储：`hs.setAttribute(String name,Object object);`

- 获取：`hs.getAttribute(String name);` 返回的数据类型为Object

  注意:存储的动作和取出的动作发生在不同的请求中，但是存储要先与取出执行。

使用时机：一般用户在登录web项目时会将用户的个人信息存储到session中，供该用户的其他请求使用。

###### 总结

session解决了一个个用户的不同请求的数据共享问题，只要在JSESSIONID不失效的情况下，用户的任意请求在处理时都能获取到同一个session对象

###### 作用域

- 一次会话
- 在JSESSIONID和session对象不失效的情况下为整个项目内

###### session失效处理

将用户请求中的JSESSIONID和后台获取到的session对象进行比对时

- 如果一致，则session没有失效，
- 如果不一致则说明session失效了，重定向到登录页面，让用户重新登录

注意： JSESSIONID存储在了Cookie的临时存储空间中，浏览器关闭即失效

##### 模拟登录案例问题

解决主页面用户名显示为null问题

原因：因为在用户登录成功后使用重定向显示主界面，两次请求，而用户的信息在第一次请求中，第二次请求中没有用户数据，所以显示为null

解决：使用session技术

使用ServletContext对象完成网页计数器：在用户登录校验中创建计数器并自增，然后存储到ServletContext对象中，在主页面里取出计数器数据显示给用户。

##### ServletContext对象

解决不同用户使用相同的数据

特点

- 服务器创建
- 用户共享

生命周期：服务器启动到服务器关闭

在web.xml中配置

```xml
<!--配置全局数据-->
<context-param>
    <param-name>name</param-name>
    <param-value>zhangsan</param-value>
</context-param>
<servlet>
    <servlet-name>context</servlet-name>
    <servlet-class>com.web.servlet.ServletContextServlet</servlet-class>
</servlet>
<servlet-mapping>
    <servlet-name>context</servlet-name>
    <url-pattern>/context</url-pattern>
</servlet-mapping>
```

使用

- 获取ServletContext对象

  ```java
  ServletContext sc1 = this.getServletContext();	// this对象直接获取
  ServletContext sc2 = this.getServletConfig().getServletContext(); // 通过ServletConfig获取
  ServletContext sc3 = req.getSession().getServletContext(); // 通过Session获取
  ```

- 使用ServletContext对象完成数据共享

  - 数据存储`sc.setAttribute(String name,Object value);`

  - 数据获取`sc.getAttribute(String name);` 返回的是**Object**类型

    注意：不同的用户可以给ServletContext对象进行数据存取，获取的数据不存在返回**null**

- 获取项目中web.xml文件的全局配置数据
  `sc.getInitParameter("name");`    返回**String**类型，如果数据不存在返回**null**
  `sc.getParameterNames();` 返回键名的**枚举**
  配置方式一组`<context-param>`标签只能存储一组键值对数据，多组可以声明多个`<context-param>`进行存储。

  ```xml
  <context-param>
      <param-name></param-name>
      <param-value></param-value>
  </context-param>
  ```

  作用：将静态数据和代码进行解耦

- 获取项目web下的资源的绝对路径
  `String path = sc.getRealPath(String path);`
  获取的路径为项目根目录，参数为项目根目录中的路径

- 获取web下的资源的流对象
  `InputStream is = sc.getResourceAsStream("doc/1.txt");`
  注意：此种方式只能获取<font color='red'>项目根目录</font>下的资源流对象，class文件的流对象需要使用类加载器获取

##### ServletConfig对象

解决在web.xml中给每个Servlet单独配置的数据

在web.xml中配置

```xml
<servlet>
    <servlet-name>sg</servlet-name>
    <servlet-class>com.web.servlet.ServletConfigServlet</servlet-class>
    <init-param>
        <param-name>config</param-name>
        <param-value>utf-8</param-value>
    </init-param>
</servlet>
<servlet-mapping>
    <servlet-name>sg</servlet-name>
    <url-pattern>/sg</url-pattern>
</servlet-mapping>
```

使用：

 *      获取ServletConfig对象	 `ServletConfig sc = this.getServletConfig();`
 *      获取web.xml中的配置数据		`String config = sc.getInitParameter("config");`

##### web.xml文件

作用：存储项目相关配置信息，保护Servlet；解耦一些数据对程序的依赖。

使用位置

- 每个web项目中
- Tomcat服务器中（在服务器目录conf目录中）

区别

- Web项目下的web.xml文件为局部配置，针对本项目的位置
- Tomcat下的web.xml文件为全局配置，配置公共信息

内容（核心组件）

- 全局上下文配置（全局配置参数）[ServletContext、context-param]
- Servlet配置
- 过滤器配置[filter]
- 监听器配置[listener]

***加载顺序***：Web容器会按<font color='red'>ServletContext->context-param->listener->filter->servlet</font>顺序加载组件，这些元素可配置在`web.xml`文件中的任意位置。

加载时机：服务器启动时

##### Server.xml文件核心组件

Tomcat的`server.xml`结构

```xml
<Server>
    <Service>
        <Connector/>
        <Connector/>
        <Engine>
            <Host>
                <Context/>
            </Host>
        </Engine>
    </Service>
</Server>
```

热部署：`<Context path="/项目虚拟名" reloadable="true" docBase="项目路径"/>`

开发时使用冷部署，需要重启服务器。

##### 过滤器

问题引入：Servlet的作用是针对浏览器发起的请求，进行请求的处理。通过Servlet技术可以灵活的进行请求的处理，但是我们不但要对请求进行处理，还需要对服务器的资源进行统一的管理，比如请求编码格式的统一设置，资源的统一分配等等，如何解决？

解决：使用过滤器

作用：对服务器资源进行管理、保护Servlet。

过滤器生命周期：<font color='red'>从服务器启动到服务器关闭</font>

###### 使用

- 创建一个实现了Filter接口的普通java类

- 覆写接口的方法

  - init()方法:服务器启动即执行，资源初始化

  - doFilter()方法:拦截请求，在此方法中可以对资源实现管理

    注意:需要手动对请求进行放行

  - destroy()方法:服务器关闭时执行

- Filter过滤器的配置：在web.xml中配置过滤器

  ```xml
  <filter>
      <filter-name>mf</filter-name>
      <filter-class>com.web.filter.MyFilter</filter-class>
  </filter>
  <filter-mapping>
      <filter-name>mf</filter-name>
      <url-pattern>/*</url-pattern>
  </filter-mapping>
  ```

  注解的形式：`@WebFilter(filterName = "MyFilter", urlPatterns = {"/*"})`

- 注意

  - url-pattern:/*  表示拦截所有的请求
  - url-pattern:*.do    表示所有以.do结尾的请求，一般是用来进行模块拦截处理
  - url-pattern:/ServletUrlPattern    表示拦截指定url的请求，针对某个Servlet的请求拦截，保护Servlet

###### 执行流程

浏览器发起请求到服务器，服务器接收到请求后根据URI信息在`web.xml`中找到对应的过滤器执行`doFilter()`方法，该方法对此次请求进行处理后如果符合要求则放行，放行后如果还有符合要求的规律其则继续进行过滤，找到对应的Servlet进行处理。Servlet对请求处理完毕后，也就是`service()`方法结束了。还需要继续返回相应的`doFilter()`方法继续执行。
###### 案例

- 统一管理字符编码
- 统一编码格式设置
- session管理
- 权限管理
- 资源管理(统一水印，和谐词汇等)

##### 监听器

###### 问题引入

在Servlet技术中学习了request、session、application作用域对象，其主要作用是实现数据在不同的场景中的灵活流转。但是数据的具体流转过程是无法看到的，比如作用域对象是什么时候创建和销毁的，数据是什么时候存取，改变和删除的。因为具体的流转过程是不可见的，所以无法在指定的实际对数据和对象进行操作，比如session销毁的时候，在线人数-1。

解决：使用监听器Listener

###### 概念

Servlet监听器时Servlet规范中定义的一种特殊类，用于监听ServletContext、HttpSession和ServletReqeust等域对象的创建与销毁事件，以及监听这些域对象中属性发生修改的事件。

监听对象

- Request
- Session
- Application

监听内容：创建、销毁、属性改变事件

监听作用：在事件发生之前，之后进行一些处理，比如统计在线人数

###### 使用

1、创建一个实现了指定接口的java类

监听request

- 监听request-->ServletRequestListener  监听request对象的创建和销毁

  - requestInitialized(ServletRequestEvent sre) 创建
  - requestDestroyed(ServletRequestEvent sre)   销毁

  注意：形参获取监听的request对象  sre.getServletRequest()

- 监听request-->ServletRequestAttributeListener 监听request作用域数据的增加、修改、删除

  - attributeAdded(ServletRequestAttributeEvent srae)
  - attributeReplaced(ServletRequestAttributeEvent srae)
  - attributeRemoved(ServletRequestAttributeEvent srae)

  注意：形参可以获取被监听的数据

  - srae.getName()  获取监听数据的键名
  - srae.getValue()  获取监听数据的值

监听session

- 监听session-->HttpSessionListener 监听session的创建和销毁

  - sessionCreated(HttpSessionEvent se)
  - sessionDestroyed(HttpSessionEvent se)

  注意:形参可以获取被监听的session对象 se.getSession();

- 监听session-->HttpSessionAttributeListener 监听session数据的创建和销毁

  - attributeAdded(HttpSessionBindingEvent se)
  - attributeReplaced(HttpSessionBindingEvent se)
  - attributeRemoved(HttpSessionBindingEvent se)

  注意:形参可以获取被监听的数据

  - se.getName()    获取数据的键名
  - se.getValue()   获取数据的值

监听application

-   监听application-->ServletContextListener  监听application对象的初始化和销毁

  - contextInitialized(ServletContextEvent sce) 初始化 服务器启动
  - contextDestroyed(ServletContextEvent sce)   销毁  服务器关闭

  注意:形参可以获取当前application对象
  sce.getApplication()

- 监听application-->ServletContextAttributeListener

  - attributeAdded(ServletContextAttributeEvent scae)
  - attributeReplaced(ServletContextAttributeEvent scae)
  - attributeRemoved(ServletContextAttributeEvent scae)

  注意:形参可以获取被监听的数据

  - scae.getName()  获取数据的键名
  - scae.getValue() 获取数据的值

2、在web.xml中配置监听类(可配置多个监听器)

```xml
<listener>
    <listener-class>com.web.listener.MyListener</listener-class>
</listener>
```

注解形式：`@WebListener(value = "/ml")`

###### 案例

统计网站在线人数

