---
title: Jsp-EL表达式-JSTL笔记
tags:
  - jsp
  - el
  - jstl
categories:
  - notes
  - 后端
abbrlink: 324a571
date: 2020-07-12 10:45:37
---

### Jsp

#### 概念

Java Server Pages，Java服务器页面

简化的Servlet设计

动态网页技术标准（数据的动态改变）

##### 特点

- 本质上是Servlet
- 跨平台
- 组件跨平台
- 健壮性和安全性（业务逻辑，数据）

##### 访问原理

Tomcat只能识别Servlet，用户访问jsp文件，服务器用JspServlet将jsp文件转义成对应的Java文件，返回给用户

```xml
<servlet>
    <servlet-name>jsp</servlet-name>
    <servlet-class>org.apache.jasper.servlet.JspServlet</servlet-class>
    <init-param>
        <param-name>fork</param-name>
        <param-value>false</param-value>
    </init-param>
    <init-param>
        <param-name>xpoweredBy</param-name>
        <param-value>false</param-value>
    </init-param>
    <load-on-startup>3</load-on-startup>
</servlet>
<servlet-mapping>
    <servlet-name>jsp</servlet-name>
    <url-pattern>*.jsp</url-pattern>
    <url-pattern>*.jspx</url-pattern>
</servlet-mapping>
```

##### 使用

<font color='blue'>JSP负责页面展现，Sevlet负责业务逻辑处理</font>

#### Jsp的语法和指令

Jsp三大指令

- page指令：配置jsp文件的转译相关的参数
- include指令：静态引入jsp文件
- taglib指令：引入jstl标签库

##### jsp的三种注释

前端语言注释(html,css,js)：会被转译，也会被发送，但是不会被浏览器执行

java语言注释(行注释，块注释，文档注释)：会被转译，但是不会被servlet执行

jsp注释：不会被转译

##### jsp的page指令

 `<@ page attribute1="value" attribute2="value" ...%>`

作用：配置jsp文件的转译相关的参数

- language:声明jsp要被转译的语言
- import:声明转译java的java文件要导入的包，不同的包使用逗号隔开
- pageEncoding:设置jsp文件的数据编码格式
- contentType:设置jsp数据响应给浏览器时，浏览器的解析和编码格式
- session:设置转译的Servlet是否开启session支持，默认开启，true开启，false关闭
- errorPage:设置jsp运行错误跳转的页面
- extends:设置jsp转译的java文件要继承的父类（包名+类名）

##### jsp的局部代码块

特点

- 布局代码块中声明的java代码会被原样转译到jsp对应的Servlet文件的_JspService方法中
- 代码块中声明的变量都是局部变量

使用：`<%java代码块%>`

缺点：使用局部代码块在jsp中进行逻辑判断，书写麻烦，阅读困难

开发：使用Sevlet进行请求逻辑处理，使用jsp进行页面展现

##### jsp的全局代码块

特点：声明的java代码作为全局代码转译到对应的Servlet类中

使用：`<%!java全局代码块%>`

<font color='red'>注意：全局代码块声明的代码，需要使用局部代码块调用</font>

##### jsp的脚本段语句

特点：帮助开发人员快速地获取变量或者方法的返回值作为数据响应给浏览器    

使用：`<%=变量名或者方法%>`    <===>   out.write(变量名或方法);

<font color='red'>注意：不要在变量名或者方法后使用分号</font>

位置：除jsp语法要求以外的任意位置

##### jsp的静态引入和动态引入

###### 静态引入

 `<%@include file="jsp文件相对地址" %>`

特点：

- 会将引入的jsp文件和当前jsp文件转译成一个java（Servlet）文件使用
- 在网页中也就显示了合并后的显示效果

注意：

- 静态引入的jsp文件<font color='red'>不会</font>单独转译成java（Servlet）文件
- 同名变量    编译报错 Duplicate local variable
- 当前文件和静态引入的jsp文件中<font color='red'>不能</font>使用java代码块声明同名变量

###### 动态引入

`<jsp:include page="jsp文件相对地址"/>`

特点：

- 会将引入的jsp文件单独转译，在当前文件转译好的java文件中调用引入的jsp文件的转译文件
- 在网页中显示合并后的显示效果

注意：动态引入<font color='red'>允许</font>文件中声明同名变量

优点：降低jsp代码的冗余，便于维护升级

##### jsp的转发标签

使用：`<jsp:forward page="jsp文件相对地址"></jsp:forward>`	jsp文件<font color='red'>相对地址</font>

特点

- 一次请求
- 地址栏信息不变

