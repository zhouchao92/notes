---
title: Java--反射
tags:
  - 反射
categories:
  - java
abbrlink: 87b8a7bb
date: 2020-05-31 22:01:42
---


#### 反射

reflection

- 程序可以访问、检测和修改它本身状态或行为的能力，即自描述和自控制。
- 可以在运行时加载、探知和使用编译期间完全未知的类。
- 给Java插上动态语言特性的翅膀，弥补强类型语言的不足。
- `java.lang.reflect`包，在Java 2就有，在Java 5得到完善

功能

- 在运行中分析类的能力
- 在运行中查看和操作对象
  - 基于反射自由创建对象
  - 反射构建出无法直接访问的类
  -  set或者get到无法访问的成员变量
  - 调用不可访问的方法
- 实现通用的数组操作代码
- 类似函数指针的功能

<!--more-->

##### 五种创建对象的方式

###### 1.静态编码和编译

```java
public class A {
    public void hello() {
        System.out.println("Hello from A.");
    }
}
```

直接new 调用构造函数

```java
A obj1 = new A();
obj1.hello();
```

2.克隆(clone)

```java
public class B implements Cloneable {
    public void hello() {
        System.out.println("Hello from B.");
    }

    protected Object clone() throws CloneNotSupportedException {
        return super.clone();
    }
}
```

使用克隆的方法创建对象

```java
B obj2 =new B();
obj2.hello();

B obj3=(B) obj2.clone();
obj3.hello();
```

###### 3.序列化(serialization)和反序列化(deserialization)

```java
public class C implements Serializable{
	private static final long serialVersionUID=1L;  // 序列化id
    public void hello(){
        System.out.println("Hello from C.");
    }
}
```

> 使用 transient 关键字可以使变量不被序列化

```java
C obj4 = new C();
ObjectOutputStream out = new ObjectOutputStream(new FileOutputStream("data.obj"));
out.writeObject(obj4);
out.close();

ObjectInputStream in = new ObjectInputStream(new FileInputStream("data.obj"));
C obj5 = (C) in.readObject();
in.close();
obj5.hello();
```

> 注意：序列化会引发安全漏洞，未来将被移除出JDK

###### 4.5.反射

> 第四种 通过Class.forName(String className).newInstance();动态获取实例
>
> 注意：className 需要加上包名，否则会报 java.lang.ClassNotFoundException 的异常

```java
Object obj6 = Class.forName("com.reflection.A").newInstance();
// A obj7 = (A) Class.forName("A").newInstance();
Method m = Class.forName("com.reflection.A").getMethod("hello");
m.invoke(obj6);
```

> 第五种	newInstance 调用构造函数

```java
Constructor<A> constructor = A.class.getConstructor();
A obj8 = constructor.newInstance();
obj8.hello();
```

##### 反射关键类

Class：类型标识

- JVM为每个对象都保留其类型标识信息(Runtime Type Identification)

  三种获取方式

  ```java
  String s1="abc";
  Class c1=s1.getClass();
  System.out.println(c1.getName());
  
  Class c2=Class.forName("java.lang.String");
  System.out.println(c2.getName());
  
  Class c3=String.class;
  System.out.println(c3.getName());
  ```

- 成员变量、方法、构造函数、修饰符、包、父类、父接口…

  - 成员变量 Field 
    getFields() 获取本类及父类所有的public字段

    getDeclaredFields()  获取本类所有声明的字段

  - 成员方法 Method
    getMethods() 获取public方法，包括父类和父接口
    getDeclaredMethod() 获取该类所有的方法

  - 构造函数 Constructor

    [Class.]getConstructors() 获取本类的所有构造函数

    [Constructor.]newInstance(Object ...args) 有参和无参构造函数

  - 父类/父接口

    [Class.]getSuperClass() 获取父类
    [Class.]getInterfaces() 获取父接口

    

##### 反射的应用

- 数据库连接
- 数组扩充器 
- 动态执行方法
- Json和Java对象互转
- Tomcat的Servlet对象创建
- MyBatis的OR/M
- Spring的Bean容器
- org.reflections包介绍

###### 数据库连接

JDBC：Connection，连接到不同数据库

```java
// 构建Java和数据库之间的桥梁介质
try {
    Class.forName("com.mysql.jdbc.Driver");
    // Class.forName(className,true,currentLoader);
    // 通知类加载器加载此类的class文件
} catch (ClassNotFoundException e) {
    e.printStackTrace();
}
```

