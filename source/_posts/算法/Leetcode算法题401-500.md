---
title: Leetcode算法题401-500
author: 不二
img: >-
  https://cdn.jsdelivr.net/gh/weiyouwozuiku/weiyouwozuiku.github.io@src/source/_posts/PageImg/算法/Leetcode算法题701-800.png
mathjax: true
date: 2022-01-16 17:40:46
tags: Cpp
categories: 算法
---

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

