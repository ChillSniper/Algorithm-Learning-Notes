# 第5、6周上机试题

## A 图的创建

分数 10
作者 朱允刚
单位 吉林大学
请编写程序创建一个有向图。有向图中包含n个顶点，编号为0至n-1

输入格式:
输入第一行为两个正整数n和e，分别表示图的顶点数和边数，其中n不超过20000，e不超过20000。接下来e行表示每条边的信息，每行为3个非负整数a、b、c，其中a和b表示该边的端点编号，c表示权值。各边并非按端点编号顺序排列。

输出格式:
按顶点编号递增顺序输出每个顶点引出的边，每个顶点占一行，若某顶点没有引出边，则不输出。每行表示一个顶点引出的所有边，格式为a:(a,b,w)……，表示有向边a->b的权值为w，a引出的多条边按编号b的递增序排列。

输入样例:

```data
7 7
0 1 5
0 3 7
0 6 6
1 2 4
2 5 1
3 5 3
6 5 4
```

输出样例:

```data
0:(0,1,5)(0,3,7)(0,6,6)
1:(1,2,4)
2:(2,5,1)
3:(3,5,3)
6:(6,5,4)
```

代码长度限制
16 KB
时间限制
500 ms
内存限制
20 MB
栈限制
8192 KB

```cpp
#include <iostream>
#include <cstdio>
#include <algorithm>
#include <cstring>
using namespace std;
//#define Test
struct Edge
{
    int to, w, Nxt;    
};
struct Info
{
    int a, b, w;
};
const int N = 2e4 + 10, M = N;
int Head[N], idx, n, m;
Info infomation[M];
Edge e[M];
bool cmp(Info x, Info y);
void AddEdge(int a, int b, int w);
int main(){
    #ifdef Test
        freopen("file.in", "r", stdin);
        freopen("file.out", "w", stdout);
    #endif
    memset(Head, -1, sizeof(Head));
    scanf("%d%d", &n, &m);
    for(int i = 0;i < m;i ++)
        scanf("%d%d%d", &infomation[i].a, &infomation[i].b, &infomation[i].w);
    sort(infomation, infomation + m, cmp);
    for(int i = 0;i < m;i ++)
        AddEdge(infomation[i].a, infomation[i].b, infomation[i].w);
    for(int i = 0;i < n;i ++){
        if(Head[i] == -1) continue;
        printf("%d:", i);
        for(int j = Head[i];j != -1;j = e[j].Nxt){
            int v = e[j].to, w = e[j].w;
            printf("(%d,%d,%d)",i, v, w);
        }
        printf("\n");
    }
    return 0;
}
bool cmp(Info x, Info y){
    if(x.a == y.a) return x.b > y.b;
    return x.a < y.a;
}
void AddEdge(int a, int b, int w){
    e[idx].to = b;
    e[idx].w = w;
    e[idx].Nxt = Head[a];
    Head[a] = idx ++;
}
```

## B 图深度优先遍历

分数 10
作者 朱允刚
单位 吉林大学
编写程序对给定的有向图（不一定连通）进行深度优先遍历，图中包含n个顶点，编号为0至n-1。本题限定在深度优先遍历过程中，如果同时出现多个待访问的顶点，则优先选择编号最小的一个进行访问，以顶点0为遍历起点。

输入格式:
输入第一行为两个整数n和e，分别表示图的顶点数和边数，其中n不超过20000，e不超过50。接下来e行表示每条边的信息，每行为两个整数a、b，表示该边的端点编号，但各边并非按端点编号顺序排列。

输出格式:
输出为一行整数，每个整数后一个空格，即该有向图的深度优先遍历结点序列。

输入样例1:

```data
3 3
0 1
1 2
0 2
```

输出样例1:

```data
0 1 2 
```

输入样例2:

```data
4 4
0 2
0 1
1 2
3 0
```

输出样例2:

```data
0 1 2 3
```

代码长度限制
16 KB
时间限制
50 ms
内存限制
64 MB
栈限制
65536 KB

