---
title: Spring5笔记
tags:
  - spring
categories:
  - notes
  - 后端
abbrlink: 70b6ad8d
date: 2020-06-27 22:55:36
---

### Spring框架概述

1、Spring是轻量级的开源JavaEE框架
2、Spring可以解决企业级应用开发的复杂性
3、Spring的两个核心部分：IOC和Aop

- IOC：控制反转，把创建对象过程交给Spring管理
- Aop：面向切面编程，不修改源代码进行功能增强

4、Spring特点

1. 方便解耦，简化开发
2. Aop编程支持
3. 方便程序测试
4. 方便和其他框架进行整合
5. 方便进行事务操作
6. 降低API开发难度

5、Spring5

![SpringFramework](https://gitee.com/zyy92/Pictures/raw/master/Spring/SpringFrame.png)

<!--more-->

#### 入门案例

[Spring主页](https://repo.spring.io/webapp/#/home)

[Spring框架](https://repo.spring.io/release/org/springframework/spring/)

### IOC

#### IOC（概念和原理）

##### IOC（Inversion of Control）

- 控制反转，把对象创建和对象之间额调用过程交给Spring进行管理
- 使用IOC目的：为了耦合度降低
- 入门案例就是IOC实现

##### IOC底层原理

xml解析、工厂模式、反射

##### IOC过程

xml配置文件，配置创建的对象	`<bean id="" class=""></bean>`

Service类和Dao类，创建工厂类

```java
class UserFactory{
    public static UserFactory getDao(){
        String classValue=class属性值;	//	xml解析
        // 通过反射创建对象
        Class clazz=Class.forName(classValue);
        return (UserDao)clazz.newInstance();
    }
}
```

进一步降低耦合度

##### IOC接口

- IOC思想基于IOC容器完成，IOC底层就是对象工厂

- Spring提供IOC容器实现的两种方式（2个接口）
  BeanFactory：IOC容器基本实现，是Spring内置接口，不提供开发人员进行使用

  加载配置文件时不会创建对象， 在获取对象或适用对象时才会创建

  ApplicationContext：BeanFactory接口的子接口，提供更强大的功能，一般由开发人员进行使用
  加载配置文件时就会把在配置文件对象进行创建

- ApplicationContext接口实现类
  FileSystemXmlApplicationContext	
  ClassPathXmlApplicationContext

#### IOC操作Bean管理

Bean管理

- Bean管理指的是两个操作
- Spring创建对象
- Spring注入属性

Bean管理操作两种方式

- 基于xml配置文件方式实现
- 基于注解方式实现

#### IOC操作Bean管理（基于xml）

##### 基于xml方式创建对象 

- 在Spring配置文件中，使用bean标签，标签里面添加对应的属性，就可以实现对象创建
- 在bean标签中有很多属性，常用属性
  id属性：唯一标识
  class属性：类全路径（包名路径）
  name属性：旧版本常用与id类似
- 创建对象时，默认执行无参构造方法完成对象创建

##### 基于xml方式注入属性

DI：依赖注入，注入属性

###### 第一种注入方式：使用set方法进行注入

1. 创建类，定义属性和相应的set方法

2. 在Spring配置文件

   ```xml
   <bean id="book" class="com.spring5.Book">
       <!--使用property完成属性注入-->
       <property name="name" value="Spring5"/>
   </bean>
   ```

###### 第二种注入方式：使用有参构造方法进行注入

1. 创建类，定义属性，创建相应的有参构造方法

2. 在Spring配置文件中

   ```xml
   <bean id="order" class="com.spring5.Order">
       <constructor-arg name="name" value="Food"/>
       <constructor-arg name="address" value="China"/>
   </bean>	
   ```
   
3. p名称空间注入
   使用p名称空间注入，可以简化基于xml注入方式

4. 添加p名称空间在配置文件中`xmlns:p="http://www.springframework.org/schema/p"`

5. 进行属性注入，在bean标签里面进行操作

   ```xml
   <bean id="b" class="com.spring5.Book" p:name="Spring5"></bean>
   ```

#### IOC操作Bean管理（xml注入其他类型属性）		

##### 字面量

null值

```xml
<property name="">
    <null/>
</property>
```

##### 属性值包含特殊符号

1. <>尖括号进行转译	`lt;` `gt;`

2. 把特殊符号内容写到CDATA

   例如：`<<value>>`

   ```xml
   <property name="name">
       <value>
           <![CDATA[<<value>>]]>
       </value>
   </property>
   ```

##### 注入属性~外部bean

创建两个类Service类和Dao类
在Service类调用Dao类里面的方法
在Spring配置文件中进行配置

```xml
<bean id="us" class="com.spring5.service.UserService">
    <!--注入UserDao对象-->
    <property name="ud" ref="udi"></property>
</bean>
<bean id="udi" class="com.spring5.dao.UserDaoImpl"></bean>
```

##### 注入属性~内部bean

一对多关系：部门和员工
在实体类之间表示一对多关系
在Spring配置文件中进行配置

```xml
<bean id="emp" class="com.spring5.bean.Employee">
    <!--设置两个普通属性-->
    <property name="name" value="Tom"/>
    <property name="gender" value="man"/>
    <!--设置对象类型属性-->
    <property name="dep">
        <bean class="com.spring5.bean.Department">
            <property name="name" value="program"></property>
        </bean>
    </property>
</bean>
```

##### 注入属性~级联赋值	

###### 方法一

```xml
<bean id="emp" class="com.spring5.bean.Employee">
    <!--设置两个普通属性-->
    <property name="name" value="Tom"/>
    <property name="gender" value="man"/>
    <!--设置对象类型属性-->
    <property name="dep">
        <bean class="com.spring5.bean.Department">
            <property name="name" ref="dep"></property>
        </bean>
    </property>
</bean>
<bean id="dep" class="com.spring5.bean.Department">
    <property name="name" value="program"></property>
</bean>
```

###### 方法二

需要生成Employee类中Department属性的set方法

```xml
<bean id="emp" class="com.spring5.bean.Employee">
    <!--设置两个普通属性-->
    <property name="name" value="Tom"/>
    <property name="gender" value="man"/>
    <!--设置对象类型属性-->			
    <property name="dep" ref="dep">
    </property>
    <property name="dep.name" value="program">
    </property>
</bean>
<bean id="dep" class="com.spring5.bean.Department">
    <property name="name" value="program">
    </property>
</bean>	
```

#### IOC操作Bean管理（xml注入集合属性）

- 注入数组类型属性

- 注入List集合类型属性

- 注入Map集合类型属性

- 注入Set集合类型属性

  ```xml
  <bean id="stu" class="com.spring5.Stu">
      <!--数组类型属性注入-->
      <property name="courses">
          <array>
              <value>Java</value>
              <value>SQL</value>
          </array>
      </property>
      <!--list类型属性注入-->
      <property name="list">
          <list>
              <value>a</value>
              <value>b</value>
          </list>
      </property>
      <!--map类型属性注入-->
      <property name="map">
          <map>
              <entry key="Java" value="java"/>
              <entry key="Python" value="python"/>
          </map>
      </property>
      <!--set类型属性注入-->
      <property name="set">
          <set>
              <value>Java</value>
              <value>MySQL</value>
          </set>
      </property>
  </bean>
  ```

- 在集合里面设置对象类型值

- 把集合的注入部分提取出来
  在Spring配置文件中引入名称空间util
  使用util标签完成诸如部分的提取

#### IOC操作Bean管理（FactoryBean）

Spring有两种类型bean，一种是普通bean，另一种是工厂bean（FactoryBean）
普通bean：在配置文件中定义bean类型就是返回类型
工厂bean：在配置文件中定义bean类型可以和返回类型不一样	

- 创建类，让这个类作为工厂bean，实现接口FactoryBean
- 实现接口里面的方法，在实现的方法中定义返回的bean类型

#### IOC操作Bean管理（bean作用域）

在Spring里面，设置创建bean实例是单实例还是多实例
在Spring里面，默认情况下，bean是单实例对象

在Spring配置文件中bean标签里面有属性值（scope）用于设置单实例还是多实例

> scope属性
> 	singleton表示单实例[默认值]
> 	prototype：表示多实例
> singleton与prototype区别
> 	singleton单实例，prototype多实例
> 	设置scope值是singleton时，加载Spring配置文件时候就会创建单实例对象
> 	设置scope值是prototype，不是在加载Spring配置文件时不会创建bean对象，在调用getBean()方法时创建多实例对象
> request、session

#### IOC操作Bean管理（生命周期）

- 通过构造器创建bean实例（无参构造）
- 为bean的属性设置值和其他bean引用（调用set方法）
- 调用bean的初始化的方法（需要进行配置初始化的方法）
  bean标签init-method属性
- bean可以使用（已获取对象）
- 当容器关闭时，调用bean的销毁的方法（需要进行配置销毁的方法）
  bean标签destroy-method属性

bean的后置处理器，bean生命周期的七步

1. 通过构造器创建bean实例（无参构造）
2. 为bean的属性设置值和其他bean引用（调用set方法）
3. 把bean实例传递bean后置处理器的方法
   postProcessBeforeInitialization(Object bean, String beanName)
4. 调用bean的初始化的方法（需要进行配置初始化的方法）
5. 把bean实例传递bean后置处理器的方法
   postProcessAfterInitialization(Object bean, String beanName)
6. bean可以使用（已获取对象）
7. 当容器关闭时，调用bean的销毁的方法（需要进行配置销毁的方法）

后置处理器
接口：BeanPostProcessor

#### IOC操作Bean管理（xml自动装配）

##### 自动装配

根据指定装配规则（属性名称或属性类型），Spring自动将配置的属性值进行注入

bean标签autowire属性

- byName根据属性名称注入，注入值bean的id值和类属性名称一样
- byType根据属性类型注入		

##### 自动装配过程

根据属性名称自动注入

根据属性类型自动注入

#### IOC操作Bean管理（外部属性文件）

直接配置数据库信息
配置连接池--德鲁伊druid（Alibaba）

```xml
<bean id="dataSource" class="com.alibaba.druid.pool.DruidDataSource">
    <property name="driverClassName" value="com.mysql.jdbc.Driver"></property>
    <property name="url" value="jdbc:mysql://localhost:3306/study"></property>
    <property name="username" value="root"></property>
    <property name="password" value="123456"></property>
</bean>
```

引入外部属性文件配置数据库连接池

- 创建外部属性文件，properties格式文件，写数据库信息
  			

  ```properties
  prop.driverClass=com.mysql.jdbc.Driver
  prop.url=jdbc:mysql://localhost:3306/study
  prop.userName=root
  prop.password=123456
  ```

- 把外部properties属性文件引入到Spring配置文件
  引入context名称空间

  ```xml
  xmlns:context="http://www.springframework.org/schema/context"
  xsi:schemaLocation=”http://www.springframework.org/schema/context https://www.springframework.org/schema/context/spring-context.xsd"
  ```

  在Spring配置文件中使用标签  

  ```xml
  <context:property-placeholder location="classpath:jdbc.properties"/>
  <bean id="dataSource" class="com.alibaba.druid.pool.DruidDataSource">
      <property name="driverClassName" value="${prop.driverClass}"/>
      <property name="url" value="${prop.url}"/>
      <property name="username" value="${prop.userName}"/>
      <property name="password" value="${prop.password}"/>
  </bean>
  ```

#### IOC操作Bean管理（基于注解方式）

Spring针对Bean管理中创建对象提供的注解

- @Component	
- @Service	业务逻辑层
- @Controller 	Web层
- @Repository 	Dao层		

以上四个注解功能时一样的，都可以用来创建bean实例

##### 基于注解方式实现对象创建

1. 引入依赖	`spring-aop.jar`

2. 开启组件扫描
   使用context命名空间
   `<context:component-scan base-package=""/>`

3. 创建类，在类上面添加相应的注解

   ```xml
   use-default-filters="false" 不适用默认的filter
   <context:include-filter type="" expression=""/> 设置扫描哪些内容
   <context:exclude-filter type="" expression=""/>设置哪些内容不进行扫描			
   ```

##### 基于注解方式实现属性注入

- @Autowired：根据属性类型进行自动装配
  把service和dao对象创建，在service和dao实现类添加创建对象注解
  在service注入dao对象，在service类添加dao类型属性，在属性上面使用注解

- @Qualifier：根据属性名称进行注入

  @Qualifier注解的使用和@Autowired一起使用

  ```java
  @Autowired
  @Qualifier(value = "userDaoImpl")   // 根据名称进行注入
  ```

- @Resource：可以根据类型注入；可以根据名称注入

  ```java
  @Resource   // 根据类型注入
  @Resource(name = "userDaoImpl") // 根据名称注入
  ```

- @Value：注入普通类型属性

  ```java
  @Value(value = "Spring")
  private String name;
  ```

##### 完全注解开发

1. 创建配置类，替代xml配置文件

   ```java
   @Configuration
   @ComponentScan(basePackages = {"com.spring5"})
   public class SpringConfig {
   }
   ```

2. 编写测试类

   ```java
   @Test
   public void testService2() {
       ApplicationContext ac = new AnnotationConfigApplicationContext(SpringConfig.class);
       UserService us = ac.getBean("userService", UserService.class);
       us.add();
   }
   ```

### AOP

#### AOP（概念）

面向切面编程，利用AOP可以对业务逻辑的各个部分进行隔离，从而使得业务逻辑各部分之间的耦合度降低，提高程序的可重用性，同时提高了开发的效率

不通过修改源代码方式，在主干功能里面添加新功能	

登录--权限判断模块

#### AOP（底层原理）

AOP底层使用动态代理
两种情况动态代理

- 有接口情况，使用JDK动态代理
  创建接口实现类的代理对象，增强类的方法
- 没有接口情况，使用CGLIB动态代理
  创建当前类子类的代理对象，增强类的方法	

##### AOP（JDK动态代理）

使用Proxy类里面的方法创建代理对象

调用newProxyInstance()方法

- ClassLoader:类加载器
- <?>[]interface:增强方法所在的类，这个类实现的接口，支持多接口
- InvocationHandler:实现这个接口，创建代理对象，写增强的方法

#### AOP（术语）	

连接点：类里面可以被增强的方法

切入点：实际被真正增强的方法

通知（增强）：实际增强的逻辑部分
通知有多种类型

- 前置通知
- 后置通知
- 环绕通知
- 异常通知
- 最终通知	

切面：把通知应用到切入点过程		

#### AOP操作（准备）		

1. Spring框架一般基于AspectJ实现AOP操作
   AspectJ：独立的AOP框架
2. 基于AspectJ实现AOP操作
   1. 基于xml配置文件实现
   2. 基于注解方式实现
3. 在项目工程里面引入AOP相关依赖
   cglib.jar
   aopalliance.jar
   weaver.jar
   spring-aspects.jar
4. 切入点表达式
   切入点表达式作用：知道对哪个类里面的哪个方法进行增强
   语法结构：`exexcution([权限修饰符][返回类型][类全路径][方法名称]([参数列表]))`			

#### AOP操作（AspectJ注解）	

1. 创建类，在类里面定义方法
2. 创建增强类（编写增强逻辑）
   在增强类里面创建方法，让不同方法待变不同通知类型
3. 进行通知的配置
   1. 在Spring配置文件中，开启注解扫描
   2. 使用注解创建User和UserProxy对象
   3. 在增强类上面添加注解@Aspect
   4. 在Spring配置文件中开启生成代理对象
4. 配置不同类型的通知
   在增强类里面，在作为通知方法上面添加通知类型注解，使用切入点表达式配置
5. 相同切入点抽取
6. 有多个增强类中在同一个方法增强，设置增强类的优先级
   在增强类上面添加注解@Order(数字类型值)，数字类型值越小优先级越高

#### AOP操作（AspectJ配置文件）	

1. 创建两个类，增强类和被增强类，创建方法
2. 在Spring配置文件中创建两个类对象
3. 在Spring配置文件中配置切入点	

### JdbcTemplate

#### JdbcTemplate（概念和准备）

JdbcTemplate：Spring框架对JDBC进行封装，使用JDBCTemplate方便实行数据库操作

准备

1. 引入相关jar包
   druid
   mysql-connector
   spring-jdbc
   spring-tx
   spring-orm
2. 在Spring配置文件配置数据库连接池
3. 配置JdbcTemplate对象，注入DataSource
4. 创建service类，创建dao类，在service中注入dao对象，在dao中注入JdbcTemplate对象

#### JdbcTemplate操作数据库（添加）

1. 对应数据库创建实体类
2. 编写service和dao
   1. 在dao进行数据库添加操作
   2. 调用JdbcTemplate对象里面update(String sql,Object.. args)方法实现添加操作
      - 参数1：sql语句
      - 参数2：可变参数，设置sql语句值
3. 测试类

#### JdbcTemplate操作数据库（查询、修改、删除）

调用JdbcTemplate对象里面update(String sql,Object.. args)方法

##### JdbcTemplate操作数据库（查询）

查询表里面有多少条记录，返回是某个值

使用JdbcTemplate对象里面selectForObject(String sql,Class<T> requiredType)方法实现查询操作

- 参数1：sql语句
- 参数2：返回类型.class

##### JdbcTemplate操作数据库（查询返回对象）

查询表返回对象

使用JdbcTemplate对象里面selectForObject(String sql,RowMapper<? extends Object> rowMapper,Object ..args )方法实现查询操作

- 参数1：sql语句
- 参数2：RowMapper,接口，返回不同的数据类型，使用这个接口的实现类完成数据封装
- 参数3：sql语句值

##### JdbcTemplate操作数据库（查询返回集合）

查询表返回集合

使用JdbcTemplate对象里面query(String sql,RowMapper<? extends Object> rowMapper,Object ..args )方法实现查询操作

- 参数1：sql语句
- 参数2：RowMapper,接口，返回不同的数据类型，使用这个接口的实现类完成数据封装
- 参数3：sql语句值

##### JdbcTemplate操作数据库（批量添加）

批量操作：操作表里面多条记录

JdbcTemplate实现批量操作batchUpdate(String sql,List<Object[]> batchArgs)

- 参数1：sql语句
- 参数2：List集合，添加多条记录数据

##### JdbcTemplate操作数据库（批量修删除）	

批量操作：操作表里面多条记录

JdbcTemplate实现批量操作batchUpdate(String sql,List<Object[]> batchArgs)

- 参数1：sql语句
- 参数2：List集合，添加多条记录数据

### 事务

事务是数据库操作最基本单元，逻辑上一组操作，要么都成功，如果一个失败所有操作都失败

典型场景：银行转账
	

##### 事务四个特性（ACID）

- 原子性
- 一致性
- 隔离性
- 持久性

###### 问题引入

1. 创建数据库表，添加记录
2. 创建service，搭建dao，完成对象创建和注入关系
   service注入dao，在dao注入JdbcTemplate，在JdbcTemplate注入DataSource
3. 在dao创建两个方法，多钱和少钱，在service创建方法（转账的方法），调用dao中的两个方法
4. 在执行过程中出现异常时，数据会出错

解决方法：使用事务

###### 事务操作过程

1. 开启事务
2. 进行业务操作
3. 没有异常，提交事务
4. 出现异常，事务回滚

##### 事务操作（Spring事务管理介绍）

1. 事务添加到Java EE三层结构里面Service层（业务逻辑层）
2. 在Spring进行事务管理操作
   编程式事务管理和声明式事务管理（使用）
3. 声明式事务管理
   1. 基于注解方法
   2. 基于xml配置文件方式
4. 在Spring进行声明式事务管理，底层使用AOP原理
5. SPring事务管理API
   PlatformTransactionManager 接口--事务管理器，针对不同框架提供不同实现类

##### 事务操作（注解声明式事务管理）		

1. 在Spring配置文件配置事务管理器

2. 在Spring配置文件中，开启事务注解

   1. 在Spring配置文件中引入名称空间tx
   2. 开启事务注解

3. 在Service类上面（或者Service类方法上面）添加事务注解

   @Transactional

   - 类：所有的方法都添加事务
   - 方法：只为当前方法添加事务

##### 事务操作（注解声明式事务管理参数配置）

1. 在Service类上面添加注解@Transactional，注解参数配置

2. Propagation：事务传播行为
   多事务方法（对数据库表数据进行变化的操作）进行调用

   | 行为         |                             描述                             |
   | ------------ | :----------------------------------------------------------: |
   | REQUIRED     | 如果有事务在运行，当前的方法就在这个事务内运行，否则，就启动一个新的事务，并在自己的事务内运行 |
   | REQUIRED_NEW | 当前的方法必须启动新事务，并在它自己的事务内运行，如果有事务正在运行，应该将它挂起 |
   | SUPPORTS     | 如果有事务在运行，当前的方法就在这个事务内运行，否则它可以不运行在事务中 |
   | NOT_SUPPORTS |   当前的方法不应该运行在事务中，如果有运行的事务，将它挂起   |
   | MANDATORY    | 当前的方法必须运行在事物内部，如果没有正在运行的事务，就抛出异常 |
   | NEVER        |  当前的方法不应该运行在事务中，如果有运行的事务，就抛出异常  |
   | NESTED       | 如果有事务在运行，当前的方法就应该在这个事务的嵌套事务内运行，否则，就启动一个新的事务，并在它自己的事务内运行 |

3. Ioslation：事务隔离级别
   事务有特性称为隔离性，多事务操作之间不会产生影响，不考虑隔离性产生很多问题
   读问题：脏读、不可重复读、虚（幻）读

   1. 脏读：一个未提交事务读取到另一个未提交事务的数据
   2. 不可重复读：一个未提交的事务读取到另一提交事务修改的数据
   3. 虚读：一个未提交事务读取到另一提交事务添加数据		

   | 隔离级别                    | 脏读 | 不可读 | 虚读 |
   | --------------------------- | ---- | ------ | ---- |
   | READ_UNCOMMITED（读未提交） | 有   | 有     | 有   |
   | READ_COMMITTED（读已提交）  | 无   | 有     | 有   |
   | REPEATABLE_READ（可重复读） | 无   | 无     | 有   |
   | SERIALIZABLE（串行化）      | 无   | 无     | 无   |

4. TimeOut：超时时间

   事务需要在一定时间内进行提交，如果不提交进行回滚
   默认值是-1，设置时间以秒为单位进行计算

5. ReadOnly：是否只读
   读：查询操作，写：添加修改删除操作

   1. 默认值为false，表示可以查询，可以添加修改删除操作
   2. 设置为true，表示只能查询

6. RollbcakFor：回滚
   设置出现哪些异常进行事务回滚

7. NoRollbackFor：不回滚
   设置出现哪些异常不进行事务回滚

##### 事务操作（XML声明式事务管理）

在Spring配置文件中进行配置

- 配置事务管理器
- 配置通知
- 配置切入点和切面

##### 事务操作（完全注解声明式事务管理）

创建配置类，使用配置类代替

### Spring5框架新特性

1. 整个Spring5框架的代码基于Java8，运行时兼容JDK9，许多不建议使用的类和方法在代码库中删除

2. Spring5框架自带了通用的日志封装
   Spring5已经移除Log4jConfigListener，官方建议使用Log4j2
   Spring5框架整合Log4j2

   1. 引入jar包
      log4j-api-2
      log4j-core-2
      log4j-slf4j-impl-2
      slf4j-api
   2. 创建log4j2.xml

3. Spring5框架核心容器支持@Nullable注解
   @Nullable注解可以用在方法、属性、参数上，表示方法返回值可以为空，属性值可以为空，参数值可以为空

4. Spring5核心容器支持函数式风格GenericApplicationContext
   注册对象

5. Spring5支持整合JUnit5

   - 整合JUnit4

     1. 引入Spring相关针对测试依赖
        spring-test
     2. 编写测试类
        @RunWith(SpringJUnit4ClassRunner.class)
        @ContextConfiguration("classpath:.xml")	

     注意：@Test导入的包为import org.junit.Test;

   - 整合JUnit5

     1. 引入JUnit的jar包
     2. 编写测试类
        @ExtendWith(SpringExtension.class)
        @ContextConfiguration("classpath:.xml")

     注意：@Test导入的包为import org.junit.jupiter.api.Test;

   - 使用复合注解
     @SpringJUnitConfig(locations="classpath:.xml")	

#### Spring5框架新功能

##### SpringWebFlux

Spring5新添加的模块，用于web开发的，功能SpringMVC类似的，WebFlux使用当前比较流程响应式编程出现的框架

传统web框架，例如SpringMVC，基于Servlet容器

WebFlux是一种异步非阻塞的框架，异步非阻塞的框架只在Servlet3.1以后才支持，核心是基于Reactor的相关API实现的
	

###### 异步非阻塞

异步与同步--调用者，调用者发送请求，对方回应后就去做其他事情--同步，不等待回应就去做其他事情--异步

非阻塞与阻塞--被调用者，被调用者收到请求之后，收到请求任务之后才给出反馈--阻塞，收到请求后马上给出反馈再去做其他事情--非阻塞

###### WebFlux特点

非阻塞式：在有限资源下，提高系统吞吐量和伸缩性，以Reactor为基础实现响应式编程

函数式编程：Spring5框架基于Java8，webFlux使用Java8函数式编程方式实现路由请求

比较SpringMVC

- 都可以使用注解方式，运行在Tomcat等容器中
- SpringMVC采用命令式编程，WebFlux采用异步响应式编程

![WebFlux对比SpringMVC](https://gitee.com/zyy92/Pictures/raw/master/Spring/WebFlux对比SpringMVC.png)

##### 响应式编程ReactiveProgramming

响应式编程是一种面向数据流和变化传播的编程范式。这意味着可以在编程语言中很方便地表达静态或动态的数据流，而相关的计算模型会自动将变化的值通过数据流进行传播。

Java8及其之前版本
提供的观察者模式两个类Observer和Observable	--伪响应式编程
		
Flow：Publisher、Subscriber

###### 响应式编程Reactor实现

1. 响应式编程操作中，满足Reactive规范框架

2. Mono和Flux两个类都实现了接口Publisher，提供丰富操作符

   1. Flux对象实现发布者，返回N个元素
   2. Mono实现发布者，返回0或1个元素

3. Flux和Mono都是数据流的发布者，使用Flux和Mono都可以发出三种数据信号
   元素值、错误信号、完成信号
   错误信号和完成信号都代表终止信号，终止信号用于告诉订阅者数据流结束
   错误信号终止数据流同时把错误信息传递给订阅者

4. 代码演示
   引入依赖

   ```xml
   <dependency>
       <groupId>io.projectreactor</groupId>
       <artifactId>reactor-core</artifactId>
       <version>3.1.5.RELEASE</version>
   </dependency>
   ```

5. 三种信号特点

   1. 错误信号和完成信号都是终止信号，不能共存
   2. 如果没有发送任何元素值，而是直接发送错误信号或者完成信号，表示是空数据流
   3. 如果没有错误信号，没有完成信号，表示是无限数据流

6. 调用just或者其他方法只是声明数据流，数据流并没有发出，只有进行订阅之后才会触发数据流，不订阅什么都不会发生的
   subscribe()

7. 操作符
   对数据流进行一道道操作，称为操作符

   - map	元素映射为新元素
   - flatMap 元素映射为流
     把每个元素转换成流，把转换后的多个流合成更大的流	

##### WebFlux执行流程和核心API

SpringWebFlux基于Reactor，默认容器是Netty，Netty是高性能的NIO框架，异步非阻塞的框架

- Netty

  - BIO：Blocking I/O
  - NIO：Non-Blocking I/O

- SpringWebFlux执行过程和SpringMVC相似
  SpringWebFlux核心控制器 DispatchHandler，实现接口WebHandler

  ```java
  public interface WebHandler {
  			Mono<Void> handle(ServerWebExchange var1);
  }
  ```

- SpringWebFlux里面DispatchHandler，负责请求的处理

  - HandlerMapping：请求查询到处理的方法
  - HandlerAdapter：真正负责请求处理
  - HandlerResultHandler：响应结果处理

- SpringWebFlux实现函数式编程，两个接口：RouterFunction（路由处理）,HandlerFunction（处理函数）
  	

##### SpringWebFlux（基于注解编程模型）

只需要把相关依赖配置到项目中，SpringBoot自动配置相关运行容器，默认情况下使用Netty容器

举例：SpringBoot	2.2.1.RELEASE

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-webflux</artifactId>
</dependency>
```

配置启动端口号

创建相关包和类service、controller

说明：

- SpringMVC方式实现，同步阻塞的方式，基于SpringMVC+Servlet+Tomcat
- SpringWebFlux方式实现，异步非阻塞方式，基于SpringWebFlux+Reactor+Netty

##### SpringWebFlux（基于函数式编程模型）

- 在使用函数式编程模型操作时，需要自己初始化服务器

- 基于函数式编程模型，两个核心接口：

  - RouterFunction：实现路由功能，请求转发给对应的handler
  - HandlerFunction：处理请求生成响应的函数

  核心任务定义两个函数式接口的实现并且启动需要的服务器

- SpringWebFlux请求和响应不再是ServletRequest和ServletResponse，而是ServerRequest和ServerResponose

##### 具体流程

1. 配置项目
2. 创建相关包和类service
3. 创建Handler
4. 初始化服务器，编写Router
5. 创建服务器完成适配
6. 普通调用或使用WebClient调用