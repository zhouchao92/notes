---
title: mysql基础
tags:
  - mysql
  - 数据库
abbrlink: 8e40d0ad
date: 2020-04-03 12:00:00
---

## SQL语句
### 数据库的常用操作
#### 登陆服务器
`mysql -h服务器地址 -u用户名 -p密码`（不安全）  

```sql
mysql -hlocalhost(本地) -u... -p...
```

#### 退出服务器
```sql
mysql> exit
```

相对安全的登陆形式
mysql -h服务器地址 -u用户名 -p(回车后输入密码)

```sql
mysql -hlocalhost -uroot -p
Enter password:...
```
<!--more-->

#### 查询所有数据库

```sql
mysql> show databases;

+--------------------+
| Database           |
+--------------------+
| information_schema |
| mysql              |
| performance_schema |
| test               |
+--------------------+
4 rows in set (0.01 sec)  
```

#### 查看当前数据库编码
`mysql> show create database 数据库名称;`  

```sql
mysql> show create database mysql;

+----------+----------------------------------------------------------------+
| Database | Create Database                                                |
+----------+----------------------------------------------------------------+
| mysql    | CREATE DATABASE `mysql` /*!40100 DEFAULT CHARACTER SET utf8 */ |
+----------+----------------------------------------------------------------+
1 row in set (0.00 sec)
```


#### 创建数据库  
`create datebase 数据库的名称 [编码]`
编码可有可无，如果没有就是此服务器的默认编码(安装时选择的编码形式)

```sql
mysql> create database demo;(默认编码)
mysql> create database demo character set gbk;(gbk格式编码)

Query OK, 1 row affected (0.01 sec)

mysql> show databases;

+--------------------+
| Database           |
+--------------------+
| information_schema |
| demo               |
| mysql              |
| performance_schema |
| test               |
+--------------------+
5 rows in set (0.00 sec)
```
#### 修改数据库名称  
`rename database 旧名称 to 新名称;`(~~5.5版本无法使用~~ )  


- 强制修改数据库名称：
	1. 退出MySQL服务器
	2. 进入数据库储存位置,找到文件夹并修改数据库名称(文件夹)
	3. 重新登陆MySQL服务器


#### 修改数据库的编码
`alter database 数据库名称 character set 编码格式;`

```sql 
mysql> alter database demo character set gbk;

Query OK, 1 row affected (0.00 sec)
```

#### 删除数据库

`drop database 数据库名称;`  

```sql
mysql> drop database demo;

Query OK, 0 rows affected (0.01 sec)
```
#### 查看当前使用的数据库
`select database();`  

```sql
mysql> select database();

+------------+
| database() |
+------------+
| demo       |
+------------+
1 row in set (0.00 sec)
```

#### 切换数据库
`use 数据库名称;`  

```sql
mysql> use demo;

Database changed
```

### 数据表的常用操作  
#### 创建数据表
`create table 数据表名称 (字段1 数据类型，字段2 数据类型，字段3 数据类型,...);` 

- 字段数据类型 
	-  int 
	- varchar(长度) 
	- double 
	- bit
	- datetime

```sql
mysql> create table demolist(
-> id int,
-> name varchar(10),
-> age int,
-> salary double,
-> sex varchar(5)
-> );

Query OK, 0 rows affected (0.01 sec)
```
#### 查看所有数据表
```sql
mysql> show tables;

+----------------+
| Tables_in_demo |
+----------------+
| demolist       |
+----------------+
1 row in set (0.01 sec)
```

#### 查看数据表编码
`mysql> show create database 数据表名称;`  

```sql
mysql> show create table demolist;

+----------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| Table    | Create Table                                                                                                                                                                                                                 |
+----------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| demolist | CREATE TABLE `demolist` (
	`id` int(11) DEFAULT NULL,
	`name` varchar(10) DEFAULT NULL,
	`age` int(11) DEFAULT NULL,
	`salary` double DEFAULT NULL,
	`sex` varchar(5) DEFAULT NULL
) ENGINE=InnoDB DEFAULT CHARSET=utf8 |
+----------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
1 row in set (0.00 sec)
```

#### 修改数据表的编码
`alter database 数据表名称 character set 编码格式;`

```sql
mysql> alter table demolist character set gbk;

Query OK, 0 rows affected (0.02 sec)
Records: 0  Duplicates: 0  Warnings: 0	
```

#### 查看数据表结构
`desc 数据表名称;`

