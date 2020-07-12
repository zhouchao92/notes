---
title: Ajax笔记
tags:
  - ajax
categories:
  - notes
  - 后端
abbrlink: 48d9bd16
date: 2020-07-12 10:47:12
---

##### Ajax概述

全称"Asynchronous JavaScript and XML" 异步JavaScript和XML，是一种创建交互网页应用的网页开发技术。

- 基于web标准(Standards-based Presentation)	XHTML+CSS
- 使用DOM(Document Object Model)进行动态显示与交互
- 使用XML和XSLT进行数据交换及相关操作
- 使用XMLHttpRequest进行异步数据查询、检索
- 使用JavaScript将所有的东西绑定在一起

本质是一个浏览器端的技术

异步刷新技术，用来在当前页面响应不同的请求内容。

<!--more-->

##### Ajax功能

需求：需要将本次的响应结果和前面的响应结果内容在同一个页面中展现给用户

解决:

- 在后台服务器端将多次相应内容重新拼接成一个jsp页面，响应对象，jsp内部使用，带有缓冲区的响应对象，效率高于response对象
  问题:造成相应你内容被重复的响应，资源浪费
- 使用Ajax技术

##### Ajax基本使用流程

1. 创建ajax引擎对象
2. 覆写onreadystatement函数
3. 判断ajax状态码
4. 判断响应状态码
5. 获取相应内容
6. 处理相应内容
7. 发送请求

```js
// 创建ajax引擎对象
var ajax;
if (window.XMLHttpRequest) {  // firefox
    ajax = new XMLHttpRequest();
} else if (window.ActiveXObject) { // ie
    ajax = new ActiveXObject("Msxml2.XMLHTTP");
}
// 覆写onreadystatement函数
ajax.onreadystatechange = function () {
    // 会执行4次,判断ajax状态码，只在数据成功接收时执行以下代码
    if (4 === ajax.readyState) {
        // 获取元素对象
        let showdiv = document.getElementById("showDiv");
        // 判断响应状态码
        if (200 === ajax.status) {
            // 获取响应内容，修改元素内容
            showdiv.innerHTML = ajax.responseText;
        }else if (404===ajax.status){
            showdiv.innerHTML="请求资源不存在";
        }else if(500===ajax.status){
            showdiv.innerHTML="服务器繁忙";
        }
    }
}
// 发送请求 get/post
// get请求实体拼接在url后面，?隔开，键值对        
ajax.open("get", "ajax?name=value");
ajax.send(null);

// post请求有单独的请求实体，    
ajax.open("post","ajax");
ajax.setRequestHeader("Content-Type","application/x-www-form-urlencoded");
ajax.send("name=value");
```

##### Ajax状态码

readyState

| readyState值 |                             含义                             |
| :----------: | :----------------------------------------------------------: |
|      0       | 表示XMLHttpRequest已建立，但还未初始化，这是尚未调用open方法 |
|      1       |   表示open方法已经调用，但未调用send方法（已创建，未发送）   |
|      2       |              表示send方法已经调用，其他数据未知              |
|      3       |              表示请求已经成功发送，正在接收数据              |
|      4       |                     表示数据已经成功接收                     |

###### 常用Http状态码

|        http状态码         |                             含义                             |
| :-----------------------: | :----------------------------------------------------------: |
|          200 OK           |                        客户端请求成功                        |
|      400 Bad Request      |           客户端请求有语法错误，不能被服务器所理解           |
|     401 Unauthorized      | 请求未经授权，这个状态代码必须和WWW-Authenticate报头域一起使用 |
|       403 Forbidden       |               服务器收到请求，但是拒绝提供服务               |
|       404 Not Found       |            请求资源不存在（可能输入了错误的URL）             |
| 500 Internal Server Error |                   服务器发生不可预期的错误                   |
|  503 Server Unavailable   |    服务器当前不能处理客户端的请求，一段时间后可能恢复正常    |

##### Ajax异步和同步

ajax.open(method, url,async); 

method：get方法，post方法

url：请求的地址

async：设置代码执行方式，false同步，true异步,默认为异步

##### Ajax请求

get：携带的数据包含在url中

```js
ajax.open("get", url + (data === null ? "" : "?" + data), async); // false同步，true异步
ajax.send(null); 
```

post

```js
ajax.open("post", url, async);	// false同步，true异步
ajax.setRequestHeader("Content-Type", "application/x-www-form-urlencoded");
ajax.send(data);
```

##### Ajax响应数据类型

###### 普通字符串

responseText

```js
// 获取响应数据
var result = ajax.responseText;
```

###### json

将数据按照json的格式拼接好的字符串，方便使用eval方法将接受的字符串直接转换成js的对象

```js
// json格式
var obj={
    name:value,
    ...
};
```

需求分类

1. 在当前页面显示查询结果
2. 创建ajax函数
3. 调用ajax函数发送请求到UserServlet
4. 调用业务层获取对应的数据

