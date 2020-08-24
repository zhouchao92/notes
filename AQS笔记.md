---
title: AQS笔记
tags:
  - AQS
  - 并发编程
categories:
  - notes
  - java
abbrlink: 74f18f35
date: 2020-07-11 20:29:29
---

#### AQS

Abstract Queued Synchronizer 抽象队列同步器
所在包：`java.util.concurrent.locks.AbstractQueuedSynchronizer;`

AQS是基于先进先出队列（FIFO），CLH（一种基于**单向链表**的高性能、**公平**的**自旋锁**）

基于AQS实现的锁

- ReentrantLock 可重入锁[*悲观锁*]
- Semaphore 信号量
- CountDownLatch 倒数计数
- ReentrantReadWriteLock 可重入读写锁

<!--more-->

##### 偏向锁、轻量级锁、重量级锁

**偏向锁**：偏向第一个线程(JDK 1.6) JVM内部

**轻量级锁**：由偏向锁升级而来，偏向锁运行在一个线程进入同步块的情况下，当第二个线程加入锁争用的时候，偏向锁就会升级为轻量级锁。

**自旋锁**：一个执行单元要想访问被自旋锁保护的共享资源，必须先得到锁，在访问完共享资源后，必须释放锁。

自旋锁--(自旋10次)-->重量级锁（悲观锁）

##### 乐观锁与悲观锁

- **悲观锁**：总是假设最坏的情况，每次拿数据都认为别的线程会修改数据，所以要加锁，别的线程只能等待，直到当前线程释放锁才能拿到锁
- **乐观锁**：总是假设最好的情况，每次拿数据都认为别的线程不会修改数据，所以不会加锁，但是更新的时候，会判断在此期间有没有线程修改过

##### CAS

Compare and Swap比较和交换

- CAS 必须是原子操作 

- 现代CPU都支持CAS 
- CAS属于乐观锁

##### AbstractQueuedSynchronizer官方实现Lock模板

```java
public class Mutex implements Lock, java.io.Serializable {

    // Our internal helper class
    private static class Sync extends AbstractQueuedSynchronizer {
        // Acquires the lock if state is zero
        public boolean tryAcquire(int acquires) {
            assert acquires == 1; // Otherwise unused
            if (compareAndSetState(0, 1)) {
                setExclusiveOwnerThread(Thread.currentThread());
                return true;
            }
            return false;
        }

        // Releases the lock by setting state to zero
        protected boolean tryRelease(int releases) {
            assert releases == 1; // Otherwise unused
            if (!isHeldExclusively())
                throw new IllegalMonitorStateException();
            setExclusiveOwnerThread(null);
            setState(0);
            return true;
        }

        // Reports whether in locked state
        public boolean isLocked() {
            return getState() != 0;
        }

        public boolean isHeldExclusively() {
            // a data race, but safe due to out-of-thin-air guarantees
            return getExclusiveOwnerThread() == Thread.currentThread();
        }

        // Provides a Condition
        public Condition newCondition() {
            return new ConditionObject();
        }

        // Deserializes properly
        private void readObject(ObjectInputStream s)
            throws IOException, ClassNotFoundException {
            s.defaultReadObject();
            setState(0); // reset to unlocked state
        }
    }

    // The sync object does all the hard work. We just forward to it.
    private final Sync sync = new Sync();

    public void lock() {
        sync.acquire(1);
    }

    public boolean tryLock() {
        return sync.tryAcquire(1);
    }

    public void unlock() {
        sync.release(1);
    }

    public Condition newCondition() {
        return sync.newCondition();
    }

    public boolean isLocked() {
        return sync.isLocked();
    }

    public boolean isHeldByCurrentThread() {
        return sync.isHeldExclusively();
    }

    public boolean hasQueuedThreads() {
        return sync.hasQueuedThreads();
    }

    public void lockInterruptibly() throws InterruptedException {
        sync.acquireInterruptibly(1);
    }

    public boolean tryLock(long timeout, TimeUnit unit)
        throws InterruptedException {
        return sync.tryAcquireNanos(1, unit.toNanos(timeout));
    }
}
```

