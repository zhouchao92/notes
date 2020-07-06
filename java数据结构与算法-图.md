---
title: java数据结构与算法--图
tags:
  - 图
categories:
  - java
  - 数据结构与算法
  - notes
abbrlink: c88bf7ec
date: 2020-04-11 21:59:01
---

## 图

#### 图的定义

###### 图的基本概念：多对多关系

图(`graph`)是一种网状数据结构，图是由非空的顶点集合和一个描述之间关系的集合组成

形式化定义：

- Graph = (V,E)  

- V={x | x∈某个数据对象}  

- E={<u,v> | P(u,v)^(u,v∈V)}

  V是具有相同特性的数据元素的集合，V中数据元素通常称为顶点(Vertex)

  E是两个顶点之间的集合，P(u,v)表示u和v之间有特定的关联属性

- 若<u,v>∈E，则<u,v>表示从顶点u到顶点v的一条弧，并称u为弧尾或起始点，称v为弧头或终止点，此时图的顶点之间的连线是有方向的，称之为**有向图**(`directed graph`)
- 若<u,v>∈E，则必有<v,u>∈E，即关系是对称的，此时可以使用一个无序对(u,v)来代替两个有序对，它表示顶点u和顶点v之间的一条边，此时图中顶点之间的连线是没有方向的，称之为**无向图**(`undirected graph`)

<!--more-->

在无向图和有向图中V的元素都称为顶点，而顶点之间的关系却又不同的称谓，即**弧**或**边**，在不影响理解的前提下，统称为**边**(`edge`)

顶点集与边集都是有限的，并记顶点与边的数量为|V|和|E|

![有向图与无向图.png](https://i.loli.net/2020/04/11/gLHlX3bun8zMKOc.png)

无向图实际上也是有向图，是双向图

---

带权图：图的每条边都与它相关的实数，称之为权，这些权值可以并表示一个顶点到另一个顶点的距离或消耗等信息，这种边上具有权值的图称为**带权图**(`weighted graph`)

![加权图.png](https://i.loli.net/2020/04/11/IVHT4lDUem3WJnC.png)

#### 图的存储

###### 邻接矩阵

二维数组（顺序存储结构），无向图关于对角线对称

![图的存储-邻接矩阵.png](https://i.loli.net/2020/04/11/gfWwLFYU1rIuy9d.png)

###### 邻接表

链表（链式存储结构）

![图的存储-邻接表.png](https://i.loli.net/2020/04/11/PGmLUVzS4YaNoTk.png)

#### 图的遍历

图的遍历：从图中某个顶点出发，按某种方法对图中所有顶点访问且仅访问一次

图的遍历算法是求解图的连通性问题、拓扑排序和求关键路径等算法的基础

- **深度优先排序**(DFS depth-first search)：类似于树的先根排序，是树的先根遍历的推广（可采用递归和借助栈的非递归方式实现）
- **广度优先排序**(BFS breadth-first search)：类似于树的层次遍历，是树的按层次遍历的推广（借助队列的非递归方式实现）

![图的遍历-图例.png](https://i.loli.net/2020/04/11/CAftQBO9Rzvrlaq.png)

- 无向图的深度优先遍历：0->1->3->7->4->2->5->6
- 无向图的广度优先遍历：0->1->2->3->4->5->6->7

![图的遍历-图例2.png](https://i.loli.net/2020/04/11/eU6MfF7P9EnOpAu.png)

- 无向图的深度优先遍历：A->B->E->F->C->D->G->H->I
- 无向图的广度优先遍历：A->B->C->D->E->F->G->H->I

#### 最短路径

###### 最短路径概念

带权图两点之间的最小消耗路径

- **段数最少的最短路径**：类似于树的层次遍历，需要借助队列来实现（广度优先搜索）

  算法：对于已经检查过结点，标记为已检查，且不再检查它，可以使用另一个列表存放已经检查过的结点，找到即为可达，第一次找到，即为跳转最少，如果到最后队列为空，则表明没有路径可以到达

- **权值最小的最短路径**：权值之和最小，狄克斯特拉算法

  狄克斯特拉算法是从一个顶点到其余各顶点的最短路径算法，解决的是有权图中最短路径问题。迪杰斯特拉算法主要特点是从起始点开始，采用贪心算法的策略，每次遍历到始点距离最近且未访问过的顶点的邻接节点，直到扩展到终点为止。

#### 狄克斯特拉算法

###### 思路

该算法复杂度为$n^2$,我们可以发现，如果边数远小于$n^2$,对此可以考虑用堆这种数据结构进行优化，取出最短路径的复杂度降为O(1)；每次调整的复杂度降为$O（e log_2 n）$；e为该点的边数，所以复杂度降为$O((m+n)log_2 n)$。

###### 实现

- 将源点加入堆，并调整堆
- 选出堆顶元素u（即代价最小的元素），从堆中删除，并对堆进行调整
- 处理与u相邻的，未被访问过的，满足三角不等式的顶点
- 若该点在堆里，更新距离，并调整该元素在堆中的位置
- 若该点不在堆里，加入堆，更新堆
- 若取到的u为终点，结束算法；否则重复步骤2、3

```java
//假设起点为src, 终点为dst, 图以二维矩阵的形式存储，若graph[i][j] == 0, 代表i,j不相连    
//visit[i] == 0,代表未访问,visit[0] == -1代表已访问    
public int Dijkstra(int src, int dst, int[][] graph,int[] visit){
    //节点个数        
    int n = graph.length;        
    PriorityQueue<Node> pq = new PriorityQueue<>(new Node());        
    //将起点加入pq        
    pq.add(new Node(src, 0));        
    while (!pq.isEmpty()){            
        Node t = pq.poll();            
        //当前节点是终点，即可返回最短路径            
        if(t.node == dst)                
            return t.cost;            
        //t节点表示还未访问            
        if (visit[t.node]==0){                
            //将节点设置为已访问                
            visit[t.node] = -1;                
            //将当前节点相连且未访问的节点遍历                
            for (int i = 0; i < n; i++) {                    
                if (graph[t.node][i]!=0 && visit[i]==0) {                        
                    pq.add(new Node(i, t.cost + graph[t.node][i]));                    
                }                
            }            
        }        
    }        
    return -1;    
}    
//定义一个存储节点和离起点相应距离的数据结构    
class Node implements Comparator<Node> {        
    public int node;        
    public int cost;
             
    public Node(){}
     
    public Node(int node, int cost){            
        this.node = node;            
        this.cost = cost;        
    }        
    @Override        
    public int compare(Node node1, Node node2){            
        return node1.cost-node2.cost;       
    }    
}
```