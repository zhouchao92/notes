---
title: Java--并发编程
tags:
  - java
  - 并发编程
  - notes
abbrlink: f0f7baf0
date: 2020-05-24 12:01:52
---


#### Java并发编程

##### 串行 vs 并行 

业务：任务多，数据量大

串行

- 编程简单，并行编程困难
- 单个计算核频率下降，计算核数增多，整体性能变高

并行困难(任务分配和执行过程**高度耦合**)

- 如何控制粒度，切割任务
- 如何分配任务给线程，监督线程执行过程

并行模式

- 主从模式 (Master-Slave)
- Worker模式(Worker-Worker)

Java并发编程

- Thread/Runnable/Thread组管理
- Executor框架
- Fork-Join框架

#### 线程组ThreadGroup

- 线程的集合
- 树形结构，大线程组可以包括小线程组
- 可以通过`enumerate`方法遍历组内的线程，执行操作
- 能够有效管理多个线程，但是<font color="red">管理效率低</font>
- 任务分配和执行过程<font color="red">高度耦合</font>
- 重复创建线程、关闭线程操作，<font color="red">无法重用线程</font>

##### Main类

```java
/**
 * 使用线程组管理多个线程
 */
public class ThreadGroupStudy {
    public static void main(String[] args) {
        //创建线程组
        ThreadGroup threadGroup = new ThreadGroup("Search");
        Result result = new Result();
        //创建线程任务
        Searcher searchTask = new Searcher(result);
        for (int i = 0; i < 10; i++) {
            //以searchTask为模板创建线程
            Thread thread = new Thread(threadGroup, searchTask);
            thread.start();
            try {
                TimeUnit.SECONDS.sleep(1);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }
        System.out.println("----------分割线----------");
        //查看线程组消息
        System.out.printf("active count:%d\n", threadGroup.activeCount());
        System.out.println("线程组信息明细");
        //打印线程组的所有信息
        threadGroup.list();
        System.out.println("----------分割线----------");
        //遍历线程组
        Thread[] threads = new Thread[threadGroup.activeCount()];
        //将线程组中active线程拷贝到数组中
        threadGroup.enumerate(threads);
        for (int i = 0; i < threadGroup.activeCount(); i++) {
            System.out.printf("Thread %s: %s\n", threads[i].getName(), threads[i].getState());
        }
        System.out.println("----------分割线----------");

        //等待活动线程数小于10
        waitFinish(threadGroup);
        //线程组发出中断命令
        threadGroup.interrupt();
    }

    public static void waitFinish(ThreadGroup threadGroup) {
        while (threadGroup.activeCount()>9){
            try{
                TimeUnit.SECONDS.sleep(1);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }
    }
}
```

##### 线程模板

```java
public class Searcher implements Runnable {
    private Result result;

    public Searcher(Result result) {
        this.result = result;
    }

    @Override
    public void run() {
        String threadName = Thread.currentThread().getName();
        System.out.printf("Thread %s start\n", threadName);
        try {
            doTask();
            result.setName(threadName);
        } catch (InterruptedException e) {
            System.out.printf("Thread %s is interrupted\n", threadName);
            return;
        }
        System.out.printf("Thread %s finished.\n", threadName);
    }

    private void doTask() throws InterruptedException {
        Random random = new Random(new Date().getTime());
        int value = (int) (random.nextDouble() * 100);
        System.out.printf("Thread %s : %d\n", Thread.currentThread().getName(), value);
        TimeUnit.SECONDS.sleep(value);
    }

}

```

##### Bean类

```java
public class Result {
    private String name;

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }
}
```

##### 输出

