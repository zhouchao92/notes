---
title: 'LeetCode--#202快乐数'
categories:
  - leetcode
abbrlink: 6cd30686
date: 2020-04-30 10:24:48
---

###### 快乐数

> 编写一个算法来判断一个数 n 是不是快乐数。
>
> 「快乐数」定义为：对于一个正整数，每一次将该数替换为它每个位置上的数字的平方和，然后重复这个过程直到这个数变为 1，也可能是**无限循环** 但始终变不到 1。如果 可以变为  1，那么这个数就是快乐数。
>
> 如果 n 是快乐数就返回 `True` ；不是，则返回 `False` 。
> 编写一个算法来判断一个数 n 是不是快乐数。

> 示例：
>
> 输入：19
> 输出：true
> 解释：
> $1^2$ + $9^2$ = 82
> $8^2$ + $2^2$ = 68
> $6^2$ + $8^2$ = 100
> $1^2$ + $0^2$ + $0^2$ = 1

> 来源：力扣（LeetCode）
> 链接：[https://leetcode-cn.com/problems/happy-number](https://leetcode-cn.com/problems/happy-number)

<!--more-->

###### 集合法

通过用Set集合来保存数n去判断是否出现循环，当出现循环时，返回`false`

时间复杂度：$O(\log_2{n})$

空间复杂度：$O(\log_2{n})$

```java
/**
* 集合法
*/
public static boolean isHappyBySet(int n) {
    //List<Integer> list = new ArrayList<>();
    //用Set比List快
    Set<Integer> list = new HashSet<>();
    while (n != 1 && !list.contains(n)) {
        list.add(n);
        int num = 0;
        //求n的每一位的平方和
        while (n != 0) {
            int bit = n % 10;
            num += bit * bit;
            n = n / 10;
        }
        n = num;
    }
    return n == 1;
}
```

###### 快慢指针法

> 使用"快慢指针"思想找出循环："快指针"每次走两步，"慢指针"每次走一步，二者相等时，即为一个循环周期。
> 此时，判断是不是因为1引起的循环，是的话就是快乐数，否则不是快乐数。

> 参考题解：
>
> 作者：linux-man
>
> 链接：[https://leetcode-cn.com/problems/happy-number/solution/shi-yong-kuai-man-zhi-zhen-si-xiang-zhao-chu-xun-h/](https://leetcode-cn.com/problems/happy-number/solution/shi-yong-kuai-man-zhi-zhen-si-xiang-zhao-chu-xun-h/)
>
> 来源：力扣（LeetCode）

时间复杂度：$O(\log_2{n})$

空间复杂度：$O(1)$

```java
/**
* 快慢指针法
 */
public static boolean isHappy(int n) {
    int slow = n, fast = n;
    do {
        slow = bitSquareSum(slow);
        fast = bitSquareSum(fast);
        fast = bitSquareSum(fast);
    } while (slow != fast);

    return slow == 1;
}
/**
* 求数n每一位的平方和
*/
private static int bitSquareSum(int n) {
    int num = 0;
    while (n != 0) {
        int bit = n % 10;
        num += bit * bit;
        n = n / 10;
    }
    return num;
}
```