```cpp
#include <iostream>
#include <cstdio>
#include <cstring>
#include <algorithm>
using namespace std;
//#define Test
const int N =  2e4 + 10, M = 5e2 + 10;
struct Edge
{
    int to, Nxt;    
};
struct Info
{
    int a, b;
};
bool cmp(Info x, Info y);
int Head[N], idx, vis[N], n, m;
Edge e[M];
Info information[M];
void Dfs(int u);
void AddEdge(int u, int v);
int main(){
    #ifdef Test
        freopen("file.in", "r", stdin);
        freopen("file.out", "w", stdout);
    #endif
    memset(Head, -1, sizeof(Head));
    scanf("%d%d", &n, &m);
    for(int i = 0;i < m;i ++)
        scanf("%d%d", &information[i].a, &information[i].b);
    sort(information, information + m, cmp);
    for(int i = 0;i < m;i ++)
        AddEdge(information[i].a, information[i].b);
    for(int i = 0;i < n;i ++)
        Dfs(i);
    return 0;
}
void Dfs(int u){
    if(vis[u] || u >= n) return ;
    vis[u] = 1;
    printf("%d ", u);
    for(int i = Head[u];i != -1;i = e[i].Nxt){
        int v = e[i].to;
        if(!vis[v]) Dfs(v);
    }
}
void AddEdge(int a, int b){
    e[idx].to = b;
    e[idx].Nxt = Head[a];
    Head[a] = idx ++;
}
bool cmp(Info x, Info y){
    if(x.a == y.a) return x.b > y.b;
    return x.a < y.a;
}
```

## C 任务排序

分数 10
作者 吉林大学
单位 吉林大学
一个工程被分解成n个子任务，编号为0至n-1。要完成整个工程需要完成所有的子任务。其中一些子任务必须先于另外一些子任务被完成。给定各子任务之间的先后关系，请编写程序给出一个合理的任务完成顺序，若工程不可行，程序亦能识别。

输入格式:
输入第一行为两个整数n和e，均不超过100。n表示子任务数。接下来e行，表示已知的两个子任务间的先后关系，每行为两个整数a和b，表示任务a必须先于任务b完成。

输出格式:
若工程不可行（一些子任务以自己为先决条件），输出“unworkable project”；若工程可行，输出为1行整数，每个整数后一个空格，为n个子任务的编号，表示子任务的完成顺序，如果有多种可能的顺序，则输出字典序最小者。

注：字典序，即对象在字典中的顺序。对于两个数字序列，从第一个数字开始比较，当某一个位置的数字不同时，该位置数字较小的序列，字典序较小，例如1 2 3 9比1 2 4 5小，1 2 8 9比1 2 10 3小。

输入样例1:

```data
3 2
0 1
1 2
```

输出样例1:

```data
0 1 2
```

输入样例2:

```data
3 3
0 1
1 2
2 0
```

输出样例2:

```data
unworkable project
```

代码长度限制
16 KB
时间限制
50 ms
内存限制
64 MB
栈限制
8192 KB

```cpp
#include <iostream>
#include <cstdio>
#include <cstring>
#include <deque>
#include <vector>
#include <algorithm>
using namespace std;
//#define Test
const int N = 1e2 + 10, M = N;
struct Edge
{
    int to, Nxt;    
};
struct Info
{
    int a, b;
};
bool cmp(Info x, Info y);
int Head[N], idx, vis[N], n, m, Indegree[N];
Edge e[M];
Info information[M];
void AddEdge(int u, int v);
int main(){
    #ifdef Test
        freopen("file.in", "r", stdin);
        freopen("file.out", "w", stdout);
    #endif
    memset(Head, -1, sizeof(Head));
    scanf("%d%d", &n, &m);
    for(int i = 0;i < m;i ++)
        scanf("%d%d", &information[i].a, &information[i].b);
    sort(information, information + m, cmp);
    for(int i = 0;i < m;i ++){
        AddEdge(information[i].a, information[i].b);
        ++ Indegree[information[i].b];
    }
    deque<int> dq;
    for(int i = 0;i < n;i ++){
        if(Indegree[i] == 0){
            dq.push_back(i);
            vis[i] = 1;
            break;
        }
    }
    if(dq.size() == 0){
        printf("unworkable project"); return 0;
    }
    vector<int> ans;
    while (!dq.empty())
    {
        int p = dq.front(); dq.pop_front();
        ans.push_back(p);
        for(int i = Head[p];i != -1;i = e[i].Nxt){
            int v = e[i].to;
            Indegree[v] --;
        }
        for(int i = 0;i < n;i ++){
            if(!vis[i] && Indegree[i] == 0){
                dq.push_back(i);
                vis[i] = 1;
                break;
            }
        }
    }
    if(ans.size() < n){
        printf("unworkable project"); return 0;
    }
    for(int i = 0;i < n;i ++){
        printf("%d ", ans[i]);
    }
    return 0;
}
void AddEdge(int a, int b){
    e[idx].to = b;
    e[idx].Nxt = Head[a];
    Head[a] = idx ++;
}
bool cmp(Info x, Info y){
    if(x.a == y.a) return x.b > y.b;
    return x.a < y.a;
}
```

