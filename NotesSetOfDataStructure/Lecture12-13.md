# 2024年秋季 数据结构课程笔记

## Lecture 12 图的概念和存储结构

### 图的基本概念

#### 基本定义

记图$G = (V, E)$，$V$为顶点的有限非空集合，$E$为连接$V$中两个不同顶点的边的有限集合

#### 邻接顶点

在有向图中，规定$<u, v>$为从点$u$指向$v$的边
也就是：
点$u$邻接到点$v$，点$v$**邻接自**点$u$

#### 完全图

>对于$n$个顶点的完全图，边的条数$e = O(n^2)$

#### 顶点的度

在有向图中，顶点的度包含**出度**和**入度**

#### 顶点的度和边的关系

记图$G$中顶点数为$n$，边数为$e$，满足$$\sum_i D(v_i) = 2\times e$$

#### 图的路径

简单路径：除了首尾点可以重复，其他点没有重复点
简单回路：首尾点重复，且路径长度$
\ge 2$

#### 一些回路的定义

欧拉回路：每条边经过一次的回路
哈密顿回路：每个顶点经过一次的回路

（当然以上不含首尾重复点）

#### 子图

支撑子图：$E(H)\subseteq E(G)$，且$V(H)=V(G)$

#### 连通性

图$G$中顶点$v_i$到顶点$v_j$存在路径，则称$v_i$和$v_j$可及（**连通**）

在**有向图**中，如果对于图$G$中任意两互异点$v_i$和$v_j$，都有相互的路径，那么称图$G$是**强连通图**

结论：
>对于有$n$个顶点的图$G$，当边数$\displaystyle \ge C_{n-1}^{2}  + 1 = \frac{(n-2)\times (n -1)}{2} + 1$，可以确保该无向图是连通图

对于无向图$G = (V, E)$，当$|V| > |E| + 1$时，$G$一定不连通
相应解释：$|E| + 1$实际上是在已经连起来的一条边集（没有首尾相连）上的点的统计，然后$|V| > |E| + 1$，即另外至少还有一个孤立点，这样$|G|$一定不连通

#### 连通子图

一个图的连通子图**不一定**唯一

注意子图中，亦可以$V(H) \subseteq V(G)$

#### 连通分量

无向图的连通分量即$G$的**极大**连通子图

注意：
>一个无向图的连通分量不一定唯一（因为本来那个无向图可能是非联通的，由几个子连通图构成）

#### 强连通子图和强连通分量

注意一个有向图的强连通分量不一定唯一

#### 带权图

实际上是给每一条边$l$给一个值$w(l)$，也就是$w(u, v)$
规定$w(u, u) = 0$，如果$u, v$间不存在边，则规定$w(u, v) = \infty$

#### 带权路径

规定一条路径上所有权值之和为路径的**长度**

### 图的存储结构

#### 邻接矩阵

顺序存储，实际上是存储边的存在关系
对于无权图，$a_{ij} = 0$没边，反之有边
对于有权图，$a_{ij} = 0$没边，反之存储边的权值

注意：无向图的邻接矩阵是对称矩阵

关于$p43$页的算法设计 待解决

邻接矩阵存储为稀疏矩阵，浪费空间较多

#### 邻接表

由边链表和顶点表组合构成

图的存储结构如下

```cpp
struct Vertex{
    int VerName;
    Edge* adjacent;
};
```

```cpp
struct Edge{
    int VerAdj;
    int cost;
    Edge* link;
};
```

邻接表空间复杂度$O(n + e)$，其中$e$为边节点的个数

#### 邻接表中统计节点的度

根据邻接表可以很方便的统计每个顶点的出度

统计节点入度算法相应代码如下

```cpp
void GetInDegree(Vertex Head[], int n, int InDegree[]){
    for(int i = 0;i < n;i ++) InDegree[i] = 0;
    for(int i = 0;i < n;i ++){
        for(Edge* p = Head[i].adjacent;p;p = p->link){
            InDegree[p->VerAdj] ++;
        }
    }
}
```

相应时间复杂度为$O(n + e)$

邻接矩阵适合稠密图，常用于查改删边
邻接表适合稀疏图，常用于寻找邻接顶点

## Lecture 13 图的遍历及应用

### 深度优先搜索

#### 图的遍历

恰访问每个顶点一次

注意：
>**图的深度优先遍历不唯一**

由图的深度优先遍历可以得到**深度优先搜索树**

对于图的DFS遍历，算法解释：对于每个点存储的为*struct Vertex*，而相应的，对于这样的每个点，有一个边链表*struct Edge*存储
然后在对每个点连接的边链表，遍历边链表上的每个点$v_i$，再逐次跳转到$v_i$对应的边链表上

算法对应代码如下

```cpp
void DFS(Vertex head[], int n, int visited[]){
    vis(n);
    visited[n] = 1;
    Edge* p = head[n].adjacent;
    while (p)
    {
        int k = p->VerAdj;
        if(!visited[k]){
            DFS(head, k, visited);
        }
        p = p->link;
    }
}
```

时间复杂度为$O(n + e)$

关于非连通图，注意需要多次调用$DFS$算法遍历多个连通分量

图的深度优先遍历的非递归算法
使用**栈结构**存储未遍历节点
算法相应代码如下

```cpp
void DFS(Vertex* head, int k, int visited[]){
    stack<int> st;
    st.push(k);
    while (!st.empty())
    {
        k = st.top(); st.pop();
        vis(k); visited[k] = 1;
        for(Edge* p = head[k].adjacent;p;p = p->link){
            if(!visited[p->VerAdj])
                st.push(p->VerAdj);
        }
    }
}
```

### 广度优先搜索

层次推进搜索
同时可以形成**广度优先搜索树**