##### 多线程高并发常见问题引入

使用100个线程完成10000个1相加的运算，每个线程都是100次自增的for循环，最后结果却小于10000。

这是因为线程操作的是<font color='red'>当前中内存中的值</font>，当执行m+2时（线程a获取内存中的值，线程b获取内存中的值，线程a执行+1操作，线程b执行+1操作，将值写入到内存中），实际上却只执行m+1了。

```java
public class Main {
    public static int m = 0;
    public static void main(String[] args) throws InterruptedException {
        Thread[] threads = new Thread[100];
        for (int i = 0; i < threads.length; i++) {
			// 以匿名内部类的方式声明100个线程
            threads[i] = new Thread(() -> {
                for (int j = 0; j < 100; j++) {
                    m++;
                }
            });
        }
        for (Thread t : threads) {
            t.start();  // 线程开始
        }
        for (Thread t : threads) {
            t.join();   // 加入当前线程
        }
        System.out.println(m);
    }
}
```

##### 解决方案——Synchronized加锁

在线程访问时进行加锁，其他线程阻塞，线程完成累加后解锁，其他线程再进行访问，循环此操作，直到100个线程完成累加。

```java
threads[i] = new Thread(() -> {
    synchronized (Main.class) {     //线程访问时加锁，结束时打开，其他线程再访问时，加锁状态时线程阻塞
        for (int j = 0; j < 100; j++) {
            m++;
        }
    }
});
}
```

##### 解决方案——ReentrantLock

使用ReentrantLock可重入锁，JDK 1.5之后添加的，互斥锁，底层实现是AQS

- lock() 加锁
- unlock() 解锁

ReentrantLock部分源码

```java
public class ReentrantLock implements Lock, java.io.Serializable {
    private final Sync sync;
    abstract static class Sync extends AbstractQueuedSynchronizer{}	// 底层实现是AQS
}
```

`Synchronized` 在<font color='red'>JDK 1.5</font>之前被视为**重量级锁**，在<font color='red'>JDK 1.6</font>以后视为**轻量级锁**

- JVM--OS(操作系统)
- 用户--kernel（实时操作系统）费时

在JDK1.5之后在JVM添加各种锁（Lock接口），不需要再与OS交互

###### Synchronized与CAS高并发性能比较

Synchronized

- 适合高并发（超高并发，锁代码执行时间长）

- 调用wait()，阻塞线程，不消耗 cpu

CAS

- 适合锁的竞争不激烈
- for循环或while(true)， 消耗cpu

###### Lock源码

```java
/** 
 * @since 1.5
 * @author Doug Lea
*/
public interface Lock {
    
    void lock(); // 加锁

    void lockInterruptibly() throws InterruptedException;

    boolean tryLock(); // 
    
    boolean tryLock(long time, TimeUnit unit) throws InterruptedException;
    
    void unlock();	// 解锁

    Condition newCondition();
}
```

###### ReentrantLock解决100个线程累加求和问题

```java
public static Lock lock = new ReentrantLock();  // JDK1.5之后添加的，互斥锁，底层实现是AQS

// 线程操作
threads[i] = new Thread(() -> {
    try {
        lock.lock();    //加锁
        for (int j = 0; j < 100; j++) {
            m++;
        }
    } finally {
        lock.unlock();  //解锁
    }
});
```

##### 解决方案——Lock+volatile

`volatile`关键字 

- 线程（内存）在写入变量后立即提交给主线程（内存），其他线程可见其改变

- 禁止指令重排序，可参考单例模式中懒汉式的相关代码

  synchronized 防止指令重排
  instance=new Instance() 只执行第2步和第4步 半初始化，使用volatile修饰instance禁止指令重排序

