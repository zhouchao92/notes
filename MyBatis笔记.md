---
title: MyBatis笔记
tags:
  - mybatis
categories:
  - notes
  - 后端
abbrlink: '93fcac00'
date: 2020-06-30 14:52:53
---

#### MyBatis简介

MyBatis 是一款优秀的持久层框架，它支持自定义 SQL、存储过程以及高级映射。MyBatis 免除了几乎所有的 JDBC 代码以及设置参数和获取结果集的工作。MyBatis 可以通过简单的 XML 或注解来配置和映射原始类型、接口和 Java POJO（Plain Old Java Objects，普通老式 Java 对象）为数据库中的记录。

##### 原始jdbc操作

原始jdbc开发存在的问题：

- 数据库连接创建、释放频繁造成系统资源浪费从而影响系统性能
- sql语句在代码中硬编码，造成代码不易维护，实际应用sql变化可能较大，sql变动需要改变java代码
- 查询操作时，需要手动将结果集中的数据封装到实体中；插入操作时，需要手动将实体的数据设置到sql语句的占位符位置

解决方案：

- 使用数据库连接池初始化连接资源
- 将sql语句抽取到xml配置文件中
- 使用反射、内省等底层技术，自动将实体与表进行属性与字段的自动映射

##### MyBatis

- MyBatis是一个优秀的基于Java的持久层框架，它内部封装了jdbc，使开发者只需要关注sql语句本身，而不需要花费精力去处理加载驱动、创建连接、创建statement等繁杂的过程。
- MyBatis通过xml或注解的方式将要执行的各种statement配置起来，并通过java对象和statement中sql的动态参数进行映射生成最终执行的sql语句。
- MyBatis框架执行sql将结果映射为java对象并返回。采用**ORM**（Object Relational Mapping 对象关系映射）思想解决了实体和数据库映射的问题，对jdbc进行了封装，屏蔽了jdbc api底层访问细节。

<!--more-->

#### MyBatis快速入门

##### 开发步骤

1. 在`pom.xml`中添加MyBatis坐标（依赖）
2. 创建user数据表
3. 编写User实体类
4. 编写映射文件UserMapper.xml
5. 编写核心文件SqlMapConfig.xml
6. 编写测试类

##### 具体代码实现

###### 添加MyBatis坐标

```xml
<dependencies>
    <dependency>
        <groupId>mysql</groupId>
        <artifactId>mysql-connector-java</artifactId>
        <version>5.1.49</version>
    </dependency>
    <dependency>
        <groupId>org.mybatis</groupId>
        <artifactId>mybatis</artifactId>
        <version>3.5.5</version>
    </dependency>
    <!--单元测试-->
    <dependency>
        <groupId>junit</groupId>
        <artifactId>junit</artifactId>
        <version>4.12</version>
        <scope>test</scope>
    </dependency>
    <!--log输出管理-->
    <dependency>
        <groupId>log4j</groupId>
        <artifactId>log4j</artifactId>
        <version>1.2.12</version>
    </dependency>
</dependencies>
```

###### 创建user数据表

```mysql
# 创建表
CREATE TABLE user  (
  uid int(10) NOT NULL AUTO_INCREMENT,
  uname varchar(50) NOT NULL,
  password varchar(50) NOT NULL,
  PRIMARY KEY (uid)
);
# 插入测试数据
INSERT INTO user VALUES(1,"zhangsan","123");
INSERT INTO user VALUES(2,"lisi","456");
INSERT INTO user VALUES(3,"wangwu","789");
```

###### 编写User实体类

```java
public class User {
    private int uid;
    private String uname;
    private String password;
    getter/setter...
}
```

###### 编写映射文件UserMapper.xml

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="userMapper">
    <select id="query" resultType="com.study.domain.User">
        select * from user
    </select>
</mapper>
```

###### 编写核心文件SqlMapConfig.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE configuration
        PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-config.dtd">
<configuration>
    <!--配置数据源环境-->
    <environments default="development">
        <environment id="development">
            <transactionManager type="JDBC"/>
            <dataSource type="POOLED">
                <property name="driver" value="com.mysql.jdbc.Driver"/>
                <property name="url" value="jdbc:mysql://localhost:3306/study"/>
                <property name="username" value="root"/>
                <property name="password" value="123456"/>
            </dataSource>
        </environment>
    </environments>
    <!--加载映射文件-->
    <mappers>
        <mapper resource="com/study/mapper/UserMapper.xml"/>
    </mappers>
</configuration>
```

