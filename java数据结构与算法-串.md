---
title: java数据结构与算法--串
tags:
  - 串
categories:
  - notes
  - java
  - 数据结构与算法
abbrlink: 7e56a691
date: 2020-04-10 14:00:00
---

## 串

内容受限的线性表

#### 串的定义

串(String)：零个或多个任意字符组成的有限序列

- 串名
- 串值
- 串长

##### 相关概念

###### 子串

一个串中任意个连续字符组成的**子序列**（含空串）称为该串的子串

真子串：不包含自身的所有子串

###### 主串

包含子串的串称为主串

###### 字符位置

字符在序列中的序号为该字符在串中的位置

###### 子串位置

子串第一个字符在主串中的位置

###### 空格串

由一个或多个空格组成的串（与空串不同）

###### 串相等

当且仅当两个串的长度相等并且各个对应位置上的字符都相同时，这两个串才是相等的

注：所有空串都是相等的

##### 串的类型定义

数据对象：D={a<sub>i</sub> | a<sub>i</sub>∈ CharacterSet, i = 1,2,...n}

数据关系：R<sub>1</sub>={<a<sub>i-1</sub>,a<sub>i</sub>>|a<sub>i-1</sub>,a<sub>i</sub> ∈ D , i = 1,2,...,n}

<!--more-->

#### 串的存储结构

###### 顺序存储结构

顺序串

```java
public class String{
    char[n] strings;	//数据
    int length;	//串长度
}
```

###### 链式存储结构

链串

```java
//数据域：每个结点存储n个字符
//指针域：存储下一个结点的地址
public class Node{
    char[n] data;	//数据--n个字符
    Node next;	//直接后继
}

public class String{
    Node head;	//头结点
    Node tail;	//尾结点
    int length; //串长度
}
```

存储密度 = (串值所占的内存) / 实际分配的内存

#### 串的基本操作

详细代码见String源码 [Java API文档](https://docs.oracle.com/javase/8/docs/api/index.html)

- 串赋值 

  `String str = "content";`

  `String str = new String("content");`

- 串比较 

  `str.compareTo(String anotherString)`

- 求串长

  `int len = str.length();`

- 串连结 

  `String newString=str.concat(String anotherString);`

- 求子串 

  `String seq = str.substring( int beginIndex);`

  `String seq = str.substring( int beginIndex, int endIndex);`

- 串拷贝 

  `String s = String.copyValueOf(str);`

- 串判空 

  `boolean empty = str.isEmpty();`

- 子串的位置

  `int index = str.indexOf(char seq)`

  `int index = str.indexOf(String seq);`

- 串替换 

  `String seq = str.replace();`

#### 串的匹配模式算法

确定主串中所含子串(模式串)第一次出现的位置

##### BF算法

简单匹配算法 Brute-Force(暴力算法) 采用的是**穷举法**

###### 算法思路

从主串的每一个字符开始依次与模式串的字符进行匹配

如果当前字符匹配成功，则继续匹配

如果匹配失败，回溯，从主串该次匹配成功的第一个字符的下一个字符开始直到模式串

###### 代码实现

```java
/**
 * 字符串匹配--暴力算法
 * 时间复杂度：O(n*m)	//m为主串长度，n为模式串长度
 * @param text      主串
 * @param pattern   模式串
 * @return 匹配成功返回模式串第一次出现的位置
 */
public static int indexByBruteForce(String text, String pattern) {
    int sLen = text.length();       //主串字符长度
    int pLen = pattern.length();    //模式串字符长度

    char[] s = text.toCharArray();      //主串字符
    char[] p = pattern.toCharArray();   //模式串字符

    if (sLen < pLen)    //主串长度比模式串长度小，直接返回不匹配
        return -1;

    int i = 0;  //主串指针位置
    int j = 0;  //模式串指针位置

    while (i < sLen && j < pLen) {
	//while (i < sLen && j < pLen && sLen - i + j >= pLen) {  // 优化sLen - i + j >= pLen保证主串有剩余字符能匹配成功

        if (s[i] == p[j]) {   //如果当前字符匹配成功，则继续匹配
            i++;
            j++;
        } else {  //如果失配，回溯
            i = i - (j - 1);    //从该次匹配成功的第一个字符的下一个字符开始
            j = 0;              //从第一个字符开始
        }
    }
    //匹配成功返回第一个字符的位置，匹配不成功，返回-1
    return j == pLen ? i - j : -1;
}
```

测试代码

```java
int index = indexByBruteForce("this is for test", "test");
System.out.println(index);
//输出
12
```

##### KMP算法

Knuth Morris Pratt

###### 算法思路

利用已经部分匹配的结果加快模式串的滑动速度，且主串s的指针i不必回溯

在**已经匹配的模式串的子串**中，找出最长的相同的前缀和后缀，然后移动使它们重叠，定义next[j]数组，表明当模式中第j个字符与主串中相应字符，不匹配时，在模式中需要重新和主串中该字符进行比较的字符的位置
$$
next[j]={\begin{cases}max\{k|1<k<j,且P_1...P_{k-1} = P_{j-k+1}...P_{j-1}\} & 集合为空\\
0 & j=1	\\
1 & 其他情况
\end{cases}}
$$
时间复杂度：O(m+n)

可参考资料：[字符串匹配的KMP算法--阮一峰](http://www.ruanyifeng.com/blog/2013/05/Knuth–Morris–Pratt_algorithm.html)

###### 算法改进

在next[j]数组的基础上引入nextval[j]数组

nextval[j]数组
$$
nextval[j]=\begin{cases}next[j]的next值 & 当前元素的符号与第next[j]的元素符号相同(循环比较直至不相同)\\
next[j]\end{cases}
$$
nextval[j]数组：相同填next值对应的比较后的值，不同为自己的next值

|   序号    |  1   |  2   |  3   |  4   |  5   |  6   |  7   |  8   |
| :-------: | :--: | :--: | :--: | :--: | :--: | :--: | :--: | :--: |
|  模式串   |  a   |  b   |  a   |  a   |  b   |  c   |  a   |  c   |
|  next值   |  0   |  1   |  1   |  2   |  2   |  3   |  1   |  2   |
| nextval值 |  0   |  1   |  0   |  2   |  2   |  3   |  0   |  2   |

###### 代码实现

```java
/**
 * @param text 主串
 * @param pattern 模式串
 * @return 如果匹配成功，返回下标，否则返回-1
 */

public static int KMP(String text, String pattern) {
    if (text.length() < pattern.length() || pattern.length() == 0 || null == pattern) //
        return -1;
    //求模式串的next数组
    int[] next = new int[pattern.length()];
    next[0] = -1;
    for (int i = 1, k = -1; i < pattern.length(); i++) {
        if (k == -1 || pattern.charAt(i - 1) == pattern.charAt(k)) {
            k++;
            next[i] = k;
        } else {
            next[i] = 0;
            k = 0;
        }
    }
    //进行匹配
    for (int i = 0, j = 0; i < text.length(); ) {
        if (text.charAt(i) != pattern.charAt(j)) {
            if (next[j] == -1)
                i++;
            else
                j = next[j];
        } else {
            if (j == pattern.length() - 1)
                return i - pattern.length() + 1;
            i++;
            j++;
        }
    }
    return -1;
}
```

测试代码

```java
int index = KMP("this is for test", "test");
System.out.println(index);
//输出
12
```