自定义Lock，使用volatile关键字定义当前线程状态，在当前线程被占用时，循环等待，直到线程被释放。等待的过程也可以用CAS自旋来实现。

```java
public class MLock implements Lock {

    private volatile int i = 0; // 0被释放，1锁定

    @Override
    public void lock() {
        synchronized (this) {
            while (i != 0) {    // 已经有线程占用
                try {
                    this.wait();    // 等待，也可使用CAS（Compare and switch）自旋
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            }
            i = 1;
        }
    }

    @Override
    public void lockInterruptibly() throws InterruptedException {

    }

    @Override
    public boolean tryLock() {
        return false;
    }

    @Override
    public boolean tryLock(long time, TimeUnit unit) throws InterruptedException {
        return false;
    }

    @Override
    public void unlock() {
        synchronized (this) {
            i = 0;
            this.notifyAll();   // 唤醒其他等待该锁的线程
        }
    }

    @Override
    public Condition newCondition() {
        return null;
    }
}
```

主类

```java
public class Main {
    public static int m = 0;
    public static Lock lock = new MLock();
    public static void main(String[] args) throws InterruptedException {
        Thread[] threads = new Thread[100];
        for (int i = 0; i < threads.length; i++) {
            threads[i] = new Thread(() -> {
                try {
                    lock.lock();    // 加锁
                    for (int j = 0; j < 100; j++) {
                        m++;
                    }
                } finally {
                    lock.unlock();  // 解锁
                }
            });
        }
        for (Thread t : threads) {
            t.start();  // 开始线程
        }
        for (Thread t : threads) {
            t.join();   // 加入当前线程
        }
        System.out.println(m);
    }
}
```

##### 解决方案——Sync+Lock

修改MLock.java，主类Main.java不变，使用Sync来替代volatile

```java
public class MLock implements Lock {

    private Sync sync = new Sync();

    @Override
    public void lock() {
        sync.acquire(1);	
    }

    @Override
    public void lockInterruptibly() throws InterruptedException {

    }

    @Override
    public boolean tryLock() {
        return false;
    }

    @Override
    public boolean tryLock(long time, TimeUnit unit) throws InterruptedException {
        return false;
    }

    @Override
    public void unlock() {
        sync.release(1);	// 释放
    }

    @Override
    public Condition newCondition() {
        return null;
    }

    /**
     * 使用内部类--推荐写法
     */
    private class Sync extends AbstractQueuedSynchronizer {

        @Override
        public boolean tryAcquire(int acquires) {
            if (compareAndSetState(0, 1)) {  // 竞争锁
                // 互斥锁 共享锁
                setExclusiveOwnerThread(Thread.currentThread());    // 设定当前线程持有该锁
                return true;
            }
            return false;
        }

        @Override
        protected boolean tryRelease(int releases) {    // 释放
            setExclusiveOwnerThread(null);
            setState(0);
            return true;
        }

        @Override
        protected boolean isHeldExclusively() {
            return getState() == 1;
        }
    }
}
```

##### 解决方案——使用CountDownLatch

CountDownLatch倒计数100，每个线程执行完后调用countDown()自减1，主线程中await()等待直到为0。

```java
public class Main {
    public static int m = 0;
    public static Lock lock = new MLock();
    public static CountDownLatch latch = new CountDownLatch(100);
    public static void main(String[] args) throws InterruptedException {
        Thread[] threads = new Thread[100];
        for (int i = 0; i < threads.length; i++) {
            threads[i] = new Thread(() -> {
                try { 
                    lock.lock();    // 加锁
                    for (int j = 0; j < 100; j++) {
                        m++;
                    }
                } finally {
                    lock.unlock();  // 解锁
                }
                latch.countDown();
            });
        }
        for (Thread t : threads) {
            t.start();  // 开始线程
        }
        latch.await();  // 线程等待
        System.out.println(m);
    }
}
```