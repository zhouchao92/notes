---
title: Web技术复习笔记大纲
categories:
  - 后端
abbrlink: 71c32ee5
date: 2020-07-08 08:54:33
tags:
  - html
  - css
  - javascript
  - http
  - servlet
  - jsp
  - el
  - jstl
---

##### C/S架构与B/S架构

C/S架构：client/server

- socket、serversocket、awt/swing做一个客户端软件
- 建好socket连接后，通过io流来交换，数据格式开发者自定义绑定

B/S架构：browser/server

- 浏览器也是一种客户端软件
- HTTP协议定义了浏览器和服务器通讯的基本规则之一

浏览器和服务器之间的交互

- 交互模式：请求-响应
- 客户端先发出请求，服务器根据请求内容发送响应的内容，客户端根据响应内容进行显示

<!--more-->

###### 客户端发出请求的格式

1. 请求行：服务器的地址、端口、资源地址

2. 请求头

3. 请求实体

   <font color="red">注：客户端向服务器传参，如果将参数放到请求行，即为*get*方式，如果将参数放在实体中，即为*post*请求</font>

###### 服务器收到请求内容后发送响应内容

- 响应内容定义
  - 状态行 200、404、500
  - 响应行
  - 响应实体

###### 客户端收到响应内容后，显示内容

- HTML超文本标记语言
- HTML元素动态 javascript（livescript）
- 控制页面元素 css Cascading Style Sheets层叠样式表

###### 服务器根据请求内容发送响应的内容

1. 收请求内容
   1. 建立监听，ServletSocket s.accept()
   2. 启动一个线程（轻量级进程），为请求的客户提供服务
   3. 为了封装客户端提供的请求信息，定义一个对象：HttpServletRequest
   4. 为了封装回送给服务端的相应内容，定义一个对象：HTTPServletResponse
2. 根据请求内容调用相应的程序（Servlet）
   1. 通过web.xml中定义的url和Servlet的关系，找到对应的Serlvet程序
   2. 定义了HttpServlet抽象类，service(HttpServletRequset req,HttpServletResponse resp)方法
   3. 将response对象中的信息，转译成基于http协议的响应内容，再将这些响应内容通过socket传送给客户端
      为了统一处理上述流程，使用Tomcat服务器，也成为web容器，Servlet容器

###### 规范、增强Servlet程序

- 浏览器发送给服务器的请求内容	HttpServletRequest对象
- 服务器回送给浏览器的相应内容	HttpServletResponse对象
- Serlvet类读取web.xml中自己的配置信息	ServletConfig
- 需要读取web.xml中的公共信息	\<context-param>	ServletContext
- 将所有Servlet放入一个容器中统一管理		ServletContext
- 保存以前操作的状态	客户端保存Cookie 	服务器保存Session
  Servlet擅于业务逻辑处理，不擅于页面展示的代码处理	改进Servlet--->JSP
  统一的转译程序，将jsp文件内容转译成java类代码，产生了jsp引擎（一般服务器自带了这个功能）

###### ajax异步模式局部刷新效果

XmlHttpRequest

通过增加XMLHttpRequet对象，使用XHR对象发送请求，获得数据后，也是先传给XHR对象，再通过js代码操作dom模型，实现局部刷新效果。

##### HTML

###### 非表单标签

- b粗体	u下划线	i斜体	del删除效果
- a 超链接 href 、target 
- img 图片
- frameset 框架 （frame）框架集
- table表格 th、tr、td（单元格table data cell）colspan、rowspan单元格合并
- ul li ol 列表标签
- embed 用来播放MP3、视频等
- div 虚拟矩形区域	span 行内元素（不会换行）	

###### 表单标签

- form表单标签 action提交表单的地址 method表单提交的方式（） name（命名方式：regFrm）
- 表单域（表单域一定要有name属性，否则该表单域的值不会被提交）
  - 文本域 text单行文本域、password密码框、textarea多行文本域
  - radio单选按钮（组，name属性一致即为一组，每组只能提交一个值）
  - chcekbox复选框（组，name属性一致即为一组，每组恶意体骄傲多个值）
  - select option下拉框（可以实现多选一，多选多）
  - file 文件域，用来实现文件上传（method=past,enctype="multipart/form-data"）
  - hidden 隐藏域（不需要用用户输入，但是服务器又需要的值）
  - reset 重置
  - submit 提交按钮
  - button 普通按钮（通常用来激活一个js函数）		

##### CSS

- 引用css信息
  - 通过html元素的style属性，class属性
  - 通过\<style>块
  - 通过引入外部的css文件\<link>
- css选择器
  - id选择器	#id名字
  - class选择器 .类名
  - 元素选择器
- css属性
  - 文本属性
  - 定位属性	静态定位、相对定位、绝对定位、固定定位、z-index
  - 列表属性
  - 布局属性
  - 边框属性

##### JavScript

###### js的基本语法

- 基本数据类型	undefined、null、number、字符串、Boolean、Object
- 变量定义 var
- 控制语句
- 内置语句 eval()、escape()、unescape()
- 常用的对象	Date、Math、String、RegExp
- 数组 var a=[];	var b=new Array();
- 对象的定义
- JSON:	声明：var user=(name:"",pwd:"",sleep:function(){});	使用：user.sleep;
- 函数也是对象
- 继承是通过prototype属性来模拟	String prototype.trim=function(){return this.replace("(^\s+)||(\s+$)","")} 	" 123 ".trim()
- 对话框	alert警告框	prompt询问框 confirm确认框
- 浏览器对象
  - window
    - open 打开子窗口	close关闭窗口
    - 定时操作	setTimeout,clearTimeout	setInterval,clearInterval
  - navigator：浏览器信息
  - history：访问历史	history.go(1)			
  - location：地址栏信息

