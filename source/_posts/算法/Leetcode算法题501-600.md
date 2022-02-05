---
title: Leetcode算法题501-600
author: 不二
img: >-
  https://cdn.jsdelivr.net/gh/weiyouwozuiku/weiyouwozuiku.github.io@src/source/_posts/PageImg/算法/Leetcode算法题501-600.png
mathjax: true
date: 2022-01-16 17:41:55
tags: Cpp
categories: 算法
---

## 501-510

#### [503. 下一个更大元素 II](https://leetcode-cn.com/problems/next-greater-element-ii/)

```cpp
class Solution {
public:
    vector<int> nextGreaterElements(vector<int> &nums) {
        int n = nums.size(), x;
        stack<int> stk;
        vector<int> res(n);
        // 破环成链，将原本的环变成两个nums的链条
        nums.insert(nums.end(), nums.begin(), nums.end());
        for (int i = 2 * n - 1; i >= 0; i--) {
            x = nums[i];
            while (stk.size() && x >= stk.top()) stk.pop();
            // 结果只存放0到nums.size()-1之间的数
            if (i < n) {
                if (stk.empty()) res[i] = -1;
                else res[i] = stk.top();
            }
            stk.push(x);
        }
        return res;
    }
};
```

## 511-520
## 521-530
## 531-540
## 541-550
## 551-560
## 561-570
## 571-580
## 581-590
## 591-600
