---
title: Java--多线程
tags:
  - 多线程
categories:
  - java
abbrlink: '83776954'
date: 2020-05-15 22:33:51
---


#### 多进程与多线程

###### 多进程

当前的操作系统都是多任务OS ，每个独立执行的任务就是一个进程，OS将时间划分为多个时间片（时间很短）

每个时间片内将CPU分配给某一个任务，时间片结束，CPU将自动回收，再分配给另外任务。从外部看，所有任务是同时在执行。但是在CPU上，任务是按照串行依次运行（单核CPU）。如果是多核，多个进程任务可以并行。但是单个核上，多进程只能串行执行。

多进程的优点

- 可以同时运行多个任务
- 程序因IO堵塞时，可以释放CPU，让CPU为其他程序服务
- 当系统有多个CPU时，可以为多个程序同时服务
  - CPU不再提高频率，而是提高核数
  - 多核和并行程序才是提高程序性能的唯一办法

多进程的缺点：太笨重，不好管理，不好切换

###### 多线程

一个程序可以包括多个子任务，可串/并行，每个子任务可以称为一个线程

如果一个子任务阻塞，程序可以将CPU调度另外一个子任务进行工作。这样CPU还是保留在本程序中，而不是被调度到别的程序(进程)去。这样，提高本程序所获得CPU时间和利用率。

###### 多进程 vs 多线程 

- 线程共享数据
- 线程通讯更高效
- 线程更轻量级，更容易切换
- 多个线程更容易管理

<!--more-->

#### Java多线程实现

###### 多线程创建

- `java.lang.Thread` 

  线程继承Thread类，实现run方法

- `java.lang.Runnable`接口

- 线程实现Runnable接口，实现run方法

###### 多线程启动

- start方法，会自动以新进程调用run方法
- 直接调用run方法，将变成串行执行
- 同一个线程，多次start会报错，只执行第一次start方法
- 多个线程启动，其启动的先后顺序是随机的
- 线程无需关闭，只要其run方法执行结束后，自动关闭
-  main函数(线程)可能早于新线程结束，整个程序并不终止
- 整个程序终止是等所有的线程都终止(包括main函数线程)

###### 多线程实现对比

- Thread占据了父类的名额，不如Runnable方便 
- Thread 类实现Runnable
- Runnable启动时需要Thread类的支持
- Runnable 更容易实现多线程中资源共享

结论：建议实现Runnable接口来完成多线程

#### Java多线程信息共享

线程类

- 通过继承Thread或实现Runnable
- 通过start方法，调用run方法， run方法工作
- 线程run结束后，线程退出 

粗粒度：子线程与子线程之间、和main线程之间缺乏交流

细粒度：线程之间有信息交流通讯

- 通过共享变量达到信息共享
- JDK原生库暂不支持发送消息(类似MPI并行库直接发送消息)

###### 信息共享

通过共享变量在多个线程中共享消息

- static变量
- 同一个Runnable类的成员变量

多线程信息共享问题

- 工作缓存副本
- 关键步骤缺乏加锁限制 

>  举例：i++，并非原子性操作
>
> - 读取主存i (正本)到工作缓存(副本)中
> - 每个CPU执行(副本)i+1操作
> - CPU将结果写入到缓存(副本)中 
> - 数据从工作缓存(副本)刷到主存(正本)中

变量副本问题的解决方法

- 采用`volatile` 关键字修饰变量
- 保证不同线程对共享变量操作时的*可见性*

关键步骤*加锁限制*

- **互斥**：某一个线程运行一个代码段(关键区)，其他线程不能同时 运行这个代码段
- **同步**：多个线程的运行，必须按照某一种规定的先后顺序来运行
- **互斥**是**同步**的一种特例 

互斥的关键字是`synchronized`

- `synchronized`代码块/函数，只能一个线程进入 
-  `synchronized`加大性能负担，但是使用简便

#### Java多线程管理

线程类

- 通过继承Thread或实现Runnable
- 通过start方法，调用run方法，run方法工作
- 线程run结束后，线程退出

粗粒度：子线程与子线程之间、和main线程之间缺乏同步 

 细粒度：线程之间有同步协作

- 等待
- 通知/唤醒
- 终止

###### 线程状态 

- NEW 刚创建(new)
- RUNNABLE 就绪态(start)
- RUNNING 运行中(run)
- BLOCK 阻塞(sleep)
- TERMINATED 结束