```sql
mysql> use demo;
Database changed
mysql> desc demolist;

+--------+-------------+------+-----+---------+-------+
| Field  | Type        | Null | Key | Default | Extra |
+--------+-------------+------+-----+---------+-------+
| id     | int(11)     | YES  |     | NULL    |       |
| name   | varchar(10) | YES  |     | NULL    |       |
| age    | int(11)     | YES  |     | NULL    |       |
| salary | double      | YES  |     | NULL    |       |
| sex    | varchar(5)  | YES  |     | NULL    |       |
+--------+-------------+------+-----+---------+-------+
5 rows in set (0.04 sec)
```

#### 修改数据表结构

##### 增加列（字段）
`alter table 数据表名称 add 字段名称 字段数据类型;`

```sql
mysql> alter table demolist add adress varchar(100);

Query OK, 0 rows affected (0.03 sec)
Records: 0  Duplicates: 0  Warnings: 0

mysql> desc demolist;

+--------+--------------+------+-----+---------+-------+
| Field  | Type         | Null | Key | Default | Extra |
+--------+--------------+------+-----+---------+-------+
| id     | int(11)      | YES  |     | NULL    |       |
| name   | varchar(10)  | YES  |     | NULL    |       |
| age    | int(11)      | YES  |     | NULL    |       |
| salary | double       | YES  |     | NULL    |       |
| sex    | varchar(5)   | YES  |     | NULL    |       |
| adress | varchar(100) | YES  |     | NULL    |       |
+--------+--------------+------+-----+---------+-------+
6 rows in set (0.02 sec)
```
##### 修改长度/类型/约束
`alter table 数据表名称 modify 字段名称 字段新数据类型(新长度 );`

```sql
mysql> alter table demolist modify name varchar(20);

Query OK, 0 rows affected (0.02 sec)
Records: 0  Duplicates: 0  Warnings: 0

mysql> desc demolist;

+--------+--------------+------+-----+---------+-------+
| Field  | Type         | Null | Key | Default | Extra |
+--------+--------------+------+-----+---------+-------+
| id     | int(11)      | YES  |     | NULL    |       |
| name   | varchar(20)  | YES  |     | NULL    |       |
| age    | int(11)      | YES  |     | NULL    |       |
| salary | double       | YES  |     | NULL    |       |
| sex    | varchar(5)   | YES  |     | NULL    |       |
| adress | varchar(100) | YES  |     | NULL    |       |
+--------+--------------+------+-----+---------+-------+
6 rows in set (0.02 sec)
```
##### 修改列名（字段名称）
`alter table 数据表名称 change 旧的字段名称 新的字段名称 字段数据类型(长度);`

```sql
mysql> alter table demolist change name username varchar(30);

Query OK, 0 rows affected (0.02 sec)
Records: 0  Duplicates: 0  Warnings: 0

mysql> desc demolist;

+----------+--------------+------+-----+---------+-------+
| Field    | Type         | Null | Key | Default | Extra |
+----------+--------------+------+-----+---------+-------+
| id       | int(11)      | YES  |     | NULL    |       |
| username | varchar(30)  | YES  |     | NULL    |       |
| age      | int(11)      | YES  |     | NULL    |       |
| salary   | double       | YES  |     | NULL    |       |
| sex      | varchar(5)   | YES  |     | NULL    |       |
| adress   | varchar(100) | YES  |     | NULL    |       |
+----------+--------------+------+-----+---------+-------+
6 rows in set (0.02 sec)
```

##### 删除列（删除字段）
`alter table 数据表名称 drop 字段名称;`

```sql
mysql> alter table demolist drop adress;

Query OK, 0 rows affected (0.03 sec)
Records: 0  Duplicates: 0  Warnings: 0

mysql> desc demolist;

+----------+-------------+------+-----+---------+-------+
| Field    | Type        | Null | Key | Default | Extra |
+----------+-------------+------+-----+---------+-------+
| id       | int(11)     | YES  |     | NULL    |       |
| username | varchar(30) | YES  |     | NULL    |       |
| age      | int(11)     | YES  |     | NULL    |       |
| salary   | double      | YES  |     | NULL    |       |
| sex      | varchar(5)  | YES  |     | NULL    |       |
+----------+-------------+------+-----+---------+-------+
5 rows in set (0.02 sec)
```
#### 修改数据表名称
`rename table 旧的数据表名称 to 新的数据表名称;`

```sql
mysql> rename table demolist to demotable;

Query OK, 0 rows affected (0.01 sec)

mysql> show tables;

+----------------+
| Tables_in_demo |
+----------------+
| demotable      |
+----------------+
1 row in set (0.01 sec)
```
#### 单表约束
##### 主键约束 primary key
为了保证一个列，数据不重复；一般来说，一个表里只有一个主键

