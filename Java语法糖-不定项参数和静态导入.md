---
title: Java语法糖--不定项参数和静态导入
tags:
  - java
  - 语法糖
  - 不定项参数
  - 静态导入
  - notes
abbrlink: 689cf977
date: 2020-04-20 10:07:53
---

##### 不定项参数

###### 介绍

普通函数的形参列表是固定个数/类型/顺序

JDK5提供了不定项参数(可变参数)功能

- 数据类型后加... 如 int... /double... /String...
- 可变参数本质是一个数组，可以是0个也可以是n个同类型参数

###### 要点

- 一个方法只能有一个不定项参数，且必须位于参数列表的最后
- 重载的优先级规则1：**固定参数的方法，比可变参数优先级更高**
- 重载的优先级规则2：**调用语句，同时与两个带可变参数的方法匹配，会报错**

```java
/**
* 固定参数
*
* @param message
*/
static void print(String message) {
    System.out.println(message);
}

/**
* 不定项参数
*
* @param message
*/
static void print(String... message) {
    if (message == null || message.length == 0) {
        System.out.println("无参");
        return;
    }
    for (String seq : message) {
        System.out.println(seq);
    }
}

/**
* 不定项参数
*
* @param str
* @param message
*/
static void print(String str, String... message) {
    for (String seq : message) {
        System.out.println(seq);
    }
}
```

```java
//测试代码
print();    //无参，调用print(String... message)
print("Hello world!"); //一个参数，调用固定参数方法print(String message)
print("Hello", "world");    //编译器报错：Ambiguous method call. Both print(String... message) and (String str, String... message) in IndefiniteParameter match
```

##### 静态导入

###### 介绍

- `import`导入程序所需的类
- `import static`导入一个类的静态方法和静态变量（JDK5开始引入）

`import static java.lang.Math.pow;`

`import static java.lang.System.out;`

###### 要点

`import static`导入一个类的静态方法和静态变量：

- 少使用 * 通配符，最好具体到静态变量或方法
- 静态方法名具有明确特征，如有重名，需要补充类名