## D 冬奥会接驳车

分数 10
作者 朱允刚
单位 吉林大学
2022年冬奥会科技感十足，奥组委配备了无人驾驶的接驳车。假定你是奥组委的软件工程师，请你为无人接驳车编写路径规划程序，使其载着运动员以最短的时间到达目的地。
接驳车的运行范围可以被建模为一张由n行m列单元格组成的地图。有的单元格是空地，可以走；有的单元格处是障碍物，不能走。假定接驳车只可以朝上、下、左、右四个方向行驶，不能斜着走。每行驶过一个位置（单元格）需要1分钟。给定地图以及运动员的起点和终点，请输出接驳车从起点行驶到终点所需的最短时间。

输入格式:
输入包含多组数据。每组数据第一行是两个整数n和m (1≤ m, n ≤100)，表示地图的长和宽；接下来是n行，每行m个数字，表示整个地图。空地格子用0表示，障碍物用1表示，运动员所在起点用3表示，目的地用4表示。

输出格式:
对于每组数据，如果接驳车能够到达目的地，输出一个整数，表示运动员从起点到目的地所需的最短时间，如果不能到达目的地，输出“unreachable”。

输入样例:

```data
5 5
1 0 1 1 1
1 0 4 1 0
1 0 0 1 0
0 0 0 1 0
1 0 3 0 1
5 5
3 0 1 1 1
1 0 1 1 0
1 0 1 1 0
0 0 0 1 0
1 0 1 0 4
```

输出样例:

```data
3
unreachable
```

代码长度限制
16 KB
时间限制
50 ms
内存限制
30 MB
栈限制
8192 KB

```cpp
#include <iostream>
#include <cstdio>
#include <vector>
#include <deque>
using namespace std;
//#define Test
typedef pair<int, int> pir;
int dir[4][2] = {1, 0, -1, 0, 0, 1, 0, -1};
int stx, sty, endx, endy, n, m;
void solve(vector<vector<int>>& graph, vector<vector<int>>& vis);
void bfs(vector<vector<int>>& graph, vector<vector<int>>& vis, int x, int y, vector<vector<int>>& distance);
int main(){
    #ifdef Test
        freopen("file.in", "r", stdin);
        freopen("file.out", "w", stdout);
    #endif
    while(scanf("%d%d", &n, &m) != EOF){
        vector<vector<int>> graph(n, vector<int>(m, 0));
        vector<vector<int>> vis = graph;
        for(int i = 0;i < n;i ++){
            for(int j = 0;j < m;j ++){
                scanf("%d", &graph[i][j]);
                if(graph[i][j] == 3) {stx = i; sty = j;}
                if(graph[i][j] == 4) {endx = i; endy = j;}
            }
        }
        //printf("%d %d\n", stx, sty);
        solve(graph, vis); 
    }
    return 0;
}
void solve(vector<vector<int>>& graph, vector<vector<int>>& vis){
    vector<vector<int>> distance = vis;
    bfs(graph, vis, stx, sty, distance);
    if(distance[endx][endy]) printf("%d\n", distance[endx][endy]);
    else printf("unreachable\n");
}
void bfs(vector<vector<int>>& graph, vector<vector<int>>& vis, int x, int y, vector<vector<int>>& distance){
    deque<pair<int, int>> dq;
    dq.push_back(pir{stx, sty});
    while (!dq.empty())
    {
        pir p = dq.front(); dq.pop_front();
        if(!vis[p.first][p.second]){
            vis[p.first][p.second] = 1;
            int flag = 0;
            for(int i = 0;i < 4;i ++){
                int nextx = p.first + dir[i][0];
                int nexty = p.second + dir[i][1];
                if(nextx < 0 || nextx >= n || nexty < 0 || nexty >= m) continue;
                if(nextx == endx && nexty == endy && !vis[endx][endy]){
                    distance[endx][endy] = distance[p.first][p.second] + 1;
                    vis[endx][endy] = 1;
                    flag = 1;
                    break;
                }
                if(!vis[nextx][nexty] && !graph[nextx][nexty]){
                    distance[nextx][nexty] = distance[p.first][p.second] + 1;
                    dq.push_back(pir{nextx, nexty});
                }
            }
            if(flag) break;
        }
    }
        
}
```