算法对应代码如下

```cpp
void BFS(Vertex* head, int k, int n, int visited[]){
    deque<int> dq;
    for(int i = 0;i < n;i ++) visited[i] = 0;
    vis(k); visited[k] = 1;
    dq.push_back(k);
    while (!dq.empty())
    {
        k = dq.front(); dq.pop_front();
        for(Edge* p = head[k].adjacent;p;p = p->link){
            if(!visited[p->VerAdj]){
                vis(p->VerAdj);
                visited[p->VerAdj] = 0;
                dq.push_back(p->VerAdj);
            }
        }
    }
}
```

时间复杂度为$O(n + e)$

相应的，还有一种用邻接矩阵存图的算法，对应时间复杂度为$O(n ^ 2)$

### 图遍历的应用

#### 判定无向图是否连通以及连通分量的数目

这个题是收费的...

#### 判定两点u,v间是否存在路径

现在的问题是对于节点之间关系的储存形式，如何转换成节点的链接表

三种方法：深搜，广搜，并查集

##### 深搜

深搜有点像二叉树操作中的前序遍历，注意要调用$visited[]$数组保存节点访问状态
相应算法代码如下

```cpp
class Solution {
public:
    bool validPath(int n, vector<vector<int>>& edges, int source, int destination) {
        vector<list<int>> graph(n);
        vector<int> visited(n, 0);
        for(auto edge:edges){
            graph[edge[0]].push_back(edge[1]); graph[edge[1]].push_back(edge[0]);
        }
        return Dfs(graph, visited, source, destination);
    }
    bool Dfs(vector<list<int>>& graph, vector<int>& visited, int source, int destination){
        visited[source] = 1;
        if(source == destination) return true;
        for(auto p:graph[source]){
            if(!visited[p] && Dfs(graph, visited, p, destination)) return true;
        }
        return false;
    }
};
```

##### 广搜

广搜类似于二叉树操作中的层次遍历。同样的，需要使用$visited[]$数组保存节点访问状态
相应算法对应代码如下

```cpp
class Solution {
public:
    bool validPath(int n, vector<vector<int>>& edges, int source, int destination) {
        vector<list<int>> graph(n);
        vector<int> visited(n, 0);
        for(auto edge:edges){
            graph[edge[0]].push_back(edge[1]); graph[edge[1]].push_back(edge[0]);
        }
        queue<int> q;
        q.push(source);
        visited[source] = 1;
        while (!q.empty())
        {
            int k = q.front(); q.pop();
            if(k == destination) return true;
            for(auto i:graph[k]){
                if(!visited[i]) q.push(i), visited[i] = 1;
            }
        }
        return false;
    }
};
```

有一点需要注意的是，在对于图的两点路径存在问题的深搜和广搜里面，似乎没有用到回溯。（因为节点被访问了就是访问了，说明此路不通，而不是统计数目）

##### 并查集

实际上考查了在一个连通分量中的节点也在同一个集合中，然后使用并查集相应的操作
相应算法对应代码如下

```cpp
class Union{
public:
    void MakeSet(int n){
        father = vector<int>(n, 0);
        rank = vector<int>(n, 0);
        for(int i = 0;i < n;i ++) father[i] = i;
    }
    int FindFather(int x){
        if(x != father[x]){
            father[x] = FindFather(father[x]);
        }
        return father[x];
    }
    void UnionTwoSet(int x, int y){
        int fx = FindFather(x), fy = FindFather(y);
        if(fx == fy) return;
        if(rank[fx] > rank[fy]){
            father[fy] = fx;
        } else{
            father[fx] = fy;
            if(rank[fx] == rank[fy]) rank[fy] ++;
        }
    }
    bool JudgeTwoPoint(int x, int y){
        return FindFather(x) == FindFather(y);
    }
private:
    vector<int> father;
    vector<int> rank;
};
class Solution {
public:
    bool validPath(int n, vector<vector<int>>& edges, int source, int destination) {
        Union u;
        u.MakeSet(n);
        for(auto edge:edges){
            u.UnionTwoSet(edge[0], edge[1]);
        }
        return u.JudgeTwoPoint(source, destination);
    }
};
```

那个期末考试题怎么说呢，加了很多限定条件
这有点像那个统计所有点u到点v路径的题，只不过此处换成了统计长度为$L$

#### 判定无向图中是否有环

环图找不到相近难度的对应题...

##### 深度优先搜索解决环的判定

相应算法代码如下

```cpp
bool DfsForNoDirectionGraphRingJudge(Vertex* head, int* visited, int u, int pre){
    visited[u] = 1;
    for(Edge* p = head[u].adjacent;p;p = p->link){
        if(visited[p->VerAdj] && pre != p->VerAdj) return true;
        if(!visited[p->VerAdj]){
            pre = u;
            DfsForNoDirectionGraphRingJudge(head, visited, p->VerAdj, pre);
        }
    }
    return false;
}
```

#### 判定有向图中是否有环

实际上将点的访问记录值$visited[]$分为三种情况
$visited[i] = \begin{cases}
    0\quad 尚未访问过 \\
    1\quad 在当前访问分支中已经被访问，但是该节点的出度还没被访问完全\\
    2\quad 当前节点的所有出度已经访问完全
\end{cases}$

相应算法对应代码如下

```cpp
bool HasCircleOfDirectedGraph(Vertex* head, int visited[], int u){
    visited[u] = 1;
    for(Edge* p = head[u].adjacent;p;p = p->link){
        if(!visited[p->VerAdj]){
            visited[p->VerAdj] = 1;
            HasCircleOfDirectedGraph(head, visited, p->VerAdj);
        } else if(visited[p->VerAdj] == 1) return true;
    }
    visited[u] = 2;
    return false;
}
```