注意：<font color='red'>在转发标签的两个标签中间除了写</font>`<jsp:param name="" value=""/>`<font color='red'>子标签不会报错，其他任意字符都会报错</font>
`<%=request.getParameter("键名")%>`
name属性为附带的数据的键名
value属性为附带的数据的内容
会将数据以?的形式在转发路径的后面

```jsp
<jsp:forward page="要转发的jsp文件的相对路径">
    <jsp:param name="str" value="a"/>
</jsp:forward>
```

#### jsp内置对象

##### 内置对象

jsp文件在转译其对应的Servlet文件的时候自动生成的并声明的对象，可以在jsp页面中直接使用。

##### 注意

内置对象在jsp页面中使用，使用局部代码块或者脚本段语句来使用，不能够在全局代码块中使用。

##### 内容

###### PageContext对象

页面上下文对象，封存了其他内置对象。封存了当前jsp的运行信息

注意：每个jsp文件单独拥有一个PageContext对象

作用域：当前页面

###### Request对象

封存当前请求数据的对象，由Tomcat服务器创建，作用域为一次请求。

```jsp
<%
// 获取请求数据
String s = request.getParameter("str");
request.getAttribute("str");
%>
```

###### Session对象

存储用户的不同请求的共享数据，作用域为一次会话

###### Application对象

ServletContext对象，一个项目只有一个，存储用户共享数据的对象，以及完成其他操作，项目内

###### Response对象

响应对象，用来响应请求处理结果给浏览器的对象，设置响应头，重定向。

```jsp
<%
    response.sendRedirect("forward.jsp");
%>
```

###### Out对象

响应对象，jsp内部使用，带有缓冲区的响应对象，效率高于response对象

###### Page对象

代表当前jsp对象，相当于java的this

###### Exception对象

异常对象，存储了当前运行的异常信息

注意：使用此对象需要在page指定使用属性isErrorPage="true"开启

###### Congfig对象

ServletConfig对象，主要用来获取web.xml中的配置数据，完成一些初始化数据据的读取

###### 四个作用域对象

pageContext：当前对象，解决了在当前页面的数据共享问题。获取其他内置对象

request:一次请求，一次请求的servlet的数据共享。通过请求转发将数据流转给下一个Servlet

session:一次会话，一个用户的不同请求的数据共享。将数据从一次请求流转给其他请求

application:项目内，不同用户的数据共享问题。将数据从一个用户流转给其他用户

作用：数据流转

#### jsp资源路径

在jsp中资源路径可以使用相对路径完成跳转，存在的问题

问题一：资源的位置不可随意更改

问题二：需要使用`../`进行文件夹的跳出，使用比较麻烦

##### 使用绝对路径

 `/`表示服务器根目录即localhost:8080/

/虚拟项目名/项目资源路径

举例：项目虚拟名为jsp	

资源文件路径webapp/jsp/a/a.jsp	`<a href="/jsp/a/a.jsp">a.jsp</a>`

webapp/jsp/jsppro.jsp	`<a href="/jsp/jsppro.jsp">jsppro.jsp</a>`

##### 使用jsp中自带的全局路径声明

```jsp
<%
String path = request.getContextPath();
String basePath = request.getScheme() + "://" + request.getServerName() + ":" + request.getServerPort() + path + "/";
%>
<html>
    <head>
        <base href="<%=basePath%>">
    </head>   
    <body>
    </body>
</html>
```

作用：给资源前面添加项目路径http://127.0.0.1:8080/虚拟项目名/

### EL表达式

问题引入

 * Servlet进行请求处理后，使用作用域对象作为数据流转的载体，将数据流转给对应的jsp文件
 * 在jsp中获取作用域中的数据

传统方式：在jsp页面中使用java脚本段语句

传统方式获取作用域数据

- 缺点一：要通过`<%@ page import=""%>`导入包
- 缺点二：需要强制转换对象
- 缺点三：获取数据的代码过于麻烦

ElServlet.java

