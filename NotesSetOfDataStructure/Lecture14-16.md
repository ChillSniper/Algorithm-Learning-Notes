# 2024年秋季 数据结构课程笔记

## Lecture 14 拓扑排序和关键路径

### 拓扑排序

#### 动机

子任务的有序完成顺序

#### $AOV$网

即**Activity On Vertex Network**
如果活动$V_i$必须在活动$V_j$之前进行，则存在有向边$V_i \to V_j$

不难发现，$AOV$网中**不能出现有向回路**

#### 拓扑序列

将$AOV$网中所有顶点排成一个线性序列

构成$AOV$网的拓扑序列的过程被称为**拓扑排序**

注意：
如果能通过拓扑排序将有向图中所有顶点排入一个拓扑序列中，则该有向图必定无环。否则，则该有向图存在环

#### 拓扑排序判断有向图中是否有环

拓扑排序使用栈来记录来记录**入度**为0的节点

相应算法代码如下

```cpp
bool TopoOrder(Vertex head[], int n){
    int InDegree[n] = {0};
    stack<int> st;
    GetInDegree(head, n, InDegree);
    for(int i = 0;i < n;i ++)
        if(!InDegree[i]) st.push(i);
    for(int i = 0;i < n;i ++){
        if(st.empty()) return false;
        int k = st.top(); st.pop();
        for(Edge* p = head[k].Adjacent;p;p = p->link){
            InDegree[p->VerAdj] --;
            if(!InDegree[p->VerAdj]) st.push(p->VerAdj);
        }
    }
    return false;
}
```

#### 深度优先遍历实现拓扑排序

深度优先搜索可以输出拓扑的逆序
...虽然不知道这个有什么用

### 关键路径

#### $AOE$网

即***Activity On Edges***
边上的权值表示活动的持续时间，顶点称为事件

在$AOE$网络中，**只有各条路径上所有活动都完成，整个工程才算完成**

完成整个工程所需的最短时间取决于从**源点**到**汇点**的最长路径长度（各边权值之和）

这条路径长度被称为**关键路径**

#### 计算所需

事件$v_j$的最早发生事件$ve(j)$，最迟发生时间$vl(j)$
活动$a_i$的最早开始时间$e(i)$，活动$a_i$的最迟开始时间$l(i)$

##### 计算$ve(j)$

需要知道已知顶点$v_j$的所有前驱顶点的最早发生时间
由此，我们需要先对$AOE$网进行拓扑排序，然后按照拓扑序递推

注意这边一个很重要的细节！！！ 就是说顶点$j$的最早开始时间要建立在所有它的前置节点都完成的基础上！（这是拓扑排序的内核）
所以这也就是为什么要求取$max(ve[j], ve[i] + cost(i\to j))$

##### 计算$vl(j)$

对于$vl(j)$的计算，需要建立在保证汇点的最早发生时间不推迟的前提下

也就是说，时间$v_j$允许的最晚开始时间，等于**ve(n)减去$v_j$到$v_n$的最长路径长度**

##### 计算活动$a_i$的最早开始时间$e(i)$

记活动$a_i$在有向边$v_j\to v_k$上，则$e(i) = ve(j)$

##### 计算活动$a_i$的最迟开始时间$l(i)$

记活动$a_i$在有向边$<v_j, v_k>$上，那么$l(i) = vl(k) - weight(j, k)$

#### 关键路径和关键活动的联系

关键路径即从源点到汇点的最长路径
关键活动即关键路径上的活动，并且注意$l(i) = e(i)$

#### 求解关键活动的步骤

首先对$AOE$网络进行拓扑排序，按照顶点的拓扑序求各顶点的最早发生事件$ve{(j)}$
然后根据顶点的**逆拓扑序**求解各顶点$v_j$的最晚发生事件$vl(j)$
根据各个顶点的$ve$和$vl$值，求出各活动的最早开始时间$e(i)$和最晚开始时间$l(i)$
如果$e(i) = l(i)$，那么$a_i$是**关键活动**

