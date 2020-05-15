---
title: 'LeetCode--#46全排列'
tags:
  - leetcode
date: 2020-04-28 21:14:09
---

###### 全排列

> 给定一个 没有重复 数字的序列，返回其所有可能的全排列。

> 示例:
>
> 输入: [1,2,3]
> 输出:
> [
> [1,2,3],
> [1,3,2],
> [2,1,3],
> [2,3,1],
> [3,1,2],
> [3,2,1]
> ]

> 来源：力扣（LeetCode）
> 链接：[https://leetcode-cn.com/problems/permutations](https://leetcode-cn.com/problems/permutations)

<!--more-->

###### 回溯法

解析：[https://leetcode-cn.com/problems/permutations/solution/quan-pai-lie-by-leetcode-solution-2/](https://leetcode-cn.com/problems/permutations/solution/quan-pai-lie-by-leetcode-solution-2/)

时间复杂度：$O(n*n!)$，其中 *n* 为序列的长度
空间复杂度：$O(n)$，其中 *n* 为序列的长度

```java
/**
* 回溯法
*
* @param len
* @param output
* @param res
* @param first
*/
public void backtrack(int len,
                      List<Integer> output,
                      List<List<Integer>> res,
                      int first) {
    // 递归结束条件：所有数都填完了
    if (first == len)
        res.add(new ArrayList<>(output));
    for (int i = first; i < len; i++) {
        // 动态维护数组
        Collections.swap(output, first, i);
        // 继续递归填下一个数
        backtrack(n, output, res, first + 1);
        // 撤销操作
        Collections.swap(output, first, i);
    }
}

public List<List<Integer>> permute(int[] nums) {
    List<List<Integer>> res = new ArrayList<>();

    ArrayList<Integer> output = new ArrayList<>();
    for (int num : nums)
        output.add(num);

    int len = nums.length;
    backtrack(len, output, res, 0);
    return res;
}
```