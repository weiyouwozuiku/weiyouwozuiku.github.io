---
title: Leetcode算法题201-300
author: 不二
img: >-
  https://cdn.jsdelivr.net/gh/weiyouwozuiku/weiyouwozuiku.github.io@src/source/_posts/PageImg/算法/Leetcode算法题201-300.jpeg
mathjax: true
date: 2022-01-16 17:40:18
tags: 
- Cpp
- Leetcode
categories: 算法
---

## 201-210

### [204. 计数质数](https://leetcode.cn/problems/count-primes/)

```cpp
// 埃氏筛 O(loglogn)
class Solution {
public:
    int countPrimes(int n) {
        vector<bool> st(n + 1, false);
        int nums = 0;
        for (int i = 2; i < n; ++i) {
            if (!st[i]) {
                nums += 1;
                for (int j = i + i; j < n; j += i) st[j] = true;
            }
        }
        return nums;
    }
};
```

```cpp
// 线性筛 O(n) 需要注意的是
class Solution {
public:
    int countPrimes(int n) {
        vector<bool> st(n + 1, false);
        vector<int> primes;
        for (int i = 2; i < n; ++i) {
            if (!st[i]) primes.push_back(i);
            for (int j = 0; j < primes.size() && primes[j] <= n / i; ++j) {
                st[primes[j]*i]=true;
                if(i%primes[j]==0) break;
            }
        }
        return primes.size();
    }
};
```

### [206.反转链表](https://leetcode.cn/problems/reverse-linked-list/description/)

```cpp
// 本题只需要注意的是维护两个指针分别指向当前节点后后一个节点，每次移动位置向后移动，最后处理原本的头节点指向null
class Solution {
public:
    ListNode *reverseList(ListNode *head) {
        if (!head) return nullptr;
        auto a = head, b = head->next;
        while (b) {
            auto c = b->next;
            b->next = a;
            a = b;
            b = c;
        }
        head->next = nullptr;
        return a;
    }
};
```





### [208. 实现 Trie (前缀树)](https://leetcode-cn.com/problems/implement-trie-prefix-tree/)

关于「二维数组」是如何工作 & 1e5 大小的估算要搞懂为什么行数估算是 1e5，首先要搞清楚「二维数组」是如何工作的。

在「二维数组」中，我们是通过 indexindex 自增来控制使用了多少行的。

当我们有一个新的字符需要记录，我们会将 indexindex 自增（代表用到了新的一行），然后将这新行的下标记录到当前某个前缀的格子中。

举个🌰，假设我们先插入字符串 abc 这时候，前面三行会被占掉。

第 0 行 a 所对应的下标有值，值为 1，代表前缀 a 后面接的字符串会被记录在下标为 1 的行内

第 1 行 b 所对应的下标有值，值为 2，代表前缀 ab 后面接的字符串会被记录在下标为 2 的行内

第 2 行 c 所对应的下标有值，值为 3，代表前缀 abc 后面接的字符串会被记录在下标为 3 的行内

当再插入 abcl 的时候，这时候会先定位到 abc的前缀行（第 3 行），将 l 的下标更新为 4，代表 abcl 被加入前缀树，并且前缀 abcl 接下来会用到第 4 行进行记录。

但当插入 abl 的时候，则会定位到 ab 的前缀行（第 2 行），然后将 l 的下标更新为 5，代表 abl 被加入前缀树，并且前缀 abl 接下来会使用第 5 行进行记录。

当搞清楚了「二维数组」是如何工作之后，我们就能开始估算会用到多少行了，调用次数为 10^4 ，传入的字符串长度为 10^3 ，假设每一次的调用都是 insert，并且每一次调用都会使用到新的 10^3行。那么我们的行数需要开到 10^7。

但由于我们的字符集大小只有 26，因此不太可能在 10^4次调用中都用到新的 10^3行。

而且正常的测试数据应该是 search和 startsWith调用次数大于 insert才有意义的，一个只有insert调用的测试数据，任何实现方案都能 AC。

因此我设定了 10^5为行数估算，当然直接开到 10^6也没有问题。

```cpp
// 这个放在外层，空间能重复利用，能大大减少算法所用空间
const static int N = 1e5 + 10;
int s[N][26] = {0}, cnt[N] = {0};
int idx;

class Trie {
public:
    Trie() {
        idx = 0;
        memset(s, 0, sizeof s);
        memset(cnt, 0, sizeof cnt);
    }

    void insert(string word) {
        int k = 0, n = word.size(), j;
        for (int i = 0; i < n; i++) {
            j = word[i] - 'a';
            if (!s[k][j]) s[k][j] = ++idx;
            k = s[k][j];
        }
        ++cnt[k];
    }

    bool search(string word) {
        int k = 0, n = word.size(), j;
        for (int i = 0; i < n; i++) {
            j = word[i] - 'a';
            if (!s[k][j]) return false;
            else k = s[k][j];
        }
        return cnt[k];
    }

    bool startsWith(string prefix) {
        int k = 0, n = prefix.size(), j;
        for (int i = 0; i < n; i++) {
            j = prefix[i] - 'a';
            if (!s[k][j]) return false;
            else k = s[k][j];
        }
        return true;
    }
};
```



## 211-220

### [215.数组中的第k个最大元素](https://leetcode.cn/problems/kth-largest-element-in-an-array/description/)

本题需要再$O(n)$时间内，因此可以采用快速排序+二分的方法。需要注意本题是最大元素。

```cpp
class Solution {
public:
    int quick_select(vector<int> &nums, int l, int r, int k) {
        if (l == r) return nums[l];
        int mid = nums[(l + r) >> 1];
        int i = l - 1, j = r + 1;
        while (i < j) {
            while (nums[++i] > mid);
            while (nums[--j] < mid);
            if (i < j) swap(nums[i], nums[j]);
        }
        if (k <= j) return quick_select(nums, l, j, k);
        else return quick_select(nums, j + 1, r, k);
    }

    int findKthLargest(vector<int> &nums, int k) {
        return quick_select(nums, 0, nums.size() - 1, k - 1);
    }
};
```



## 221-230
## 231-240

#### [231.2 的幂](https://leetcode-cn.com/problems/power-of-two/)

符合2的幂的数在二进制中表示为首位为1，其余位为0。

```cpp
class Solution {
public:
    bool isPowerOfTwo(int n) {
        return (n > 0) && (n & (n - 1)) == 0;
    }
};
```

### [236. 二叉树的最近公共祖先](https://leetcode.cn/problems/lowest-common-ancestor-of-a-binary-tree/description/)

```cpp
 // 本题的解题思路就是dfs，通过保存判断当前节点的state进行判断p和q是否在这条路径中
 // 需要注意的是本题需要的最近的节点，因此需要保证ans只被第一次找到公共祖先时赋值
class Solution {
public:
    TreeNode *res = nullptr;

    TreeNode *lowestCommonAncestor(TreeNode *root, TreeNode *p, TreeNode *q) {
        dfs(root, p, q);
        return res;
    }

    int dfs(TreeNode *root, TreeNode *p, TreeNode *q) {
        if (!root) return 0;
        int state = dfs(root->left, p, q);
        if (root == p) state |= 1;
        if (root == q) state |= 2;
        state |= dfs(root->right, p, q);
        if (state == 3 && !res) return res = root;
        return state;
    }
};
```

## 241-250

## 251-260
## 261-270
## 271-280
## 281-290
## 291-300