关键路径算法相应代码如下

```cpp
void GetIndegree(Vertex head[], int n, int Indegree[]){
    for(int i = 1;i <= n;i ++) Indegree[i] = 0;
    for(int i = 1;i <= n;i ++){
        for(Edge* p = head[i].Adjacent;p;p = p->link){
            int k = p->VerAdj; Indegree[k] ++;
        }
    }
}
void CriticalPath(Vertex head[], int n){
    int Ve[N] = {0}, Vl[N] = {0}, Indegree[N] = {0}, Topo[N];
    GetIndegree(head, n, Indegree);
    TopoSort(head, Topo, Indegree, n);
    VertexEarliestTime(head, n, Topo, Ve);
    VertexLatestTime(head, n, Topo, Ve, Vl);
    vector<int> ans = ActivityStartTime(head, n, Topo, Ve, Vl);
}

void TopoSort(Vertex head[], int Topo[], int Indegree[], int n){
    stack<int> st;
    int TopoIndex = 0;
    for(int i = 1;i <= n;i ++)
        if(!Indegree[i]) st.push(i);
    for(int i = 1;i <= n;i ++){
        if(st.empty()) return;
        int k = st.top(); st.pop();
        Topo[TopoIndex ++] = k;
        for(Edge* p = head[k].Adjacent;p;p = p->link){
            Indegree[p->VerAdj] --;
            if(!Indegree[p->VerAdj]) st.push(p->VerAdj);
        }
    }
}

void VertexEarliestTime(Vertex head[], int n, int Topo[], int ve[]){
    for(int i = 1;i <= n;i ++) ve[i] = 0;
    // 此处求取各顶点最早发生时间要按照拓扑排序的顶点顺序！！！
    for(int i = 1;i <= n;i ++){
        int k = Topo[i];
        for(Edge* p = head[k].Adjacent;p;p = p->link){
            ve[p->VerAdj] = max(ve[p->VerAdj], ve[k] + p->cost);
        }
    }
}

void VertexLatestTime(Vertex head[], int n, int Topo[], int ve[], int vl[]){
    for(int i = 1;i <= n;i ++) vl[i] = ve[Topo[n]];
    for(int i = n;i >= 1;i --){
        int k = Topo[i];
        for(Edge* p = head[k].Adjacent;p;p = p->link){
            vl[p->VerAdj] = min(vl[p->VerAdj], vl[k] - p->cost);
        }
    }
}

vector<int> ActivityStartTime(Vertex head[], int n, int Topo[], int ve[], int vl[]){
    vector<int> ans;
    ans.push_back(Topo[1]);
    for(int i = 1;i <= n;i ++){
        int k = Topo[i];
        for(Edge* p = head[k].Adjacent;p;p = p->link){
            if(vl[p->VerAdj] - p->cost == ve[k]) ans.push_back(p->VerAdj);
        }
    }
    return ans;
}
```

该算法步骤较多，注意对求取顶点最晚发生时间步骤的理解

那个***课下思考***我觉得是为了确定关键路径，而且在仅仅知道$ve[]$的情况下，如何确定最长路径呢？

## Lecture 15 最短路径

### 无权图最短路径

采用广度优先搜索，使用一个队列，来逐层遍历节点

但是这边有个问题，如何确保不被重复遍历？采用了记录路径初始值解决
相应算法对应代码如下

```cpp
const int MAX = 0x3f3f3f3f;

void BfsForShortestPath(Vertex* head, int u, int n, int pre[], int distance[]){
    deque<int> dq;
    // pay attention to the preparation
    for(int i = 1;i <= n;i ++){
        pre[i] = -1; distance[i] = MAX;
    }
    distance[u] = 0;
    dq.push_back(u);
    while (!dq.empty())
    {
        int k = dq.front(); dq.pop_front();
        for(Edge* p = head[k].Adjacent;p;p = p->link){
            if(distance[p->VerAdj] == MAX){
                pre[p->VerAdj] = k;
                distance[p->VerAdj] = distance[k] + 1;
                dq.push_back(p->VerAdj);
            }
        }
    }
    
}
```

