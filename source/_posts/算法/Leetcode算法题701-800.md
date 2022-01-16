---
title: Leetcode算法题701-800
author: 不二
img: >-
  https://cdn.jsdelivr.net/gh/weiyouwozuiku/weiyouwozuiku.github.io@src/source/_posts/PageImg/算法/Leetcode算法题701-800.png
mathjax: true
date: 2022-01-16 17:42:15
tags: Cpp
categories: 算法
---

#### [781. 森林中的兔子](https://leetcode-cn.com/problems/rabbits-in-forest/)

这题的难点在于分析兔子的种类数量与同一种类中兔子的数量。因此分为两步计算：

假设有x只兔子说有y只兔子同色。

1. 计算兔子种类：$\lceil\frac{x}{y+1}\rceil$
2. 计算同一种类兔子的数量：$y+1$
3. 二者相乘就是本题答案

注意代码中公式的顺序，因为整数除法涉及小数舍去。

代码中$\lceil\frac{x}{y+1}\rceil*(y+1)$通过$\frac{x+y}{y+1}*(y+1)$实现。

下面证明 $\lceil\frac{n}{x+1}\rceil = \frac{n +x}{x+1}$ 。

![Leetcode刷题记录_781.png](https://cdn.jsdelivr.net/gh/weiyouwozuiku/weiyouwozuiku.github.io@src/source/_posts/算法/Leetcode算法题701-800/Leetcode刷题记录_781.png)

```cpp
class Solution {
public:
    int numRabbits(vector<int>& answers) {
        unordered_map<int,int> cnt;
        for(auto itor : answers) cnt[itor]+=1;
        int res=0;
        for(auto [k,v]:cnt){
            res+=(v+k)/(k+1)*(k+1);
        }
        return res;
    }
};
```

