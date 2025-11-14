# 2024年秋季 数据结构课程笔记

## Lecture 17 平方阶排序算法

### 排序概念

对**排序稳定性**解释：
对于两个对象$R_i$和$R_j$，其关键词相同。若排序前后，$R_i$和$R_j$的先后顺序不变，则该排序算法稳定。反之，该排序算法不稳定。

#### 平方阶算法

即指平均时间复杂度为$O(n^2)$的算法

### 直接插入排序

直接插入排序算法对应代码如下

```cpp
void InsertionSort(vector<int>& arr){
    for(int i = 1;i < N;i ++){
        int j = i - 1, tmp = arr[i];
        while (j >= 0 && arr[j] > tmp)
        {
            arr[j + 1] = arr[j];
            j --;   
        }
        arr[j + 1] = tmp;
    }
}
```

注意：***直接插入排序算法是稳定的！***

### 冒泡排序

时间复杂度为$O(n^2)$
注意：**冒泡排序为稳定的排序算法**

之所以稳定，是因为当前后两个元素相等时，并不发生交换

改进后的冒泡排序算法对应代码如下

```cpp
void BubbleSort(vector<int>& a){
    int n = a.size(), bound = n - 1;
    while (bound > 0)
    {
        int t = 0;
        for(int i = 0;i < bound;i ++){
            if(a[i] > a[i + 1]){
                swap(a[i], a[i + 1]);
                t = i;
            }
        }
        bound = t;
    }
}
```

### 直接选择排序

不断选取未确定序列中最大的元素，放置到未确定序列的末尾，同时未确定序列不断减小。

时间复杂度$O(n^2)$
注意：直接选择排序无论选取相等最大元素的顺序如何，排序都是**不稳定**的。

直接选择排序算法对应代码如下

```cpp
void SelectionSort(vector<int>& arr){
    int n = arr.size();
    for(int i = n - 1;i >= 0;i --){
        int t = 0;
        for(int j = 0;j <= i;j ++)
            if(arr[j] > arr[t]){t = j;}
        swap(arr[i], arr[t]);
    }
}
```

### 希尔排序

实际上是将元素分成$d$组，每组元素间隔分布

注意：希尔排序是一种**不稳定**的排序

在增量序列为逐次减半时，时间复杂度为$O(n ^2)$

算法对应代码如下

```cpp
void ShellSort(vector<int>& arr){
    for(int d = N / 2;d > 0;d /= 2){
        for(int i = 0;i < N;i ++){
            int j = i - d, tmp = arr[i];
            while (j >= 0 && arr[j] > tmp)
            {
                arr[j + d] = arr[j];
                j -= d;
            }
            arr[j + d] = tmp;
        }
    }
}
```

## Lecture 18 堆排序

### 锦标赛排序

利用**胜者树**

时间复杂度$O(nlogn)$

### 堆的定义以及基本操作

$Heap$

最大堆(大根堆)：注意是**完全二叉树**， 任意结点的关键字$\ge$其两个孩子的关键字

#### 堆的上浮

对当前节点和父节点进行比较，直到当前节点$\le$父节点为止

#### 堆的下沉

对当前子节点中最大值进行比较

#### 初始建堆

初始建堆时间复杂度为$O(n)$

$P56$页选择为
$D$
实际上是$P55$计算的变式

将$n$个元素建成一个堆，初始建堆算法最坏时间复杂度为$O(n)$

注意初始建堆算法是把数组中前一半的元素不断往树的下层比较并且下移（元素下标从$1$开始）

### 堆排序算法

首先注意到一个问题，当使用初始建堆算法后，元素**并不是严格按照递减顺序排列的**！！！

关于堆排序算法的叙述：实际上是将堆顶元素不断与当前堆尾元素交换，直到当前堆大小为1为止。

算法时间复杂度$O(nlogn)$，为**不稳定**的排序！

### 堆和优先队列

堆的删除操作：实际上是将当前要删除的元素和堆尾元素交换，堆大小减一，然后把原本移动的那个堆尾元素进行上浮和下沉操作。

#### 优先级队列 ***Priority Queue***

**C++ STL**中的优先队列默认为大根堆，可以修改为小根堆

#### $d$堆

实则将完全二叉树修改为完全$d$叉树

### **Top K**问题

可以先用前$k$个元素构建一个小根堆，然后对数组后续的元素不断与当前堆顶元素比较替换
时间复杂度可以优化至$O(nlogk)$

对于动态$TOP$ $K$问题，可以维护一个小根堆和一个大根堆（实际上是一个对顶堆）

## Lecture 19 快速排序

### **Quick Sort Algorithm**

实际上是分治的策略，将数组不断划分成左右两半，左边小于$k$, 右边大于$k$

快速排序算法对应代码如下

