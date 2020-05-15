---
title: java数据结构与算法--排序
tags:
  - java
  - 数据结构与算法
  - notes
date: 2020-04-13 10:08:34
---

## 排序

#### 排序基本概念

###### 排序

排序是将一个数据元素的任意序列，重新排列成一个按关键字有序的序列

###### 内部排序和外部排序

内部排序：整个排序过程在内存储器中进行

外部排序：待排序元素量太大，内存储器无法容纳全部数据，排序需要借助外部存储设备才能完成

###### 稳定排序和不稳定排序

如果在待排序的序列中存在多个具有相同关键字的元素

假设K<sub>i</sub>=K<sub>j</sub> { 1≤i≤n，1≤j≤n，i≠j }，排序前序列中的R<sub>i</sub>在R<sub>j</sub>之前，经过排序后得到的序列R<sub>i</sub>仍在R<sub>j</sub>之前，则称所用排序方法是**稳定的**，反之则是**不稳定的**

###### 比较排序和非比较排序

大部分排序需要通过比较来判断大小，作为排序的依据，而计数排序、基数排序不需要进行比较

###### 其他排序

- 插入排序：将无序子序列中的一个或几个记录“插入”到有序序列中，从而增加记录的有序子序列的长度
- 交换排序：通过“交换”无序序列中的记录从而得到其中关键字最小或最大的记录，并将它加入到有序子序列中，以此方法增加记录的有序子序列的长度
- 选择排序：从记录的无序子序列中“选择”关键字最小或最大的记录，并将它加入到有序子序列中，以此方法增加记录的有序子序列的长度
- 归并排序：通过“归并”两个或两个以上的记录有序子序列，逐步增加记录有序序列的长度

<!--more-->


#### 交换排序

##### 冒泡排序

###### 算法步骤

- 比较相邻的元素。如果第一个比第二个大，就交换他们两个。
- 对每一对相邻元素作同样的工作，从开始第一对到结尾的最后一对。这步做完后，最后的元素会是最大的数。
- 针对所有的元素重复以上的步骤，除了最后一个。
- 持续每次对越来越少的元素重复上面的步骤，直到没有任何一对数字需要比较。

###### 代码实现

```java
/**
* 冒泡排序
* 按小到大排序
*
* @param array 待排序数组
*/
public static void bubbleSort(int[] array) {
    for (int i = 0; i < array.length - 1; i++) {
        for (int j = 0; j < array.length - i - 1; j++) {
            if (array[j] > array[j + 1]) {
                int temp = array[j + 1];
                array[j + 1] = array[j];
                array[j] = temp;
            }
        }
        System.out.println(Arrays.toString(array));	//输出当前排序
    }
}
```

测试代码：

```java
int[] arr = {9, 8, 7, 6, 5, 4, 3, 2, 1};
bubbleSort(arr);
//输出
[8, 7, 6, 5, 4, 3, 2, 1, 9]
[7, 6, 5, 4, 3, 2, 1, 8, 9]
[6, 5, 4, 3, 2, 1, 7, 8, 9]
[5, 4, 3, 2, 1, 6, 7, 8, 9]
[4, 3, 2, 1, 5, 6, 7, 8, 9]
[3, 2, 1, 4, 5, 6, 7, 8, 9]
[2, 1, 3, 4, 5, 6, 7, 8, 9]
[1, 2, 3, 4, 5, 6, 7, 8, 9]
```

##### 快速排序

快速排序是冒泡排序的改进版，也是最好的一种内排序，涉及到分治和递归

分治法(Dvide-and-Conquer Method)

###### 算法步骤：

- 从数列中去一个数作为基准数（简单起见可以取第一个数）
- 分区过程，将比这个数大的数全放到它的右边，小于或等于它的数全放到他的左边（**分区**）
- 再对左右区间重复第一步、第二步，直到各区间只有一个数（**递归**）

###### 代码实现

```java
public static void quickSort(int[] array) {
    int start = 0;
    int end = array.length - 1;
    quickSort(array, start, end);
}

/**
* 快速排序递归部分
*
* @param array 待排数组
* @param start 
* @param end 
*/
private static void quickSort(int[] array, int start, int end) {
    if (start < end) {  //递归结束条件
        //从数列中取一个数作为基准数（简单起见取第一个数）
        int base = array[start];
        int pLeft = start;	//左指针
        int pRight = end;	//右指针
        //分区过程，将比这个数大的数全放到它的右边，小于或等于它的数全放到他的左边（**分区**）
        while (pLeft < pRight) {    //pLeft!=pRight
            //从右到左，找到第一个小于基准值的值，将其移动到左指针的位置，并左指针+1
            while (pRight > pLeft) {
                if (array[pRight] < base) {
                    array[pLeft] = array[pRight];
                    pLeft++;
                    break;
                }
                pRight--;
            }
            //从左到右，找到第一个大于基准值的值，将其移动到右指针的位置，并右指针-1
            while (pLeft < pRight) {
                if (array[pLeft] > base) {
                    array[pRight] = array[pLeft];
                    pRight--;
                    break;
                }
                pLeft++;
            }
        }
        //基准值填坑
        array[pLeft] = base;
        //对左区间快排
        quickSort(array, start, pLeft - 1);
        //对右区间快排
        quickSort(array, pRight + 1, end);
    }
}
```

