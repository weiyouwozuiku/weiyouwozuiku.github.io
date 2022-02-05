---
title: Leetcode算法题401-500
author: 不二
img: >-
  https://cdn.jsdelivr.net/gh/weiyouwozuiku/weiyouwozuiku.github.io@src/source/_posts/PageImg/算法/Leetcode算法题401-500.jpeg
mathjax: true
date: 2022-01-16 17:40:46
tags: Cpp
categories: 算法
---

## 401-410
## 411-420

#### [415. 字符串相加](https://leetcode-cn.com/problems/add-strings/)

```cpp
class Solution {
public:
    string addStrings(string num1, string num2) {
        vector<int> A, B;
        int k = 0;
        string res = "";
        for (int i = num1.size() - 1; i >= 0; i--) {
            A.push_back(num1[i] - '0');
        }
        for (int i = num2.size() - 1; i >= 0; i--) {
            B.push_back(num2[i] - '0');
        }
        for (int i = 0; i < A.size() || i < B.size(); i++) {
            if (i < A.size()) k += A[i];
            if (i < B.size()) k += B[i];
            res += char(k % 10 + '0');
            k /= 10;
        }
        if (k) res += char('1');
        reverse(res.begin(), res.end());
        return res;
    }
};
```



## 421-430
## 431-440
## 441-450
## 451-460
## 461-470

#### [461. 汉明距离](https://leetcode-cn.com/problems/hamming-distance/)

本题属于位运算的组合题。先使用异或的性质去除所有二进制不同位的位，再使用与计算二进制中1的个数。

```cpp
class Solution {
public:
    int hammingDistance(int x, int y) {
        x ^= y;
        int n = 0;
        while (x) {
            x &= (x - 1);
            ++n;
        }
        return n;
    }
};
```



## 471-480
## 481-490
## 491-500

#### [496. 下一个更大元素 I](https://leetcode-cn.com/problems/next-greater-element-i/)

本题本质上是一个单调栈的模板题，唯一不同的地方就是使用了两个vector存放数据，因此，在找到num2所有元素的下一个更大元素存入哈希表。再将num1中的元素通过哈希表查出对应的答案。

```cpp
class Solution {
public:
    vector<int> nextGreaterElement(vector<int> &nums1, vector<int> &nums2) {
        // key为nums2中的值，value为对应值下一个更大的元素
        unordered_map<int, int> heap;
        // 存放单调栈
        int stack[1001], top = 0, x;
        vector<int> res;
        for (int i = nums2.size() - 1; i >= 0; i--) {
            x = nums2[i];
            while (top && stack[top] < x) --top;
            if (top) heap[x] = stack[top];
            else heap[x] = -1;
            stack[++top] = x;
        }
        for (int item:nums1){
            res.push_back(heap[item]);
        }
        return res;
    }
};
```