```java
// 构建Java和数库之间的桥梁：URL，用户名，密码
Connection conn = DriverManager.getConnection(url, "root", "123456");
// DriverManager将会挑选加载合适的驱动类，并采用getConnection()方法连接
```

###### 数组扩充

给定一个数组(任意类型)，扩充指定到长度

- Java的数组一旦创建，其长度是不再更改的
- 新建一个大数组(相同类型)，然后将旧数组的内容拷贝过去

```java
public static Object arrCopy(Object oldArray, int newLength) {
    // Array类型
    Class clazz = oldArray.getClass();
    // 获取数组中的单个元素类型
    Class componentType = clazz.getComponentType();
    // 旧数组长度
    int oldLength = Array.getLength(oldArray);
    // 创建新数组
    Object newArray = Array.newInstance(componentType, newLength);
    // 拷贝旧数据
    System.arraycopy(newArray, 0, oldArray, 0, oldLength);
    return newArray;
}
```

###### 动态执行方法

给定类名，方法名，即可执行

加上定时器，即可做定时任务执行

```java
class Work{
    public static void hello(){
        System.out.println("Hello World!");
    }
}
class MyTask extends TimerTask {
    public void run(){
        try{
            Method m= Class.forName(packageName+"Work").getMethod("hello");
            m.invoke(null);	// 静态方法可以不用new对象
        }catch(Exception e){
            e.printStackTrace();
        }
    }
}
```

```java
Timer timer=new Timer();
Calendar now=Calendar.getInstance();
now.set(Calendar.SECOND,now.get(Calendar.SECOND)+1);
Date runDate=now.getTime();
MyTask task=new Task();
timer.scheduleAtFixedRate(task,runDate,3000);
```

###### Json和Java对象互转

```java
Gson gson=new Gson();
// json 字符串转bean对象
String s; 	// 待转换的json字符串
[Bean] json=gson.fromJson(s,[Bean.class]);

// json bean对象转字符串
Bean bean;
String t=gson.toJson(bean,[Bean.class]);
```

###### Tomcat的Servlet创建

详见源码

###### MyBatis的OR/M

MyBatis: OR/M(Object-Relation Mapping) 
数据库表和Java的POJO/DAO类对应关系

###### Spring Framework的Bean容器

Spring Framework: Java EE的主要框架
IoC 的Bean容器(HashMap)

###### org.reflections

Reflection的增强工具包

- [https://github.com/ronmamo/reflections](https://github.com/ronmamo/reflections) 
- Java runtime metadata analysis Java运行时元数据分析
  - 获取某类的所有子类型
  - 获取有特殊annotation的类型或者成员变量/方法
  - 根据正则表达式获取资源(类/成员变量/方法)
  -  根据组合条件查询相应的方法

##### 编译器API

反射

- 可以查看对象的类型标识
- 可以动态创建对象、访问其属性，调用其方法
- <font color="red">前提：类(class文件)必须先存在</font>

编译器API

- 对.java文件即时编译
- 对字符串即时编译
- 监听在编译过程中产生的警告和错误
- 在代码中运行编译器(并非：Runtime命令行调用javac命令)

###### JavaCompiler

- 自Java 1.6 推出，位于`javax.tools`包中。
- 可用在程序文件中的Java编译器接口(代替javac.exe)。
- 在程序中编译java文件，产生class文件。
- run方法(继承自`java.tools.Tools`)：可以编译java源文件，生成class文件，但不能指定输出路径,监控错误信息, 调用后就在源码所在目录生成class文件。
- getTask方法：可以编译java源文件，包括在内存中的java文件(字符串)，生成class文件。

###### Java编译器API作用

- Java EE的JSP编译
- 在线编程环境
- 在线程序评判系统(Online Judge系统)
- 自动化的构建和测试工具

###### 基于JavaCompiler的集成工具

- <font color="red">Janino</font>，[http://janino-compiler.github.io/janino](http://janino-compiler.github.io/janino)
- InMemoryJavaCompile， [https://github.com/trung/InMemoryJavaCompiler](https://github.com/trung/InMemoryJavaCompiler)
- Java-Runtime-Compiler, [https://github.com/OpenHFT/JavaRuntime-Compiler](https://github.com/OpenHFT/JavaRuntime-Compiler)
- Apache Commons JCI(Java Compiler Interface), [http://commons.apache.org/proper/commons-jci/index.html](http://commons.apache.org/proper/commons-jci/index.html)，<font color="red">适用于JDK1.5及以前的版本</font>