### $Dijkstra$算法

使用广度优先搜索并不能解决问题，因为$bfs$仅仅解决了经过的边的个数的问题

#### 算法基本思路

原始顶点集合为$V$
**集合$S$**包含已经确定最短路径的节点
按照和源点**最短路径递增顺序**的原则逐个将顶点放入$S$

注意一个细节，初始时刻$S$为空，**并不包含源点$u$**

首先是选取不在集合$S$中的路径长度$D_v$最小的顶点$v$放入集合$S$
然后对$v$更新所有不在集合$S$中的邻接点$w$，更新$D_w$的最短距离长度

如果说采用邻接表存图，但是这边有个问题，这边的权值怎么存进去呢
如果采用邻接表，就得自己写结构体去存，那还不如用**邻接矩阵**存

$Dijkstra$算法代码如下

```cpp
class Solution{
public:
    void DijkstraAlgorithm(vector<vector<int>>& graph, int n, int u, int pre[], int dis[]){
        int s[N] = {0};
        for(int i = 1;i <= n;i ++){pre[i] = -1; dis[i] = MaxDistance;}
        dis[u] = 0;
        for(int i = 1;i <= n;i ++){
            int v = FindMinVertex(graph, s, dis, n);
            s[v] = 1;
            for(auto j:graph[v])
                if(!s[j] && dis[v] + graph[v][j] < dis[j]){
                    dis[j] = dis[v] + graph[v][j];
                    pre[j] = v;
                }
        }
    }
private:
    int FindMinVertex(vector<vector<int>>& graph, int s[], int dis[], int n){
        int res, MinDistance = MaxDistance;
        for(int i = 1;i <= n;i ++){
            if(!s[i] && dis[i] < MinDistance){
                MinDistance = dis[i];
                res = i;
            }
        }
        return res;
    }
    const int N = 1e5 + 10;
    const int MaxDistance = 0x3f3f3f3f;
};
```

时间复杂度为$O(n^2 + e)$

#### 关于$Dijkstra$算法的总结

有向图、无向图均适用

可以用来求源点$s$到某个顶点$t$的**最短路径**

理论上使用**斐波那契堆**，时间复杂度可以降至$O(nlogn + e)$
***后面去看一下这个斐波那契堆是什么玩意***

$Dijkstra$算法无法处理**负权图**

而对于负权图的单源最短路径问题，可以采用$Bellman-Ford$算法解决
时间复杂度为$O(n\times e)$
后面去看一下这个算法！！！

#### $Dijstra$算法的堆优化版本

时间复杂度降至$O(nlogn + e)$
算法对应代码如下

```cpp
struct Edge
{
    int to;
    int nxt;
    int weight;
};

typedef long long ll;

struct Priority
{
    int id;
    ll ans;
    bool operator <(const Priority& x)const{
        return x.ans < this->ans;
    }
};

const int N = 1e5 + 10, M = 2 * N;
int idx, Head[N], n, m, vis[N];
Edge edge[M];
ll path[N];

void AddEdge(int x, int y, int w){
    edge[idx].weight = w;
    edge[idx].to = y;
    edge[idx].nxt = Head[x];
    Head[x] = idx ++;
}

void Dijkstra(int u){
    memset(vis, 0, sizeof(vis));
    priority_queue<Priority> p;
    for(int i = 1;i <= n;i ++) path[i] = INT_MAX;
    path[u] = 0;
    p.push(Priority{u, path[u]});
    while (!p.empty())
    {
        Priority tmp = p.top(); p.pop();
        int w = tmp.id;
        if(!vis[w]){
            vis[w] = 1;
            for(int i = Head[w];i != -1;i = edge[i].nxt){
                int v = edge[i].to;
                if(path[v] > path[w] + edge[i].weight){
                    path[v] = path[w] + edge[i].weight;
                }
                if(!vis[v]){
                    p.push(Priority{v, path[v]});
                }
            }
        }
    }
    for(int i = 1;i <= n;i ++)
        printf("%d ", path[i]);
    puts("");
}
```

