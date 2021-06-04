---
title: Acwing刷题记录
author: 不二
mathjax: true
date: 2021-03-23 02:35:22
tags: 
- Cpp
img: https://cdn.jsdelivr.net/gh/weiyouwozuiku/weiyouwozuiku.github.io@src/source/_posts/PageImg/algorithm2.jpg
categories: 算法
---

## 前言

本文记录本人刷Acwing过程的收获和代码。语言选择C++。

## 1～1000

### 1～500

#### 1～100

[19. 二叉树的下一个节点](https://www.acwing.com/problem/content/31/)

这题主要想法就是判断当前节点是否拥有右节点。如果拥有右节点，则中序遍历的后一个节点为右节点子树的最左的节点。即，通过右节点循环找到子树的最左的节点；如果没有右节点，则中序遍历的后一个节点为该节点父节点的拐点。即上一次子树的根节点。

```cpp
class Solution {
public:
    TreeNode* inorderSuccessor(TreeNode* p) {
        if(!p) return nullptr;
        //有右节点的情况
        if(p->right){
            p=p->right;
            while(p->left)
                p=p->left;
            return p;
        }else{
            //没有右节点的情况
            while(p->father&&p->father->right==p) p=p->father;
            return p->father;
        }
    }
};
```

[26. 二进制中1的个数](https://www.acwing.com/problem/content/25/)

```cpp
class Solution {
public:
    int NumberOf1(int n) {
        int res=0;
        while(n) n-=n&-n,res+=1;
        return res;
    }
};
```



[33. 链表中倒数第k个节点](https://www.acwing.com/problem/content/32/)

本题解题分为两步：

1. 计算当前链表长度
2. 通过n-k循环找到返回的节点

```cpp
class Solution {
public:
    ListNode* findKthToTail(ListNode* pListHead, int k) {
        if(!pListHead) return NULL;
        int len=0;
        for(auto p=pListHead;p;p=p->next) len+=1;
        if(k>len)return NULL;
        else{
            auto p=pListHead;
            for(int i=0;i<len-k;i++) p=p->next;
            return p;
        }
    }
};
```

[35. 反转链表](https://www.acwing.com/problem/content/33/)

此题有两个版本，一个迭代，一个递归。

迭代版：

```cpp
class Solution {
public:
    ListNode* reverseList(ListNode* head) {
        //如果没有节点或者只有一个节点
        if(head==NULL||head->next==NULL) return head;
        auto current=head;
        ListNode* pre=NULL;
        while(current){
            auto next=current->next;
            current->next=pre;
            pre=current;
            current=next;
        }
        return pre;
    }
};
```

递归版：

```cpp

```

[77.翻转单词顺序](https://www.acwing.com/problem/content/73/)

本题分为二步：

1. 首先将整个字符串进行翻转
2. 将每个字母进行翻转。此处用双指针算法，设置j获取当前字母长度，进行翻转

```cpp
class Solution {
public:
    string reverseWords(string s) {
        reverse(s.begin(),s.end());
        for(int i=0;i<s.size();i++){
            int j=i+1;
            while(s[j]!=' '&&j<s.size()) j++;
            reverse(s.begin()+i,s.begin()+j);
            i=j;
        }
        return s;
    }
};
```



#### 101~200

[154.滑动窗口](https://www.acwing.com/problem/content/156/)

本题利用了结果本身的单调性。因为滑动窗口的长度限制，所以使用单调队列而非单调栈。当新元素入队的时候，元素从队尾移除。当新入队元素小于(或大于)等于队尾元素时，将相应元素从队尾出队。生成的单调队列具有单调性，其最值即为队尾或队首元素。

```cpp
#include <iostream>
using namespace std;
const int N = 1e6 + 10;
//q数组负责记录提供的数据，s数组负责记录q数组对应元素的索引
int q[N], s[N];
//hh表示单调队列的头部，tt表示单调队列的尾部
int hh = 0, tt = -1;

int main() {
    int n, k;
    scanf("%d%d", &n, &k);
    for (int i = 0; i < n; ++i) scanf("%d", &q[i]);
    for (int i = 0; i < n; ++i) {
        //检查队头是否出队列
        if (hh <= tt && i - k + 1 > s[hh]) ++hh;
        //检查单调队列队尾元素是否大于等于新入队元素
        while (hh <= tt && q[s[tt]] >= q[i]) --tt;
        //新元素入队
        s[++tt] = i;
        if (i >= k - 1) printf("%d ", q[s[hh]]);
    }
    printf("\n");
    //重新初始化
    hh = 0, tt = -1;
    for (int i = 0; i < n; ++i) {
        //检查队头是否出队列
        if (hh <= tt && i - k + 1 > s[hh]) ++hh;
        //检查单调队列队尾元素是否小于等于新入队元素
        while (hh <= tt && q[s[tt]] <= q[i]) --tt;
        //新元素入队
        s[++tt] = i;
        if (i >= k - 1) printf("%d ", q[s[hh]]);
    }
    return 0;
}
```



[197. 阶乘分解](https://www.acwing.com/problem/content/description/199/)

质数定理：不超过x的质数的个数近似为$\frac{x}{ln(x)}$。

底数为10时，对数可简写为$lg(x)$。

底数为e时，对数可简写为$ln(x)$。

#### 201~300

#### 301~400

#### 401~500

### 501～1000



#### 501~600

#### 601~700

#### 701~800

[785. 快速排序](https://www.acwing.com/problem/content/787/)

```cpp
#include <iostream>

using namespace std;
const int N = 1e6 + 10;
int q[N];

void quicksort(int q[], int l, int r) {
    if (l >= r) return;
    int x = q[l + r >> 1];
    int i = l - 1, j = r + 1;
    while (i < j) {
        do i++; while (q[i] < x);
        do j--; while (q[j] > x);
        if (i<j) swap(q[i],q[j]);
    }
    quicksort(q,l,j);
    quicksort(q,j+1,r);
}

int main() {
    int n;
    scanf("%d", &n);
    for (int i = 0; i < n; i++) scanf("%d", &q[i]);
    quicksort(q, 0, n - 1);
    for (int i = 0; i < n; i++) printf("%d ", q[i]);
    return 0;
}
```

[787. 归并排序](https://www.acwing.com/problem/content/789/)

```cpp
#include <iostream>

using namespace std;
const int N = 1e6 + 10;
int q[N], tmp[N];

void merge_sort(int q[], int l, int r) {
    if (l >= r) return;
    int mid = (l + r) >> 1;
    merge_sort(q,l, mid), merge_sort(q,mid + 1, r);
    int i = l, j = mid + 1, k = 0;
    while (i <= mid && j <= r) {
        if (q[i] <= q[j]) tmp[k++] = q[i++];
        else tmp[k++] = q[j++];
    }
    while (i <= mid) tmp[k++] = q[i++];
    while (j <= r) tmp[k++] = q[j++];
    for(i=l,j=0;i<=r;i++,j++) q[i]= tmp[j];
}

int main() {
    int n;
    scanf("%d", &n);
    for (int i = 0; i < n; i++) scanf("%d ", &q[i]);
    merge_sort(q, 0, n - 1);
    for (int i = 0; i < n; i++) printf("%d", q[i]);
    return 0;
}
```

[789. 数的范围](https://www.acwing.com/problem/content/791/)

```cpp
#include < iostream >

const int N = 10010;

int n, m;
int q[N];

int main() {
    scanf("%d %d", &n, &m);
    for (int i = 0; i < n; ++i) {
        scanf("%d", &q[i]);
    }
    while (m--) {
        int x;
        scanf("%d", &x);
        int l = 0, r = n - 1;
        while (l < r) {
            int mid = l + r >> 1;
            if (q[mid] >= x) {
                r = mid;
            } else {
                l = mid + 1;
            }
        }
        if (q[l] != x) {
            std::cout << "-1 -1" << std::endl;
        } else {
            std::cout << l << " ";
            int l = 0, r = n - 1;
            while (l < r) {
                int mid = l + r + 1 >> 1;
                if (q[mid] <= x) {
                    l = mid;
                } else {
                    r = mid - 1;
                }
            }
            std::cout << l << std::endl;
        }
    }
    return 0;
}
```

[790. 数的三次方根](https://www.acwing.com/problem/content/792/)

本题需要主要三次方的时候，当数字是0.001之类的小于1的数时，寻找范围需要扩大。

```cpp
#include <iostream>

using namespace std;

int main() {
    double x;
    scanf("%lf", &x);
    if (x < 0) {
        printf("-");
        x = -x;
    }
    double l = 0, r = x, mid;
    if (x<1) r=1;
    while (r - l >= 1e-8) {
        mid = (r + l) / 2;
        if (mid * mid * mid > x) r = mid;
        else l = mid;
    }
    printf("%lf", mid);
    return 0;
}
```

[791. 高精度加法](https://www.acwing.com/problem/content/793/)

这里的选择使用数组存储大整数，这里第0位存个位数，最高位放在数组最后面。这样当发生进位的时候，容易处理。

```cpp
#include <iostream>
#include <vector>

using namespace std;
const int N = 16 + 10;

vector<int> add(vector<int> &A, vector<int> &B) {
    vector<int> C;
    int t = 0;
    for (int i = 0; i < A.size() || i < B.size(); i++) {
        if (i < A.size()) t += A[i];
        if (i < B.size()) t += B[i];
        C.push_back(t % 10);
        t /= 10;
    }
    if (t) C.push_back(t);
    return C;
}

int main() {
    vector<int> A, B;
    string a, b;
    cin >> a >> b;
    for (int i = a.size() - 1; i >= 0; i--) A.push_back(a[i] - '0');
    for (int i = b.size() - 1; i >= 0; --i) B.push_back(b[i] - '0');
    auto c = add(A, B);
    for (int i = c.size() - 1; i >= 0; --i) printf("%d", c[i]);
    return 0;
}
```

[792. 高精度减法](https://www.acwing.com/problem/content/794/)

记得去掉结果中多余的0.

```cpp
#include <iostream>
#include <vector>

using namespace std;

vector<int> sub(vector<int> &A, vector<int> &B) {
    vector<int> C;
    int t = 0;
    for (int i = 0; i < A.size(); i++) {
        t = A[i] - t;
        if (i < B.size()) t -= B[i];
        C.push_back((t + 10) % 10);
        if (t < 0) t = 1;
        else t = 0;
    }
    while (C.size() > 1 && C.back() == 0) C.pop_back();
    return C;
}

//A>=B
bool cmp(vector<int> &A, vector<int> &B) {
    if (A.size() != B.size()) return A.size() > B.size();
    else {
        for (int i = A.size() - 1; i >= 0; --i) {
            if (A[i] != B[i]) return A[i] > B[i];
        }
    }
    return true;
}

int main() {
    string a, b;
    cin >> a >> b;
    vector<int> A, B;
    for (int i = a.size() - 1; i >= 0; i--) A.push_back(a[i]-'0');
    for (int i = b.size() - 1; i >= 0; i--) B.push_back(b[i]-'0');
    if (cmp(A, B)) {
        auto C = sub(A, B);
        for (int i = C.size() - 1; i >= 0; i--) printf("%d", C[i]);
    } else {
        auto C = sub(B, A);
        printf("-");
        for (int i = C.size() - 1; i >= 0; i--) printf("%d", C[i]);
    }
    return 0;
}
```

[793. 高精度乘法](https://www.acwing.com/problem/content/795/)

```cpp
#include <iostream>
#include <vector>

using namespace std;

vector<int> mul(vector<int> &A, int B) {
    vector<int> C;
    int t = 0;
    for (int i = 0; i < A.size() || t; i++) {
        if (i < A.size()) t += A[i] * B;
        C.push_back(t % 10);
        t /= 10;
    }
    while (C.size() > 1 && C.back() == 0) C.pop_back();
    return C;
}

int main() {
    string a;
    vector<int> A;
    int b;
    cin >> a >> b;
    for (int i = a.size() - 1; i >= 0; --i) A.push_back(a[i] - '0');
    auto C = mul(A, b);
    for (int i = C.size() - 1; i >= 0; i--) printf("%d", C[i]);
    return 0;
}
```

[794. 高精度除法](https://www.acwing.com/problem/content/796/)

除法这里需要注意，运算从高位开始处理。所以需要反过来进行处理。

```cpp
#include <iostream>
#include <vector>
#include <algorithm>

using namespace std;

vector<int> div(vector<int> &A, int b, int &r) {
    vector<int> C;
    for (int i = A.size() - 1; i >= 0; i--) {
        r = r * 10 + A[i];
        C.push_back(r / b);
        r %= b;
    }
    reverse(C.begin(), C.end());
    while (C.size() > 1 && C.back() == 0) C.pop_back();
    return C;
}

int main() {
    string a;
    int b;
    vector<int> A;
    cin >> a >> b;
    for (int i = a.size() - 1; i >= 0; i--) A.push_back(a[i] - '0');
    //r为余数
    int r = 0;
    auto C = div(A, b, r);
    for (int i = C.size() - 1; i >= 0; i--) printf("%d", C[i]);
    printf("\n%d", r);
    return 0;
}
```

[795. 前缀和](https://www.acwing.com/problem/content/797/)

本题使用的是一维前缀和。注意这里的s数组存放前缀和，相减的时候需要将左边界-1。s[0],a[0]处放0，以便后续操作。

```cpp
#include <iostream>

using namespace std;
const int N = 1e6 + 10;
int q[N];
int s[N];

int main() {
    int n, m;
    s[0] = 0;
    scanf("%d%d", &n, &m);
    for (int i = 1; i <= n; ++i) {
        scanf("%d", &q[i]);
        s[i] = s[i - 1] + q[i];
    }
    while (m--) {
        int l, r;
        scanf("%d%d", &l, &r);
        printf("%d\n", s[r] - s[l - 1]);
    }
    return 0;
}
```

[796. 子矩阵的和](https://www.acwing.com/problem/content/798/)

本题依旧是前缀和，不过是二维前缀和，重点就两个公式。此处假设S为前缀和数组，q为相应的差分数组。

更新前缀和数组：$S[x-1,y]+S[x,y-1]-S[x-1,y-1]+q[x,y]$

计算前缀和之差：$S[x_2,y_2]-S[x_1-1,y_2]-S[x_2,y_1-1]+S[x_1-1,y_1-1]$

```cpp
#include <iostream>

using namespace std;
const int N = 1010;
int x[N][N], s[N][N];

int main() {
    int n, m, q;
    scanf("%d%d%d", &n, &m, &q);
    for (int i = 1; i <= n; ++i) {
        for (int j = 1; j <= m; ++j) {
            scanf("%d", &x[i][j]);
            s[i][j] = s[i][j - 1] + s[i - 1][j] - s[i - 1][j - 1] + x[i][j];
        }
    }
    int x1, y1, x2, y2;
    while (q--) {
        scanf("%d%d%d%d", &x1, &y1, &x2, &y2);
        printf("%d\n", s[x2][y2] - s[x1 - 1][y2] - s[x2][y1 - 1] + s[x1 - 1][y1 - 1]);
    }
    return 0;
}
```

[797. 差分](https://www.acwing.com/problem/content/799/)

```cpp
#include <iostream>

using namespace std;
const int N = 1e6 + 10;
// q数组为原数组,b数组为差分数组
int q[N], b[N];
void insert(int l,int r,int c){
    b[l]+=c;
    b[r+1]-=c;
}

int main() {
    int n, m;
    scanf("%d%d", &n, &m);
    for (int i = 1; i <= n; i++) {
        scanf("%d", &q[i]);
        //step1 构造差分数组
        insert(i,i,q[i]);
    }
    //step2 更新差分数组信息
    int l,r,c;
    while (m--) {
        scanf("%d%d%d",&l,&r,&c);
        insert(l,r,c);
    }
    //step3 更新前缀和,需要注意这里是在差分数组的基础上进行前缀和的计算，所以都是+=操作
    //此处的b数组成为前缀和数组
    for(int i=1;i<=n;i++) {
        b[i]+=b[i-1];
        printf("%d ",b[i]);
    }
    return 0;
}
```

[798. 差分矩阵](https://www.acwing.com/problem/content/800/)

```cpp
#include <iostream>

using namespace std;
const int N = 1010;
int a[N][N], s[N][N];

//不同于一维差分数组，二维差分数组中[x1,y1]之后的正方形区域都会+c。
//因此需要将[x1,y2+1]和[x2+1,y1]之后的区域-c,还要给[x2+1,y2+1]+c
void insert(int x1, int y1, int x2, int y2, int c) {
    s[x1][y1] += c;
    s[x1][y2 + 1] -= c;
    s[x2 + 1][y1] -= c;
    s[x2 + 1][y2 + 1] += c;
}

int main() {
    int n, m, q;
    scanf("%d%d%d", &n, &m, &q);
    for (int i = 1; i <= n; ++i) {
        for (int j = 1; j <= m; ++j) {
            scanf("%d", &a[i][j]);
            insert(i, j, i, j, a[i][j]);
        }
    }
    int x1, y1, x2, y2, c;
    while (q--) {
        scanf("%d%d%d%d%d", &x1, &y1, &x2, &y2, &c);
        insert(x1, y1, x2, y2, c);
    }
    //需要注意这里是在差分数组的基础上进行前缀和的计算，所以都是+=操作
    for (int i = 1; i <= n; ++i)
        for (int j = 1; j <= m; ++j)
            s[i][j] += s[i - 1][j] + s[i][j - 1] - s[i - 1][j - 1];
    for (int i = 1; i <= n; ++i) {
        for (int j = 1; j <= m; ++j)
            printf("%d ", s[i][j]);
        printf("\n");
    }
    return 0;

```

[799. 最长连续不重复子序列](https://www.acwing.com/problem/content/801/)

```cpp
#include <iostream>

using namespace std;
const int N = 1e6 + 10;
//数组q记录题目信息，数组s记录下标数字出现的次数
int q[N], s[N];

int main() {
    int n, res = 0;
    scanf("%d", &n);
    for (int i = 0; i < n; ++i) scanf("%d", &q[i]);
    //j在前，i在后
    //当s[q[i]]>1时说明有重复数字，此时，j向后移动至q[i]第一次出现的位置的后一个，此过程中将所有j经过的数字对应的s[q[j]]--
    for (int i = 0, j = 0; i < n; ++i) {
        ++s[q[i]];
        while (s[q[i]] > 1) --s[q[j++]];
        res = i - j + 1 > res ? i - j + 1 : res;
    }
    printf("%d", res);
    return 0;
}
```





#### 801~900

[801. 二进制中1的个数](https://www.acwing.com/problem/content/803/)

```cpp
#include <iostream>

using namespace std;
int lowbit(int x){
    return x&-x;
}
int main() {
    int n;
    scanf("%d", &n);
    while (n--) {
        int res=0,x;
        scanf("%d",&x);
        while (x){
            x-=lowbit(x);
            ++res;
        }
        printf("%d ",res);
    }
    return 0;
}
```

[802. 区间和](https://www.acwing.com/problem/content/804/)

```cpp
#include <iostream>
#include <vector>
#include <algorithm>

using namespace std;
const int N = 3 * 1e5 + 10;
typedef pair<int, int> PAIR;
//存放插入与查询信息
vector<PAIR> add, query;
//存放计算区间和的差分数组和前缀和数组
int a[N], s[N];
//alls存放对应索引信息
vector<int> alls;

//unique的自己实现
vector<int> unique(vector<int> &A){
	int j=0;
	for(int i=0;i<A.size();i++){
        //要么是第一个数要么不重复
		if(!i||A[i]!=A[i-1]) A[j++]=A[i];
	}
    return A.begin()+j;
}

int find(int x) {
    int l = 0, r = alls.size() - 1;
    while (l < r) {
        int mid = l + r >> 1;
        if (alls[mid] >= x) r = mid;
        else l = mid + 1;
    }
    return l;
}

int main() {
    int n, m, index, data, l, r;
    scanf("%d%d", &n, &m);
    //step1 将所有用到的位置索引存入alls容器中,将相应的插入信息与查询信息存入add和query中
    for (int i = 0; i < n; ++i) {
        scanf("%d%d", &index, &data);
        add.push_back({index, data});
        alls.push_back(index);
    }
    for (int i = 0; i < m; ++i) {
        scanf("%d%d", &l, &r);
        query.push_back({l, r});
        alls.push_back(l);
        alls.push_back(r);
    }
    //step2 索引位置去重,此时alls下标就是相应索引映射后的位置
    sort(alls.begin(), alls.end());
    alls.erase(unique(alls.begin(), alls.end()), alls.end());
    //step3 插入数据
    for (auto item: add) a[find(item.first)] += item.second;
    //step4 计算前缀和
    for (int i = 1; i <= alls.size(); ++i) s[i] += s[i - 1] + a[i];
    //step5 返回结果
    for (auto item :query) {
        printf("%d\n", s[find(item.second)] - s[find(item.first) - 1]);
    }
    return 0;
}
```

[803. 区间合并](https://www.acwing.com/problem/content/805/)

```cpp
#include <iostream>
#include <vector>
#include <algorithm>

using namespace std;
typedef pair<int, int> PAIR;
vector<PAIR> all;

vector<PAIR> merge(vector<PAIR> &x) {
    vector<PAIR> res;
    sort(x.begin(), x.end());
    int st = -2e9, ed = -2e9;
    for (auto itor:x) {
        if (ed < itor.first) {
            if (st != -2e9)res.push_back({st, ed});
            st = itor.first;
            ed = itor.second;
        } else {
            ed = max(ed, itor.second);
        }

    }
    //将最后一组加入结果集，需要判断st!=-2e9避免传入空数组
    if (st != -2e9) res.push_back({st, ed});
    return res;
}

int main() {
    int n, l, r;
    scanf("%d", &n);
    while (n--) {
        scanf("%d%d", &l, &r);
        all.push_back({l, r});
    }
    printf("%d", merge(all).size());
    return 0;
}
```

[823.模拟栈](https://www.acwing.com/problem/content/830/)

```cpp
#include <iostream>
#include <cstring>

using namespace std;
const int N = 1e6 + 10;
int stk[N], top = -1;

void push(int x) {
    stk[++top] = x;
}

int pop() {
    return stk[top--];
}

const char *empty() {
    return top == -1 ? "YES" : "NO";
}

int query() {
    return stk[top];
}

int main() {
    int n;
    char op[6];
    scanf("%d", &n);
    while (n--) {
        int x;
        scanf("%s", &op);
        if (!strcmp(op, "push")) {
            scanf("%d", &x);
            push(x);
        }
        if (!strcmp(op, "pop")) pop();
        if (!strcmp(op, "empty")) printf("%s\n", empty());
        if (!strcmp(op, "query")) printf("%d\n", query());
    }
    return 0;
}
```



[826.单链表](https://www.acwing.com/problem/content/828/)

本题注意当删除头节点时，需要特判。

```cpp
#include <iostream>

using namespace std;
const int N = 1e6 + 10;
int e[N], ne[N];
int idx = 0, head = -1;

void insertFromHead(int x) {
    e[idx] = x;
    ne[idx] = head;
    head = idx++;
}

void insert(int k, int x) {
    e[idx] = x;
    ne[idx] = ne[k];
    ne[k] = idx++;
}

void pop(int k) {
    ne[k] = ne[ne[k]];
}

int main() {
    int m;
    char method;
    scanf("%d", &m);
    while (m--) {
        getchar();
        scanf("%c", &method);
        if (method == 'H') {
            int x;
            scanf("%d", &x);
            insertFromHead(x);
        }
        if (method == 'D') {
            int k;
            scanf("%d", &k);
            if (!k) head = ne[head];
            else pop(k - 1);
        }
        if (method == 'I') {
            int k, x;
            scanf("%d%d", &k, &x);
            insert(k - 1, x);
        }
    }
    for (int i = head; i != -1; i = ne[i]) printf("%d ", e[i]);
    return 0;
}
```

[827.双链表](https://www.acwing.com/problem/content/description/829/)

```cpp
#include <iostream>
#include <cstring>

using namespace std;
const int N = 1e6 + 10;
int l[N], r[N], e[N];
int idx;

void init() {
    //0代表起始端点，1代表结束端点
    //真实数字从idx==2开始存放
    idx = 2;
    l[1] = 0;
    r[0] = 1;
}

//此函数的逻辑是插入k的右侧节点
//若需要实现插入k的左侧节点只需传入l[k]即可
void add(int k, int v) {
    e[idx] = v;
    l[idx] = k;
    r[idx] = r[k];
    l[r[k]] = idx;
    r[k] = idx;
    ++idx;
}

void remove(int k) {
    r[l[k]] = r[k];
    l[r[k]] = l[k];
}


int main() {
    int n;
    init();
    scanf("%d", &n);
    while (n--) {
        char op[3];
        int x, y;
        scanf("%s", &op);
        if (!strcmp("L", op)) {
            scanf("%d", &x);
            add(0, x);
        }
        if (!strcmp("R", op)) {
            scanf("%d", &x);
            add(l[1], x);
        }
        if (!strcmp("D", op)) {
            scanf("%d", &x);
            remove(x + 1);
        }
        if (!strcmp("IL", op)) {
            scanf("%d%d", &x, &y);
            add(l[x + 1], y);
        }
        if (!strcmp("IR", op)) {
            scanf("%d%d", &x, &y);
            add(x + 1, y);
        }
    }
    for (int i = r[0]; i != 1; i = r[i]) printf("%d ", e[i]);
    return 0;
}
```

[829.模拟队列](https://www.acwing.com/problem/content/831/)

```cpp
#include <iostream>
#include <cstring>

using namespace std;
const int N = 1e6 + 10;
int e[N], head, tail;

void init() {
    head = 0;
    tail = -1;
}

void push(int x) {
    e[tail++] = x;
}

void pop() {
    ++head;
}

int query() {
    return e[head];
}

const char *empty() {
    return tail > head ? "NO" : "YES";
}

int main() {
    int n;
    scanf("%d", &n);
    char op[6];
    int x;
    while (n--) {
        scanf("%s", &op);
        if (!strcmp(op, "push")) {
            scanf("%d", &x);
            push(x);
        }
        if (!strcmp(op, "pop")) pop();
        if (!strcmp(op, "empty")) printf("%s\n", empty());
        if (!strcmp(op, "query")) printf("%d\n", query());
    }
    return 0;
}
```

[830.单调栈](https://www.acwing.com/problem/content/832/)

本题利用栈的性质

```cpp
#include <iostream>

using namespace std;
const int N = 1e5 + 10;
int stk[N], top = -1;

int main() {
    int n, x;
    scanf("%d", &n);
    while (n--) {
        scanf("%d", &x);
        while (top != -1 && stk[top] >= x) --top;
        if (top != -1) printf("%d ", stk[top]);
        else printf("-1 ");
        //注意，需要将当前数字压入栈中
        stk[++top] = x;
    }
    return 0;
}
```

[831.KMP字符串](https://www.acwing.com/problem/content/description/833/)

```cpp
#include <iostream>
#include <cstring>
#include <vector>

using namespace std;
const int N = 1e5 + 10, M = 1e6 + 10;
char p[N], s[M];
vector<int> ne;

// brute force暴力枚举算法
// 复杂度为O(n*m)。当n和m为1e5数量级时，算法完全不可用。
void bruteForce(char *S, char *P) {
    int lens = strlen(S), lenp = strlen(P);
    for (int i = 0; i <= lens - lenp; ++i) {
        bool flag = true;
        for (int j = 0; P[j] != '\0'; ++j) {
            if (S[i + j] != P[j]) {
                flag = false;
                break;
            }
        }
        if (flag) printf("%d ", i);
    }
}

// KMP算法
// 复杂度为O(m+n)
void build(const char *pattern) {
    int len = strlen(pattern);
    ne.resize(len + 1);
    for (int i = 0, j = ne[0] = -1; i < len; ne[++i] = ++j) {
        while (~j && pattern[j] != pattern[i]) j = ne[j];
    }
}

vector<int> match(const char *text, const char *pattern) {
    vector<int> res;
    int lenp = strlen(pattern), lent = strlen(text);
    build(pattern);
    for (int i = 0, j = 0; i < lent; ++i) {
        while (j > 0 && text[i] != pattern[j]) j = ne[j];
        if (text[i] == pattern[j]) ++j;
        if (j == lenp) res.push_back(i - lenp + 1), j = ne[j];
    }
    return res;
}

int main() {
    int n, m;
    scanf("%d", &n);
    getchar();
    for (int i = 0; i < n; ++i) scanf("%c", &p[i]);
    scanf("%d", &m);
    getchar();
    for (int i = 0; i < m; ++i) scanf("%c", &s[i]);
    auto res = match(s, p);
    for (auto itor:res) printf("%d ", itor);
    return 0;
} 
```

[835.Trie字符串统计](https://www.acwing.com/problem/content/837/)

```cpp
#include <iostream>

using namespace std;
const int N = 1e5 + 1;
int s[N][26], cnt[N], idx;
char op, str[N];

void insert(const char *str) {
    int p = 0;
    for (int i = 0; str[i]; ++i) {
        int u = str[i] - 'a';
        if (!s[p][u]) s[p][u] = ++idx;
        p = s[p][u];
    }
    ++cnt[p];
}

int query(const char *str) {
    int p = 0;
    for (int i = 0; str[i]; ++i) {
        int u = str[i] - 'a';
        if (!s[p][u]) return 0;
        p = s[p][u];
    }
    return cnt[p];
}

int main() {
    int n;

    scanf("%d", &n);
    getchar();
    while (n--) {
        scanf("%c", &op);
        getchar();
        scanf("%s", &str);
        getchar();
        if (op == 'I') insert(str);
        if (op == 'Q') printf("%d\n",query(str));
    }
    return 0;
}
```

[836.合并集合](https://www.acwing.com/problem/content/838/)

```cpp
#include <iostream>
using namespace std;

```

#### 901~1000

## 1001～2000

### 1001～1500

#### 1001～1100

#### 1101～1200

#### 1201～1300

#### 1301～1400

[1381. 阶乘](https://www.acwing.com/problem/content/1383/)

本题重点在于如何获取最后一个非零的数。通过分解质因数可知，这里计算出来的阶乘可以拆分为$n!=2^{\alpha-k}*5^{\beta-k}*10^k*C$ ,阶乘中的0就来源于这里的2和5之积，也就是k的数量。

```cpp
#include <iostream>
using namespace std;
int main(){
    int n;
    scanf("%d",&n);
    //这里的d2表示因数中2的个数，d5表示5的个数
    int res=1,d2=0,d5=0;
    for(int i=1;i<=n;i++){
        int x=i;
        while(x%2==0) x/=2,d2+=1;
        while(x%5==0) x/=5,d5+=1;
        //这里取余可以有效的减小数字的大小，对于结果没有影响
        res=res*x%10;
    }
    //将多扣除的2或者5乘回去
    int k=d2<d5?d2:d5;
    //这里取余可以有效的减小数字的大小，对于结果没有影响
    for(int i=0;i<d2-k;i++) res=res*2%10;
    for(int i=0;i<d5-k;i++) res=res*5%10;
    printf("%d",res%10);
    return 0;
}
```

## 2001～3000

## 3001～4000

### 3001～3500

#### 3001～3100

#### 3100～3200

[3192. 出现次数最多的数](https://www.acwing.com/problem/content/3195/)

```cpp
#include <iostream>

using namespace std;
const int N = 10000 + 10;
int q[N];

int main() {
    int n, num, min = 0;
    scanf("%d", &n);
    while (n--) {
        scanf("%d", &num);
        q[num] += 1;
    }
    for (int i = 0; i < N; i++) {
        if (q[min] < q[i]) min = i;
    }
    printf("%d", min);
    return 0;
}
```