> 输出
>
> Thread Thread-0 start
> Thread Thread-0 : 6
> Thread Thread-1 start
> Thread Thread-1 : 4
> Thread Thread-2 start
> Thread Thread-2 : 32
> Thread Thread-3 start
> Thread Thread-3 : 23
> Thread Thread-4 start
> Thread Thread-4 : 51
> Thread Thread-1 finished.
> Thread Thread-5 start
> Thread Thread-5 : 42
> Thread Thread-6 start
> Thread Thread-6 : 21
> Thread Thread-0 finished.
> Thread Thread-7 start
> Thread Thread-7 : 12
> Thread Thread-8 start
> Thread Thread-8 : 39
> Thread Thread-9 start
> Thread Thread-9 : 30
> ----------分割线----------
> active count:8
> 线程组信息明细
> java.lang.ThreadGroup[name=Search,maxpri=10]
>  Thread[Thread-2,5,Search]
>  Thread[Thread-3,5,Search]
>  Thread[Thread-4,5,Search]
>  Thread[Thread-5,5,Search]
>  Thread[Thread-6,5,Search]
>  Thread[Thread-7,5,Search]
>  Thread[Thread-8,5,Search]
>  Thread[Thread-9,5,Search]
> ----------分割线----------
> Thread Thread-2: TIMED_WAITING
> Thread Thread-3: TIMED_WAITING
> Thread Thread-4: TIMED_WAITING
> Thread Thread-5: TIMED_WAITING
> Thread Thread-6: TIMED_WAITING
> Thread Thread-7: TIMED_WAITING
> Thread Thread-8: TIMED_WAITING
> Thread Thread-9: TIMED_WAITING
> ----------分割线----------
> Thread Thread-3 is interrupted
> Thread Thread-5 is interrupted
> Thread Thread-8 is interrupted
> Thread Thread-6 is interrupted
> Thread Thread-7 is interrupted
> Thread Thread-4 is interrupted
> Thread Thread-9 is interrupted
> Thread Thread-2 is interrupted
>
> Process finished with exit code 0

#### Java并发框架Executor

从JDK 5开始提供Executor FrameWork (==java.util.concurrent.*==)

- 分离任务的创建和执行者的创建
- 线程重复利用(new线程代价很大)

理解共享线程池的概念

- 预设好的多个Thread，可弹性增加
- 多次执行很多很小的任务
- 任务创建和执行过程解耦
- 程序员无需关心线程池执行任务过程

主要类：Executor Service, ThreadPoolExecutor，Future

- Executors.newCachedThreadPool
  Executors.newFixedThreadPool(指定线程数，一般为CPU核数的2/4倍) 创建线程池
- Executor Service 线程池服务
- Callable 具体的逻辑对象(线程类)，类似于Runnable接口，有返回值
- Future 返回结果

##### Server实例代码

- Main类 通过服务器调用100个任务
- Server类 线程池管理
- Task类 线程具体任务实现

```Java
public class Main {
    public static void main(String[] args) throws InterruptedException {
        //创建一个服务器
        Server server = new Server();
        //创建100个任务，并发交给执行器，等待完成
        for (int i = 0; i < 100; i++) {
            Task task = new Task("Task" + i);
            Thread.sleep(10);
            server.submitTask(task);	// 提交任务
        }
        server.endServer();	// 关闭服务器
    }
}
```

```java
public class Server {

    //线程池
    private ThreadPoolExecutor executor;

    public Server() {
        //创建默认的线程池
        executor = (ThreadPoolExecutor) Executors.newCachedThreadPool();
        //创建指定个数的线程池
        //executor = (ThreadPoolExecutor) Executors.newFixedThreadPool(5);
    }

    public void submitTask(Task task) {
        System.out.print("Sever: A new task is arrived.\n");
        //执行task
        executor.execute(task);

        System.out.printf("Server: Pool size is %d\n", executor.getPoolSize());
        System.out.printf("Server: Active count is %d\n", executor.getActiveCount());
        System.out.printf("Server: Completed task is %d\n", executor.getCompletedTaskCount());
    }

    public void endServer() {
        //结束服务
        executor.shutdown();
    }
}
```

```java
public class Task implements Runnable {

    private String name;

    public Task(String name) {
        this.name = name;
    }

    @Override
    public void run() {
        try {
            long duration = (long) (Math.random() * 1000);
            System.out.printf("Thread %s:doing a task during %d seconds\n", Thread.currentThread().getName(), duration);
            Thread.sleep(duration);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
        System.out.printf("Thread %s is finished on %s.\n", Thread.currentThread().getName(), new Date());
    }
}
```

##### Callable+Future回调结果实例

- SumTest类 实现多线程运行1-1000的求和
- SumTask类 每个线程的具体任务实现，并通过Callable回调结果