#### 对于$Dijkstra$算法的深入理解

那三个课后思考值得仔细研究

##### 负权图增加权值问题

对于负权图最短路径问题，不可以加上一个值，然后求取最短路。（详见$OI$ $Wiki$上面的反例！）

##### 求取图最长路径问题

这个按理说不行，因为如果贪心的求取最长边值，但是扩展的已遍历节点是按最短边值确定的
至于将边权值取倒数，也是错的，因为贪心算法没法取到所有的边。

##### 最短路与关键路径算法的比较

这个后面再说，关键路径算法需要复习

### $A*$算法

用于解决正权图**单源单目标点**最短路径问题

### Floyd算法

用于求取任意两点间最短路径问题

算法步骤理解

实际上，将图中的顶点$v_1, v_2, \dots v_n$分成集合$I_0, I_1, \dots I_n$，然后满足$D_k$是$v_i$经过顶点集合$I_k$中的点到达$v_j$的最短路径长度

然后寻求$D_{k - 1}$到$D_k$之间的**递推**关系

最终得到递推公式$D^k{[i][j]} = min\{D^{k - 1}{[i][j]}, D^{k - 1}{[i][k] + D^{k - 1}[k][j]}\}$

对应算法代码如下

```cpp
void Floyd(vector<vector<int>>& graph, vector<vector<int>>& D, vector<vector<int>>& path){
    int n = graph.size() - 1;
    // Init();
    for(int i = 1;i <= n;i ++){
        for(int j = 1;j <= n;j ++){
            D[i][j] = graph[i][j];
            if(i != j && graph[i][j]) path[i][j] = j;
            else path[i][j] = -1;
        }
    }
    // dynamic planning
    for(int k = 1;k <= n;k ++){
        for(int i = 1;i <= n;i ++){
            for(int j = 1;j <= n;j ++){
                if(D[i][k] + D[k][j] < D[i][j]){
                    D[i][j] = D[i][k] + D[k][j];
                    path[i][j] = path[i][k];
                }
            }
        }
    }
}
void PrintPath(vector<vector<int>>& path, int a, int b){
    for(int k = a;k != b;k = path[k][b]){
        printf("%d", path[k][b]);
    }
}
```

时间复杂度为$O(n ^ 3)$，相比$n$次调用$Dijkstra$算法，实现更为简便
**更重要的是，$Floyd$算法可以实现负权边！！！**

#### 有向图的传递闭包问题

传递闭包：由有向图$G$的顶点集$V$，边集$E$，以及表示两点间是否可及的信息构成的图（邻接矩阵）称为$G$的传递闭包

构造传递闭包的算法：$Warshall$算法

关键递推公式
$R^k[i][j] = R^{k - 1}[i][j] \quad OR\quad (R^{k - 1}[i][k]\quad AND\quad R^{k - 1}[k][j])$

算法对应代码如下

```cpp
void WarShall(vector<vector<int>>& graph, int n, vector<vector<int>>& R){
    for(int i = 1;i <= n;i ++){
        for(int j = 1;j <= n;j ++){
            R[i][j] = graph[i][j];
        }
    }
    for(int k = 1;k <= n;k ++){
        for(int i = 1;i <= n;i ++){
            for(int j = 1;j <= n;j ++){
                R[i][j] = R[i][j] || (R[i][k] && R[k][j]);
            }
        }
    }
}
```

### 满足约束的最短路径

实际上用于解决任意两点间第$K$短路径问题

解决方案

#### $Yen$算法

实际上是对最短路径中的逐个点进行偏离，然后对各条偏离路径进行选取最小值

时间复杂度为$O(kn^3)$

#### $Dijkstra$算法改进

