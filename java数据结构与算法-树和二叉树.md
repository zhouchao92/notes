---
title: java数据结构与算法--树和二叉树
tags:
  - 二叉树
categories:
  - 数据结构与算法
abbrlink: 49f58aa9
date: 2020-04-10 21:30:09
---

#### 树的基本概念

##### 树的定义

树是由一个集合以及在该集合上定义的一种关系构成的，集合中的元素称为树的结点，所定义的关系称为父子关系。

- 父子关系在书的结点之间建立了一个层次结构
- 树的结点包含一个数据元素及若干指向其子树的若干分支
- 特殊位置的结点，树的根结点，简称为树根

树是n（n≥0）个结点的有限集

- n=0	空树，不包含任何结点
- n>0    非空树，有且仅有一个特定的称为*根*（root）的结点

<!--more-->

##### 结点的度与树的度

结点拥有的子树的数目称为结点的度（Degree）

- 度为0的结点称为叶子（leaf）或终端结点
- 度不为0的结点称为非终端节点或分支结点，除根之外的分支结点也称为内部结点
- 树内各结点的度的最大值称为树的度

![结点的度.png](https://i.loli.net/2020/04/10/tnWjOpQkV2KIChH.png)

##### 结点的层次与树的深度

**结点的层次**从根开始定义，层次数为1的结点是根节点，其子树的根的层次数为2

树中的结点的最大层次数为**树的深度或高度**

![结点的层次数.png](https://i.loli.net/2020/04/10/hwtWHY5pPCSuQe7.png)

##### 父亲  儿子 兄弟

**父亲**：一个结点的直接前驱结点

**儿子**：一个结点的直接后继结点

**兄弟**：同一个父亲结点的其他结点

##### 祖先 子孙 堂兄弟

结点的**祖先**是从根结点到该点路径上的所有结点

以某结点为根的树中的任一结点都称为该点的**子孙**

父亲在同一层次的结点互为**堂兄弟**

##### 有序树  m叉树  森林

如果将树中结点的各子树看成是从左至右是有次序的，则称该树为**有序树**

若不考虑子树的顺序则称为**无序树**

- 有序树的子树之间不能互换位置

**m叉树**：树中所有结点最大度数为m的有序树

**森林**：m(m≥0)棵互不相交的树的集合，对树中每个结点而言，其子树的集合即为森林

树和森林的概念相近

- 树→森林：删除一棵树的根，就可以得到一个森林
- 森林→树：加上一个结点作为树根，森林就变成一棵树

#### 二叉树

##### 二叉树概念

**二叉树**：每个结点的度**均不超过2**的**有序树**

二叉树的递归定义：二叉树或者是一颗空树，或是由一个根结点和两颗互不相交的分别称为根的左子树和右子树的子树所组成的非空树

- 二叉树每个结点的孩子数只能是0、1或2，并且每个孩子都有左右之分
- 位于左边的孩子称为左孩子，位于右边的孩子称为右孩子
- 以左孩子为根的子树称为左子树，以右孩子为根的子树称为右子树

![二叉树子树.png](https://i.loli.net/2020/04/10/uC8m2JexAwyDtEG.png)

##### 满二叉树

高度为k并且由2<sup>k+1</sup>-1个结点的二叉树

在满二叉树中，每层结点都达到最大数，即每层节点都是满的

##### 完全二叉树

若在一颗满二叉树中，在最下层从最右侧起去掉相邻若干的若干叶子结点，得到的二叉树即为完全二叉树

![满二叉树与完全二叉树.png](https://i.loli.net/2020/04/10/uj16T3eG4tJmoMF.png)

满二叉树必为完全二叉树，而完全二叉树不一定是满二叉树

##### 二叉树性质

- 在二叉树的第i层上最多有2<sup>i-1</sup>个结点(根是第1层)
- 高度为h的二叉树至多有2<sup>h</sup>-1个结点
- 对任何一棵二叉树T ,如果其终端结点数为n<sub>0</sub>, 度为2的结点数为n<sub>2</sub>，则n<sub>0</sub> = n<sub>2</sub>+ 1
- 有n个结点的完全二叉树的高度为 log<sub>2</sub>n + 1 ,其中 log<sub>2</sub>n 是向下取整
- 含有n≥1个结点的二叉树的高度至多为n-1 ;高度至少为 log<sub>2</sub>n + 1 ,其中 log<sub>2</sub>n是向下取整
- 如果对-棵有n个结点的完全二叉树的结点进行编号,则对任结点i( 1≤i≤n ), 有
  - 如果i=1,则结点i是二叉树的根,无双亲;如果i>1 ,则其双亲结点PARENT(i)是结点 i/2
  - 如果2i>n.则结点i无左孩子;否则其左孩子是结点2i
  - 如果2i+1>n,则结点i无右孩子;否则其右孩子是结点2i+1

##### 二叉树的存储结构

###### 顺序存储结构

对于满二叉树和完全二叉树来说，可以将其数据元素逐层存放到一组连续的存储单元中

用一维数组来实现顺序存储结构时，将二叉树中变好为i的结点存放到数组的第i个分量中

根据二叉树的性质，可以得到结点i的父节点、左右孩子节点分别存放在2i以及2i+1分量中

![二叉树的顺序存储结构.png](https://i.loli.net/2020/04/10/8PNs9rgDuYLkUXy.png)

- 满二叉树和完全二叉树才用顺序存储结构既不浪费空间，也可通过公式很快地确定结点之间的关系
- 一般二叉树：必须用"虚结点"将二叉树补成一棵满二叉树来存储（浪费空间）

###### 链式存储结构

设计不同的结点结构可构成不同的链式存储结构

- **二叉链表**结点：数据域、左孩子域、右孩子域

  `Data lChild rChild`

  ![二叉链表结点.png](https://i.loli.net/2020/04/10/MAsnYuV96kOyBrJ.png)

- **三叉链表**结点：数据域、左孩子域、父结点、右孩子域

  `Data lChild parent rChild`

  ![三叉链表结点.png](https://i.loli.net/2020/04/10/uYqsLXa2bwzUZkF.png)

#### 二叉树的实现

##### 二叉树遍历策略

遍历(Traverse)：按照某种次序访问树中的所有结点，且每个结点恰好访问一次（**非线性结构线性化**）

二叉树：根、左子树、右子树

遍历方式

- 先序/根遍历DLR：根  左子树  右子树
- 中序/根遍历LDR：左子树  根  右子树
- 后序/根遍历LRD：左子树  右子树  根

题：<u>已知一颗二叉树的后序遍历的序列为`5 4 3 7 6 2 1`，中序遍历的序列为`4 5 1 3 2 6 7`，则其先序遍历的序列是什么?</u>

解：`1  4  5  2  3  6  7`

思路：

- 根据后序可知1为根结点，则有根：1 左子树：45 右子树：2637

- 中序54 + 后序45可知 4 为根，5为右子树

- 后序3762可知 2 为根，中序3267则 367为右子树，3为2的左子树，67为2的右子树

- 中序3267可知6为根，7为右子树

  <img src="https://i.loli.net/2020/04/10/iIL6xv9DlOYd2Bn.png" alt="二叉树遍历题.png" style="zoom:80%;" />

##### 二叉树结点

```java
/**
 * 链式二叉表的结点
 */
public class Node {

    private Object value;  //值
    private Node leftChild; //左子树的引用
    private Node rightChild; //右子树的引用

    public Node(int value) {
        this.value = value;
    }

    public Node(int value, Node leftChild, Node rightChild) {
        super();
        this.value = value;
        this.leftChild = leftChild;
        this.rightChild = rightChild;
    }

    public Object getValue() {
        return value;
    }

    public void setValue(Object value) {
        this.value = value;
    }

    public Node getLeftChild() {
        return leftChild;
    }

    public void setLeftChild(Node lNode) {
        this.leftChild = lNode;
    }

    public Node getRightChild() {
        return rightChild;
    }

    public void setRightChild(Node rNode) {
        this.rightChild = rNode;
    }

}
```

##### 二叉树接口

```java
/**
* 二叉树操作接口
*/

public interface BinaryTree {

    /**
     * 是否为空树
     *
     * @return
     */
    public boolean isEmpty();

    /**
     * 树结点数量
     *
     * @return
     */
    public int size();

    /**
     * 树的高度
     *
     * @return
     */
    public int getHeight();

    /**
     * 查询指定值的结点
     *
     * @param value
     * @return
     */
    public Node findKey(Object value);

    /**
     * 前序递归遍历
     */
    public void preOrderTraverse();

    /**
     * 中序递归遍历
     */
    public void inOrderTraverse();

    /**
     * 后序递归遍历
     */
    public void postOrderTraverse();

    /**
     * 前序遍历非递归操作
     */
    public void preOrderByStack();

    /**
     * 中序遍历非递归操作
     */
    public void inOrderByStack();

    /**
     * 后序遍历非递归操作
     */
    public void postOrderByStack();

    /**
     * 按照层次遍历二叉树
     */
    public void levelOrderByStack();

}
```

##### 二叉树实现

- 递归遍历
- <font color="red">前序、中序、后序非递归遍历</font>
- <font color="blue">按层次遍历</font>

```java
/**
* 二叉链表实现
*/
public class LinkedBinaryTree implements BinaryTree {

    private Node root;  //根结点

    public LinkedBinaryTree() {
    }

    public LinkedBinaryTree(Node root) {
        super();
        this.root = root;
    }

    @Override
    public boolean isEmpty() {
        return this.root == null;
    }

    @Override
    public int size() {
        System.out.print("二叉树的结点数：");
        return this.size(root);
    }

    private int size(Node root) {
        if (root != null) {
            //左子树结点数
            int leftSize = this.size(root.getLeftChild());
            //右子树结点数
            int rightSize = this.size(root.getRightChild());
            return leftSize + rightSize + 1;
        }
        return 0;
    }

    @Override
    public int getHeight() {
        System.out.print("二叉树的高度：");
        return this.getHeight(root);
    }

    private int getHeight(Node root) {
        if (root == null) {
            return 0;
        } else {
            //左子树的高度
            int leftHeight = this.getHeight(root.getLeftChild());
            //右子树的高度
            int rightHeight = this.getHeight(root.getRightChild());
            //返回结点的高度
            return leftHeight > rightHeight ? leftHeight + 1 : rightHeight + 1;
        }
    }

    @Override
    public Node findKey(Object value) {
        return this.findKey(value, root);
    }

    private Node findKey(Object value, Node root) {
        if (root == null)
            return null;
        else if (root.getValue().equals(value))
            //是否为根节点
            return root;
        else {
            Node leftChild = this.findKey(value, root.getLeftChild());
            Node rightChild = this.findKey(value, root.getRightChild());
            if (leftChild != null && leftChild.getValue().equals(value))
                //是否为左子树
                return leftChild;
            else if (rightChild != null && rightChild.getValue().equals(value))
                //是否为右子树
                return rightChild;
            else
                return null;
        }
    }

    @Override
    public void preOrderTraverse() {
        /**
         if (this.root != null) {
         //根结点的值
         System.out.print(root.getValue() + " ");
         //对左子树进行先序遍历
         BinaryTree leftTree = new LinkedBinaryTree(root.getLeftChild());
         leftTree.preOrderTraverse();
         //对右子树进行先序遍历
         BinaryTree rightTree = new LinkedBinaryTree(root.getRightChild());
         rightTree.preOrderTraverse();
         }
         */
        System.out.println("前序遍历--递归");
        this.preOrderTraverse(this.root);
        System.out.println();	//换行符
    }

    private void preOrderTraverse(Node root) {
        if (root != null) {
            //输出结点的值
            System.out.print(root.getValue() + " ");
            //遍历左子树
            this.preOrderTraverse(root.getLeftChild());
            //遍历右子树
            this.preOrderTraverse(root.getRightChild());
        }
    }

    @Override
    public void inOrderTraverse() {
        /**
         if (this.root != null) {
         //对左子树进行中序遍历
         BinaryTree leftTree = new LinkedBinaryTree(root.getLeftChild());
         leftTree.inOrderTraverse();
         //根结点的值
         System.out.print(root.getValue() + " ");
         //对右子树进行中序遍历
         BinaryTree rightTree = new LinkedBinaryTree(root.getRightChild());
         rightTree.inOrderTraverse();
         }
         System.out.println();
         */
        System.out.println("中序遍历--递归");
        this.inOrderTraverse(this.root);
        System.out.println();	//换行符
    }

    private void inOrderTraverse(Node root) {
        if (root != null) {
            //遍历左子树
            this.inOrderTraverse(root.getLeftChild());
            //输出结点的值
            System.out.print(root.getValue() + " ");
            //遍历右子树
            this.inOrderTraverse(root.getRightChild());
        }
    }

    @Override
    public void postOrderTraverse() {
        /**
         if (this.root != null) {
         //对左子树进行后序遍历
         BinaryTree leftTree = new LinkedBinaryTree(root.getLeftChild());
         leftTree.postOrderTraverse();
         //对右子树进行后序遍历
         BinaryTree rightTree = new LinkedBinaryTree(root.getRightChild());
         rightTree.postOrderTraverse();
         //根结点的值
         System.out.print(root.getValue() + " ");
         }
         */
        System.out.println("后序遍历--递归");
        this.postOrderTraverse(this.root);
        System.out.println();	//换行符
    }

    private void postOrderTraverse(Node root) {
        if (root != null) {
            //对左子树进行后序遍历
            this.postOrderTraverse(root.getLeftChild());
            //对右子树进行后序遍历
            this.postOrderTraverse(root.getRightChild());
            //根结点的值
            System.out.print(root.getValue() + " ");
        }
    }

    @Override
    public void preOrderByStack() {
        if (root == null)
            return;
        System.out.println("前序非递归遍历");
        Deque<Node> deque = new LinkedList<>();
        Node current = root;
        while (current != null || !deque.isEmpty()) {
            while (current != null) {
                deque.push(current);
                System.out.print(current.getValue() + " ");
                current = current.getLeftChild();
            }
            if (!deque.isEmpty()) {
                current = deque.pop();
                current = current.getRightChild();
            }
        }
        System.out.println();	//换行符
    }

    @Override
    public void inOrderByStack() {
        if (root == null)
            return;
        System.out.println("中序非递归遍历");
        Deque<Node> deque = new LinkedList<>();
        Node current = root;
        while (current != null || !deque.isEmpty()) {
            while (current != null) {
                deque.push(current);    //入栈
                current = current.getLeftChild();
            }
            if (!deque.isEmpty()) {
                current = deque.pop();   //出栈
                System.out.print(current.getValue() + " ");
                current = current.getRightChild();
            }
        }
        System.out.println();	//换行符
    }

    /**
     * 用来记录最新出栈的节点，
     * 如果当前节点的右孩子与flag相同，说明当前节点右子树已完成遍历
     */
    @Override
    public void postOrderByStack() {
        if (root == null)
            return;
        System.out.println("后序非递归遍历");
        Deque<Node> deque = new LinkedList<>();
        Node current = root;
        Node flag = null;
        while (current != null) {
            //根结点及其根结点的左子树(最左侧的所有结点+根节点)...全部入栈
            deque.push(current);
            current = current.getLeftChild();
        }
        while (!deque.isEmpty()) {
            current = deque.pop();  //出栈
            if (current.getRightChild() == null || current.getRightChild() == flag) {
                System.out.print(current.getValue() + " "); //输出当前值
                flag = current;
            } else {
                deque.push(current);
                current = current.getRightChild();
                while (current != null) {
                    deque.push(current);
                    current = current.getLeftChild();
                }
            }
        }
        System.out.println();	//换行符
    }

    @Override
    public void levelOrderByQueue() {
        if (root == null)
            return;
        System.out.print("按照层次进行遍历");
        Queue<Node> queue = new LinkedList<>();
        queue.add(root);    //root入队
        while (queue.size() != 0) {
            int len = queue.size();
            for (int i = 0; i < len; i++) {
                Node temp = queue.poll();   //出队
                if (temp != null) {
                    System.out.print(temp.getValue() + " ");
                    if (temp.getLeftChild() != null)
                        queue.add(temp.getLeftChild());
                    if (temp.getRightChild() != null)
                        queue.add(temp.getRightChild());
                }
            }
        }
        System.out.println();	//换行符
    }
}
```