```java
public class SumTest {
    public static void main(String[] args) {
        // 执行线程池
        ThreadPoolExecutor executor = (ThreadPoolExecutor) Executors.newFixedThreadPool(4);

        List<Future<Integer>> resultList = new ArrayList<>();

        // 统计1-1000总和，分成10个任务计算，提交任务
        for (int i = 0; i < 10; i++) {
            SumTask calculator = new SumTask(i * 100 + 1, (i + 1) * 100);
            Future<Integer> result = executor.submit(calculator);   // 接收任务回调
            resultList.add(result);
        }
        // 每隔50ms，轮询等待10个任务结束
        do {
            System.out.printf("Main:已经完成%d个任务\n", executor.getCompletedTaskCount());
            for (int i = 0; i < resultList.size(); i++) {
                Future<Integer> result = resultList.get(i);
                System.out.printf("Main:Task %d : %s\n", i, result.isDone());
            }
            try {
                Thread.sleep(50);   // 休眠50ms
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        } while (executor.getCompletedTaskCount() < resultList.size());
        // 在所有任务结束，综合计算结果
        int total = 0;
        for (Future<Integer> result : resultList) {
            Integer sum = null;
            try {
                sum = result.get();
                total += sum;
            } catch (InterruptedException | ExecutionException e) {
                e.printStackTrace();
            }
        }
        // 输出最终结果
        System.out.println("1-1000的总和："+total);

        // 关闭线程池
        executor.shutdown();
    }
}
```

```java
public class SumTask implements Callable<Integer> {

    // 定义每个线程计算的区间
    private int startNumber;
    private int endNumber;

    public SumTask(int startNumber, int endNumber) {
        this.startNumber = startNumber;
        this.endNumber = endNumber;
    }

    @Override
    public Integer call() throws Exception {
        int sum = 0;
        for (int i = startNumber; i <=endNumber; i++) {
            sum += i;
        }
        Thread.sleep(new Random().nextInt(1000));   // 休眠一段时间

        System.out.printf("%s:%d\n", Thread.currentThread().getName(), sum);

        return sum;
    }
}
```

#### Java并发框架Fork-Join

 Java 7 提供的一种并行框架：分解、治理、合并(分治编程)

适合用于整体任务量不好确定的场合(最小任务可确定)

关键类

- ForkJoinPool 任务池
- RecursiveAction
- RecursiveTask

使用Fork-Join框架执行1-10000000的求和

- SumTest类 线程池管理线程
- SumTask类 线程任务分解、治理、合并

```java
public class SumTest {
    public static void main(String[] args) throws ExecutionException, InterruptedException {
        // 创建执行线程池
        ForkJoinPool pool = new ForkJoinPool();
        //ForkJoinPool pool = new ForkJoinPool(4);

        // 创建任务
        SumTask task = new SumTask(1, 10000000);

        // 提交任务
        ForkJoinTask<Long> result = pool.submit(task);

        // 等待结果,每隔50ms检查一次
        do {
            System.out.println("Main:Active Thread Count-->" + pool.getActiveThreadCount());
            System.out.println("Main:Parallelism-->" + pool.getParallelism());
            try {
                Thread.sleep(50);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        } while (!task.isDone());

        // 输出结果
        System.out.println(result.get().toString());
    }
}
```

```java
public class SumTask extends RecursiveTask<Long> {

    private int start;
    private int end;

    public static final int threadHold = 5; // 求和最小个数

    public SumTask(int start, int end) {
        this.start = start;
        this.end = end;
    }

    @Override
    protected Long compute() {
        Long sum = 0L;

        // 如果任务足够小就直接执行
        boolean canCompute = (end - start) <= threadHold;
        if (canCompute) {
            for (int i = start; i <= end; i++) {
                sum += i;
            }
        } else {
            // 任务大于阀值，分为2个任务
            int middle = start + (end - start) / 2;
            SumTask sumTaskLeft = new SumTask(start, middle);
            SumTask sumTaskRight = new SumTask(middle + 1, end);

            invokeAll(sumTaskLeft, sumTaskRight);

            Long sumLeft = sumTaskLeft.join();
            Long sumRight = sumTaskRight.join();

            // 合并结果
            sum = sumLeft + sumRight;
        }
        return sum;
    }
}
```

