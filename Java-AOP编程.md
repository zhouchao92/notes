---
title: Java--AOP编程
tags:
  - aop
categories:
  - notes
  - java
abbrlink: d97d56d4
date: 2020-06-04 15:44:45
---


##### AOP编程

AOP：Aspect Oriented Programming

面向切面编程 vs 面向对象编程 (Object Oriented Programming)

- 面向对象(OOP)：将需求功能划分为不同的、独立，封装良好的类，并让它们通过继承和多态实现相同和不同行为。
-  面向切面：将通用需求功能从众多类中分离出来，使得很多类共享一个 行为，一旦发生变化，不必修改很多类，而只需要修改这个行为即可

###### 特点

- 分离代码的耦合(高内聚，低耦合)
- 业务逻辑变化不需要修改源代码/不用重启
- 加快编程和测试速度
- AOP编程是一个概念/规范，没有限定语言
- 不是取代OOP编程，而是OOP的补充，和数据库的触发器有点相似

###### 主要内容

- Aspect ：配置文件，包括一些Pointcut和相应的Advice
- Joint point：在程序中明确定义的点，如方法调用、对类成员访问等
- Pointcut：一组joint point, 可以通过逻辑关系/通配符/正则等组合起来，定义了相应advice将要发生的地方
-  Advice：定义了在pointcut处要发生的动作,通过before/after/around/来关联
-  weaving：advice代码在具体joint point的关联方式

###### Java的AOP实现

- AspectJ(Eclipse)， [https://www.eclipse.org/aspectj/](https://www.eclipse.org/aspectj/)
- Spring AOP，[https://spring.io/projects/spring-framework](https://spring.io/projects/spring-framework)
- Spring AOP 与 AspectJ 比较：[https://www.baeldung.com/spring-aop-vs-aspectj](https://www.baeldung.com/spring-aop-vs-aspectj)