关于求取第$k$短的路径，采用用优先队列维护的$Dijkstra$算法。
遗留问题，后面解决

## Lecture 16 最小支撑树

### 最小支撑树定义

也就是最小生成树，满足边权和最小

注意：**最小支撑树中无环**

重要性质：
**最小跨集合边**一定在某棵最小支撑树中

### $Prim$算法

算法步骤：不断选取权值最小的跨集合边，并将已知选取的跨集合边的顶点放入已知集合中，直到已知集合为顶点集合$V$

算法对应代码如下

```cpp
int Prim(int G[N][N], int n){
    int Pre[N], vis[N] = {0}, dis[N], ans = 0;
    for(int i = 1;i <= n;i ++){
        Pre[i] = -1;
        dis[i] = INF;
    }
    dis[1] = 0;
    for(int i = 1;i <= n;i ++){
        int u = FindMin(vis, dis, n);
        // pay attention to the serious situation
        if(u == -1) return ans;
        // Operation here
        vis[u] = 1;
        ans += dis[u];
        for(int j = 1;j <= n;j ++){
            if(!vis[j] && G[u][j] < dis[j]){
                dis[j] = G[u][j];
                Pre[j] = u;
            }
        }
    }
    return ans;
}
int FindMin(int* vis, int* dis, int n){
    int Min = INF, res = -1;
    for(int i = 1;i <= n;i ++){
        if(!vis[i] && dis[i] < Min){
            Min = dis[i];
            res = i;
        }
    }
    return res;
}
```

时间复杂度为$O(n^2)$

### $Kruskal$算法

**逐边加入**
算法解释：不断减少连通分量时加入边，并且使得支撑树的构造过程中不产生环

有关算法的实现

注意$Kruskal$算法是对**边**的操作！！！

算法对应代码如下

```cpp
int KruskalAlgorithm(Edge* E[], int n, int e){
    Unit(n);
    sort(E, E + e, cmp);
    int ans = 0, k = 0;
    for(int i = 0;i < e;i ++){
        int u = E[i]->head, v = E[i]->tail, w = E[i]->weight;
        if(FindFather(u) != FindFather(v)){
            k ++;
            ans += w;
            // Related Operation
            // ...
            Union(u, v);
        }
        if(k  == n - 1) return ans;
    }
    return -1;
}
```

实际上，$Kruskal$算法是不断寻求最小跨集合边的过程
时间复杂度为$O(eloge)$，其中$e$为边数

#### 关于最小生成树的思考

对于图$G$中的最小边，由$Kruskal$算法，图$G$必然包含最小边
很显然，也包含第二小的边

但是，不一定包含第三小的边，可能第三小的边恰在图中与已知最小的两个边的顶点构成一个环

**如果图$G$中边权互异，最小支撑树唯一**

若图中有权值相同的边，最小支撑树不一定不唯一（这个问题有点怪，如果这两条权值相等的边没有共同交点，那也没什么联系啊...）

$Prim$算法更适合**稠密图**，$Kruskal$算法更适合**稀疏图**

#### 最小生成树的拓展问题

必须包含某些特定边的最小支撑树
最大支撑树
最小乘积支撑树

这三个问题后续解决

**增量最小支撑树**：在无向带权连通图中加入一条新边$e$，并且顶点集不变。寻找新图$G + e$的最小支撑树。
解法：加入边$e$后会导致最小支撑树$T$产生环，由此，需要去除环中的最大边，然后与$e$的权值进行比较，决定是否删去这条边并且加入$e$

#### 最小生成树的应用：聚类

执行$Kruskal$算法直至剩下$k$个连通分量，也就是$k$组

### 强连通分量

即**Strongly Connected Components**

注意极端情况下，一个单独的点可以作为一个连通分量

求取强连通分量的朴素算法
中间需要先求取传递闭包
时间复杂度为$O(n^3)$

后续待解决
$Tarjan$算法
$Kosaraju$算法
求取强连通分量
