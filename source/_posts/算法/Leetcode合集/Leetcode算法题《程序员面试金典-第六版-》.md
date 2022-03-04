---
title: Leetcode算法题《程序员面试金典(第六版)》
author: 不二
img: >-
  https://cdn.jsdelivr.net/gh/weiyouwozuiku/weiyouwozuiku.github.io@src/source/_posts/PageImg/算法/Leetcode算法题《程序员面试金典-第六版-》.png
mathjax: true
date: 2022-01-16 17:48:14
tags: 
- Cpp
- Leetcode
categories: 算法
---

#### [面试题 05.01. 插入](https://leetcode-cn.com/problems/insert-into-bits-lcci/)

```cpp
class Solution {
public:
    int insertBits(int N, int M, int i, int j) {
        for (int k = i; k <= j; k++) {
            //这里用longlong是为了防止出现越界
            //与0相与将i到j的二进制全部变成0
            N &= ~((long long) 1 << k);
        }
        return N | (M << i);
    }
};
```

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



