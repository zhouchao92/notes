---
title: java数据结构与算法--查找
tags:
  - 查找
categories:
  - notes
  - java
  - 数据结构与算法
abbrlink: e1d5c8a7
date: 2020-04-12 16:59:53
---

## 查找

#### 线性表查找

##### 顺序查找

存储结构：顺序表或链表

逐个比较查询，如果找到，返回数据或索引，如果最后没找到就返回null

在各个结点查找概率相同情况下，平均查询长度为表的一半长度，所以时间复杂度T(n)=O(n)

```java
/**
* 顺序查找
* 功能：在数组中查找指定的值，返回返回第一个匹配到的索引值
*
* 所求的值存在，返回第一个匹配到的索引值
* 所求的值不存在，返回-1
*
* 时间复杂度：T(n)=O(n)
* 空间复杂度：S(n)=O(1)
* @param array 查找表
* @param value 所求的值
* @return
*/
public static int orderSearch(int[] array, int value) {
    int index = -1;
    for (int i = 0; i < array.length; i++) {
        if (array[i] == value) {
            index = i;
            break;  //去除break则返回最后一个匹配到的值的位置
        }
    }
    return index;
}
```

<!--more-->

##### 折半查找(二分查找)

待查的查找表满足条件：

- 顺序存储结构
- 查找表按关键字大小有序排列

###### 递归实现折半查找

```java
/**
* 二分查找--递归
*时间复杂度：T(n)=O(log2 n)
*空间复杂度：S(n)=O(log2 n)
* @param array
* @param value
* @return
*/
public static int binarySearchByRecursion(int[] array, int value) {
    //指定low和high
    int low = 0;
    int high = array.length - 1;
    return binarySearchByRecursion(array, value, low, high);
}

public static int binarySearchByRecursion(int[] array, int value, int low, int high) {
    if (low > high) //递归结束条件--没找到
        return -1;
    int middle = (low + high) / 2;
    if (value == array[middle]) //递归结束条件--找到
        return middle;
    else if (value < array[middle])
        return binarySearchByRecursion(array, value, low, middle);
    else
        return binarySearchByRecursion(array, value, middle, high);
}
```

###### 非递归实现折半查找

```java
/**
* 二分查找
* 功能：在数组中查找指定的值，返回返回第一个匹配到的索引值
* <p>
* 所求的值存在，返回第一个匹配到的索引值
* 所求的值不存在，返回-1
* <p>
* 时间复杂度：T(n)=O(log2 n)
* 空间复杂度：S(n)=O(n)
*
* @param array 有序表
* @param value 所求值
* @return
*/
public static int binarySearch(int[] array, int value) {
    //指定low和high
    int low = 0;
    int high = array.length - 1;
    //二分查找
    while (low <= high) {
        //求得middle
        int middle = (low + high) / 2;
        if (array[middle] == value) //判断是否等于
            return middle;
        else if (array[middle] > value) //在小的部分
            high = middle - 1;
        else    //在大的部分
            low = middle + 1;
    }
    return -1;
}
```

#### 查找树

##### 二叉查找/搜索/排序树 BST (binary search/sort tree)

- 一颗空树
- 具有以下性质的二叉树：
  - 若它的左子树不为空，则左子树上所有结点均小于它的根结点的值
  - 若它的右子树不为空，则右子树上所有结点均大于它的根结点的值
  - 它的左、右子树也分别为二叉排序树

对二叉查找树进行**中序遍历**，得到**有序集合**

##### 平衡二叉树(Self-balancing binary search tree)

自平衡二叉查找树，AVL树

- 一颗空树
- 具有以下性质的二叉树：
  - 它的左右两个子树的高度差(平衡因子)的绝对值不超过1
  - 左右两个子树都是一颗平衡二叉树

平衡二叉树必定是二叉搜索树，但二叉搜索树不一定是平衡树

###### 平衡因子(平衡度)

结点的平衡因子是结点的左子树减去右子树的高度

###### 平衡二叉树

定义：每个结点的平衡因子都为1,-1,0的二叉排序树

目的是减少二叉查找树的层次，提高查找速度