```java
@WebServlet(name = "ElServlet", urlPatterns = {"/es"})
public class ElServlet extends HttpServlet {
    @Override
    protected void service(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        // 设置请求编码
        req.setCharacterEncoding("utf-8");
        // 设置响应编码
        resp.setContentType("text/html;charset=utf-8");
        // 获取请求信息
        String uname = req.getParameter("uname");
        String pwd = req.getParameter("pwd");
        // 处理请求信息
        System.out.println(uname + "===" + pwd);
        // 响应处理结果
        // 使用request作用域进行数据流转
        // 普通字符串
        req.setAttribute("str", "今天天气很好!");
        // 对象类型
        User u = new User(0, "张三", "跑步", new Address("湖北", "黄冈", "红安县"));
        req.setAttribute("user", u);
        // 集合类型
        // List集合
        // 存储普通字符串
        List<String> list = new ArrayList<>();
        list.add("周润发");
        list.add("刘德华");
        list.add("周星驰");
        req.setAttribute("list", list);
        // 存储对象
        User u2 = new User(1, "李四", "游泳", new Address("湖北", "武汉", "武昌区"));
        List<User> lu = new ArrayList<>();
        lu.add(u);
        lu.add(u2);
        req.setAttribute("lu", lu);
        // Map集合
        // 存储普通字符串
        Map<String, String> map = new HashMap<>();
        map.put("a", "北京");
        map.put("b", "上海");
        map.put("c", "广州");
        map.put("d", "深圳");
        req.setAttribute("map", map);
        // 存储对象
        Map<String, User> mu = new HashMap<>();
        mu.put("a", new User(2, "张家辉", "演戏", new Address("广东省", "广州市", "中山区")));
        req.setAttribute("mu", mu);
        // 空值判断
        req.setAttribute("s0","");
        req.setAttribute("s1",new User());
        req.setAttribute("s2",new ArrayList<>());
        req.setAttribute("s3",new HashMap<>());
        
        req.getRequestDispatcher("/el.jsp").forward(req, resp);
        return;
    }
}
```

相关bean类

```java
public class Address {
    private String pre;
    private String city;
    private String town;
	getter/setter...
}
public class User {
    private int uid;
    private String uname;
    private String fav;
    private Address addr;
    getter/setter...
}
```

jsp文件

```jsp
<b><%=request.getParameter("uname")%></b><br>
<b><%=request.getAttribute("str")%></b><br>
<b><%=((User) request.getAttribute("user")).getAddr().getTown()%></b><br>
<b><%=((ArrayList) request.getAttribute("list")).get(1)%></b><br>
<b><%=((User) ((ArrayList) request.getAttribute("lu")).get(0)).getAddr().getPre()%></b><br>
<b><%=((ArrayList<User>) request.getAttribute("lu")).get(1).getAddr().getPre()%></b><br>
<b><%=((HashMap) request.getAttribute("map")).get("b")%></b><br>
<b><%=((HashMap<String, User>) request.getAttribute("mu")).get("a").getAddr().getTown()%></b>
```

#### 概述

全称：Expression Language，一种写法非常简洁的表达式，语法简单易懂，便于使用，表达式语言的灵感来自于ESMAScript和XPath表达式语言。

#### 作用

让jsp书写起来更加的方便，简化在jsp中获取作用域或者请求数据的写法，也会搭配JSTL来进行使用

#### 使用

语法结构：$(expression)提供`.`和`[]`两种运算符来存取数据	

##### 使用EL表达式获取请求数据

获取用户请求数据 \${requset.attribute}

获取请求头数据

- \${header}   返回所有的请求头数据
- \${header["键名"]}   返回指定键名的请求头数据
- \${headerValues["键名"]} 返回指定得键名(同键不同值)

```jsp
${header}
${header["User-Agent"]}
${headerValues["Accept-Language"][0]}
```

获取Cookie数据

- \${cookie}   返回存储了所有的cookie对象的map集合
- \${cookie.键名}    返回指定的cookie对象
- \${cookie.键名.name}   返回指定的cookie对象存储的数据的键名
- \${cookie.键名.value}   返回指定的cookie对象存储的数据的值

```jsp
${cookie}
${cookie.JSESSIONID.value}
```

##### 使用EL表达式获取作用域数据

###### 获取作用域数据

- request对象存储了请求数据    param.键名         返回的是值
- request对象存储了请求数据    paramValues.键名   返回的是数组

通过setAttribute方法存储到作用域对象中的数据

- \${键名}   返回键名所对应的值

注意

- 如果存储的是普通字符串则直接返回
- 如果存储的是对象，则返回的是对象

获取对象中的数据

- 普通对象:${键名.属性名.属性名...}
- 集合对象:
  - list集合\${键名[角标]}
  - map集合\${键名.map集合存储的键名}

###### 作用域查找顺序

默认查找顺序：pageContext-->reqeust-->session-->application

<font color='red'>注意：每次查找都从小到大进行查找，找到了则获取，不再继续找了</font>

###### 获取指定作用域的数据

- \${requestScope.键名}
- \${pageScope.键名}
- \${sessionScope.键名}
- \${applicationScope.键名}

##### 使用EL表达式进行运算

算术运算\${算术表达式}

关系运算\${关系表达式}

逻辑运算\${逻辑表达式}

三目运算\${三目表达式}

<font color='red'>注意：</font>`+`<font color='red'>表示加法运算，不表示字符连接，使用EL表达式进行字符连接会报错</font>

