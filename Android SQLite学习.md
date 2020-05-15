---
title: android sqlite学习
tags:
  - android
  - sqlite
date: 2020-04-03 12:00:00
---

### 创建数据库

1. 写一个类继承SQLOpenHelper  
2. 实现里面的方法，创建构造方法  
3. 调用子类对象，再使用getWriteableDatabase()或getReadableDatabase()方法即可创建数据库

参数解释

```java
SQLiteOpenHelper  
context 上下文  
name  数据库名称  
factory 游标工厂  
version 版本号
```

<!--more-->

重写SQLiteHelper类

```java
package com.study.sqlitestudy;

import android.content.Context;
import android.database.sqlite.SQLiteDatabase;
import android.database.sqlite.SQLiteOpenHelper;
import android.util.Log;


public class DatabaseHelper extends SQLiteOpenHelper {

    private static final String TAG ="database" ;

    public DatabaseHelper(Context context) {
        super(context, Constants.DATABASE_NAME, null, Constants.VERSION_CODE);
    }

    @Override
    public void onCreate(SQLiteDatabase db) {
        //第一次创建数据库时的回调
        Log.d(TAG, "创建数据库");       //log打印  D/database: 创建数据库
        String sql="create table "+Constants.TABLE_NAME+"(_id integer,age integer,name varchar,salary integer);";
        db.execSQL(sql);    //执行sql语句
    }

    @Override
    public void onUpgrade(SQLiteDatabase db, int oldVersion, int newVersion) {
        //升级数据库时的回调
        //更新版本号必须满足 newVersion>oldVersion  不能降级，否则会报异常，程序崩溃
        Log.d(TAG, "更新数据库");   //log打印  D/database: 更新数据库
        String sql="alter table "+Constants.TABLE_NAME+" add phone integer;";
        db.execSQL(sql);
    }
}
```

数据库相关常量

```java
package com.study.sqlitestudy;

public class Constants {
    public static final int VERSION_CODE =2;
    public static final String DATABASE_NAME ="demo";
    public static final String TABLE_NAME = "demotable";
}
```

MainActivity创建数据库  

```java
DatabaseHelper helper=new DatabaseHelper(this);
helper.getWritableDatabase();
```

### 编写Dao操作类

```java
package com.study.sqlitestudy;

import android.content.Context;
import android.database.Cursor;
import android.database.sqlite.SQLiteDatabase;
import android.util.Log;
public class Dao {

    private static final String TAG ="Dao" ;
    private final DatabaseHelper helper;

    public Dao(Context context){
        helper = new DatabaseHelper(context);

    }
    public void insert(){
        SQLiteDatabase db=helper.getWritableDatabase();
        String sql="insert into "+Constants.TABLE_NAME+ "(_id,age,name,salary) values(?,?,?,?)";
        db.execSQL(sql,new Object[]{101,20,"Wang",1000});
        db.close();
    }
    public void delete(){
        SQLiteDatabase db=helper.getWritableDatabase();
        String sql="delete from "+Constants.TABLE_NAME+ " where age = 20";
        db.execSQL(sql);
        db.close();
    }
    public void update(){
        SQLiteDatabase db=helper.getWritableDatabase();
        String sql="update "+Constants.TABLE_NAME+ " set salary = 2000 where age = 20";
        db.execSQL(sql);
        db.close();
    }
    public void query(){
        SQLiteDatabase db=helper.getWritableDatabase();
        String sql="select * from "+Constants.TABLE_NAME+ " where age = 20";
        Cursor cursor = db.rawQuery(sql,null);
        while(cursor.moveToNext()){
            int index = cursor.getColumnIndex("name");
            String name = cursor.getString(index);
            Log.d(TAG, "name :"+name);
        }
        cursor.close();
        db.close();
    }
}
```

### 编写测试类

- 数据库升级
- 数据库的增删改查

```java
package com.study.sqlitestudy;

import android.support.test.InstrumentationRegistry;
import android.support.test.runner.AndroidJUnit4;

import org.junit.Test;
import org.junit.runner.RunWith;

@RunWith(AndroidJUnit4.class)
public class TestDatabase{
    @Test
    public void testCreate(){

    }
    @Test
    public void testInsert(){
        Dao dao=new Dao(InstrumentationRegistry.getTargetContext());
        dao.insert();
    }
    @Test
    public void testDelete(){
        Dao dao=new Dao(InstrumentationRegistry.getTargetContext());
        dao.delete();
    }
    @Test
    public void testUpdate(){
        Dao dao=new Dao(InstrumentationRegistry.getTargetContext());
        dao.update();
    }
    @Test
    public void testQuery(){
        Dao dao=new Dao(InstrumentationRegistry.getTargetContext());
        dao.query();
    }
}
```

### 使用Android的API操作增删改查

#### 增 insert

```java
import android.content.ContentValues;

public void insert(){
    SQLiteDatabase db=helper.getWritableDatabase();
    ContentValues values=new ContentValues();
    values.put("_id",1001);
    values.put("age",20);
    values.put("name","Zhang");
    values.put("salary",1000);
    db.insert(Constants.TABLE_NAME,null,values);
    db.close();
}
```

#### 删 delete

```java
ContentValues values=new ContentValues();
String []whereArgs={String.valueOf(20)};
String whereClasuse="age=?";
int delete = db.delete(Constants.TABLE_NAME, whereClasuse, whereArgs);
Log.d(TAG, "delete: "+delete);
db.close();
```

#### 改 update

```java
ContentValues values=new ContentValues();
values.put("salary",5000);
String whereClasuse="age=?";    //字段名称
String[] whereArgs={String.valueOf(25)};   //值
db.update(Constants.TABLE_NAME,values,whereClasuse,whereArgs);
```

#### 查 query

```java
SQLiteDatabase db=helper.getWritableDatabase();
Cursor cursor=db.query(Constants.TABLE_NAME,null,null,null,null,null,null);
//参数：数据表名称,columns,selection,selectionArgs,groupBy分组,having,orderBy排序
while(cursor.moveToNext()){
    int id = cursor.getInt(0);
    String name = cursor.getString(1);
    Log.d(TAG, "query: "+id);
    Log.d(TAG, "query: "+name);
    //  D/Dao: query: 100
    //  D/Dao: query: 20
    //  D/Dao: query: 100
    //  D/Dao: query: 25
}
cursor.close();     //关闭游标资源
db.close();
```

### 数据库事务

1. 安全性

| _id  | age  | name  |
| ---- | ---- | ----- |
| 1001 | 20   | Zhang |
| 1002 | 25   | Li    |

操作：Zhang salary - 500 且 Li salary + 500
在操作过程中出现异常，则会只执行一部分，使用SQLite的事务可以解决这个不同步的问题，防止数据出错 

```java
SQLiteDatabase db=helper.getWritableDatabase();
db.beginTransaction(); //开启事务
try{
    db.execSQL("update demotable set salary = 1000-500 where name = 'Zhang'");
    int i=10/0;     //扔出异常
    db.execSQL("update demotable set salary = 5000+500 where name = 'Li'");
    db.setTransactionSuccessful();
}catch (Exception e){
    e.printStackTrace();
}finally {
    db.endTransaction();
    db.close();
}
```

执行结果后，数据没有发生变化
屏蔽掉扔出异常的语句后

| _id  | age  | name  |
| ---- | ---- | ----- |
| 1001 | 20   | Zhang |
| 1002 | 25   | Li    |

2. 高效性

在使用事务的情况下，执行速度比普通条件下更快。  
