---
title: Leetcode算法题201-300
author: 不二
img: >-
  https://cdn.jsdelivr.net/gh/weiyouwozuiku/weiyouwozuiku.github.io@src/source/_posts/PageImg/
mathjax: true
date: 2022-01-16 17:40:18
tags: Cpp
categories: 算法
---

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

