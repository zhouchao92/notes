---
title: 'LeetCode--#1两数之和'
categories:
  - leetcode
abbrlink: eb1f9a79
date: 2020-04-28 20:59:53
---


###### 两数之和

> 给定一个整数数组 `nums` 和一个目标值 `target`，请你在该数组中找出和为目标值的那两个整数，并返回他们的数组下标。
>
> 你可以假设每种输入只会对应一个答案。但是，数组中同一个元素不能使用两遍。

> 示例:
>
> 给定 nums = [2, 7, 11, 15], target = 9
>
> 因为 nums[0] + nums[1] = 2 + 7 = 9
> 所以返回 [0, 1]

> 来源：力扣（LeetCode）
> 链接：[https://leetcode-cn.com/problems/two-sum](https://leetcode-cn.com/problems/two-sum)

<!--more-->

###### 暴力解法(超时)

枚举比较，从第一个数到最后一个数逐个求和比较，直至匹配成功

时间复杂度：$O(n^2)$
空间复杂度：$O(1)$

```java
/**
* 暴力解法
*/
public int[] twoSumByBF(int[] nums, int target) {
    if (nums == null || nums.length < 2)
        return new int[]{-1, -1};
    for (int i = 0; i < nums.length - 1; i++) {
        for (int j = i + 1; j < nums.length; j++) {
            if (target == nums[i] + nums[j]) {
                return new int[]{i, j};
            }
        }
    }
    return new int[]{-1, -1};
}
```

###### 两遍hash表

第一遍：将数组转换成`hash`表，以数组的数值作为键，以索引作为值存储在`hash`表中
第二遍：在`hash`表中查找，查找能与当前数组的元素的和为`target`的键

时间复杂度：O(n)
空间复杂度：O(n)

```java
/**
* 两遍hash表
* @param nums
* @param target
* @return
*/
public int[] twoSumByHashMap(int[] nums, int target) {
    if (nums == null || nums.length < 2)
        return new int[]{-1, -1};
    Map<Integer, Integer> table = new HashMap<>();
    //将数组转换成hash表
    for (int i = 0; i < nums.length; i++)
        table.put(nums[i], i);	//以数值为键，索引为值
    //查找
    for (int i = 0; i < nums.length - 1; i++) {
        int searched = target - nums[i];
        if (table.containsKey(searched) && table.get(searched) != i) {
            return new int[]{i, table.get(searched)};
        }
    }
    return new int[]{-1, -1};
}
```

###### 一遍hash表

一边插入hash表，一边查找

时间复杂度：O(n)
空间复杂度：O(n)

```java
/**
* 一遍hash表，边插入边查找
* @param nums
* @param target
* @return
*/
public int[] twoSumByHashMapOnce(int[] nums, int target) {
    if (nums == null || nums.length < 2)
        return new int[]{-1, -1};
    Map<Integer, Integer> table = new HashMap<>();
    //将数组转换成hash表
    for (int i = 0; i < nums.length; i++) {
        int searched = target - nums[i];
        //先查找
        if (table.containsKey(searched) && table.get(searched) != i)	//保证不是同一个元素
            return new int[]{table.get(searched), i};
		//后插入表中
        table.put(nums[i], i);
    }
    return new int[]{-1, -1};
}
```

