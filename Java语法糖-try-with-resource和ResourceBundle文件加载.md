---
title: Java语法糖--try-with-resource和ResourceBundle文件加载
tags:
  - 语法糖
categories:
  - java
abbrlink: 868afa36
date: 2020-04-20 12:35:57
---


##### try-with-resource

###### try-with-resource与try-catch-finally

程序打开外部资源，在使用后需要正确关闭

考虑到异常因素，Java提供`try-catch-finally`进行保证

JDK7提供`try-with-resource`，比`try-catch-finally`更简便

```java
//try-catch-finally结构
FileInputStream fis = ...;
try{
    ...
}catch(Exception e){
    //处理异常
}finally{
    if(fis!=null)
        fis.close();	//关流
}

//try-with-resource结构
try(FileInputStream fis = ...){
    ...
}catch(Exception e){
    //处理异常
}
```

JDK7提供`try-with-resource`要求`resource`定义在try中，若外部已经定义需要一个本地变量

JDK9不再要求定义临时变量，可以直接使用外部资源变量

<!--more-->

###### 原理

资源对象必须继承`AutoCloseable`接口，即实现`close()`方法

`public class FileInputStream extends InputStream`

`public abstract class InputStream implements Closeable` 

`public interface Closeable extends AutoCloseable`

```java
//FileInputStream类close方法
public void close() throws IOException {
    synchronized (closeLock) {
        if (closed) {
            return;
        }
        closed = true;
    }
    if (channel != null) {
       channel.close();
    }

    fd.closeAll(new Closeable() {
        public void close() throws IOException {
           close0();
       }
    });
}
```

##### ResourceBundle

###### 文件加载优化

Java 8及以前，ResourceBundle默认以ISO-8859-1方式加载Properties文件：需要利用native2ascii工具(JDK自带)对文件进行转义

Java 9及以后，ResourceBundle默认以UTF-8方式加载Properties文件

- JDK9以后，已经删除native2ascii工具
- 新的Properties文件可以直接以UTF-8保存
- 已利用native2ascii工具转化后的文件，不受影响。ResourceBundle若解析文件不是有效的UTF-8，则以ISO-8859-1方式加载

