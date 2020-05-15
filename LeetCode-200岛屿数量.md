---
title: 'LeetCode--#200岛屿数量'
tags:
  - leetcode
abbrlink: c2dfdd41
date: 2020-04-21 19:14:08
---


#### 题目

###### 岛屿数量

> - 给你一个由 '1'（陆地）和 '0'（水）组成的的二维网格，请你计算网格中岛屿的数量。
> - 岛屿总是被水包围，并且每座岛屿只能由水平方向和/或竖直方向上相邻的陆地连接形成。
> - 此外，你可以假设该网格的四条边均被水包围。
>

> 示例一：
>
> ```shell
> 输入:
>     11110
>     11010
>     11000
>     00000
> 输出: 1
> ```
>
> 解释: 每座岛屿只能由水平和/或竖直方向上相邻的陆地连接而成。
>
> 示例二：
>
> ```shell
> 输入:
>     11000
>     11000
>     00100
>     00011
> 输出: 3
> ```
>
> 解释: 每座岛屿只能由水平和/或竖直方向上相邻的陆地连接而成。
>

###### 来源

> 来源：力扣（LeetCode）
>
> 链接：[https://leetcode-cn.com/problems/number-of-islands](https://leetcode-cn.com/problems/number-of-islands)
>

<!--more-->

#### 解法

##### 深度优先搜索DFS

###### 思路

深度优先搜索：借助递归

- 遍历整个数组 
- 如果一个位置为 1，则以其为起始节点开始进行深度优先搜索
- 在深度优先搜索的过程中，每个搜索到的 1 都会被重新标记为 0
- 最终岛屿的数量就是进行深度优先搜索的次数

算法时间复杂度：O(MN)  M：二维网格的行数，N：二维网格的列数

算法空间复杂度：O(1)

###### 代码

```java
public int numIslandsByDFS(char[][] grid) {
    if (grid == null || grid.length == 0) { //判空
        return 0;
    }
    int islandNum = 0;          //岛屿数量
    int row = grid.length;      //行数
    int col = grid[0].length;   //列数

    for (int r = 0; r < row; r++) {     //遍历列元素
        for (int c = 0; c < col; c++) { //遍历行元素
            if (grid[r][c] == '1') {
                islandNum++;        //岛屿数量+1
                dfs(grid, r, c);    //循环深度优先搜索
            }
        }
    }
    return islandNum;   //返回岛屿数量
}

/**
* 深度优先搜索--借助递归
* 遍历整个数组
* 如果一个位置为 1，则以其为起始节点开始进行深度优先搜索
* 在深度优先搜索的过程中，每个搜索到的 1 都会被重新标记为 0
* 最终岛屿的数量就是进行深度优先搜索的次数
*
* @param grid 二维网格
* @param r	当前元素行数
* @param c	当前元素列数
*/
private void dfs(char[][] grid, int r, int c) {
    int row = grid.length;      //行数
    int col = grid[0].length;   //列数

    if (r < 0 || c < 0 ||
        r >= row || c >= col ||
        grid[r][c] == '0') {    //递归结束条件
        return;
    }
    grid[r][c] = '0';     //重新标记
    dfs(grid, r - 1, c);    //正上方
    dfs(grid, r + 1, c);    //正下方
    dfs(grid, r, c - 1);    //正左方
    dfs(grid, r, c + 1);    //正右方
}
```
##### 广度优先搜索BFS

###### 思路

广度优先搜索：借助队列

- 遍历整个数组
- 如果一个位置为 1，则将其加入队列，开始进行广度优先搜索
- 在广度优先搜索的过程中，每个搜索到的 1 都会被重新标记为 0
- 直到队列为空，搜索结束
- 最终岛屿的数量就是进行广度优先搜索的次数

算法时间复杂度：O(MN)

算法空间复杂度：O(min(M,N))	M：二维网格的行数，N：二维网格的列数

###### 代码

```java
public static int numIslandsByBFS(char[][] grid) {
    if (grid == null || grid.length == 0) {
        return 0;
    }
    int islandsNum = 0;         //岛屿数量
    int row = grid.length;      //行数
    int col = grid[0].length;   //列数
    //遍历
    for (int r = 0; r < row; r++) {
        for (int c = 0; c < col; c++) {
            if (grid[r][c] == '1') {    //陆地
                grid[r][c] = '0';   //重新标记
                islandsNum++;
                Deque<Integer> queue = new LinkedList<>();
                queue.add(r * col + c);         //入队
                while (!queue.isEmpty()) {      //队列不为空
                    int id = queue.remove();    //出队
                    //当前元素的位置
                    int cr = id / col;    //当前元素的行
                    int cc = id % col;    //当前元素的列
                    if (cr - 1 >= 0 && grid[cr - 1][cc] == '1') {   //正上方
                        queue.add((cr - 1) * col + cc);
                        grid[cr - 1][cc] = '0'; //重新标记
                    }
                    if (cr + 1 < row && grid[cr + 1][cc] == '1') { //正下方
                        queue.add((cr + 1) * col + cc);
                        grid[cr + 1][cc] = '0'; //重新标记
                    }
                    if (cc - 1 >= 0 && grid[cr][cc - 1] == '1') {   //正左方
                        queue.add(cr * col + cc - 1);
                        grid[cr][cc - 1] = '0'; //重新标记
                    }
                    if (cc + 1 < col && grid[cr][cc + 1] == '1') { //正右方
                        queue.add(cr * col + cc + 1);
                        grid[cr][cc + 1] = '0'; //重新标记
                    }
                }
            }
        }
    }
    return islandsNum;
}
```
