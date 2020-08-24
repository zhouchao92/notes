---
title: java数据结构与算法--线性表
tags:
  - 线性表
categories:
  - notes
  - java
  - 数据结构与算法
abbrlink: 5b4e0ff4
date: 2020-04-10 13:10:08
---

#### 线性表及其结构

###### 定义

线性表是n个类型*相同数据*元素的*有限序列*

- 相同数据类型：相同数据类型意味着在内存中存储时，每个元素占用相同的内存空间，便于后续的查询定位
- 序列(顺序性)：在线性表的相邻数据元素之间存在着序偶关系
  - a<sub>i-1</sub>是a<sub>i</sub>的直接前驱，则a<sub>i+1</sub>是a<sub>i</sub>的直接后续
  - 唯一没有直接前驱的元素a<sub>0</sub>一端称为表头
  - 唯一没有直接后续的元素a<sub>n-1</sub>一端称为表尾
  - 除表头和表尾元素外，任何一个元素都有且仅有一个直接前驱和直接后继
- 有限：线性表中数据元素的个数n定义为线性表的长度，n是一个有限值
  - n=0时线性表为空表
  - 在非空的线性表中每个数据元素中都有唯一确定的序号
  - 在一个具有n>0个数据元素的线性表中，数据元素序号的范围是[0,n-1]

<!--more-->

###### 线性表的存储结构

- 顺序表------顺序存储结构
  - 特点：在内存中分配连续的空间，只存储数据，不需要存储地址信息。位置中隐含着地址

  - 优点：

    - 节省存储空间，分配给数据的存储单元全用存放结点的数据，结点之间的逻辑关系没有占用额外的存储空间；

    - 索引查找效率高

      序号为i+1的数据元素存储地址可表示为LOC(a<sub>i</sub>)=LOC(a<sub>0</sub>))+i*K  K为数据所占存储单元

  - 缺点：

    - 插入和删除操作需要移动元素，效率较低
    - 必须提前分配固定数量的空间，如果存储元素少，可能导致空闲浪费
    - 按照内容查询效率低（逐个比较判断）

- 链表------链式存储结构
  - 特点：数据元素的存储对应的是不连续的存储空间，每个存储结点对应一个需要存储的数据元素。

    每个结点是由数据域和指针组成，元素之间的逻辑关系通过存储结点之间的链接关系反映出来，逻辑上相邻的结点物理上不必相邻。

  - 优点：

    - 插入、删除灵活（不必移动结点，只需要改变结点中的指针，但是需要先定位到元素上）
    - 有元素才会分配结点空间， 不会有闲置的结点

  - 缺点：

    - 比顺序存储结构的存储密度小
    - 查找结点时链式存储要比顺序存储慢（每个结点地址不连续、无规律，导致按照索引查找效率低下）

#### 线性表操作

