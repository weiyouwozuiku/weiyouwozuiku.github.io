---
title: Leetcode算法题601-700
author: 不二
img: >-
  https://cdn.jsdelivr.net/gh/weiyouwozuiku/weiyouwozuiku.github.io@src/source/_posts/PageImg/算法/Leetcode算法题601-700.jpeg
mathjax: true
date: 2022-01-16 17:42:05
tags: 
- Cpp
- Leetcode
categories: 算法
---

## 601-610
## 611-620
## 621-630
## 631-640
## 641-650
## 651-660
## 661-670
## 671-680

## 681-690

#### [682. 棒球比赛](https://leetcode-cn.com/problems/baseball-game/)

```cpp
class Solution {
public:
    int calPoints(vector <string> &ops) {
        vector<int> res;
        for (auto op: ops) {
            // 前一次得分无效
            if (op == "C") {
                res.pop_back();
            }
                // 本回合新获得的得分是前一次得分的两倍
            else if (op == "D") {
                res.push_back(res.back() * 2);
            }
                // 本回合新获得的得分是前两次得分的总和
            else if (op == "+") {
                res.push_back(res[res.size() - 1] + res[res.size() - 2]);
            } else {
                res.push_back(stoi(op));
            }
        }
        int ans = 0;
        for (auto it: res) ans += it;
        return ans;
    }
};
```

## 691-700

#### [693. 交替位二进制数](https://leetcode-cn.com/problems/binary-number-with-alternating-bits/)

相邻两位不同，可以通过二进制11去检测，如果是00或者11则会与运算出00或11，即0或3。

```cpp
class Solution {
public:
    bool hasAlternatingBits(int n) {
        while (n) {
            if ((n & 3) == 3 || (n & 3) == 0) {
                return false;
            }
            n >>= 1;
        }
        return true;
    }
};
```

