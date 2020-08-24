---
title: java数据结构与算法--栈和队列
tags:
  - 栈
  - 队列
categories:
  - notes
  - java
  - 数据结构与算法
abbrlink: f483932c
date: 2020-04-10 13:12:34
---

#### 栈

###### 定义

栈（stack）：堆栈，是*运算受限*的线性表

仅允许在表的一端进行插入和删除操作，不允许在其他任何位置进行插入、查找、删除等操作

表中进行插入、删除操作的一端称为栈顶（top），栈顶保存的元素称为*栈顶元素*，相对的，表的另一端为栈底（bottom）

- 当栈中没有元素时成为空栈
- 向一个栈插入元素又称为入栈或进栈
- 从一个栈中删除元素又称为出栈或退栈
- 由于栈的插入和删除操作仅在栈顶进行，后进的元素必定先出栈，堆栈又称为**后进先出表**（Last In First Out， LIFO）

<!--more-->

###### 栈接口

`push` 入栈	`pop` 出栈 	`peek` 取栈顶元素

```java
public interface Stack {
    //返回堆栈的大小
    public int getSize();

    //判断栈顶是否为空
    public boolean isEmpty();

    //数据元素e入栈
    public void push(Object e);

    //栈顶元素出栈
    public Object pop();

    //取栈顶元素
    public Object peek();
}
```

###### 栈的存储结构

- 顺序栈：使用顺序存储结构实现的堆栈，即利用一组地址连续的存储单元依次存放堆栈中的数据元素
- 链栈：采用链表作为存储结构实现的栈

顺序栈、链栈操作数据元素的时间复杂度均为O（1）

#### 队列

###### 定义

队列（queue）简称为队，是运算受限的线性表

仅允许在表的一端进行插入，而在表的另一端进行删除

- 在队列把插入数据元素的一端成为队尾（rear），删除数据元素的一端称为队首（front）
- 向队尾插入元素称为进队或入队，新元素入队后成为新的队尾元素
- 向队列中删除元素称为离队或出队，元素出队后，其后续元素成为新的队首元素
- 先进队的元素先离队，队列：先进先出表（First In First Out，FIFO）

###### 队列接口

入队、出队

```java
public interface Queue {
    //返回队列的大小
    public int getSize();

    //判断队列是否为空
    public boolean isEmpty();

    //数据元素e入队
    public void enqueue(Object e);

    //队首元素出队
    public Object dequeue();

    //取队首元素
    public Object peek();
}
```

###### 队列存储结构

- 顺序队列
  - 使用数组作为存储结构：使用普通数组实现队列，不能使用front之前的空间，会导致大量空间丢失
  - 使用循环数组作为存储结构：末尾元素的下一个元素为数组的头元素
- 链式队列：使用单链表实现

###### 双端队列

双端队列：两端都可以进行进队和出队的队列

输出受限的双端队列：一个端点允许插入和删除元素，另一个端点只允许*插入*的双端队列

输入受限的双端队列：一个端点允许插入和删除元素，另一个端点只允许*删除*的双端队列

#### java中的栈和队列

###### Stack

栈类（已过时）`public class Stack<E> extends Vector<E>`

###### Queue

队列类	

`public interface Queue<E> extends Collection`  扩展了`java.util.Collection`接口

###### Deque

双端队列	`public interface Deque<E> extends Queue<E>`

###### LinkedList

栈+队列

`public class LinkedList<E>
    extends AbstractSequentialList<E>
    implements List<E>, Deque<E>, Cloneable, java.io.Serializable`

###### ArrayDeque

数组形式的双端队列

`public class ArrayDeque<E> extends AbstractCollection<E>
                           implements Deque<E>, Cloneable, Serializable`

###### 使用栈实现进制转换

```java
int num = 13;
int temp = num;
//定义空栈
Deque<Integer> stack = new LinkedList<>();
while (temp > 0) {
    //取余数
    int mod = temp % 2;
    //入栈
    stack.push(mod);
    //除2
    temp = temp >> 1;
}
System.out.print(num + "--->");
while (!stack.isEmpty()) {
    //出栈
    System.out.print(stack.pop());
}
```