测试代码

```java
int[] arr = {4, 2, 8, 6,10, 1, 3, 7, 9, 5};
quickSort(arr);	//快速排序
System.out.println(Arrays.toString(arr));	
//输出
[1, 2, 3, 4, 5, 6, 7, 8, 9, 10]
```

#### 选择排序

##### 基本选择排序

###### 算法步骤

- 首先在未排序序列中找到最小（大）元素，存放到排序序列的起始位置。
- 再从剩余未排序元素中继续寻找最小（大）元素，然后放到已排序序列的末尾。
- 重复第二步，直到所有元素均排序完毕。

###### 代码实现

```java
/**
* 选择排序
* @param array
*/
public static void selectionSort(int[] array) {
    // 总共要经过 N-1 轮比较
    for (int i = 0; i < array.length - 1; i++) {
        int min = i;    //记录最小值的位置

        // 每轮需要比较的次数 N-i
        for (int j = i + 1; j < array.length; j++) {
            if (array[j] < array[min]) {
                // 记录目前能找到的最小值元素的下标
                min = j;
            }
        }

        // 将找到的最小值和i位置所在的值进行交换
        if (i != min) {
            int temp = array[i];
            array[i] = array[min];
            array[min] = temp;
        }
    }
}
```

测试代码

```java
int[] arr = {8, 3, 10, 6, 4, 1, 2, 7, 9, 5};
selectionSort(arr);
System.out.println(Arrays.toString(arr));
//输出
[1, 2, 3, 4, 5, 6, 7, 8, 9, 10]
```

#### 插入排序

##### 直接插入排序

###### 算法步骤

将第一待排序序列第一个元素看做一个有序序列，把第二个元素到最后一个元素当成是未排序序列。

从头到尾依次扫描未排序序列，将扫描到的每个元素插入有序序列的适当位置。（如果待插入的元素与有序序列中的某个元素相等，则将待插入元素插入到相等元素的后面。）

###### 代码实现

```java
/**
* 插入排序
* @param array
*/
public static void insertSort(int[] array) {
    // 从下标为1的元素开始选择合适的位置插入，因为下标为0的只有一个元素，默认是有序的
    for (int i = 1; i < array.length; i++) {

        // 记录要插入的数据
        int temp = array[i];

        // 从已经排序的序列最右边的开始比较，找到比其小的数
        int j = i;
        while (j > 0 && temp < array[j - 1]) {
            array[j] = array[j - 1];
            j--;
        }

        // 存在比其小的数，插入
        if (j != i) {
            array[j] = temp;
        }

    }
}
```

测试代码

```java
int[] arr = {2, 3, 10, 9, 4, 8, 1, 7, 6, 5};
insertSort(arr);
System.out.println(Arrays.toString(arr));
//输出
[1, 2, 3, 4, 5, 6, 7, 8, 9, 10]
```

##### 二分法插入排序

###### 算法

在插入第i个元素时，对前面的0～i-1元素进行折半，先跟他们中间的那个元素比，如果小，则对前半再进行折半，否则对后半进行折半，直到left<right，然后再把第i个元素前1位与目标位置之间的所有元素后移，再把第i个元素放在目标位置上。

- 二分法查找插入位置
- 后移
- 插入

[2, 3, 10, 9, 4, 8, 1, 7, 6, 5]

[2, <font color="red">3</font>, 10, 9, 4, 8, 1, 7, 6, 5]
[2, 3, <font color="red">10</font>, 9, 4, 8, 1, 7, 6, 5]
[2, 3,<font color="red"> 9</font>, 10, 4, 8, 1, 7, 6, 5]
[2, 3, <font color="red">4</font>, 9, 10, 8, 1, 7, 6, 5]
[2, 3, 4, <font color="red">8</font>, 9, 10, 1, 7, 6, 5]
[<font color="red">1</font>, 2, 3, 4, 8, 9, 10, 7, 6, 5]
[1, 2, 3, 4, <font color="red">7</font>, 8, 9, 10, 6, 5]
[1, 2, 3, 4, <font color="red">6</font>, 7, 8, 9, 10, 5]
[1, 2, 3, 4, <font color="red">5</font>, 6, 7, 8, 9, 10]

