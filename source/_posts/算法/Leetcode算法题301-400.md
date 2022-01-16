---
title: Leetcode算法题301-400
author: 不二
img: >-
  https://cdn.jsdelivr.net/gh/weiyouwozuiku/weiyouwozuiku.github.io@src/source/_posts/PageImg/算法/Leetcode算法题301-400.jpeg
mathjax: true
date: 2022-01-16 17:40:32
tags: Cpp
categories: 算法
---

#### [341. 扁平化嵌套列表迭代器](https://leetcode-cn.com/problems/flatten-nested-list-iterator/)

递归写法：

```cpp
class NestedIterator {
public:
    NestedIterator(vector<NestedInteger> &nestedList) {
        k=0;
        for (auto &itor: nestedList) dfs(itor);
    }

    void dfs(NestedInteger &next){
        if(next.isInteger()) q.push_back(next.getInteger());
        else{
            for (auto &itor:next.getList()) dfs(itor);
        }
    }
    
    int next() {
        return q[k++];
    }
    
    bool hasNext() {
        return k<q.size();
    }

private:
    vector<int> q;
    int k;
};
```

#### [371. 两整数之和](https://leetcode-cn.com/problems/sum-of-two-integers/)

加法可以分为两步：

1. 使用a^b计算出不带进位的加法
2. 使用\(a&b\)<<1计算出进位

针对每一位二进制，递归上述流程。

```cpp
class Solution {
public:
    int getSum(int a, int b) {
        return b==0? a:getSum(a^b,(unsigned int)(a&b)<<1);
    }
};
```