#### Java并发数据结构

常用的数据结构是线程不安全的

- ArrayList, HashMap, HashSet 非同步的
- 多个线程同时读写，可能会抛出异常或数据错误

传统Vector，Hashtable等同步集合性能过差

并发数据结构：数据添加和删除

- 阻塞式集合：当集合为空或者满时，等待
- 非阻塞式集合：当集合为空或者满时，不等待，返回null或异常

##### 常用数据结构

###### List

- Vector 同步安全，<font color="red">写多读少</font>
- ArrayList 不安全
- Collections.synchronizedList(List list) 基于synchronized，效率差
- CopyOnWriteArrayList <font color="red">读多写少</font>，基于复制机制，非阻塞

###### Set

- HashSet 不安全
- Collections.synchronizedSet(Set set) 基于synchronized，效率差
- CopyOnWriteArraySet (基于CopyOnWriteArrayList实现) <font color="red">读多写少</font>， 非阻塞

###### Map

- Hashtable 同步安全，<font color="red">写多读少</font>
-  HashMap 不安全
- Collections.synchronizedMap(Map map) 基于synchronized，效率差
- ConcurrentHashMap <font color="red">读多写少</font>，非阻塞

###### Queue & Deque (JDK 1.5 提出)

- ConcurrentLinkedQueue 非阻塞
- ArrayBlockingQueue/LinkedBlockingQueue 阻塞

#### Java并发协作控制

Thread/Executor/Fork-Join

- 线程启动，运行，结束
- 线程之间缺少协作

synchronized 同步

- 限定只有一个线程才能进入关键区
- 简单粗暴，<font color="red">性能损失有点大</font>

##### Lock

Lock也可以实现同步的效果

- 实现更复杂的临界区结构
- tryLock方法可以预判锁是否空闲
- 允许分离读写的操作，多个读，一个写
- 性能更好 

ReentrantLock类，可重入的互斥锁

ReentrantReadWriteLock类，可重入的读写锁

lock和unlock函数

##### Semaphore

信号量，由1965年Dijkstra提出的

信号量：本质上是一个计数器

计数器大于0，可以使用，等于0不能使用

可以设置多个并发量，例如限制10个访问

Semaphore

- acquire获取
- release释放

比Lock更进一步，可以控制多个同时访问关键区

###### SemaphoreExample

```java
public class SemaphoreExample {

    // 信号量，车位数为5
    private final Semaphore placeSemaphore = new Semaphore(5);

    /**
     * 5个车位，10辆车需要停放，每次停放时，去申请信号量
     *
     * @param args
     * @throws InterruptedException
     */
    public static void main(String[] args) throws InterruptedException {
        int tryToParkCnt = 10;
        SemaphoreExample semaphoreExample = new SemaphoreExample();
        Thread[] parker = new Thread[tryToParkCnt];

        for (int i = 0; i < tryToParkCnt; i++) {
            parker[i] = new Thread(() -> {
                try {
                    long randomTime = (long) (Math.random() * 1000);
                    Thread.sleep(randomTime);
                    if (semaphoreExample.parking()) {
                        long parkingTime = (long) (Math.random() * 1200);
                        Thread.sleep(parkingTime);
                        semaphoreExample.leaving();
                    }
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            });
            parker[i].start();
        }

        for (int i = 0; i < tryToParkCnt; i++) {
            parker[i].join();
        }

    }

    public boolean parking() {
        if (placeSemaphore.tryAcquire()) {
            System.out.println(Thread.currentThread().getName() + ":停车成功");
            return true;
        } else {
            System.out.println(Thread.currentThread().getName() + ":没有空位");
            return false;
        }
    }

    public void leaving() {
        placeSemaphore.release();
        System.out.println(Thread.currentThread().getName() + ":离开");
    }
}
```

##### Latch

等待锁，是一个**同步辅助类**

用来同步执行任务的一个或者多个线程

不是用来保护临界区或者共享资源

CountDownLatch

- countDown() 计数减1
- await() 等待latch变成0

##### Barrie

集合点，也是一个**同步辅助类**

允许多个线程在某一个点上进行同步

CyclicBarrier

- 构造函数是需要同步的线程数量
- await等待其他线程，到达数量后，就放行