```cpp
void QuickSort(vector<int>& arr, int m, int n){
    if(m < n){
        int mid = Partition(arr, m, n);
        QuickSort(arr, m, mid - 1);
        QuickSort(arr, mid + 1, n);
    }
}
int Partition(vector<int>& arr, int m, int n){
    int K = arr[m], L = m + 1, R = n;
    while (L <= R)
    {
        while (arr[L] <= K && L <= n){L ++;}
        while (arr[R] > K && R >= m){R --;}
        if(L < R) {swap(arr[L], arr[R]); L ++; R --;}
    }
    swap(arr[R], arr[m]);
    return R;
}
```

### 时间复杂度分析

分析计算过程跳过

时间复杂度为$O(nlogn)$
注意**Quick Sort**是**不稳定**的排序算法

### 关于快速排序的优化策略

实际上是对基准比较元素的选取优化问题

1. 处理小数据时采用插入排序（此时插入排序更快）既可以判断数组长度，选择使用插入排序。也可以当数组长度小于一个阈值时，搁置对数组的操作。最后进行一个总的插入排序
2. 对基准元素随机选取
3. 三数取中，选取一个中间值

实际上，这些优化策略围绕两点，基准元素选取的优化和递归的减少

### 关于三路划分-荷兰国旗问题

此处思路较为混乱，实际上是维护三个指针进行操作，时间复杂度可以降至$O(n)$

算法对应代码如下

```cpp
void sortColors(vector<int>& nums) {
        int n = nums.size();
        int i = 0, j = 0, k = n - 1;
        while (j <= k) // 注意此处的特判条件
        {
            if(nums[j] == 0){
                swap(nums[j], nums[i]);
                i ++; j ++; // 其实此处对于为什么j ++就很奇怪
                // 注意到i, j在这种划分算法下是同步进行的过程
            } else if(nums[j] == 2){
                swap(nums[j], nums[k]);
                k --;
            } else{
                j ++;
            }
        }
    }
```

#### 快速排序和堆排序的比较

注意排序时间还联系到内存的访问，由于快速排序实际上倾向于访问物理内存相邻的数据，因此$Cache$命中率更高，速度更快

所以实际上在矩阵乘法时，可以利用内存的连续性，编写访问速度更快的代码

### 快速选择算法

但实际上这个算法在**Top K**问题中没有堆好。

根据写下来的经验，还是用堆排序算法比较合适

## Lecture 20 归并排序和比较排序下界

### 归并排序

时间复杂度$O(nlogn)$， 空间复杂度$O(n)$

算法对应代码如下

```cpp
void MergeSort(vector<int>& arr, int l, int r){
    if(l >= r) return ;
    int mid = l + (r - l) / 2;
    MergeSort(arr, l, mid);
    MergeSort(arr, mid + 1, r);
    Merge(arr, l, mid, r);
}
void Merge(vector<int>& arr, int l, int mid, int r){
    vector<int> tmp;
    int i = l, j = mid + 1;
    for(;i <= mid && j <= r;){
        if(arr[i] <= arr[j]){
            tmp.push_back(arr[i]);
            i ++;
        }
        else if(arr[j] < arr[i]){
            tmp.push_back(arr[j]);
            j ++;
        }
    }
    while(i <= mid){
        tmp.push_back(arr[i]); i ++;
    }
    while (j <= r){
        tmp.push_back(arr[j]); j ++;
    }
    for(int idx = 1;idx <= r - l + 1;idx ++){
        arr[idx + l - 1] = tmp[idx - 1]; 
    }
}
```

注意归并排序是**稳定**的算法，并且是**最快的稳定性排序算法**

对单链表进行排序，最合适的排序算法是**归并排序**

### 基于关键词比较的排序算法

**稳定**的：直接插入排序、冒泡排序、归并排序
**不稳定**的：直接选择排序、堆排序、快速排序

$P33$选择$A、D$
实际上就是选择稳定的排序算法

$P34$选择$C$
注意希尔排序是**不稳定**的

$P35$选择$A$
注意：每一次排序后至少能确定一个元素位置的排序算法有
**直接选择排序、冒泡排序、堆排序、快速排序**

$P36$选择$C$
因为直接插入排序可能会不断右移元素

### 排序算法时间下界

基于比较的排序算法时间复杂度下界为$\large \Omega(nlog_2n)$

同时，关键词的平均比较次数至少为$\large\Omega(nlog_2{n})$

### 外排序方法

当待排序文件大于内存时，应当采取**归并排序**

## Lecture 21 分布排序

### 桶排序

注意桶排序是**稳定**的排序算法，时间复杂度为$O(n + m)$

### 计数排序

计数排序时间复杂度为$O(n + m)$，是**稳定**的排序算法

$P40$页依次选择$B、A$

### 基数排序

注意基数排序是**稳定**的排序，时间复杂度为$O(d(n + r))$