###### dom操作

- 获取节点

  - 直接获得
    - id
    - name
    - class
    - tagName
  - 间接获得
    - 通过父节点获得子节点
      - childNode
      - firstChild、lastChild
    - 通过子节点获得父节点：parentNode
    - 兄弟节点：nextSibling、preSibling					

- 操作节点

  - 判断节点类型 nodeType	1元素节点	3文本节点
  - 节点的属性	通过点操作符
  - 节点的文本内容	innerHTML

- 改变DOM结构

  - 增加节点 appendChild，insertBefore
  - 删除节点 removeChild
  - 替换节点 replaceChild

- 表单的操作

  - 通用属性 value，disabled，表单对象.elements

  - 单选按钮和复选框操作的时候，一般采用遍历

  - 表单验证 

    ```html
<submit value onclick="return check(this.from)"
    <form onsubmit="return check(this)"
    ```

##### HTTP协议

Tomcat服务器

- server.xml
- web.xml
- 目录结构的作用
  - lib
  - work（存放jsp编译后的Java文件）
  - webapps（项目部署）
  - conf

##### Servlet

浏览器发送给服务器的请求内容	HttpServletRequest对象

- getParameter()，getParameterValues()处理复选框
- 作用域方法：setAttribute()，getAttibute()，removeAttribue()
- getReqeustURL()，getReqeuestURI()，getQueryString()，getContextPath()
- 获取客户端ip和端口：getRemoteAddr(),getRemotePort()
- request.setCharacterEncoding()	设置请求实体的编码，对post请求有效，对get请求无效（可通过Tomcat调整`server.xml`）
- **请求转发**：rqeuset.getReqeustDispatcher().forward(requset,response)

服务器回送给浏览器的相应内容	HttpServletResponse对象

- setCharacterEndoding()设置响应编码格式
- setContentType()设置响应内容
- getWriter()输出文本	getOutSteam()	输出非文本
- **重定向**：resposne.sendRedire()

Serlvet类读取`web.xml`中自己的配置信息	ServletConfig

- getInitParameter

需要读取`web.xml`中的公共信息	\<context-param>	ServletContext

- 获取：this.getServletContext()
- 作用域：可以被所有的Servlt共享
- 其他方法：getRealPath()，getResourcePaths()

将所有Servlet放入一个容器中统一管理		ServletContext

保存以前操作的状态	客户端保存**Cookie** 	服务器保存**Session**

- 客户端保存cookie流程
  1. 服务器端写代码：Cookie c=new Cookie(); response.setCookie(c);
  2. 响应头中就会有一个SetCookie
  3. 浏览器收到以后，先放到内存中，如果cookie设置了有效期，则会写到硬盘的cookie文件中
  4. 浏览器以后访问服务器时，就会自动携带（请求头）有效的cookie信息	
- 服务器端保存信息session的跟踪机制
  1. 服务器会new一个session对象，同时指定一个id给session对象
  2. 通过响应头设置
  3. 浏览器收到sessionId这个信息，下次访问一定会携带sessionId
  4. 服务器收到session后，根据session找到对应的session对象，然后就可以查看对象里面的内容，从而，确定之前我们进行了什么操作，也就是实现了状态的保存

###### Servlet生命周期

1. 加载
   - 第一个请求的时候（Servlet是单例，只有一个Servlet对象）
   - 如果配置了load-on-startup，启动时加载
2. 初始化：执行init()
3. 调用：service()、doGet()、doPost()
4. 销毁：destroy()

###### 过滤器

- 实现Filter相关接口
- doFilter
- 配置位于\<servlet>之前

###### 监听器

- 实现Listener相关接口
- 配置位于\<filter>之后，\<servlet>之前

##### JSP

<%%>java脚本段

<%=%>表达式

<%! %> 声明

<%----%> 注释

<%@ include file="" %>静态导入，两个jsp文件编译时合并到一起

\<jsp:include>相当于一个类调用另一个类

jsp九个内置对象：

- page：`java.lang.object`
- **request**：`javax.servlet.http.HttpServletrequest` 获取用户的请求信息
- **resposne**：`javax.servlet.http.HttpServletResponse` 服务器向客户端的回应信息
- out ：`javax.servlet.jsp.jspWriter` 页面输出
- session：`javax.servlet.http.HttpSession` 用来保存每一个用户的信息
- **pageContext**：`javax.servlet.jsp.PageContext` JSP的页面容器
- exception：`java.lang.Throwable`
- config：`javax.servlet.ServletConfig` 服务器配置信息，可以取得初始化参数
- **application**：`javax.servlet.ServletContext`表示所有用户的共享信息
  	

##### EL和JSTL

EL表达式：操作的是作用域中的属性

使用：`${}`

JSTL标签库:

- foreach
- if
- choose when otherwise
- set
- remove
- import

菜鸟教程JSTL：[https://www.runoob.com/jsp/jsp-jstl.html](https://www.runoob.com/jsp/jsp-jstl.html)

##### ajax

核心技术：通过XHR对象发送请求，通过XHR对象接收响应，再通过js操作dom对象，将收到的新的信息，赋给某个元素，实现局部刷新效果	

基本流程

1. 创建XHR对象
2. 创建一个请求
3. 设置监听，处理服务器发送的响应内容
4. 发送请求

##### XML

XML基本语法

XML的解析（DOM方式、SAX方式、JDOM、DOM4J）

dtd/schema	定义xml数据的语法规则

xslt	定义xml数据的显示方式