[百度百科：平衡二叉树](https://baike.baidu.com/item/平衡树)

###### 实现方法

AVL、红黑树[RBT](https://baike.baidu.com/item/%E7%BA%A2%E9%BB%91%E6%A0%91)、[替罪羊树](https://baike.baidu.com/item/替罪羊树)、[Treap](https://baike.baidu.com/item/Treap)、伸展树[Splay](https://baike.baidu.com/item/%E4%BC%B8%E5%B1%95%E6%A0%91)、[SBT](https://baike.baidu.com/item/SBT)

###### 平衡树

[平衡树](https://baike.baidu.com/item/平衡树)

B-树：在它内部结点中存储键值，但不需在叶子结点上存储这些键值的记录

![B-树.jpg](https://gitee.com/zyy92/Pictures/raw/master/Btree/B-树.jpg)

B+树：在B-树基础上，为叶子结点增加链表指针，所有关键字都在叶子结点中出现，非叶子结点作为叶子结点的索引，B+树总是到叶子结点才命中

数据库的索引的默认数据结构就是采用B+树

![B+树.jpg](https://gitee.com/zyy92/Pictures/raw/master/Btree/B+树.jpg)

B*树：B+树的变体，在B+树的非根和非子叶结点再增加指向兄弟的指针

![B*树.jpg](https://gitee.com/zyy92/Pictures/raw/master/Btree/B※树.jpg)

#### 哈希表查找

###### 哈希表

不需要通过关键字值与定值比较，来确定位置，根据定值能直接定位记录的存储位置

###### 哈希表的结构和特点

hashtable：散列表

特点：

- 添加、查询、删除、更新数据快
- 无序
- 唯一

结构（多种）：

- 顺序表+链表：主结构是顺序表，每个顺序表的结点在单独引出一个链表

  ![hashtable顺序表&链表结构.jpg](https://gitee.com/zyy92/Pictures/raw/master/Hashtable/hashtable顺序表&链表结构.jpg)

  哈希表初始操作状态

  ![hashtable初始操作状态.jpg](https://gitee.com/zyy92/Pictures/raw/master/Hashtable/hashtable初始操作状态.jpg)

###### 哈希表添加数据

- 计算哈希码(调用`hashCode()`），结果是一个int值，整数的哈希码取自身即可
- 计算在哈希表中的存储位置 `y = k(x) = x % 11`	x:哈希码，k(x):函数，y:在哈希表的存储位置
- 存入哈希表
  - 一次添加成功
  - 多次添加成功（出现冲突，调用`equals()`和对应链表的元素进行比较，比较到最后，结果都是`false`，创建新结点，存储数据，并加入链表末尾）
  - 不添加（出现冲突，调用`equals()`和对应的元素进行比较，经过一次或多次比较后，结果都是`true`，表明重复，不添加）

哈希表最终状态

![hashtable最终状态.jpg](https://gitee.com/zyy92/Pictures/raw/master/Hashtable/hashtable最终状态.jpg)

###### 哈希表查找、删除、更新

查找：与添加相似，通过函数计算对应位置

删除：改变直接前驱结点指向直接后继结点

更新：先查找到位置，更新数据

###### hashCode与equals

`hashCode()`：计算哈希码，根据哈希码可以计算出数据在哈希表中的存储位置

`equals()`：添加时出现冲突和查询时，通过`equals()`进行比较，判断是否相同

###### 各种类型数据的哈希码的获取

| 数据类型 | 方法                                                         |
| -------- | :----------------------------------------------------------- |
| int      | 取自身                                                       |
| double   | `long bits=doubleToLongBits(value);`<br />`(int)(bits ^ (bits >>> 32));` |
| String   | `int h=hash;`<br />`if(h==0 && value.length > 0){`<br />    `char var[] = value;`<br />    `for(int i=0; i<value.length; i++){`<br />         ` h = 31 * h + val[i];`<br />      }<br />      `hash=h;`<br />`}`<br />`return hash;` |
| 对象     | 各个属性的哈希码进行某些相加相乘的运算                       |

###### 减少冲突

- 哈希表的长度和表中的记录数的比例--装填因子：

  根据最终记录存储个数和关键字的分布特点来确定哈希表大小

  动态变化**装填因子 = 表中的记录的数 / 哈希表的长度**

  - 装填因子越小，表中还有很多的空单元，则添加发生冲突的可能性越小
  - 装填因子越小，发生冲突可能性越大，在查找时所耗费的事件越多
  - 装填因子=0.5时，哈希表性能能达到最优

- 哈希函数的选择：

  直接定址法，平方取中法，折叠法，**除留去余法**(y= x % 11)

- 处理冲突方法：

  **链地址法**，开放地址法，再散列法，建立一个公共溢出区

#### Java中的查找树和哈希表

###### TreeSet和TreeMap

`TreeSet`和`TreeMap`底层使用红黑树

`TreeSet`底层结构是`TreeMap`

添加结点过程会通过旋转等方法，保证每次添加前后都是平衡树

###### HashSet HashMap HashTable(过时)

`HashSet` `HashMap` `HashTable`底层使用哈希表

在`JDK1.7`及其之前，`HashMap`存储结构为：`table数组`+`链表`

在`JDK1.8`中，当链表的存储数据个数大于等于`8`时，不再采用链表存储，而采用红黑树存储结构（在查询时间复杂度上：链表为**O(n)**,红黑树为**O(lon<sub>2</sub>n)**）

`HashSet`底层结构是`HashMap`，`HashSet的元素`作为`HashMap的key`，统一使用`Object`对象作为`value`

```java
/**
* HashSet部分源码
*/
private static final Object PRESENT = new Object();
public boolean add(E e) {
    return map.put(e, PRESENT)==null;
}
```

#### 相关代码下载

[相关代码](/download/search.rar)