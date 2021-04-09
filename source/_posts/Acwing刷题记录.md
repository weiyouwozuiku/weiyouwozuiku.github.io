---
title: Acwing刷题记录
author: 不二
mathjax: true
date: 2021-03-23 09:35:22
tags: C++
img:
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



#### 801~900

#### 901~1000

## 1001～2000

### 1001～1500

#### 1001～1100

#### 1101～1200

#### 1201～1300

#### 1301～1400

[1381. 阶乘](https://www.acwing.com/problem/content/1383/)

本题重点在于如何获取最后一个非零的数。通过分解质因数可知，这里计算出来的阶乘可以拆分为$n!=2^{\alpha-k}*5^{\beta-k}*10^k*C$,阶乘中的0就来源于这里的2和5之积，也就是k的数量。

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