```jsp
1+3=>${1+3}<br>
1*2=>${1*2}<br>
1/2=>${1/2}<br>
4-2=>${4-2}<br>
4%2=>${4%2}<br>
4==3=>${4==3}<br>
4>2=>${4>2}<br>
1==1?'男':'女'=>${1==1?'男':'女'}<br>
1+"2"=>${1+"2"}
```

##### 使用EL表达式控制判断empty

${empty 键名}

作用：判断键名对象的值是否存有数据

### JSTL标签库

#### 概述

JSTL是apache对EL表达式的扩展（JSTL依赖于EL），JSTL是标签语言。JSTL标签使用起来非常方便，它与JSP动作标签一样，只不过是它不是JSP内置的标签，需要导包，以及指定标签库。

使用MyEclipse开发JavaWeb，在项目发布到Tomcat时，MyEclipse会在lib目录下存放jstl的jar包。如果没有使用MyEclipse开发需要手动导入jar包。Maven项目需要导入JSTL的相关依赖。

#### 作用

用来提升在JSP页面的逻辑代码的编码效率，使用标签来替换逻辑代码的直接书写，高效、美观、整洁、易读。

#### 内容

- 核心标签库
- 格式化标签库
- JSTL的SQL标签库
- JSTL的函数标签库
- JSTL的XML标签库

菜鸟教程-JSTL：[https://www.runoob.com/jsp/jsp-jstl.html](https://www.runoob.com/jsp/jsp-jstl.html)

##### JSTL的核心标签库

1. 导入jar包

2. 声明jstl标签库的引入(核心标签库)

   ```jsp
   <%@taglib prefix="c" uri="http://java.sun.com/jstl/core" %>
   <%@taglib prefix="c" uri="http://java.sun.com/jstl/core_rt" %>
   ```

###### 基本标签

`<c:out value="数据" default="默认值"/>`

数据可以作为常量值也可以是EL表达式

作用：将数据输出给客户端

`<c:set var="hello" value="hello pageContext" scope="page"/>`

作用：存储数据到作用域对象中

- var:表示存储的键名
- value:表示存储的数据
- scope:表示要存储的作用域对象 page request session application

`<c:remove var="hello" scope="request"/>`

作用：删除作用域中指定键的数据

- var:表示要删除的键的名字
- scrope:表示要删除的作用域(可选)
- <font color='red'>注意：如果在不指定作用域的情况使用该标签删除数据，会将四个作用域对象中的符合要求的数据全部删除</font>

###### 逻辑标签

```jsp
<c:if test="\${表达式}">
    前端代码
</c:if>
```

作用：进行逻辑判断，相当于java代码的单分支判断。

注意：逻辑判断标签需要依赖于EL的逻辑运算，也就是表达式中涉及到的数据必须从作用域中拿。

```jsp
<c:choose>
    <c:when test="">执行内容</c:when>
    ...
    <c:otherwise>执行内容</c:otherwise>
</c:choose>
```

作用:用来进行多条件逻辑判断，类似java中的多分支语句。

注意：条件成立只会执行一次，都不成立则执行otherwise。

###### 循环标签

```jsp
<c:forEach begin="1" end="4" step="1">
    循环体
</c:forEach>
```

作用：循环内容进行处理

- begin：声明循环开始位置

- end：声明循环结束位置

- step：设置循环步长

- varStatus：声明变量记录每次循环的数据(角标、次数，是否是第一次循环，是否是最后一次循环)

  注意:数据存储在作用域中，需要使用EL表达式获取

  例如：\${vs.index}--\${vs.count}--​\${vs.first}--​\${vs.last}

- items：声明要遍历的对象，结合EL表达式获取对象

- var：声明变量记录每次循环的结果，存储在作用域中，通过EL表达式获取对象

数据

```jsp
<%
ArrayList<String> list = new ArrayList<>();
list.add("a");
list.add("b");
list.add("c");
request.setAttribute("list", list);

HashMap<String, String> map = new HashMap<>();
map.put("hello", "Hello");
map.put("world", "world");
request.setAttribute("map", map);
%>
```

```jsp
<%--常量循环--%>
<c:forEach begin="1" end="4" step="1" varStatus="vs">
    ${vs.index}--${vs.count}--${vs.first}--${vs.last}<br>
</c:forEach>
<%--动态循环--%>
<c:forEach items="${list}" var="varlist">
    ${varlist}<br>
</c:forEach>

<%--JSTL方式创建表格--%>
<table border="1px">
    <tr>
        <td>字段名</td>
    </tr>
    <c:forEach items="${list}" var="s">
        <tr>
            <td>${s}</td>
        </tr>
    </c:forEach>
</table>
<%--遍历map集合--%>
<c:forEach items="${map}" var="m">
    ${m.key}:${m.value} <br>
</c:forEach>
```