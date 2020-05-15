---
title: Java语法糖--接口方法
tags:
  - java
  - 语法糖
  - 接口方法
  - notes
date: 2020-04-20 11:52:31
---


###### 接口的默认方法

Java最初的设计中，接口的方法都是没有实现的、公开的

Java 8推出接口的默认方法/静态方法(都带有实现的)，为Lambda表达式提供支持

```java
public interface Animal {
    void move();
}

//Java 8新特性
public interface NewAnimal {
    default void move() {
        System.out.println("I am moving.");
    }
}
```

- 以`default`关键字标注，其他的定义和普通函数一样
- 规则：
  - 默认方法<font color=red>不能重写</font>Object中的方法(`equals()` `toString()` `hashcode()`)
  - 实现类可以<font color=red>继承/重写</font>父接口的默认方法
  - 接口可以<font color=red>继承/重写</font>父接口的默认方法
  - 当父类和父接口都有（同名同参数）默认方法，<font color=red>子类继承父类的默认方法</font>，兼容JDK及其以前的代码
  - 子类实现了2个接口（均有同名同参数的默认方法），<font color=red>编译失败</font>，必须在子类中重写这个`default`方法

###### 接口的静态方法

Java 8接口的静态方法（带实现的）

- 该静态方法属于本接口的，不属于子类/子接口
- 子类（子接口）没有继承该静态方法，只能通过所在的接口名来调用

```java
public interface StaticAnimal {
    static void move() {
        System.out.println("static animal move()");
    }
}

public interface StaticLandAnimal extends StaticAnimal {
    //不能继承StaticAnimal的move()方法
}

public static class TestStatic implements StaticLandAnimal {
    public static void main(String[] args) {
        StaticAnimal.move();        
        StaticLandAnimal.move();    //编译器报错
        new TestStatic().move();    //编译器报错
    }
}
```

###### 接口的私有方法

Java 9接口的私有方法（带实现的）

- 解决多个默认方法/静态方法内容重复问题
- 私有方法属于本接口，只在本接口内使用，不属于子类/子接口
- 子类（子接口）没有继承该私有方法，也无法调用
- 静态私有方法可以被静态/默认方法调用，非静态私有方法被默认方法调用

###### 接口与抽象类比较

相同点（--Java 12）

- 都是抽象的，都不能被实例化
- 都可以有实现方法
- 都可以不需要继承者实现所有的方法

不同点

- 抽象类最多只能继承一个，接口可以实现多个
- 接口的变量默认是 `public static final`且必须有初值，子类不能修改，而抽象类的变量默认是`default`，子类可以继承修改
- 接口没有构造函数，抽象类有构造函数
- 接口没有main函数，抽象类可以有main函数
- 接口有`public` `default` `private`的方法，抽象类有 `public` `default` `protected` `private`的方法