## E 单源最短路径

分数 10
作者 朱允刚
单位 吉林大学
请编写程序求给定正权有向图的单源最短路径长度。图中包含n个顶点，编号为0至n-1，以顶点0作为源点。

输入格式:
输入第一行为两个正整数n和e，分别表示图的顶点数和边数，其中n不超过20000，e不超过1000。接下来e行表示每条边的信息，每行为3个非负整数a、b、c，其中a和b表示该边的端点编号，c表示权值。各边并非按端点编号顺序排列。

输出格式:
输出为一行整数，为按顶点编号顺序排列的源点0到各顶点的最短路径长度（不含源点到源点），每个整数后一个空格。如源点到某顶点无最短路径，则不输出该条路径长度。

输入样例:

```data
4 4
0 1 1
0 3 1
1 3 1
2 0 1
```

输出样例:

```data
1 1 
```

代码长度限制
16 KB
时间限制
100 ms
内存限制
20 MB
栈限制
8192 KB

```cpp
#include <iostream>
#include <cstdio>
#include <cstring>
using namespace std;
//#define Test
struct Edge
{
    int to, w, Nxt;    
};
const int N = 2e4 + 10, INF = 0x3f3f3f3f, M = 1e3 + 10;
int Head[N], idx, n, m, Path[N], vis[N];
Edge e[M];
void Dijkstra();
int FindMin();
void AddEdge(int u, int v, int w);
int main(){
    #ifdef Test
        freopen("file.in", "r", stdin);
        freopen("file.out", "w", stdout);
    #endif
    memset(Head, -1, sizeof(Head));
    scanf("%d%d", &n, &m);
    for(int i = 0;i < m;i ++){
        int u, v, w;
        scanf("%d%d%d", &u, &v, &w);
        AddEdge(u, v, w);
    }
    Dijkstra();
    for(int i = 1;i < n;i ++){
        if(vis[i]) printf("%d ", Path[i]);
    }
    return 0;
}
void AddEdge(int u, int v, int w){
    e[idx].to = v;
    e[idx].w = w;
    e[idx].Nxt = Head[u];
    Head[u] = idx ++;
}
void Dijkstra(){
    for(int i = 0;i < n;i ++) Path[i] = INF;
    Path[0] = 0;
    for(int i = 0;i < n;i ++){
        int u = FindMin();
        if(u == -1) break;
        vis[u] = 1;
        for(int i = Head[u];i != -1;i = e[i].Nxt){
            int v = e[i].to;
            if(!vis[v] && Path[v] > Path[u] + e[i].w){
                Path[v] = Path[u] + e[i].w;
            }
        }
    }
}
int FindMin(){
    int Min = INF, res = -1;
    for(int i = 0;i < n;i ++){
        if(!vis[i] && Path[i] < Min){
            Min = Path[i];
            res = i;
        }
    }
    return res;
}
```