###### Thread API

Thread的部分API已经废弃（不建议再使用）

- 暂停和恢复 ~~suspend/resume~~
- 消亡 ~~stop/destroy~~

线程阻塞/和唤醒

- sleep，时间一到，自己会醒来
- wait/notify/notifyAll，等待，需要别人来唤醒
- join，等待另外一个线程结束
- interrupt，向另外一个线程发送中断信号，该线程收到信号，会触发InterruptedException(可解除阻塞)，并进行下一步处理

###### 线程暂停和中止--主动与被动

线程被动地暂停和终止

- 依靠别的线程来拯救自己
  - wait
  - notify
  - notifyAll
- 没有及时释放资源

线程主动暂停和终止

- 定期监测共享变量
- 如果需要暂停或者终止，先释放资源，再主动动作
- 暂停：`Thread.sleep()`，休眠
- 终止：run方法结束，线程终止

###### 多线程死锁

- 每个线程互相持有别的线程需要的锁
- 预防死锁，对资源进行等级排序

```java
/**
 * 测试死锁
 */
public class DeadLockThread {
    public static Integer one = 1;
    public static Integer two = 2;

    public static void main(String[] args) throws InterruptedException {
        Thread12 thread12 = new Thread12();
        Thread21 thread21 = new Thread21();
        thread12.start();
        thread21.start();
    }
}

class Thread12 extends Thread {

    @Override
    public void run() {
        //先拿1再拿2
        synchronized (DeadLockThread.one) {
            try {
                TimeUnit.SECONDS.sleep(3);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
            synchronized (DeadLockThread.two) {
                System.out.println("Thread 12 is running");
            }
        }
    }
}

class Thread21 extends Thread {

    @Override
    public void run() {
        //先拿2再拿1
        synchronized (DeadLockThread.two) {
            try {
                TimeUnit.SECONDS.sleep(3);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
            synchronized (DeadLockThread.one) {
                System.out.println("Thread 21 is running");
            }
        }
    }
}
```

> 线程报错信息：
>
> "Monitor Ctrl-Break" #6 daemon prio=5 os_prio=0 tid=0x0000000018bce000 nid=0x1280 runnable [0x000000001908e000]
> java.lang.Thread.State: RUNNABLE
> 	at java.net.SocketInputStream.socketRead0(Native Method)
> 	at java.net.SocketInputStream.socketRead(SocketInputStream.java:116)
> 	at java.net.SocketInputStream.read(SocketInputStream.java:170)
> 	at java.net.SocketInputStream.read(SocketInputStream.java:141)
> 	at sun.nio.cs.StreamDecoder.readBytes(StreamDecoder.java:284)
> 	at sun.nio.cs.StreamDecoder.implRead(StreamDecoder.java:326)
> 	at sun.nio.cs.StreamDecoder.read(StreamDecoder.java:178)
>
> locked <0x00000000d5fb3550> (a java.io.InputStreamReader)
> at java.io.InputStreamReader.read(InputStreamReader.java:184)
> at java.io.BufferedReader.fill(BufferedReader.java:161)
> at java.io.BufferedReader.readLine(BufferedReader.java:324)
>
> locked <0x00000000d5fb3550> (a java.io.InputStreamReader)

###### 守护(后台)线程

`Thread.setDaemon()`设置线程为守护线程

- 普通线程的结束，是run方法运行结束
- 守护线程的结束，是run方法运行结束，或main函数结束
- 守护线程永远不要访问资源，如文件或数据库等

```java
public class MultiThreading {
    public static void main(String[] args) throws InterruptedException {
        System.out.println("Main thread is running.");
        ThreadOne threadOne = new ThreadOne();
        //守护线程
        threadOne.setDaemon(true);
        threadOne.start();
        //主线程休眠2秒
        Thread.sleep(2000);
        System.out.println("Main thread is finished.");
    }
}

class ThreadOne extends Thread {

    @Override
    public void run() {
        while (true) {
            System.out.println("Thread one is running.");
            try {
                //休眠1秒
                Thread.sleep(1000);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }
    }
}
```

> 输出：
>
> Main thread is running.
> Thread one is running.
> Thread one is running.
> Thread one is running.
> Main thread is finished.
>
> Process finished with exit code 0

###### 线程查看工具 Jvisualvm

> 参考：[Jvisualvm简单使用教程](https://www.cnblogs.com/mzq123/p/11166640.html)
