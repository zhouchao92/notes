---
title: Java语法糖--var类型和switch
tags:
  - java
  - 语法糖
  - var
  - switch
  - notes
abbrlink: ab1a490c
date: 2020-04-20 13:06:13
---


##### var类型

Java 10 推出var：局部变量推断

- 避免信息冗余
- 对齐了变量名
- 更容易阅读
- 本质上还是强类型语言，编译器负责推断类型，并写入字节码文件，推断后不能更改

```java
var a = 5;
var b = 0.25;
var c = "Hello";
var d = new URL("https://github.com);
c = 5; //编译器报错
```

###### var的限制

- 可以用在局部变量上，非类成员变量
- 可以用在for/for-each循环中
- 声明时必须初始化
- 不能用在方法（形式）参数和返回类型
- 大面积滥用会使代码整体阅读性变差
- var只在编译时起作用，没有在字节码中引入新的内容，也没有专门的JVM指令处理var

##### switch

多分支选择语句

- 支持的类型：`byte/Byte` `short/Short` `int/Integer` `char/Character` `String`(JDK7.0) `Enum`(JDK5.0) 不支持`long` `float` `double`

###### 多分支合并

采用 -> 直接连接判定条件和动作(JDK12)

```java
int days;
switch(month){
    case "Jan","Nar","May","July","Aug","Oct","Dec" -> days = 31;
    case "Apr","June","Sep","Nov" -> days = 30;
	case "Feb" -> days = 28;
	default -> days = -1;	//必须有default
}
```

###### 直接在表达式赋值

直接在表达式赋值(JDK12)

```java
int num = 1;
int days = switch (num){
	case 1,3,5,7,8,10,12 ->31;
	case 4,6,9,11 -> 30;
	default -> {
		int result = 28;
		break result;	//代码块中的break返回结果
	}
}
```

