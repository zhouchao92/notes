---
title: html基础
tags:
  - html
categories:
  - 前端
abbrlink: 1a147173
date: 2020-04-03 12:00:00
---

#### head标签

```html
<title></title>网页标题标签
```

```html
<meta charset="utf-8"/> HTML5网页解析编码格式配置

<meta http-equiv="content-type"  content="text/html; charset
=utf-8"/>HTML4文档编码格式设置
```


搜索优化标签：关键字、网页描述、作者


```html

<meta name="keywords" content="关键字，关键字"/>	网页关键字

<meta name="description" content="描述"/>		网页描述

<meta name="author" content="人名"/>	网页作者

<meta http-equiv="refresh"  content="5(秒数) ; url= (网址)">	设置网页自动跳转
```

其他：css引入标签，js引入标签

<!--more-->

#### 标题标签

```html
<h(序号) > </h(序号)>
<h1>一级标题</h1>
<h2>二级标题</h2>
<h3>三级标题</h3>
<h4>四级标题</h4>
<h5>五级标题</h5>
<h6>六级标题</h6>
```

h1到h6：数据加粗显示，显示依次减弱，标题标签自动换行(属于块级标签)

属性 align：center，left，right    设置位置（对齐）

```html
<hr />  水平线 默认居中
<hr width="600px" size="20px" color=" " align=" "/>
```

- width：水平线宽度

- size：水平线高度

- color：水平线颜色

- align：水平线位置

像素单位(px)：占据电脑屏幕的大小

百分比单位(%)：占据浏览器比例的大小

#### 段落标签


段落标签 p：会将一段数据作为整体进行显示，主要是进行css和js操作时比较方便，会自动换行(块级标签)

- 特点：段与段的间距比较大

换行符：br 告诉浏览器此位置进行换行    

空格符：&nbsp 告诉浏览器在此位置增加空格

权重标签:

- b 会将内容加黑显示
- i 会将内容斜体显示
- u 会将内容增加下划线
- del 增加中划线

(以上不会自动换行可嵌套使用)

#### 列表标签

1. 无序列表 

   ul    li 该标签中书写列表内容，一个li标签代表列表中的一行数据

   特点：默认数据前有一个黑圆圈

2. 有序列表 

   ol   li 该标签中书写列表内容，一个li标签代表列表中的一行数据

   特点：会自动的给列表进行顺序编码，格式从小到大并且是连续的

   属性：type 可以改变顺序编码的值，可以是1，A，a，Ⅰ默认使用阿拉伯数字进行顺序编码

3. 自定义列表

   dl  dt数据的标题

   dd数据的具体内容，一个dd表示一条数据

#### 图片标签

img 图片资源：

```html
本地资源 <img src="img/1.png " width=" px"/>

网络资源<img src=" URL " />
```

src 图片路径：

- 相对路径 从当前文件出发查找另一个文件所经过的路径
- 绝对路径 从根盘符出发所查找文件的路径

属性：

- width 设置图片的宽度，如果是单独设置，则在图片保证不失真的情况下自动缩小或放大，单位可以是px也可以是百分比 
- height 设置图片的高度
- title 设置图片标题，鼠标放在图片上的时候显示
- alt 图片加载失败显示

<font color="#f00">图片不会自动换行(图片属于行内元素)</font>

#### 超链接标签

a标签   href 要跳转的网页资源路径

```html
本地资源       <a href="路径"/>文字描述</a>		相对路径或绝对路径

网络资源      <a href="URL"/>文字描述</a>        网络资源(网页)的URL，以http://...
```

属性：target ="_" 指明要跳转的网页资源的显示位置

- _self在当前页中刷新显示
- _blank在新的标签页中显示
- _top在顶层页面中显示
- _parent在父级页面中显示

<font color="#f00">超链接标签中一定要声明访问方式，可以是文字也可以是图片</font>

##### 锚点

作用：在一张网页中进行资源跳转

```html
使用：先使用超链接标签在指定的网页位置增加锚点              格式为<a name ="锚点名"></a>   

再使用a标签可以跳转指定的锚点，达到网页内部资源跳转的目的    格式为<a href ="#锚点名"></a>

回到网页顶部   <a href ="#">文字描述</a>
```

#### 表格标签

table

