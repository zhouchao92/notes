---
title: 'LeetCode--#1095山脉数组中查找目标值'
categories:
  - leetcode
abbrlink: 8d630654
date: 2020-04-29 20:17:43
---

###### 山脉数组中查找目标值

> 给你一个 山脉数组 `mountainArr`，请你返回能够使得 `mountainArr.get(index)` 等于 target 最小 的下标 index 值。
>
> 如果不存在这样的下标 index，就请返回 -1。
>
>  
>
> 何为山脉数组？如果数组 A 是一个山脉数组的话，那它满足如下条件：
>
> 首先，A.length >= 3
>
> 其次，在 0 < i < A.length - 1 条件下，存在 i 使得：
>
> A[0] < A[1] < ... A[i-1] < A[i]
> A[i] > A[i+1] > ... > A[A.length - 1]
>
>
> 你将 不能直接访问该山脉数组，必须通过 `MountainArray` 接口来获取数据：
>
> `MountainArray.get(k)` - 会返回数组中索引为k 的元素（下标从 0 开始）
> `MountainArray.length()` - 会返回该数组的长度
>
> 注意：
> 对 `MountainArray.get` 发起超过 100 次调用的提交将被视为错误答案。

> 示例 1：
>
> 输入：array = [1,2,3,4,5,3,1], target = 3
> 输出：2
> 解释：3 在数组中出现了两次，下标分别为 2 和 5，我们返回最小的下标 2。
>
> 示例 2：
>
> 输入：array = [0,1,2,4,2,1], target = 3
> 输出：-1
> 解释：3 在数组中没有出现，返回 -1。
>
> 提示：
> 3 <= `mountain_arr.length()` <= 10000
> 0 <= target <= $10^9$
> 0 <= `mountain_arr.get(index)` <= $10^9$

> 来源：力扣（LeetCode）
> 链接：[https://leetcode-cn.com/problems/find-in-mountain-array](https://leetcode-cn.com/problems/find-in-mountain-array)

<!--more-->

###### 三次二分查找

第一次二分查找：找到峰值

第二次二分查找：要返回最小的下标的目标值，从峰值左边开始二分查找，找到返回其索引，没找到则返回-1

第三次二分查找：当峰值左边没有目标值时，再对峰值右边进行二分查找，找到返回其索引，没找到则返回-1

代码

```java
interface MountainArray {
    public int get(int index);

    public int length();
}

//题解
public int findInMountainArray(int target, MountainArray mountainArr) {
    int len = mountainArr.length();
    int top = findMountainTop(mountainArr, 0, len - 1);	//找峰值的位置
    int index = findFromMountainLeft(mountainArr, 0, top, target);	// 在峰值左边进行二分查找
    if (index == -1)
        return findFromMountainRight(mountainArr, top, len - 1, target);

    return index;
}

/**
* 二分法找峰值
*/
private int findMountainTop(MountainArray mountainArr, int left, int right) {
    while (left < right) {
        // int mid =(left+right)/2;	可能会导致整形溢出
        int mid = left + (right - left) / 2;
        if (mountainArr.get(mid) < mountainArr.get(mid + 1))
            // 如果当前的数比右边的数小，它一定不是山顶
            left = mid + 1;
        else 
            right = mid;
    }
    //峰值是一定存在的
    return left;
}

/**
* 二分法在峰值左边查找
*/
private int findFromMountainLeft(MountainArray mountainArr, int left, int right, int target) {
    while (left < right) {
        int mid = left + (right - left) / 2;
        if (mountainArr.get(mid) < target)
            // 如果当前的数比目标值小，则目标数在大的一部分(右边)
            left = mid + 1;
		else
            right = mid;
    }
    //剩余一个数时，判断是否是目标值
    if (mountainArr.get(left) == target)
        return left;

    return -1;
}

/**
* 二分法在峰值右边查找
*/
private int findFromMountainRight(MountainArray mountainArr, int left, int right, int target) {
    while (left < right) {	//结束条件相当于left=right
        int mid = left + (right - left) / 2;
        if (mountainArr.get(mid) > target)
            // 如果当前的数比目标值小，则目标数在小的一部分(右边)
            left = mid + 1;
        else
            right = mid;
    }
    //剩余一个数时，判断是否是目标值
    if (mountainArr.get(left) == target)
        return left;
    
    return -1;
}
```

参考题解：[https://leetcode-cn.com/problems/find-in-mountain-array/solution/shi-yong-chao-hao-yong-de-er-fen-fa-mo-ban-python-/](https://leetcode-cn.com/problems/find-in-mountain-array/solution/shi-yong-chao-hao-yong-de-er-fen-fa-mo-ban-python-/)