修改：`alter table 数据表名称 modify 字段名称 primary key;`
```sql
mysql> alter  table demotable modify id int primary key;

Query OK, 0 rows affected (0.03 sec)
Records: 0  Duplicates: 0  Warnings: 0

mysql> desc  demotable;

+----------+-------------+------+-----+---------+-------+
| Field    | Type        | Null | Key | Default | Extra |
+----------+-------------+------+-----+---------+-------+
| id       | int(11)     | NO   | PRI | NULL    |       |
| username | varchar(30) | YES  |     | NULL    |       |
| age      | int(11)     | YES  |     | NULL    |       |
| salary   | double      | YES  |     | NULL    |       |
| sex      | varchar(5)  | YES  |     | NULL    |       |
+----------+-------------+------+-----+---------+-------+
5 rows in set (0.03 sec)
```
创建：`alter table 数据表名称 add 字段名称 字段数据类型 primary key auto_increment;`
```sql
mysql> alter table demotable add id int primary key auto_increment;
Query OK, 0 rows affected (0.03 sec)
```

##### 唯一约束 
为了保证不重复，与主键不同的是可以控制多个字段不重复
```sql
mysql> create table demoinfo(		
	-> id int primary key auto_increment,
	-> name varchar(50),
	-> dna varchar(32) unique,
	-> age int 
	-> );
	
Query OK, 0 rows affected (0.01 sec)

mysql> desc demoinfo;

+-------+-------------+------+-----+---------+----------------+
| Field | Type        | Null | Key | Default | Extra          |
+-------+-------------+------+-----+---------+----------------+
| id    | int(11)     | NO   | PRI | NULL    | auto_increment |
| name  | varchar(50) | YES  |     | NULL    |                |
| dna   | varchar(32) | YES  | UNI | NULL    |                |
| age   | int(11)     | YES  |     | NULL    |                |
+-------+-------------+------+-----+---------+----------------+
4 rows in set (0.02 sec)
```

##### 非空约束
被约束的字段必须有数据
```sql
mysql> alter table demoinfo modify name varchar(50) not null;

Query OK, 0 rows affected (0.03 sec)
Records: 0  Duplicates: 0  Warnings: 0

mysql> desc demoinfo;

+-------+-------------+------+-----+---------+----------------+
| Field | Type        | Null | Key | Default | Extra          |
+-------+-------------+------+-----+---------+----------------+
| id    | int(11)     | NO   | PRI | NULL    | auto_increment |
| name  | varchar(50) | NO   |     | NULL    |                |
| dna   | varchar(32) | YES  | UNI | NULL    |                |
| age   | int(11)     | YES  |     | NULL    |                |
+-------+-------------+------+-----+---------+----------------+
4 rows in set (0.02 sec)
```

##### 删除主键约束/唯一约束/非空约束
###### 删除主键   
`alter table 数据表名称  drop primary;`

```sql
mysql> alter table demoinfo drop primary key;

ERROR 1075 (42000): Incorrect table definition; there can be only one auto column and it must be defined as a key
//出现此错误，是因为这个是 自动增长的 主键
```
```sql
//先修改掉自动增长
mysql> alter table demoinfo modify id int;

Query OK, 0 rows affected (0.03 sec)
Records: 0  Duplicates: 0  Warnings: 0

//删除主键
mysql> alter table demoinfo drop primary key;

Query OK, 0 rows affected (0.02 sec)
Records: 0  Duplicates: 0  Warnings: 0

mysql> desc demoinfo;

+-------+-------------+------+-----+---------+-------+
| Field | Type        | Null | Key | Default | Extra |
+-------+-------------+------+-----+---------+-------+
| id    | int(11)     | NO   |     | 0       |       |
| name  | varchar(50) | NO   |     | NULL    |       |
| dna   | varchar(32) | YES  | UNI | NULL    |       |
| age   | int(11)     | YES  |     | NULL    |       |
+-------+-------------+------+-----+---------+-------+
4 rows in set (0.03 sec)
```
###### 删除唯一约束   
`alter table 数据表名称  drop index 字段名称;`

