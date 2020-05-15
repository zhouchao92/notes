---
title: 'LeetCode--面试题#51数组中的逆序对'
tags:
  - leetcode
date: 2020-04-25 10:43:20
---


###### 数组中的逆序对

> 在数组中的两个数字，如果前面一个数字大于后面的数字，则这两个数字组成一个逆序对。输入一个数组，求出这个数组中的逆序对的总数。

>  示例 1:
>
>  输入: [7,5,6,4]
>  输出: 5
>
>
>  限制：
>
>  0 <= 数组长度 <= 50000

> 来源：力扣（LeetCode）
>
> 链接：[https://leetcode-cn.com/problems/shu-zu-zhong-de-ni-xu-dui-lcof/](https://leetcode-cn.com/problems/shu-zu-zhong-de-ni-xu-dui-lcof/)

<!--more-->

###### 暴力解法

数组从前往后，逐个比较

时间复杂度：O(n<sup>2</sup>)

空间复杂度：O(1)

```java
/**
 * 暴力算法：超时
 *
 * @param nums
 * @return
 */
public int reversePairsBF(int[] nums) {
    if (nums == null || nums.length < 2) {
        return 0;
    }
    int res = 0;
    for (int i = 0; i < nums.length - 1; i++) {
        for (int j = i + 1; j < nums.length; j++) {
            if (nums[i] > nums[j])
                res++;
        }
    }
    return res;
}
```

###### 归并排序-分治法

归并排序

> 如　设有数列{6，202，100，301，38，8，1}
> 初始状态：6,202,100,301,38,8,1
> 第一次归并后：{6,202},{100,301},{8,38},{1}，比较次数：3；
> 第二次归并后：{6,100,202,301}，{1,8,38}，比较次数：4；
> 第三次归并后：{1,6,8,38,100,202,301},比较次数：4；
> 总的比较次数为：3+4+4=11；
> 逆序数为14；

> - 申请空间，使其大小为两个已经[排序](https://baike.baidu.com/item/排序)序列之和，该空间用来存放合并后的序列
> - 设定两个指针，最初位置分别为两个已经排序序列的起始位置
> - 比较两个指针所指向的元素，选择相对小的元素放入到合并空间，并移动指针到下一位置
> - 重复步骤3直到某一指针超出序列尾
> - 将另一序列剩下的所有元素直接复制到合并序列尾

> [百度百科：归并排序](https://baike.baidu.com/item/%E5%BD%92%E5%B9%B6%E6%8E%92%E5%BA%8F)

```java
public int[] mergeSort(int[] nums, int low, int high) {
    if (low == high)
        return new int[]{nums[low]};

    int mid = low + (high - low) / 2;
    int[] leftArr = mergeSort(nums, low, mid); //左有序数组
    int[] rightArr = mergeSort(nums, mid + 1, high); //右有序数组
    int[] newNum = new int[leftArr.length + rightArr.length]; //新有序数组

    int m = 0, i = 0, j = 0;
    while (i < leftArr.length && j < rightArr.length) {
        newNum[m++] = leftArr[i] < rightArr[j] ? leftArr[i++] : rightArr[j++];
    }
    while (i < leftArr.length)
        newNum[m++] = leftArr[i++];
    while (j < rightArr.length)
        newNum[m++] = rightArr[j++];
    return newNum;
}
```

归并排序解题

* 在归并的过程中计算每个小区间的逆序对数，进而计算出大区间的逆序对数
* 时间复杂度：O(n log<sub>2</sub> n)
* 空间复杂度：O(n)

```java
/**
* 借助归并排序：分治法
*
* @param nums
* @return 逆序对数
*/
public int reversePairs(int[] nums) {
    int[] temp = new int[nums.length];   //临时数组，用于存放有序数组
    return merge(nums, 0, nums.length - 1,temp);
}

private int merge(int[] nums, int low, int high,int[] temp) {
    if (low >= high)    //递归结束条件
        return 0;
    int mid = low + (high - low) / 2;
    int res = merge(nums, low, mid,temp) + merge(nums, mid + 1, high,temp);	//区间划分

    int i = low, j = mid + 1, k = 0;
    while (i <= mid && j <= high) {
        res += nums[i] <= nums[j] ? j - (mid + 1) : 0;
        temp[k++] = nums[i] <= nums[j] ? nums[i++] : nums[j++];
    }
    while (i <= mid) {
        res += j - (mid + 1);	
        temp[k++] = nums[i++];
    }
    while (j <= high)
        temp[k++] = nums[j++];
    System.arraycopy(temp, 0, nums, low, high - low + 1);   //复制到原数组中
    return res;
}
```