###### 编写测试类

```java
public class MyBatisTest {

    @Test
    public void testQuery() throws IOException {
        // 获取核心配置文件
        InputStream resourceAsStream = Resources.getResourceAsStream("SqlMapConfig.xml");
        // 获取sqlSession工厂对象
        SqlSessionFactory sqlSessionFactory = new SqlSessionFactoryBuilder().build(resourceAsStream);
        // 获取sqlSession会话对象
        SqlSession sqlSession = sqlSessionFactory.openSession();
        // 执行操作
        List<User> userList = sqlSession.selectList("userMapper.query");
        System.out.println(userList);
        // 释放资源
        sqlSession.close();
    }
}
```

#### MyBatis映射文件概述

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="userMapper">
    <select id="query" resultType="com.study.domain.User">
        select * from user
    </select>
</mapper>
```

映射文件DTD约束头

```xml
<!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN" "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
```

`<mapper></mapper>`根标签

`namespace="userMapper"`命名空间，与下面语句的id组成查询的标识

`<select></select>`查询操作，可选的还有`insert`、`update`、`delete`标签

` id="query"`语句的id标识，与上面的命令空间组成查询的标识

`resultType="com.study.domain.User"`查询结果对应的实体类型

`select * from user`要执行的sql语句

##### namespace命名空间

命名空间的作用有两个，一个是利用更长的全限定名来将不同的语句隔离开来，同时也实现了你上面见到的接口绑定。就算你觉得暂时用不到==接口绑定==，你也应该遵循这里的规定，以防哪天你改变了主意。 长远来看，只要将命名空间置于合适的 Java 包命名空间之中，你的代码会变得更加整洁，也有利于你更方便地使用 MyBatis。

#### MyBatis增删改查操作

##### MyBatis的插入数据操作

1. 编写映射文件UserMapper.xml
2. 修改编写实体User的代码

###### 编写映射文件UserMapper.xml

```xml
<insert id="add" parameterType="com.study.domain.User">
    insert into user values(#{uid},#{uname},#{password})
</insert>
```

###### 修改编写实体User的代码

```java
@Test
public void testAdd() throws IOException {
    // 模拟User对象
    User u = new User();
    u.setUname("zhaoliu");
    u.setPassword("000");

    // 获取核心配置文件
    InputStream resourceAsStream = Resources.getResourceAsStream("SqlMapConfig.xml");
    // 获取sqlSession工厂对象
    SqlSessionFactory sqlSessionFactory = new SqlSessionFactoryBuilder().build(resourceAsStream);
    // 获取sqlSession会话对象
    SqlSession sqlSession = sqlSessionFactory.openSession();
    // 执行操作
    int res = sqlSession.insert("userMapper.add", u);
    System.out.println(res);
    // 事务提交
    sqlSession.commit(res > 0);
    // 释放资源
    sqlSession.close();
}
```

###### 插入操作注意的问题

- 插入语句使用`insert`标签
- 在映射问文件中使用`parameterType`属性指定要插入的数据类型
- sql语句中使用`#{实体属性名}`方式引用实体中的属性值
- 插入操作使用的API是`sqlSession.insert("命名空间.id",具体实体对象)`
- 插入操作设计数据库数据变化，所以要用sqlSession对象显式的提交即`sqlSession,commit()`

##### MyBatis的修改数据操作

###### 编写映射文件UserMapper.xml

```xml
<update id="update" parameterType="com.study.domain.User">
    update user set uname=#{uname},password=#{password} where uid=#{uid}
</update>
```

###### 修改编写实体User的代码

```java
@Test
public void testUpdate() throws IOException {
    // 模拟User对象
    User u = new User();
    u.setUid(3);
    u.setUname("wangwu333");
    u.setPassword("333");

    // 获取核心配置文件
    InputStream resourceAsStream = Resources.getResourceAsStream("SqlMapConfig.xml");
    // 获取sqlSession工厂对象
    SqlSessionFactory sqlSessionFactory = new SqlSessionFactoryBuilder().build(resourceAsStream);
    // 获取sqlSession会话对象
    SqlSession sqlSession = sqlSessionFactory.openSession();
    // 执行操作
    int res = sqlSession.update("userMapper.update", u);
    System.out.println(res);
    // 事务提交
    sqlSession.commit(res > 0);
    // 释放资源
    sqlSession.close();
}
```

###### 修改操作注意的问题

- 修改语句使用update标签
- 修改操作使用的API是sqlSession.updae("命名空间.id",具体实体对象)

##### MyBatis的删除数据操作

###### 编写映射文件UserMapper.xml

```xml
<delete id="delete" parameterType="java.lang.Integer">
    delete from user where uid=#{uid}
</delete>
```

###### 修改编写实体User的代码

```java
@Test
public void testDelete() throws IOException {
    // 获取核心配置文件
    InputStream resourceAsStream = Resources.getResourceAsStream("SqlMapConfig.xml");
    // 获取sqlSession工厂对象
    SqlSessionFactory sqlSessionFactory = new SqlSessionFactoryBuilder().build(resourceAsStream);
    // 获取sqlSession会话对象
    SqlSession sqlSession = sqlSessionFactory.openSession();
    // 执行操作
    int res = sqlSession.delete("userMapper.delete", 6);
    System.out.println(res);
    // 事务提交
    sqlSession.commit(res > 0);
    // 释放资源
    sqlSession.close();
}
```

###### 删除操作注意的问题

- 删除语句使用delete标签
- sql语句中使用#{任意字符串}方式引用传递的单个参数
- 删除操作使用的API是sqlSession.delete("命名空间.id",Object);

#### MyBatis核心配置文件概述

##### MyBatis核心配置文件层级关系

configuration配置

- properties 属性
- settings 设置
- typeAliases 类型别名
- objectFactory 对象工厂
- plugins 插件
- environments 环境
  - environment 环境变量
    - transactionManager 事务管理器
    - dataSource 数据源
- databaseIdProvider 数据库厂商标识
- mappers 映射器

##### MyBatis常用配置解析

###### environments标签

数据库环境的配置，支持多环境配置

```xml
<!--指定默认的环境名称-->
<environments default="development">
    <!--指定当前的环境名称-->
    <environment id="development">
        <!--指定事务管理器类型是JDBC-->
        <transactionManager type="JDBC"/> 
        <!--指定当前数据源类型是连接池-->
        <dataSource type="POOLED"> 
            <!--数据源驱动-->
            <property name="driver" value="com.mysql.jdbc.Driver"/>	 
            <!--url-->
            <property name="url" value="jdbc:mysql://localhost:3306/study"/>  
            <!--用户名-->
            <property name="username" value="root"/> 
            <!--密码-->
            <property name="password" value="root"/>  
        </dataSource>
    </environment>
</environments>
```

其中，事务管理器（transactionManager）类型有两种

- JDBC：使用JDBC的提交和回滚事务设置，它依赖于从数据源得到的连接来管理事务作用域。
- MANAGED：它从来不提交或回滚一个连接，而是让容器来管理事务的整个生命周期（比如JEE应用服务器的上下文）。默认情况下它会关闭连接，然而一些容器并不希望这样，因此需要将closeConnection设置为false来组织它默认的关闭行为。

数据源（datasource）类型有三种

- UNPOOLED：这种数据源实现只是每次被请求时打开和关闭连接。
- POOLED：这种数据源的实现利用==“池”==的概念将JDBC连接对象组织起来。
- JNDI：这种数据源的实现是为了能在如EJB或应用服务器这类容器中使用，容器可以集中或在外部配置数据源，然后在放置一个JNDI上下文的引用。

###### mappers标签

mappers标签的作用是加载映射，加载方式

- 使用相对类路径的资源引用，例如：`<mapper resource="org/mybtatis/builder/AuthorMapper.xml"/>`。
- 使用完全限定资源定位符（URL），例如：`<mapper ="file:///var/mappers/AuthorMapper.xml"/>`。
- 使用映射器接口实现类的完全限定类名，例如：`<mapper class="org.mybatis.builder.AuthorMapper"/>`。
- 将包内的映射器接口实现全部注册为映射器，例如：`package name="org.mybatis.builder"`。

###### properties标签

> `jdbc.properties`文件
>
> ```properties
> jdbc.driver=com.mysql.jdbc.Driver
> jdbc.url=jdbc:mysql://localhost:3306/study
> jdbc.username=root
> jdbc.password=root
> ```
>

> `SqlMapConfig.xml`文件
>
> ```xml
> <!--引入外部properties配置文件-->
> <properties resource="jdbc.properties"/>
> 
> <!--配置数据源环境-->
> <environments default="development">
>     <environment id="development">
>         <transactionManager type="JDBC"/>
>         <dataSource type="POOLED">          
>             <property name="driver" value="${jdbc.driver}"/>
>             <property name="url" value="${jdbc.url}"/>
>             <property name="username" value="${jdbc.username}"/>
>             <property name="password" value="${jdbc.password}"/>
>         </dataSource>
>     </environment>
> </environments>
> ```
>

###### typeAliases标签

类型别名是为java类型设置别名，配置typeAliases为`com.sutdy.domain.User`定义为别名。

```xml
<typeAliases>
    <typeAlias type="com.study.domain.User" alias="user"/>
</typeAliases>
```

```xml
<select id="query" resultType="user">
    select * from user
</select>
```

MyBatis框架已定义常用的类型别名

|   别名    | 数据类型  |
| :-------: | :-------: |
| `string`  | `String`  |
|  `long`   |  `Long`   |
|   `int`   | `Integer` |
| `double`  | `Double`  |
| `boolean` | `Boolean` |

#### MyBatis相应API

##### SqlSession工厂构建器SqlSessionFactoryBuilder

常用API：`SqlSessionFactoryBuilder().build(InputStream inputStream);`
通过加载MyBatis的核心文件的输入流的形式构建一个SqlSessionFactory对象。

```java
// 获取核心配置文件
InputStream resourceAsStream = Resources.getResourceAsStream("SqlMapConfig.xml");
// 获取sqlSession工厂对象
SqlSessionFactory sqlSessionFactory = new SqlSessionFactoryBuilder().build(resourceAsStream);
```

其中，Resources工具类在org.apache.ibatis,io包中，Resources类从类路径下、文件系统或一个web URL中加载资源文件。

##### SqlSession工厂对象SQLSessionFactory

SqlSessionFactory有多个方法创建SqlSession实例，常用方法：

|               方法                |                             解释                             |
| :-------------------------------: | :----------------------------------------------------------: |
|          `openSession()`          | 会默认开启一个事务，但事务不会自动提交，需要手动提交该事务，更新操作（增、删、改）才会持久化到数据库中。 |
| `openSession(boolean autoCommit)` | 参数为是否自动提交事务，如果设置为`true`，则不需要手动提交事务。 |

###### SqlSessionj会话对象

SqlSession实例，包含执行语句、提交、回滚事务和获取映射器实例的方法。

执行语句的方法

- `<T> T selectOne(String statement, Object parameter);`
- `<E> List<E> selectList(String statement, Object parameter);`
- `int insert(String statement, Object parameter);`
- `int update(String statement, Object parameter);`
- `int delete(String statement, Object parameter);`

操作事务的方法

- `void commit(boolean paramter);`
- `void rollback();`

#### MyBatis的Dao层实现

##### 传统方式

- 编写UserDao接口和UserDaoImpl实现类
- 编写UserService接口和UserServiceImpl实现类
- 编写客户端测试类

##### 代理开发方式

采用MyBatis的代理开发方式实现Dao层的开发。

Mapper接口方法只需要编写Mapper接口（相当于Dao接口），由MyBatis框架根据接口定义创建接口的动态代理对象，代理对象的方法体与传统的Dao接口实现类方法相同。

Mapper接口开发需要遵循的规范：

1. mapper.xml文件中的namespace与Mapper接口的全限定名相同
2. Mapper几口方法名和Mapper.xml中定义的每个statement的id相同
3. Mapper接口方法的输入参数类型和mapper.xml中定义的每个sql的parameterType的类型相同
4. Mapper接口方法的输出参数类型和mapper.xml中定义的每个sql的resultType的类型相同

#### MyBatis映射文件

MyBatis映射文件Mapper.xml标签

- `<select>`：查询
- `<insert>`：插入
- `<update>`：修改
- `<delete>`：删除
- `<where>`：where条件
- `<if>`：if判断
- `<foreach>`：循环
- `<sql>`：sql片段抽取

##### 动态sql语句

MyBatis的映射文件中，业务逻辑复杂时，sql语句是动态变化的。

- if
- choose(when,otherwise)
- trim(where,set)
- foreach

###### if标签

and多条件类型

`select * from where uid=? and uname=? and password=?`

```xml
<select id="findByCondition" parameterType="user" resultType="user">
    select *
    from user
    <where>
        <if test="uid!=0">
            and uid = #{uid}
        </if>
        <if test="uname!=null">
            and uname = #{uname}
        </if>
        <if test="password!=null">
            and password = #{password}
        </if>
    </where>
</select>
```

###### foreach标签

or多条件类型

`select * from where uid in(?,?,...)`

```xml
<select id="findByIds" resultType="user" parameterType="int">
    select * from user
    <where>
        <foreach collection="list" open="uid in(" close=")" item="uid" separator=",">
            #{uid}
        </foreach>
    </where>
</select>
```

##### sql片段抽取

将sql语句中重复的部分抽取出来，使用时用include引用即可，达到sql重用的目的。

```xml
<!--sql抽取-->
<sql id="selectUser">
    select *
    from user
</sql>
<!--引用-->
<include refid="selectUser"/>
```

#### MyBatis核心配置文件

##### typeHandlers标签

无论是MyBatis在预处理语句（PrepareStatement）中设置一个参数时，还是从结果中取出一个值，都会用类型处理器将获取的值以合适的方式转换称Java类型。

<div align="center">默认的类型处理器</div>

|      类处理器       |         Java类型          |              JDBC类型              |
| :-----------------: | :-----------------------: | :--------------------------------: |
| BooleantTypeHandler | java.lang.Boolean,boolean |        数据库兼容的BOOLEAN         |
|   ByteTypeHandler   |    java.lang.Byte,byte    |     数据库兼容的NUMERIC或BYTE      |
|  ShortTypeHandler   |   java.lang.Short,short   | 数据库兼容的NUMERIC或SHORT INTEGER |
| IntegerTypeHandler  |   java.lang.Integer,int   |    数据库兼容的NUMERIC或INTEGER    |
|   LongTypeHandler   |    java.lang.Long,long    | 数据库兼容的NUMERIC或LONG INTEGER  |

可以重写类型处理器或创建自定义的类型处理器来处理不支持或非标准的类型。

具体做法

- 方法一：实现org.apach.ibatis.type.TypeHandler接口
- 方法二：继承org.apach.ibatis.type.BaseTypeHandler类，然后选择性地将它映射到一个JDBC类型。

###### 自定义类型处理器开发步骤

1. 定义转换类继承`BaseTypeHandler<T>`

2. 覆盖4个未实现的方法，其中setNonNullParameter为java程序设置数据到数据库的回调方法，getNullableResult为查询时mysql的字符串类型转换成java的Type类型的方法

   ```java
   public class DateTypeHandler extends BaseTypeHandler<Date> {
       // java-->数据库
       public void setNonNullParameter(PreparedStatement preparedStatement, int i, Date date, JdbcType jdbcType) throws SQLException {
           preparedStatement.setLong(i, date.getTime());
       }
   
       // 数据库-->java
       public Date getNullableResult(ResultSet resultSet, String s) throws SQLException {
           long time = resultSet.getLong(s);
           return new Date(time);
       }
   
       // 数据库-->java
       public Date getNullableResult(ResultSet resultSet, int i) throws SQLException {
           long time = resultSet.getLong(i);
           return new Date(time);
       }
   
       // 数据库-->java
       public Date getNullableResult(CallableStatement callableStatement, int i) throws SQLException {
           long time = callableStatement.getLong(i);
           return new Date(time);
       }
   }
   ```

3. 在MyBatis核心配置文件中进行注册

   ```xml
   <!--自定义类型处理器-->
   <typeHandlers>
       <typeHandler handler="com.study.handler.DateTypeHandler"/>
   </typeHandlers>
   ```

4. 测试转换是否正确

##### plugins标签

MyBatis可以使用第三方的插件来对功能进行扩展，分页助手PageHandler是将分页的复杂操作进行封装，使用简单的方式即可获得分页的相关数据。

开发步骤

1. 导入PagerHelper坐标

   ```xml
   <dependency>
       <groupId>com.github.pagehelper</groupId>
       <artifactId>pagehelper</artifactId>
       <version>5.1.11</version>
   </dependency>
   <dependency>
       <groupId>com.github.jsqlparser</groupId>
       <artifactId>jsqlparser</artifactId>
       <version>3.1</version>
   </dependency>
   ```

2. 在MyBatis核心配置文件中配置PageHelper插件

   ```xml
   <plugins>
       <plugin interceptor="com.github.pagehelper.PageInterceptor">
           <!--PageHelper4.0以后不用配置-->
           <!--<property name="dialect" value="mysql"/>-->
       </plugin>
   </plugins>
   ```

3. 测试分页数据获取

   ```java
   // 当前页码，每一页内容数量
   PageHelper.startPage(int pageNum, int pageSize);	
   ```

其他分页信息获取

```java
Logger logger = Logger.getLogger(UserMapperTest.class);

// 获取与分页相关参数
PageInfo<User> pageInfo = new PageInfo<User>(res);
logger.debug("当前页：" + pageInfo.getPageNum());
logger.debug("总页数：" + pageInfo.getPages());
logger.debug("总条数：" + pageInfo.getTotal());
logger.debug("每页显示的数量：" + pageInfo.getPageSize());
logger.debug("上一页：" + pageInfo.getPrePage());
logger.debug("下一页：" + pageInfo.getNextPage());
logger.debug("是否是首页：" + pageInfo.isIsFirstPage());
logger.debug("是否是尾页：" + pageInfo.isIsLastPage());
```

#### MyBatis多表操作

```powershell
mysql> select * from user_mb;
+-----+-----------+----------+---------------+
| uid | uname     | password | birthday      |
+-----+-----------+----------+---------------+
|   1 | zhangsan  | 123      | 1593414232744 |
|   2 | lisi      | 456      | 1593414233749 |
|   3 | wangwu333 | 333      | 1593414234745 |
|   7 | tom       | 222      | 1593414234749 |
+-----+-----------+----------+---------------+

mysql> select * from order_mb;
+-----+---------------------+---------+-----+
| oid | orderTime           | total   | uid |
+-----+---------------------+---------+-----+
|   1 | 2020-06-29 16:16:03 | 1000.00 |   1 |
|   2 | 2020-06-29 16:16:25 | 1500.00 |   1 |
|   3 | 2020-06-29 16:16:42 | 2000.00 |   2 |
|   4 | 2020-06-29 16:17:14 | 3000.00 |   3 |
+-----+---------------------+---------+-----+

mysql> select * from role_mb;
+-----+-----------------+
| rid | rolename        |
+-----+-----------------+
|   1 | 程序员           |
|   2 | 前端设计师        |
|   3 | 项目经理         |
|   4 | 测试人员         |
|   5 | 架构师           |
+-----+-----------------+

mysql> select * from user_role_mb;
+-----+-----+
| uid | rid |
+-----+-----+
|   1 |   1 |
|   1 |   2 |
|   2 |   3 |
|   3 |   4 |
+-----+-----+
```

##### 一对一查询

模型：用户表和订单表的关系为，一个用户有多个订单，一个订单只从属于一个用户。

一对一查询的需求：查询一个订单，与此同时查询出该订单所属的用户。

配置：`<resultMap>`

###### 映射方式一

```xml
<mapper namespace="com.study.mapper.OrderMapper">
    <!--
    指定字段与实体属性的映射关系
    column:数据表的字段名称
    property:实体的属性名称
-->
    <resultMap id="orderMap" type="order">
        <id column="oid" property="oid"/>
        <result column="orderTime" property="orderTime"/>
        <result column="total" property="total"/>
        <result column="uid" property="user.uid"/>
        <result column="uname" property="user.uname"/>
        <result column="password" property="user.password"/>
        <result column="birthday" property="user.birthday"/>
    </resultMap>
    <!--查询-->
    <select id="findAll" resultMap="orderMap">
        select *
        from order_mb o,
        user_mb u
        where o.uid = u.uid
    </select>
</mapper>
```

###### 映射方式二 - association

```xml
<resultMap id="orderMap" type="order">
    <id column="oid" property="oid"/>
    <result column="orderTime" property="orderTime"/>
    <result column="total" property="total"/>
    <!--
        property:当前实体(order)中的属性名称(private User user);
        javaType:当前实体(order)中的属性的类型(com.study.domain.User)
    -->
    <association property="user" javaType="user">
        <id column="uid" property="uid"/>
        <result column="uname" property="uname"/>
        <result column="password" property="password"/>
        <result column="birthday" property="birthday"/>
    </association>
</resultMap>
```

##### 一对多查询

用户表和订单表的关系为，一个用户有多个订单，一个订单只从属于一个用户。

一对一查询的需求：查询用户，与此同时查询出该用户的所有订单。

配置：`<resultMap>+<collection>`

```xml
<mapper namespace="com.study.mapper.UserMapper">
    <resultMap id="userMap" type="user">
        <id column="uid" property="uid"/>
        <result column="uname" property="uname"/>
        <result column="password" property="password"/>
        <result column="birthday" property="birthday"/>
        <!--
            配置集合
            property:集合名称
            ofType:当前集合中的数据类型
        -->
        <collection property="order" ofType="order">
            <id column="oid" property="oid" />
            <result column="total" property="total"/>
            <result column="orderTime" property="orderTime"/>
        </collection>
    </resultMap>

    <select id="findAll" resultMap="userMap">
        select *
        from user_mb u,
             order_mb o
        where u.uid = o.uid
    </select>
</mapper>
```

##### 多对多查询

用户表和角色表的关系为，一个用户有多个角色，一个角色被多个用户使用。

多对多查询的需求：查询用户同时查询出该用户的所有角色。

配置：`<resultMap>+<collection>`

```xml
<resultMap id="userRoleMap" type="user">
    <id column="uid" property="uid"/>
    <result column="uname" property="uname"/>
    <result column="password" property="password"/>
    <result column="birthday" property="birthday"/>

    <collection property="role" ofType="role">
        <id column="rid" property="rid"/>
        <result column="rolename" property="rolename"/>
    </collection>
</resultMap>

<select id="findUserRole" resultMap="userRoleMap">
    select *
    from user_mb u,
         role_mb r,
         user_role_mb ur
    where u.uid = ur.uid
      and r.rid = ur.rid
</select>
```

#### MyBatis注解开发

常用注解

- `@Insert`：实现新增
- `@Update`：实现删除
- `@Select`：实现查询
- `@Result`：实现结果集封装
- `@Results`：可以与Result一起使用，封装多个结果集
- `@One`：实现一对一结果集封装
- `@Many`：实现一对多结果集封装

##### 增删改查

```java
public interface UserMapper {

    @Insert("insert into user_mb values(#{uid},#{uname},#{password},#{birthday})")
    void save(User user);

    @Update("update user_mb set uname=#{uname},password=#{password},birthday=#{birthday} where uid=#{uid}")
    void update(User user);

    @Delete("delete from user_mb where uid=#{uid}")
    void delete(int uid);

    @Select("select * from user_mb where uid=#{uid}")
    User findById(int uid);

    @Select("select * from user_mb")
    List<User> findAll();

}
```

```xml
<!--加载映射关系-->
<mappers>
    <!--指定Mapper接口所在的包-->
    <package name="com.study.mapper"/>
</mappers>
```

```java
public class AnnotationTest {

    public static final Logger logger = Logger.getLogger(AnnotationTest.class);
    private UserMapper mapper;

    @Before
    public void before() throws IOException {
        InputStream inputStream = Resources.getResourceAsStream("SqlMapConfig.xml");
        SqlSessionFactory sqlSessionFactory = new SqlSessionFactoryBuilder().build(inputStream);
        SqlSession sqlSession = sqlSessionFactory.openSession(true);
        mapper = sqlSession.getMapper(UserMapper.class);

    }

    @Test
    public void testSave() {
        User user = new User();
        user.setUname("tianqi");
        user.setPassword("222");
        user.setBirthday(new Date());
        mapper.save(user);
    }

    @Test
    public void testUpdate() {
        User user = new User();
        user.setUid(8);
        user.setUname("tianqi555");
        user.setPassword("555");
        user.setBirthday(new Date());
        mapper.update(user);
    }

    @Test
    public void testDelete() {
        mapper.delete(8);
    }

    @Test
    public void testFindById() {
        User user = mapper.findById(2);
        logger.debug(user);
    }

    @Test
    public void testFindAll() {
        List<User> result = mapper.findAll();
        logger.debug(result);
    }
}
```

##### 复杂映射开发

|     注解      |                             说明                             |
| :-----------: | :----------------------------------------------------------: |
|  `@Results`   | 代替的是标签\<resultMap>,该注解中可以使用单个@Result注解，也可以使用@Result结合。使用格式：@Results({@Result(),@Result()})或@Results(@Result()) |
|   `@Result`   | 代替了\<id>标签和\<result>标签<br />@Result中属性介绍<br />column：数据库的列名<br />property：需要装配的属性名<br />one：需要使用的@One注解，@Result(one=@One()<br />many：需要使用的@Many注解，@Result(many=@Many()) |
| `@One`一对多  | 代替了`<assocation>`标签，是多表查询的关键，在注解中用来指定子查询返回单一对象。@One注解属性介绍<br />select：指定用来多表查询的sqlmapper<br />使用格式：@Result(column="",property="",one=@One(select="")) |
| `@Many`多对一 | 代替了`<collection>`标签，是多表查询的关键，在注解中用来指定子查询返回对象集合，使用格式：@Result(column="",property="",many=@Many(select="")) |

###### 一对多查询

User实体中的属性

- private int uid;
- private String uname;
- private String password;
- private Date birthday;

Order实体中的属性

- private int oid;
- private Date orderTime;
- private double total;
- private User user;

UserMapper接口方法

```java
public interface UserMapper {
    @Select("select * from user_mb where uid=#{uid}")
    User findById(int uid);
}
```

OrderMapper接口方法

```java
public interface OrderMapper {

    @Select("select * from order_mb")
    @Results(
        {@Result(id = true, column = "oid", property = "oid"),
         @Result(column = "total", property = "total"),
         @Result(column = "orderTime", property = "orderTime"),
         @Result(
             column = "uid", // 根据该字段去查询user表的数据
             property = "user", // 要封装的属性名称
             javaType = User.class, // 要封装的实体类型
             one = @One(select = "com.study.mapper.UserMapper.findById")
         )
        }
    )
    List<Order> findAll();
}
```

测试

```java
public static final Logger logger = Logger.getLogger(AnnotationOMTest.class);
private OrderMapper mapper;

@Before
public void before() throws IOException {
    InputStream inputStream = Resources.getResourceAsStream("SqlMapConfig.xml");
    SqlSessionFactory sqlSessionFactory = new SqlSessionFactoryBuilder().build(inputStream);
    SqlSession sqlSession = sqlSessionFactory.openSession(true);
    mapper = sqlSession.getMapper(OrderMapper.class);

}

@Test
public void findAll() {
    List<Order> result = mapper.findAll();
    logger.debug(result);
}
```

###### 多对一查询

User实体中的属性

- private int uid;
- private String uname;
- private String password;
- private Date birthday;
- private List\<Order> orders;

Order实体中的属性

- private int oid;
- private Date orderTime;
- private double total;
- private User user;

OrderMapper接口方法

```java
public interface OrderMapper {
    @Select("select * from order_mb where uid=#{uid}")
    List<Order> findById(int uid);
}
```

UserMapper接口方法

```java
public interface UserMapper {
    @Select("select * from user_mb")
    @Results({
        @Result(id = true, column = "uid", property = "uid"),
        @Result(column = "uname", property = "uname"),
        @Result(column = "password", property = "password"),
        @Result(column = "birthday", property = "birthday"),
        @Result(
            column = "uid",
            property = "orders",
            javaType = List.class,
            many = @Many(select = "com.study.mapper.OrderMapper.findById"))
    })
    List<User> findUserAndOrderAll();
}
```

测试

```java
public static final Logger logger = Logger.getLogger(AnnotationMOTest.class);
private UserMapper mapper;

@Before
public void before() throws IOException {
    InputStream inputStream = Resources.getResourceAsStream("SqlMapConfig.xml");
    SqlSessionFactory sqlSessionFactory = new SqlSessionFactoryBuilder().build(inputStream);
    SqlSession sqlSession = sqlSessionFactory.openSession(true);
    mapper = sqlSession.getMapper(UserMapper.class);

}

@Test
public void testFindUserAndOrderAll() {
    List<User> result = mapper.findUserAndOrderAll();
    logger.debug(result);
}
```

###### 多对多查询

User实体中的属性

- private int uid;
- private String uname;
- private String password;
- private Date birthday;
- private List\<Role> roles;

Role实体中的属性

- private int rid;
- private String rolename;

RoleMapper接口方法

```java
public interface RoleMapper {
    @Select("select * from user_role_mb ur,role_mb r where ur.uid=#{uid} and ur.rid=r.rid")
    List<Role> findByUid(int uid);
}
```

UserMapper接口方法

```java
public interface UserMapper {
    @Select("select * from user_mb")
    @Results({
        @Result(id = true, column = "uid", property = "uid"),
        @Result(column = "uname", property = "uname"),
        @Result(column = "password", property = "password"),
        @Result(column = "birthday", property = "birthday"),
        @Result(
            column = "uid",
            property = "roles",
            javaType = List.class,
            many = @Many(select = "com.study.mapper.RoleMapper.findByUid"))
    })
    List<User> findUserAndRoleAll();
}
```

测试

```java
public static final Logger logger = Logger.getLogger(AnnotationMMTest.class);
private UserMapper mapper;

@Before
public void before() throws IOException {
    InputStream inputStream = Resources.getResourceAsStream("SqlMapConfig.xml");
    SqlSessionFactory sqlSessionFactory = new SqlSessionFactoryBuilder().build(inputStream);
    SqlSession sqlSession = sqlSessionFactory.openSession(true);
    mapper = sqlSession.getMapper(UserMapper.class);

}

@Test
public void testFindUserAndOrderAll() {
    List<User> result = mapper.findUserAndRoleAll();
    logger.debug(result);
}
```

