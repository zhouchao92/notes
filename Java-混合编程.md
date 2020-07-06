---
title: Java--混合编程
tags:
  - 混合编程
categories:
  - java
  - notes
abbrlink: 6d00d2a2
date: 2020-05-26 10:45:45
---

#### Java混合编程

###### Java调用Java程序

RMI

###### Java调用C程序

JNI Java Native Interface

###### Java调用JavaScript程序

脚本引擎 Script Engine

JDK8：Nashorn

JDK6/7：Rhino

###### Java调用Python程序

Jython（JPython）

关键类

PythonInterpreter

- exec 执行语句
- set 设置变量值
- get 获取变量值
- execfile 执行一个python文件

PyObject
PyFunction

###### Java调用Web Service

wsimport

###### Java调用命令行

Runtime类

Process类