- tr 声明一行
- th声明一个单元格，表头格，默认居中加黑显示
- td声明一个单元格，默认局左显示原始数据

<font color="#f00">行高即改行所有单元格的宽度，单元格的宽度即列宽</font>

属性：

- border 给表格添加边框
- width 设置表格的宽度
- height 设置表格的高度
- cellpadding 设置内容居边框的距离
- cellspacing 设置边框的大小(以上单位均为px)
- colspan="个数" 合并左右单元格(实现行合并)
- rowspan="个数" 合并上下单元格(实现列合并)

特点：默认根据数据的多少进行表格的大小显示

单元格的合并

1. 首先确保表格是一个规整的表格

2. 根据要合并的单元格，找到其所在的源码位置

3. 行合并：在要合并的单元格的第一个单元格使用其属性colspan，并删除其要合并的单元格

   列合并：在要合并的单元格的任一个单元格使用其属性rowspan，并删除其要合并的单元格


#### 内嵌标签 

iframe 

src：要显示的网页资源路径(本地资源or网络资源)

​		默认当前页面打开及加载src指向的资源

width：设置显示区域的宽度

height：设置显示区域的高度

name：设置内嵌区域的名字，结合超链接标签的target属性使用

<font color="#f00">作用 在当前网页中加载其他网页的资源，达到不同网页资源之间不相互干扰，并能在同一个页面中展现给用户的目的。</font>

#### 框架标签

frameset

rows：按照行进行切分页面

cols：按照列进行切分页面

子标签frame：进行分区域的占位，一个frame可以单独加载网页资源

 属性：

- src：资源路径 (本地资源or网络资源)
- name：区域名

<font color="#ff0000">构建frameset，一定要删除body标签</font>

#### form标签

form表单标签

作用：收集并提交用户的数据给指定服务器

属性：

action：收集的数据提交地址(URL)

method：收集的数据的提交方式

- get：适合小量数据，表单数据以？隔开拼接在URL后面，不同键值对使用&符号隔开，不安全
- post：适合大量数据，安全，隐式提交

<font color="#f00">表单数据的提交，要提交的表单项必须拥有name属性值，否则不会提交。提交的表单数据为键值对，键为name属性的值，值为用户书写的数据</font>

<font color="#f00">form标签会收集其标签内部的数据</font>

<font color="#f00">form表单的数据提交需要依赖于submit提交按钮</font>

<font color="#f00">表单数据提交的是表单域标签的value值</font>

form表单标签的使用：在点击数据提交时，form标签会将其内部所有form表单域标签中用户书写的数据按照method指明的提交方式提交给action属性所指明的提交地址

form表单域标签

作用：给用户提供可以进行数据书写或者选择的标签

使用：

```html
<!--账号密码的提交-->
<form action="" method="post">
	账号:<input type="text" name="" value=""/>
    密码:<input type="text" name="" value=""/>
    <input type="submit" value="提交">
</form>
```

###### 文本框：input

- type：
  - text  收集少量文本数据，用户可见
  - password 收集用户密码数据

- name：数据提交的键，也会被js使用

- value：默认值

  ```html
  <input type="text" name="" value=""/>
  <input type="password" name="" value=""/>
  ```

###### 单选框：input

type：`radio`

- name：属性值相同的单选框只能选择一项数据

- value：要提交的数据

- checked：使用此属性的单选默认是选择状态

  ```html
  <input type="radio" name="" value="" checked=""/>
  ```

###### 多选框：input

type：`checkbox`

- name：一个多远组需要使用相同的name属性值
- value：要提交的数据
- checked：使用此属性的多选框默认是选择状态

###### 下拉框：select  

name：数据提交的键名，必须声明

子标签 option：一个option标签表示一个下拉选项   

value：要提交的数据

###### 文本域 input

 textarea：声明一个可以书写大量文字的文本区域

 name：数据提交的键名，js和css会使用

 rows：声明文本框的行数

 cols：声明文本框的列数

###### 普通按钮: input

type：button

value 按钮显示的文字内容

###### 隐藏标签 input

type：hidden

name

value

一般用于提交给服务器作为方法标识，不显示在网页上。

#### 编译预显示

将源代码显示在浏览器页面中  

```html
<pre></pre>浏览预显示
<>需要进行替换，否则浏览器会编译执行
<     &lt   尖括号不会被编码
>     &gt
```
