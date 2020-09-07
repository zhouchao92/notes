---
title: css基础
tags:
  - css
categories:
  - notes
  - 前端
abbrlink: eda1a9a9
date: 2020-04-03 12:00:00
---

##### css声明

1. 在head标签中使用style标签声明：
   作用：此声明一般声明当前网页的公共样式或者给某个标签的单独样式

   ```css
   <style type="text/css">
   hr{
       width:50%;
       height:20px;
       color:red;
       background-color:red;
   }	
   </style>
   ```

2. 在标签上使用style属性进行声明：
   作用：此声明会将css样式直接作用于当前标签

   ```html
   <hr  style="background-color:blue; height:50px;"/>
   ```

3. 在head标签中使用link标签引入外部声明好的css文件
   作用：此声明相当于调用,解决了不同网页间样式重复使用的问题

   ```html
   <link rel="stylesheet" type="text/css" href="css/my.css">
   ```

一次声明，随处使用

问题：不同的声明给同一个标签操作了同一个样式，会使用谁的？
如果CSS的声明全部在head标签中，则遵循<font color="red">就近原则</font>，谁离标签近，谁就会被显示

<!--more-->

##### css的选择器

- 标签选择器：
  标签名{样式名1： 样式值1；……}
  作用：会将当前网页内的所有该标签增加相同的样式
- id选择器：
  #标签名的id属性值{样式名1：样式值1；……}
  作用：给某个指定的标签添加指定的样式
- 类选择器：
  .类选择器名{样式名1：样式值1……}
  作用：给不同的标签添加相同的样式
- 全部选择选择器：
  *{样式名1：样式值1……}
  作用：选择所有的HTML标签，并添加相同的样式                
- 组合选择器：
  选择器1，选择器2，……{样式名1：样式值1……}
  作用：解决不同的选择器之间重复样式的问题
- 子标签选择器：
  选择器1 子标签选择器{样式名1：样式值1……}
- 属性选择器：
  标签名[属性名=属性值]{样式名1：样式值1……}
  作用：选择某标签指定具备某属性并且属性值为某属性值的标签

##### css的使用过程

1. 声明css代码域
2. 使用选择选择器要添加样式的标签
   根据需要来：
   - 使用*选择器来给整个页面添加基础样式
   - 使用类选择器给不同的标签添加基础样式
   - 使用标签选择器给某类标签添加基础样式
   - 使用id、属性选择器、style属性声明方式给某个标签添加个性化样式
3. 书写样式单
   - 边框设置
     border：solid 1px；
   - 字体设置
     - font-size:10px;
     - font-family:"宋体";(设置字体格式）
     - font-weight:bold;（设置字体加粗）
   - 字体颜色设置
     color:颜色;
   - 背景颜色设置
     background-color:颜色;
   - 背景图片设置
     - background-img:url;(图片相对路径)
     - background-repeate：no-repeate；（设置图片不重复）
     - background-size：cover； （图片平铺整个页面）
   - 高和宽设置	width/height:数值px
   - 浮动设置	float：left/right
   - 行高设置	line-height：10；

##### 样式使用

```css
<style type="text/css">
/*添加网页背景图*/
body{
    background-image: url(img/1.jpg);/*添加背景图片*/
    background-repeat: no-repeat;/*设置图片不同重复*/
    background-size:cover;    /*设置图片平铺*/
}
/*使用标签选择器*/
table{
    /*background-image: url(img/0.jpg);
    backgroud-size: cover;*/
}
/*设置table的行高 */
tr{
    height: 40px;
}
/*设置td*/
td{
    width：100px;
    border: solid 1px red;
    border-radius: 10px; /*圆环*/
    background-color:orange;
    text-align: center;
    color: blueviolet;
    font-weight: bold;
    letter-spacing: 10px; /*字间距*/
}
/*------------------------------------------------------*/
ul{
    height: 50px;
    background-color: gray;
}
li{
    list-style-type: none; /*去除li的标识符*/
    /*display: inline;*/
    float: left; /*设置菜单左悬浮*/
    width: 100px;
    height: 100px;     
}
li a{
    color: black;
    text-decoration: none; /*设置超链接去除下划线*/
    font-size: 20px;
    font-weight: blod;
    margin-left: 20px;
    position: relative;
    top: 10px;
}
</style>
```

```html
<body>
    <h3 align="center">css的样式使用</h3>
    <hr />
    <table align="center">
        <tr>
            <td>姓名</td>
            <td>性别</td>
            <td>爱好</td>
        </tr>
        <tr>
            <td>张三</td>
            <td>男</td>
            <td>唱歌</td>
        </tr>
        <tr>
            <td>李四</td>
            <td>男</td>
            <td>打球</td>
        </tr>
    </table>
    <hr />
    <ul>
        <li><a href="">首页</li>
        <li><a href="">html</li>
        <li><a href="">java</li>
        <li><a href="">客服</li>
    </ul>
</body>
```

##### css盒子模型

###### div标签

块级标签：主要用来进行网页布局的， 会将其中的子元素内容作为一个独立的整体存在。

特点：默认宽度是页面的宽度，但可以设置

高度默认是没有的，但是可以设置（可以顶开）

<font color='red'>如果子元素设置了百分比的高或者宽，占据的是div的百分比，不是页面的。</font>

###### 盒子模型

外边距：margin	
作用：用来设置元素与元素之间的间隔

居中设置：`margin：0px auto;`上下间隔是0px，水平居中
可以根据需求单独的设置上下左右的外边距

边框：border	作用：用来设置元素的边框大小，可以设置上下左右
内边距：padding	作用：设置内容和边框之间的间隔
注意：内边距不会改变内容区域的大小，可以单独设置上下左右的内边距

内容区域：
作用：改变内容区域的大小
设置宽和高即可改变内容区域大小


##### css的定位

position

- 相对定位：relative
  作用：相对于原有位置移动指定的距离
  可以使用top，left，right，bottom来进行设置
  注意：其他元素的位置是不改变的
- 绝对定位：absolute
  作用：可以使用元素参照界面或者相对父元素来进行移动
  注意：如果父级元素成为参照元素，必须使用相对定位属性
  默认情况下以界面为基准进行移动的
- 固定定位：fixed
  作用：将元素固定显示在页面的指定位置，不会随着滚动条的移动而改变位置
  以上定位都可以使用top，bottom，left，right来进行移动；

如果相对应的元素的父级元素没有对应的position属性，会逐级向上匹配，直到body元素为止。

z-index:此属性是用来声明元素的显示级别的，等级高的会比等级低的优先显示