###### CyclicBarrierExample

```java
public class CyclicBarrierExample {
    /**
     * 三个一维向量用三个线程分别求和，最后计算总和
     *
     * @param args
     */
    public static void main(String[] args) {
        final int[][] numbers = new int[3][5];
        final int[] results = new int[3];
        int[] row1 = {1, 2, 3, 4, 5};
        int[] row2 = {6, 7, 8, 9, 10};
        int[] row3 = {11, 12, 13, 14, 15};
        numbers[0] = row1;
        numbers[1] = row2;
        numbers[2] = row3;

        CalculateFinalResult finalResultCalculator = new CalculateFinalResult(results);
        CyclicBarrier barrier = new CyclicBarrier(3, finalResultCalculator);

        // 当有3个线程在barrier上await时，就执行finalResultCalculator

        for (int i = 0; i < 3; i++) {
            CalculateEachRow rowCalculator = new CalculateEachRow(numbers, i, results, barrier);
            new Thread(rowCalculator).start();
        }

    }
}

class CalculateEachRow implements Runnable {

    final int[][] numbers;
    final int rowNumbers;
    final int[] res;
    final CyclicBarrier barrier;

    public CalculateEachRow(int[][] numbers, int rowNumbers, int[] res, CyclicBarrier barrier) {
        this.numbers = numbers;
        this.rowNumbers = rowNumbers;
        this.res = res;
        this.barrier = barrier;
    }

    @Override
    public void run() {
        int[] row = numbers[rowNumbers];
        int sum = 0;
        for (int datum : row) {
            sum += datum;
            res[rowNumbers] = sum;
        }
        try {
            System.out.println(Thread.currentThread().getName() + ":计算第" + (rowNumbers + 1) + "行结束，结果为-->" + sum);
            barrier.await(); // 等待，只要超过3个，就放行
        } catch (InterruptedException | BrokenBarrierException e) {
            e.printStackTrace();
        }
    }
}

class CalculateFinalResult implements Runnable {

    final int[] eachRowRes;
    int finalRes;

    public CalculateFinalResult(int[] eachRowRes) {
        this.eachRowRes = eachRowRes;
    }

    public int getFinalRes() {
        return finalRes;
    }

    @Override
    public void run() {
        int sum = 0;
        for (int datum : eachRowRes) {
            sum += datum;
        }
        finalRes = sum;
        System.out.println("最终结果为-->" + finalRes);
    }
}
```

##### Phaser

允许执行并发多阶段任务，**同步辅助类**

在每一个阶段结束的位置对线程进行同步，当所有的线程都到达这步，再进行下一步

Phaser

- arrive()
- arriveAndAwaitAdvance()  等待

##### Exchanger 

允许在并发线程中<font color="red">互相交换消息</font>

允许在**2**个线程中定义同步点，当两个线程都到达同步点， 它们交换数据结构

Exchanger

- exchange(), 线程双方互相交互数据
- 交换数据是双向的，传递null时不需要接受

#### Java定时任务执行

Thread/Executor/Fork-Join 多线程

- 立刻执行
- 框架调度 

定时执行

- 固定某一个时间点运行
- 以某一个周期执行

##### 简单定时器机制

设置计划任务，也就是在指定的时间开始执行某一个任务 

TimerTask 封装任务 实现Runable接口

Timer类 定时器

- scheduleAtFixedRate(TimerTask timerTask,Date firstTime,long period) 固定速率
- cancel() 取消定时器

##### Executor +定时器机制

ScheduledExecutorService

- 定时任务

  schedule(Runable command,long delay,TimeUnit unit)

- 周期任务

  - scheduleAtFixedRate(Runable command,long initialDelay，long period,TimeUnit unit) 固定速率 以上一个任务开始的时间计时
  - scheduleWithFixedDelay(Runable command,long initialDelay,long delay,TimeUnit unit)

- shutdown() 结束

##### Quartz（第三方库）

Quartz是一个较为完善的任务调度框架

- trigger 触发器
- job 任务

解决程序中Timer零散管理的问题

功能更加强大

- Timer执行周期任务，如果中间某一次有异常，整个任务终止执行
- Quartz执行周期任务，如果中间某一次有异常，不影响下次任务执行

