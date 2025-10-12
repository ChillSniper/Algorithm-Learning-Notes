# 第二次上机编程作业

## A 二叉树的创建与遍历

分数 10
作者 朱允刚
单位 吉林大学
通过带空指针信息的先根序列（亦称先序序列）创建二叉树，并进行先根（先序）、中根（中序）、后根（后序）遍历。二叉树结点数据域值为不等于0的整数（可能是正数也可能是负数），空指针用0表示，例如1 5 8 0 0 0 6 0 0表示如下图的二叉树。

![alt text](image-10.png)

输入格式:
输入为一组用空格间隔的整数，表示带空指针信息的二叉树先根序列。其中空指针信息用0表示。二叉树结点个数不超过150000，高度不超过6000。输入数据保证二叉树各结点数据值互不相等。

输出格式:
输出为3行整数，每个整数后一个空格。第1行为该二叉树的先根序列，第2行为中根序列，第3行为后根序列。

输入样例:

```data
1 5 8 0 0 0 6 0 0
```

输出样例:

```data
1 5 8 6 
8 5 1 6 
8 5 6 1
```

代码长度限制
16 KB
时间限制
200 ms
内存限制
20 MB
栈限制
8192 KB

题解

```cpp
#include <iostream>
#include <stack>
#include <cstdio>
using namespace std;
//#define Test
struct TreeNode{
    int val;
    TreeNode* Left, *Right;
    TreeNode(): val(0), Left(nullptr), Right(nullptr){}
    TreeNode(int x): val(x), Left(nullptr), Right(nullptr){}
    TreeNode(int x, TreeNode* l, TreeNode* r): val(x), Left(l), Right(r){}
};
TreeNode* BuildTree();
void PrintPre(TreeNode* p);
void PrintIn(TreeNode* p);
void PrintPost(TreeNode* p);
int main(){
    #ifdef Test
    freopen("file.in", "r", stdin);
    freopen("file.out", "w", stdout);
    #endif
    TreeNode* root = BuildTree();
    PrintPre(root);
    puts("");
    PrintIn(root);
    puts("");
    PrintPost(root);
    puts("");
    return 0;
}
TreeNode* BuildTree(){
    int tmp;
    scanf("%d", &tmp);
    if(!tmp) return nullptr;
    TreeNode* p = new TreeNode(tmp);
    p->Left = BuildTree();
    p->Right = BuildTree();
    return p;
}
void PrintPre(TreeNode* p){
    if(!p) return;
    printf("%d ", p->val);
    PrintPre(p->Left);
    PrintPre(p->Right);
}
void PrintIn(TreeNode* p){
    if(!p) return;
    PrintIn(p->Left);
    printf("%d ", p->val);
    PrintIn(p->Right);
}
void PrintPost(TreeNode* p){
    if(!p) return;
    PrintPost(p->Left);
    PrintPost(p->Right);
    printf("%d ", p->val);
}
```

## B 二叉树查找结点及父结点

分数 10
作者 朱允刚
单位 吉林大学
编写程序在二叉树中查找给定结点及父结点。二叉树结点的数据域值不等于0的整数。

输入格式:
输入第1行为一组用空格间隔的整数，表示带空指针信息的二叉树先根序列，其中空指针用0表示。例如1 5 8 0 0 0 6 0 0表示如下图的二叉树。第2行为整数m，表示查询个数。接下来m行，每行为一个不等于0的整数K，表示要查找的结点的数据值。m不超过100，二叉树结点个数不超过150000，高度不超过6000。输入数据保证二叉树各结点数据值互不相等。

![alt text](image-11.png)

输出格式:
输出为m行，每行1个整数，表示被查找结点K的父结点数据值，若二叉树中无结点K或结点K无父结点，则输出0。

输入样例:

```data
1 5 8 0 0 0 6 0 0
3
8
1
6
```

输出样例:

```data
5
0
1
```

代码长度限制
16 KB
时间限制
300 ms
内存限制
20 MB
栈限制
131000 KB

```cpp
#include <iostream>
#include <cstdio>
#include <unordered_map>
using namespace std;
//#define Test
struct TreeNode{
    int val;
    TreeNode* Left, *Right;
    TreeNode(): val(0), Left(nullptr), Right(nullptr){}
    TreeNode(int x): val(x), Left(nullptr), Right(nullptr){}
    TreeNode(int x, TreeNode* l, TreeNode* r): val(x), Left(l), Right(r){}
};
TreeNode* BuildTree();
unordered_map<int, int> Father;
int main(){
    #ifdef Test
    freopen("file.in", "r", stdin);
    freopen("file.out", "w", stdout);
    #endif
    TreeNode* root = BuildTree();
    int m;
    scanf("%d", &m);
    while (m --)
    {
        int x;
        scanf("%d", &x);
        if(Father.count(x)) printf("%d\n", Father[x]);
        else printf("0\n");
    }
    
    return 0;
}
TreeNode* BuildTree(){
    int tmp;
    scanf("%d", &tmp);
    if(!tmp) return nullptr;
    TreeNode* p = new TreeNode(tmp);
    p->Left = BuildTree();
    if(p->Left) Father[p->Left->val] = p->val;
    p->Right = BuildTree();
    if(p->Right) Father[p->Right->val] = p->val;
    return p;
}
```