```jsp
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<%
String path = request.getContextPath();
String basePath = request.getScheme() + "://" + request.getServerName() + ":" + request.getServerPort() + path + "/";
%>
<html>
    <head>
        <base href="<%=basePath%>">
        <title>AjaxData</title>
        <!--引入外部js文件，见ajax封装-->
        <script type="text/javascript" src="js/ajaxutil.js"></script> 
        <!--声明js代码域-->
        <script type="text/javascript">
            var obj = {};
            function getData() {
                // 获取用户请求信息
                var name = document.getElementById("uname").value;
                // 创建ajax引擎对象
                var ajax;
                if (window.XMLHttpRequest) {	// firefox
                    ajax = new XMLHttpRequest();
                } else if (window.ActiveXObject) {	// ie
                    ajax = new window.ActiveXObject("Msxml2.XMLHTTP");
                }
                // 覆写onreadystatementchange函数
                ajax.onreadystatechange = function () {
                    // 判断ajax状态码
                    if (ajax.readyState === 4) {
                        // 判断响应状态码
                        if (ajax.status === 200) {
                            // 获取响应数据
                            var result = ajax.responseText;
                            eval("u=" + result);
                            // 处理响应数据
                            // 获取table元素对象
                            var ta = document.getElementById("table");
                            ta.innerHTML = "";
                            // 插入新的行
                            var tr = ta.insertRow(0);
                            var uid = tr.insertCell(0);
                            uid.innerHTML = "编号";
                            var uname = tr.insertCell(1);
                            uname.innerHTML = "英雄";
                            var price = tr.insertCell(2);
                            price.innerHTML = "价格";
                            var location = tr.insertCell(3);
                            location.innerHTML = "位置";
                            var description = tr.insertCell(4);
                            description.innerHTML = "描述";

                            var tr = ta.insertRow(1);
                            var uid = tr.insertCell(0);
                            uid.innerHTML = u.uid;
                            var uname = tr.insertCell(1);
                            uname.innerHTML = u.uname;
                            var price = tr.insertCell(2);
                            price.innerHTML = u.price;
                            var location = tr.insertCell(3);
                            location.innerHTML = u.location;
                            var description = tr.insertCell(4);
                            description.innerHTML = u.description;
                        }
                    }
                }
                // 发送请求
                ajax.open("get", "user?name=" + name);
                ajax.send(null);
            }
        </script>
    </head>
    <body>
        <h3>AjaxData</h3>
        <hr>
        名称: <input type="text" name="uname" value="" id="uname">
        <input type="button" value="搜索" onclick="getData()">
        <hr>
        <table border="1px" id="table"></table>
    </body>
</html>
```

###### XML数据

responseXML，返回document对象
通过document对象将数据从xml中获取出来

```jsp
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<%
String path = request.getContextPath();
String basePath = request.getScheme() + "://" + request.getServerName() + ":" + request.getServerPort() + path + "/";
%>
<html>
    <head>
        <base href="<%=basePath%>">
        <title>XML数据格式学习</title>
        <!--声明js代码域-->
        <script type="text/javascript">
            function getXML() {
                // 创建ajax引擎对象
                var ajax;
                if (window.XMLHttpRequest) {
                    ajax = new XMLHttpRequest();
                } else if (window.ActiveXObject) {
                    ajax = new window.ActiveXObject("Msxml2.XMLHTTP");
                }
                // 覆写onreadystatementchange函数
                ajax.onreadystatechange = function () {
                    // 判断ajax状态码
                    if (ajax.readyState === 4) {
                        // 判断响应状态码
                        if (ajax.status === 200) {
                            // 获取响应数据
                            var result = ajax.responseXML;
                            alert(result.getElementsByTagName("uname")[0].innerHTML);
                        }
                    }
                }
                // 发送请求
                ajax.open("get", "xml",true);
                ajax.send(null);
            }
        </script>
    </head>
    <body>
        <h3>XML数据格式学习</h3>
        <hr>
        <input type="button" value="测试" onclick="getXML()">
    </body>
</html>
```

##### Ajax封装

封装ajax方便调用

```js
/**
 * ajax封装
 * @param method 请求方式，get或post
 * @param url 请求地址
 * @param data 请求数据，格式"name=value&..."，没有值需要传入null
 * @param deal200 接收一个带有一个形参的js函数对象，形参接收的实参是ajax引擎对象
 * @param deal404 接收一个js函数对象
 * @param deal500 接收一个js函数对象
 * @param async 异步或同步，boolean
 */

function myAjax(method, url, data, deal200, deal404, deal500, async=true) {
    // 创建ajax引擎对象
    var ajax = getAjax();
    // 覆写onreadystatement函数
    ajax.onreadystatechange = function () {
        // 获取元素对象
        let showdiv = document.getElementById("showDiv");
        // 会执行4次,判断ajax状态码，只在数据成功接收时执行以下代码
        if (4 === ajax.readyState) {
            // 判断响应状态码
            if (200 === ajax.status) {
                if (deal200)
                    deal200(ajax);
            } else if (404 === ajax.status) {
                if (deal404)
                    deal404();
            } else if (500 === ajax.status) {
                if (deal500)
                    deal500();
            }
        }
    }
    if ("get" === method) {
        // 发送请求
        ajax.open("get", url + (data === null ? "" : "?" + data), async); // false同步，true异步
        ajax.send(null);
    } else if ("post" === method) {
        ajax.open("post", url, async);
        ajax.setRequestHeader("Content-Type", "application/x-www-form-urlencoded");
        ajax.send(data);
    }
}

/*ajax的封装*/
function getAjax() {
    var ajax;
    if (window.XMLHttpRequest) {  // firefox
        ajax = new XMLHttpRequest();
    } else if (window.ActiveXObject) { // ie
        ajax = new ActiveXObject("Msxml2.XMLHTTP");
    }
    return ajax;
}
```