[Java API文档](https://docs.oracle.com/javase/8/docs/api/index.html)

```java
public interface List{
    
    //返回线性表的大小，即元素个数
    public int size();

    //返回线性表中序号为i的数据元素
    public Object get(int i);

    //如果线性表为空返回true，否则返回false
    public boolean isEmpty();

    //判断线性表是否包含数据元素e
    public boolean contains(Object e);

    //返回数据元素e在线性表中的序号
    public int indexOf(Object e);

    //将数据元素e插入到线性表中i号位置
    public void add(int i, Object e);

    //将数据元素e插入到线性表末尾
    public void add(Object e);

    //将数据元素e插入到obj之前
    public boolean addBefore(Object obj, Object e);

    //将数据元素e插入到元素obj之后
    public boolean addAfter(Object obj, Object e);

    //删除线性表中序号为i的元素，并返回之
    public Object remove(int i);

    //删除线性表中第一个与e相同的元素
    public boolean remove(Object e);

    //替换线性表中序号为i的数据元素为e，返回数据元素
    public Object replace(int i, Object e);
    
}
```

```java
public class Node {

    private Object data;    //元素数据
    private Node next;      //下一个结点

    public Node(Object data) {
        this.data = data;
    }

    public Node(Object data, Node next) {
        this.data = data;
        this.next = next;
    }

    public Object getData() {
        return data;
    }

    public void setData(Object data) {
        this.data = data;
    }

    public Node getNext() {
        return next;
    }

    public void setNext(Node next) {
        this.next = next;
    }
    
}
```

#### 顺序表实现--模拟ArrayList

```java
/**
 * 顺序表
 * 底层采用数组，但长度可以动态变化
 */
public class ArrayList implements List {

    private Object[] elementData;    //底层为数组，长度未定

    private int size;    //元素的个数

    @Override
    public String toString() {
        if (size == 0) {
            return "[]";
        }
        StringBuilder builder = new StringBuilder("[");
        for (int i = 0; i < size; i++) {
            if (i != size - 1) {
                builder.append(elementData[i]).append(",");
            } else {
                builder.append(elementData[i]);
            }
        }
        builder.append("]");
        return builder.toString();
    }

    public ArrayList() {
        //没有指定长度，默认长度是4;
        this(4);
        //没有指定长度，长度是0
        //elementData=new Object[]{};
    }

    public ArrayList(int initialCapacity) {
        //给数组分配指定数量的空间
        elementData = new Object[initialCapacity];
        //指定顺序表的元素个数,默认是0
        //size=0;
    }

    @Override
    public int size() {
        return size;
    }

    @Override
    public Object get(int i) {
        if (i < 0 || i >= size) {
            throw new RuntimeException("数组索引越界异常：" + i); //可使用自定义异常
        }
        return elementData[i];
    }

    @Override
    public boolean isEmpty() {
        return size == 0;
    }

    @Override
    public boolean contains(Object e) {
        return indexOf(e) >= 0;
    }

    @Override
    public int indexOf(Object e) {
        if (e == null) {
            for (int i = 0; i < size; i++)
                if (elementData[i] == null)
                    return i;
        } else {
            for (int i = 0; i < size; i++)
                if (e.equals(elementData[i]))
                    return i;
        }
        return -1;
    }

    @Override
    public void add(int i, Object e) {
        rangeCheck(i);
        //数组满了，就扩容
        if (size == elementData.length) {
            grow();
        }
        //后移i及其后面的位置，从最后一个开始
        for (int j = size; j > i; j--) {
            elementData[j] = elementData[j - 1];
        }
        //给数组第i个位置赋值
        elementData[i] = e;
        //元素个数加1
        size++;
    }

    private void rangeCheck(int index) {
        //判断i是否为异常值
        if (index < 0 || index > size) {
            throw new RuntimeException("数组索引越界异常：" + index);
        }
    }

    @Override
    public void add(Object e) {
        this.add(size, e);
        /**
         //数组满了，就扩容
         if (size == elementData.length) {
         grow();
         }
         //给数组赋值
         //elementData[size]=e;
         //元素个数加1
         //size++;
         elementData[size++] = e;
         */
    }

    private void grow() {
        //新创建一个新的数组，长度是旧数组的2倍,源码是增长50%
        Object[] newArr = new Object[elementData.length * 2];
        //将旧数组的数据拷贝到新数组
        for (int i = 0; i < size; i++) {
            newArr[i] = elementData[i];
        }
        //让elementData指向新数组
        elementData = newArr;
        //等价于elementData = Arrays.copyOf(elementData,elementData.length*2);
    }

    @Override
    public boolean addBefore(Object obj, Object e) {
        int index = 0;
        for (; index < size; index++) {
            if (elementData[index].equals(obj)) {
                this.add(index, e);
                return true;
            }
        }
        return false;
    }

    @Override
    public boolean addAfter(Object obj, Object e) {
        int index = 0;
        for (; index < size; index++) {
            if (elementData[index].equals(obj)) {
                this.add(index + 1, e);
                return true;
            }
        }
        return false;
    }

    @Override
    public Object remove(int i) {
        rangeCheck(i);
        Object oldElement = elementData[i];
        fastRemove(i);
        return oldElement;
    }

    @Override
    public boolean remove(Object e) {
        if (e == null) {
            for (int index = 0; index < size; index++)
                if (elementData[index] == null) {
                    fastRemove(index);
                    return true;
                }
        } else {
            for (int index = 0; index < size; index++)
                if (e.equals(elementData[index])) {
                    fastRemove(index);
                    return true;
                }
        }
        return false;
    }

    private void fastRemove(int index) {
        Object[] newArr = new Object[size - 1];
        int newI = 0;
        for (int i = 0; i < size; i++) {
            if (i == index) {
                continue;
            }
            newArr[newI] = elementData[i];
            newI++;
        }
        elementData = newArr;
        size--;
    }

    @Override
    public Object replace(int i, Object e) {
        rangeCheck(i);
        elementData[i] = e;
        return elementData;
    }
}
```

#### 单链表实现--模拟LinkedList

```java
public class SingleLinkedList implements List {

    private Node head = new Node();     //头结点，不存储数据，只为操作链表

    private int size;      //结点数，不包括头结点

    @Override
    public String toString() {
        if (size == 0) {
            return "[]";
        }
        StringBuilder builder = new StringBuilder("[");
        Node p = head.getNext();
        for (int i = 0; i < size; i++) {
            if (i != size - 1) {
                builder.append(p.getData()).append(",");
            } else {
                builder.append(p.getData());
            }
            //移动指针
            p = p.getNext();
        }
        builder.append("]");
        return builder.toString();
    }

    @Override
    public int size() {
        return size;
    }

    @Override
    public Object get(int i) {
        Node p = head;
        for (int j = 0; j <= i; j++) {
            p = p.getNext();
        }
        return p.getData();
    }

    @Override
    public boolean isEmpty() {
        return size == 0;
    }

    @Override
    public boolean contains(Object e) {
        return indexOf(e) > 0;
    }

    @Override
    public int indexOf(Object e) {
        Node p = head;
        int index = 0;
        if (e == null) {
            for (; index < size; index++) {
                p = p.getNext();
                if (p.getData() == null) {
                    return index;
                }
            }
        } else {
            for (; index < size; index++) {
                p = p.getNext();
                if (p.getData().equals(e)) {
                    return index;
                }
            }
        }
        return -1;
    }

    @Override
    public void add(int i, Object e) {
        //检查索引是否越界
        rangeCheck(i);
        //找到前一个结点，从头结点开始
        Node p = head;
        for (int j = 0; j < i; j++) {
            p = p.getNext();
        }
        //创建新结点
        Node newNode = new Node();
        newNode.setData(e);
        //指明新结点的直接后继
        newNode.setNext(p.getNext());
        //指明新结点的直接前驱
        p.setNext(newNode);
        //元素个数+1
        size++;
    }

    private void rangeCheck(int index) {
        //判断i是否为异常值
        if (index < 0 || index > size) {
            throw new RuntimeException("数组索引越界异常：" + index);
        }
    }

    @Override
    public void add(Object e) {
        this.add(size, e);
    }

    @Override
    public boolean addBefore(Object obj, Object e) {
        Node p = head;
        //先找到前一个结点，在前一个结点后面添加
        for (int i = 0; i < size; i++) {
            if (obj == null) {
                if (p.getNext().getData() == null) {
                    this.add(i, e);
                    return true;
                }
            } else {
                if (p.getNext().getData().equals(obj)) {
                    this.add(i, e);
                    return true;
                }
            }
            p = p.getNext();
        }
        return false;
    }

    @Override
    public boolean addAfter(Object obj, Object e) {
        Node p = head;
        for (int i = 0; i < size; i++) {
            p = p.getNext();
            if (obj == null) {
                if (p.getData() == null) {
                    this.add(i + 1, e);
                    return true;
                }
            } else {
                if (p.getData().equals(obj)) {
                    this.add(i + 1, e);
                    return true;
                }
            }
        }
        return false;
    }

    @Override
    public Object remove(int i) {
        rangeCheck(i);
        //拿到前一个结点
        Node p = head;
        for (int j = 0; j < i; j++) {
            p = p.getNext();
        }
        //获取该节点
        Node now = p.getNext();
        //删除该结点：前一个结点直接指向该结点的直接后续
        p.setNext(now.getNext());
        //元素数量-1
        size--;
        return now.getData();
    }

    @Override
    public boolean remove(Object e) {
        //拿到前一个结点
        Node p = head;
        for (int i = 0; i < size; i++) {
            if (e == null) {
                if (p.getNext().getData() == null) {
                    //删除操作
                    Node next = p.getNext().getNext();
                    p.setNext(next);
                    //元素数量-1
                    size--;
                    return true;
                }
            } else {
                if (p.getNext().getData().equals(e)) {
                    //删除操作
                    Node next = p.getNext().getNext();
                    p.setNext(next);
                    //元素数量-1
                    size--;
                    return true;
                }
            }
            p = p.getNext();
        }
        return false;
    }

    @Override
    public Object replace(int i, Object e) {
        rangeCheck(i);
        //拿到前一个结点
        Node p = head;
        for (int j = 0; j <= i; j++) {
            p = p.getNext();
        }
        Object oldData = p.getData();
        p.setData(e);
        return oldData;
    }
}
```

#### 其他链表

###### 双向链表

双向链表每一个结点有三部分组成：前驱指针域、数据域、后继指针域

`Node:	pre,data,next`

头结点：`Node head=Node(null,data,next)`

尾结点：`Node tail=Node(pre,data,null)`

###### 循环链表

单链表--头结点的直接前驱为尾节点，最后一个结点的直接后继为头结点

*单链表为空时：*`head.next=head`

双链表--头结点的直接前驱为尾节点，尾结点的直接后继为头结点

`head.pre=tail`		`tail.next=head`

#### Java中线性表

###### Vector 顺序表

- 底层为可变的数组
- 增加线程安全，效率低下
- 扩容每次增长*1*倍（未指定capacity Increment时）

###### ArrayList顺序表

- 底层为可变的数组
- 去除了线程安全换取高效率
- 扩容每次增长*50%*

###### LinkedList双向链表

- 双向循环链表

#### 相关代码下载

[线性表相关代码](/download/lineartable.rar)

