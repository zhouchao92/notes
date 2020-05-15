---
title: 'LeetCode--#33搜索旋转排序数组'
tags:
  - leetcode
abbrlink: 69254e2b
date: 2020-04-28 21:12:06
---


###### 搜索旋转排序数组

> 假设按照升序排序的数组在预先未知的某个点上进行了旋转。
>
> ( 例如，数组 [0,1,2,4,5,6,7] 可能变为 [4,5,6,7,0,1,2] )。
>
> 搜索一个给定的目标值，如果数组中存在这个目标值，则返回它的索引，否则返回 -1 。
>
> 你可以假设数组中不存在重复的元素。
>
> 你的算法时间复杂度必须是 O(log n) 级别。

> 示例 1:
>
> 输入: nums = [4,5,6,7,0,1,2], target = 0
> 输出: 4
>
> 示例 2:
>
> 输入: nums = [4,5,6,7,0,1,2], target = 3
> 输出: -1

> 来源：力扣（LeetCode）
> 链接：https://leetcode-cn.com/problems/search-in-rotated-sorted-array

<!--more-->

###### 二分搜索

算法时间复杂度要求必须是 O(log n) 级别，需要使用二分法求解，而数组不一定是完全有序的，是部分有序的，要考虑到旋转点的位置

```java
/**
* 递归二分搜索
*
* @param nums
* @param target
* @return
*/
public static int searchByRecursion(int[] nums, int target) {
    if (nums == null || nums.length == 0)	//判空，避免空指针异常和索引越界
        return -1;
    return search(nums, target, 0, nums.length - 1);
}

private static int search(int[] nums, int target, int left, int right) {
    int mid = left + (right - left) / 2;
    //使用>=可以避免数组索引越界
    if (left >= right && target != nums[mid])   //递归结束条件：没找到
        return -1;
    if (target == nums[mid])    //递归结束条件：找到
        return mid;
    if (nums[0] <= nums[mid]) { 
        if (nums[0] <= target && target < nums[mid])    //target在前半部分
            return search(nums, target, left, mid - 1);
        else
            return search(nums, target, mid + 1, right);
    } else {
        if (nums[mid] < target && target <= nums[nums.length - 1])  //target在后半部分
            return search(nums, target, mid + 1, right);
        else
            return search(nums, target, left, mid - 1);
    }
}
```