时间复杂度：O(n<sup>2</sup>)

空间复杂度：O(1)

###### 代码

```java
public static void binaryInsertSort(int[] arr) {
    for (int i = 1; i < arr.length; i++) {
        int temp = arr[i];  //要操作的数
        int low = 0;
        int high = i - 1;
        int mid = -1;
        while (low <= high) {
            mid = (low + high) / 2;
            if (temp > arr[mid])
                low = mid + 1;
            else
                high = mid - 1;
        }
        //从后往前移动数组，直到temp要插入的位置
        for (int j = i - 1; j >= low; j--)
            arr[j + 1] = arr[j];
        //插入
        arr[low] = temp;
    }
}
```

测试代码

```java
int[] arr = {2, 3, 10, 9, 4, 8, 1, 7, 6, 5};
binaryInsertSort(arr);
System.out.println(Arrays.toString(arr));
//输出：
[1, 2, 3, 4, 5, 6, 7, 8, 9, 10]
```

#### 各种排序比较

###### 排序类型

![排序算法类型](https://gitee.com/zyy92/Pictures/raw/master/SortAlgorithm/排序算法.png)

###### 排序效率

<table border="1" >
  <tr align="center">
    <th colspan="7" >各种常用排序算法</th>
  </tr>
  <tr align="center" >
    <td rowspan="2">类别</td>
    <td rowspan="2">排序方法</td>
    <td colspan="3">时间复杂度</td>
    <td >空间复杂度</td>
    <td rowspan="2">稳定性</td>
  </tr>
  <tr align="center">
    <td>平均情况</td>
    <td>最好情况</td>
    <td>最坏情况</td>
    <td>辅助存储</td>
  </tr>
  <tr align="center">
    <td rowspan="2">插入排序</td>
    <td>直接插入</td>
    <td>O(n<sup>2</sup>)</td>
    <td>O(n)</td>
    <td>O(n<sup>2</sup>)</td>
    <td>O(1)</td>
    <td>稳定</td>
  </tr>
  <tr align="center">
    <td>希尔排序</td>
    <td>O(n<sup>1.3</sup>)或O(nlog<sub>2</sub>n)</td>
    <td>O(n)</td>
    <td>O(n<sup>2</sup>)</td>
    <td>O(1)</td>
    <td>不稳定</td>
  </tr>
  <tr align="center">
    <td rowspan="2">选择排序</td>
    <td>直接选择</td>
    <td>O(n<sup>2</sup>)</td>
    <td>O(n<sup>2</sup>)</td>
    <td>O(n<sup>2</sup>)</td>
    <td>O(1)</td>
    <td>不稳定</td>
  </tr>
  <tr align="center">
    <td>堆排序</td>
    <td>O(nlog<sub>2</sub>n)</td>
    <td>O(nlog<sub>2</sub>n)</td>
    <td>O(nlog<sub>2</sub>n)</td>
    <td>O(1)</td>
    <td>不稳定</td>
  </tr>
  <tr align="center">
    <td rowspan="2">交换排序</td>
    <td>冒泡排序</td>
    <td>O(n<sup>2</sup>)</td>
    <td>O(n)</td>
    <td>O(n<sup>2</sup>)</td>
    <td>O(1)</td>
    <td>稳定</td>
  </tr>
  <tr align="center">
    <td>快速排序</td>
    <td>O(nlog<sub>2</sub>n)</td>
    <td>O(nlog<sub>2</sub>n)</td>
    <td>O(n<sup>2</sup>)</td>
    <td>O(nlog<sub>2</sub>n)</td>
    <td>不稳定</td>
  </tr>
  <tr align="center">
    <td colspan="2">归并排序</td>
    <td>O(nlog<sub>2</sub>n)</td>
    <td>O(nlog<sub>2</sub>n)</td>
    <td>O(nlog<sub>2</sub>n)</td>
    <td>链表：O(1)或数组：O(n)</td>
    <td>稳定</td>
  </tr>
  <tr align="center">
    <td colspan="2">基数排序</td>
    <td>O(d(r+n))</td>
    <td>O(d(n+rd))</td>
    <td>O(d(r+n))</td>
    <td>O(rd+n)</td>
    <td>稳定</td>
  </tr>
  <tr align="center">
    <td colspan="7">注：基数排序的算法复杂度中，r代表关键字的基数，d代表长度，n代表关键字的个数</td>
  </tr>
</table>


#### 菜鸟教程

[十大经典排序算法](https://www.runoob.com/w3cnote/ten-sorting-algorithm.html)

#### 相关代码下载

[代码](/download/sort.rar)