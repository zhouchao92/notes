---
title: 'LeetCode--#199二叉树的右视图'
tags:
  - leetcode
abbrlink: 60a087a9
date: 2020-04-22 12:08:07
---


###### 二叉树的右视图

*给定一棵二叉树，想象自己站在它的右侧，按照从顶部到底部的顺序，返回从右侧所能看到的节点值。*

> ```shell
> 示例:
> 
> 输入: [1,2,3,null,5,null,4]
> 
> 输出: [1, 3, 4]
>  
> 解释:
> 
>     1              <---
>   /   \
> -  2 -  3          <---
>     \    \
>    -  5 -  4       <---
> ```

> 来源：力扣（LeetCode）
>
> 链接：[https://leetcode-cn.com/problems/binary-tree-right-side-view](https://leetcode-cn.com/problems/binary-tree-right-side-view)

<!--more-->

###### 解法--二叉树按层次遍历

按照二叉树的按层次遍历的方式（也可理解为深度优先遍历DFS），但每一层只输出最右边的结点，借助队列来实现

**代码**

```java
//结点定义
public class TreeNode {
    int val;	    //值
    TreeNode left;	//左子树
    TreeNode right;	//右子树

    TreeNode(int x) {
        val = x;
    }
}

public List<Integer> rightSideView(TreeNode root) {
        List<Integer> list = new ArrayList<>();
        if (root == null) {
            return list;
        }
        Queue<TreeNode> queue = new LinkedList<>();
        queue.add(root);    //root入队
        while (!queue.isEmpty()) {
            int len = queue.size();
            while (--len >= 0) {	//循环出队
                TreeNode temp = queue.poll();	//队首出队
                if (temp != null) {
                    if (len == 0)	//当前是否为队尾元素
                        list.add(temp.val); 	//最右侧结点的值添加到列表中
                    if (temp.left != null)
                        queue.add(temp.left);	//左子树入队
                    if (temp.right != null)
                        queue.add(temp.right);	//右子树入队
                }
            }
        }
        return list;
    }
}
```

###### 解法--二叉树先序遍历

对二叉树的先序遍历进行修改

- 遍历顺序为 根结点 右子树 左子树 ，并且只输出最右侧的结点，
- 用变量`level`记录当前层数，只有`level`等于列表的长度时才加入列表中(`level`从`0`开始)

**代码**

```java
/**
 * 二叉树先序遍历法
 */
public List<Integer> rightSideViewByPre(TreeNode root) {
    List<Integer> res = new ArrayList<>();	//列表用于存储结果
    dfs(root, 0, res);	//递归
    return res;
}

/**
 * 遍历顺序：根结点 右子树 左子树
 * @param root  根节点
 * @param level 当前层数
 * @param res   列表
 */
private void dfs(TreeNode root, int level, List<Integer> res) {
    if (root == null)	//递归结束条件
        return;
    if (level == res.size())
        res.add(root.val);	//加入到列表中
    
    dfs(root.right, level + 1, res);    //先遍历右子树
    dfs(root.left, level + 1, res);
}
```