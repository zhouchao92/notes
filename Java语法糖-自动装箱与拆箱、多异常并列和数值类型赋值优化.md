---
title: Java语法糖--自动装箱与拆箱、多异常并列和数值类型赋值优化
tags:
  - Java
  - 语法糖
  - 自动装箱与拆箱
  - 多异常并列
  - 数值类型赋值优化
  - notes
abbrlink: e81b4a74
date: 2020-04-20 10:59:10
---


##### 自动装箱与拆箱

###### 介绍

auto-boxing / auto-unboxing

- 从JDK5.0开始引入，简化基本类型和对象的写法
- 基本类型：`boolean` `byte` `char` `int` `short` `long` `float` `double`
- 对象：`Boolean` `Byte` `Character` `Integer` `Short` `Long` `Float` `Double`

```java
Integer integer1 = 1;    //自动装箱
Integer integer2 = Integer.valueOf(1);  

int int1 = integer1;    //自动拆箱
int int2 = integer2.intValue();
```

###### 注意事项

- 自动装箱和拆箱是编译器的工作，在class中已经添加转化，虚拟机没有自动装箱和拆箱的语句
- ==判断：基本类型是内容相同，对象是指针是否相同(内存同一个区域)
- 基本类型没有空值，对象有null，可能触发NullPointerException（空指针异常）
- 当一个基础数据类型与封装类进行 == + - * / 运算时，会将封装类进行拆箱，对基础数据类型进行运算
- 谨慎使用多个非同类的数值类对象进行运算

<!--more-->

##### 多异常并列

###### 简介

多异常并列在一个catch中，从JDK7.0开始引入，简化写法

```java
try {
    test();     //操作
} catch (IOException ie) {
    ie.printStackTrace();
} catch (SQLException se) {
    se.printStackTrace();
}
```

简化写法   | 管道符号

```java
try {
    test();     //操作
} catch (IOException | SQLException ie) {
    ie.printStackTrace();
}
```

###### 注意

多个异常之间不能有直接/间接继承关系，否则会报错

处理方法不同，不能合并

##### 数值赋值优化

###### 整数类型用二进制数赋值

整数类型用二进制数赋值

Java7的新语法：

- 避免二进制计算
- `byte` `short` `int` `long`

###### 数字中的下划线

Java7的新语法：

- 增加数字的可读性和纠错功能
- `short` `int` `long` `float` `double`
- 下划线只能出现数字中间，前后必须有数字
- 允许在二/八/十/十六进制的数字中使用

`int a = 0b0011_1011_0001;`二进制

`int b =02_014;`八进制

`int c=123__456;`十进制，可使用多个下划线

`int d =0x7_B_1;`十六进制