```sql
mysql> alter table demoinfo drop index dna;

Query OK, 0 rows affected (0.02 sec)
Records: 0  Duplicates: 0  Warnings: 0

mysql> desc demoinfo;

+-------+-------------+------+-----+---------+-------+
| Field | Type        | Null | Key | Default | Extra |
+-------+-------------+------+-----+---------+-------+
| id    | int(11)     | NO   |     | 0       |       |
| name  | varchar(50) | NO   |     | NULL    |       |
| dna   | varchar(32) | YES  |     | NULL    |       |
| age   | int(11)     | YES  |     | NULL    |       |
+-------+-------------+------+-----+---------+-------+
4 rows in set (0.02 sec)
```
###### 删除非空约束    
直接修改即可 
```sql
mysql> alter table demoinfo modify name varchar(50);

Query OK, 0 rows affected (0.02 sec)
Records: 0  Duplicates: 0  Warnings: 0

mysql> desc demoinfo;

+-------+-------------+------+-----+---------+-------+
| Field | Type        | Null | Key | Default | Extra |
+-------+-------------+------+-----+---------+-------+
| id    | int(11)     | NO   |     | 0       |       |
| name  | varchar(50) | YES  |     | NULL    |       |
| dna   | varchar(32) | YES  |     | NULL    |       |
| age   | int(11)     | YES  |     | NULL    |       |
+-------+-------------+------+-----+---------+-------+
4 rows in set (0.03 sec)
```
#### 删除数据表 
`drop table 数据表名称;`

```sql
mysql> drop table demoinfo;

Query OK, 0 rows affected (0.01 sec)
```
### 数据的常用操作
#### 插入数据
##### 插入完整数据
`insert into 数据表名(字段名称1,字段名称2...) value(值1，值2...);` 
`insert into 数据表名 value(值1，值2...);` (简洁写法)

