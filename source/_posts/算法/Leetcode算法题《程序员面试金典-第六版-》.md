---
title: Leetcode算法题《程序员面试金典(第六版)》
author: 不二
img: >-
  https://cdn.jsdelivr.net/gh/weiyouwozuiku/weiyouwozuiku.github.io@src/source/_posts/PageImg/算法/Leetcode算法题《程序员面试金典-第六版-》.png
mathjax: true
date: 2022-01-16 17:48:14
tags: Cpp
categories: 算法
---

#### [面试题 16.01. 交换数字](https://leetcode-cn.com/problems/swap-numbers-lcci/)

本题使用了位运算的性质：

- a^a=0
- a^0=a

因此本题解析为：

a=a^b;

b=a\^b=\(a\^b\)\^b=a\^\(b\^b\)=a\^0=a

a=a\^b=a\^b\^a=0\^b=b

```cpp
class Solution {
public:
    vector<int> swapNumbers(vector<int> &numbers) {
        numbers[0] = numbers[0] ^ numbers[1];
        numbers[1] = numbers[0] ^ numbers[1];
        numbers[0] = numbers[0] ^ numbers[1];
        return numbers;
    }
};
```