```sql
mysql> insert into demotable(username,age,salary,sex,id) value("zhangsan",20,0,"man",123);

Query OK, 1 row affected (0.01 sec)
```
```sql
mysql> insert into demotable value("lisi",20,0,"man",1234);

Query OK, 1 row affected (0.01 sec)
```
[查看数据](#data)

##### 插入部分数据
字段与值对应着写
```sql
mysql> insert into demotable(username,age,sex) value("wangwu",20,"man");

Query OK, 1 row affected (0.01 sec)
```
[查看数据](#data)

##### 插入中文
直接输入中文，报错  
```sql
mysql> insert into demotable(username,age) value("老王",20);

ERROR 1366 (HY000): Incorrect string value: '\xC0\xCF\xCD\xF5' for column 'username' at row 1
```
<font color="#ff0000">原因：控制台是gbk编码，mysql服务器是utf8编码</font>
<font color="#00ff00">找到mysql的安装文件中的my.ini文件，修改</font>

```c
//修改前
[mysql] 
default-character-set=utf8
//修改后
[mysql] 
default-character-set=GBK
```
<font color="#00ff00">保存后，重启服务（我的电脑右键管理-->服务-->重新启动MySQL），重新进入mysql</font>

```sql
mysql> insert into demotable(username,age) value("老王",20);

Query OK, 1 row affected (0.01 sec)
```
[查看数据](#data)

#### 查询表中的数据
`select * from 数据表名;`
<span id="data">

```sql
mysql> select  * from demotable;

//id 为auto_increment 自动增长
+----------+------+--------+------+------+
| username | age  | salary | sex  | id   |
+----------+------+--------+------+------+
| zhangsan |   20 |      0 | man  |  123 |
| lisi     |   20 |      0 | man  | 1234 |
| wangwu   |   20 |   NULL | man  | 1235 |
| 老王     |   20 |   NULL | NULL | 1236 |
+----------+------+--------+------+------+
4 rows in set (0.00 sec)
```
</span>

#### 修改数据
对数据进行更新
`update 数据表名称 set 字段名称 = 值 条件 (where);` 

```sql
mysql> update demotable set salary = 1  where username = "wangwu";

Query OK, 1 row affected (0.01 sec)
Rows matched: 1  Changed: 1  Warnings: 0

mysql> select * from demotable;

+----------+------+--------+------+------+
| username | age  | salary | sex  | id   |
+----------+------+--------+------+------+
| zhangsan |   20 |      0 | man  |  123 |
| lisi     |   20 |      0 | man  | 1234 |
| wangwu   |   20 |      1 | man  | 1235 |
| 老王     |   20 |   NULL | NULL | 1236 |
+----------+------+--------+------+------+
4 rows in set (0.00 sec)
```

#### 删除数据表中的数据
`delete from 数据表名称 条件(where);`

```sql
mysql> delete from demotable where username = "wangwu";

Query OK, 1 row affected (0.01 sec)

mysql> select * from demotable;

+----------+------+--------+------+------+
| username | age  | salary | sex  | id   |
+----------+------+--------+------+------+
| zhangsan |   20 |      0 | man  |  123 |
| lisi     |   20 |      0 | man  | 1234 |
| 老王     |   20 |   NULL | NULL | 1236 |
+----------+------+--------+------+------+
3 rows in set (0.00 sec)
```
### 数据表中的数据的查询详细
#### 条件
- = 
- <>
- \>=
- \<=
- \>
- \<
-  is null
-  is not null
- and 
	- between ... and ... 在...与...之间，前面的要小于后面的
- or
	- 列名 in(值1,值2,...)
	- 列名 = 值1 or 列名 = 值2 or ...
- not 
- like

#### select * from 数据表名称 [where 条件];
```sql
mysql> select * from demotable where salary = 0;

+----------+------+--------+------+------+
| username | age  | salary | sex  | id   |
+----------+------+--------+------+------+
| zhangsan |   20 |      0 | man  |  123 |
| lisi     |   20 |      0 | man  | 1234 |
+----------+------+--------+------+------+
2 rows in set (0.01 sec)
```

#### select 列名,列名,... from 数据表名称 [where 条件];
```sql
mysql> select username,age from demotable;

+----------+------+
| username | age  |
+----------+------+
| zhangsan |   20 |
| lisi     |   20 |
| 老王     |   20 |
+----------+------+
3 rows in set (0.00 sec)
```

#### select 列名,列名,... from 数据表名称 by 列名 asc(升序) / desc(降序);  
```sql
mysql> select * from demotable order by id desc;

+----------+------+--------+------+------+
| username | age  | salary | sex  | id   |
+----------+------+--------+------+------+
| 老王     |   20 |   NULL | NULL | 1236 |
| lisi     |   20 |      0 | man  | 1234 |
| zhangsan |   20 |      0 | man  |  123 |
+----------+------+--------+------+------+
3 rows in set (0.00 sec)
```


#### select 列名 as 别名,列名 as 别名,... from 数据表名称 where 条件;

```sql
mysql> select username as "名字",age as "年龄" from demotable;

+----------+------+
| 名字     | 年龄 |
+----------+------+
| zhangsan |   20 |
| lisi     |   20 |
| 老王     |   20 |
+----------+------+
3 rows in set (0.00 sec)
```

#### 模糊查询
`select * from 数据表名称 where 字段名称 like 关键字%`  

- %表示通配符  
- _表示占位符  
```sql
mysql> select * from demotable where username like "zhang%";
+----------+------+--------+------+-----+
| username | age  | salary | sex  | id  |
+----------+------+--------+------+-----+
| zhangsan |   20 |      0 | man  | 123 |
+----------+------+--------+------+-----+
1 row in set (0.02 sec)
```


### 函数
#### count： 统计个数
```sql
mysql> select count(age),age from demotable where age = 20;
+------------+------+
| count(age) | age  |
+------------+------+
|          3 |   20 |
+------------+------+
1 row in set (0.01 sec)
```

#### sum函数： 求和

```sql
mysql> select sum(salary) from demotable;
+-------------+
| sum(salary) |
+-------------+
|           0 |
+-------------+
1 row in set (0.00 sec)
```

#### avg函数： 求平均数
```sql
mysql> select avg(id) from demotable;
+----------+
| avg(id)  |
+----------+
| 864.3333 |
+----------+
1 row in set (0.00 sec)
```

#### max与min：求最大值与最小值

```sql
mysql> select max(id),min(id) from demotable;
+---------+---------+
| max(id) | min(id) |
+---------+---------+
|    1236 |     123 |
+---------+---------+
1 row in set (0.00 sec)
```
### 分组 group by
以某一个字段为组，相同的进行折叠  
```sql
mysql> select * from demotable group by age;
+----------+------+--------+------+-----+
| username | age  | salary | sex  | id  |
+----------+------+--------+------+-----+
| zhangsan |   20 |      0 | man  | 123 |
+----------+------+--------+------+-----+
1 row in set (0.00 sec)
```

统计各年龄段人数
```sql
mysql> select age,count(age) from demotable group by age;
+------+------------+
| age  | count(age) |
+------+------------+
|   20 |          3 |
+------+------------+
1 row in set (0.00 sec)
```

### 数据库备份/恢复
#### 备份
`mysqldump -h服务器地址 -u用户名 -p(密码/回车写) 数据库名称 > 数据备份的路径`


#### 恢复
`mysql -u用户名 -p(密码/回车写) 新数据库名称`