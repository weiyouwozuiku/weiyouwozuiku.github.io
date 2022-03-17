---
title: Leetcode算法题301-400
author: 不二
img: >-
  https://cdn.jsdelivr.net/gh/weiyouwozuiku/weiyouwozuiku.github.io@src/source/_posts/PageImg/算法/Leetcode算法题301-400.jpeg
mathjax: true
date: 2022-01-16 17:40:32
tags: 
- Cpp
- Leetcode
categories: 算法
---

## 301-310

#### [303. 区域和检索 - 数组不可变](https://leetcode-cn.com/problems/range-sum-query-immutable/)

前缀和模板题，注意下下标是从0开始的，适当的将前缀和数组右移一位。

```cpp
class NumArray {
public:
    vector<int> ans;

    NumArray(vector<int> &nums) {
        int n = nums.size();
      // 只有声明了容量才能直接使用下标
        ans.resize(n + 1);
        for (int i = 0; i < n; i++) ans[i + 1] = ans[i] + nums[i];
    }

    int sumRange(int left, int right) {
        return ans[right + 1] - ans[left];
    }
};
```



## 311-320

#### [316. 去除重复字母](https://leetcode-cn.com/problems/remove-duplicate-letters/)

```cpp
class Solution {
public:
    string removeDuplicateLetters(string s) {
        string stk;
        // 存放当前字符是否存在stk中
        unordered_map<char, bool> box;
        // 存放当前字符在整个人字符串的最后位置
        unordered_map<char, int> lastIndex;
        for (int i = 0; i < s.size(); ++i) lastIndex[s[i]] = i;
        for (int i = 0; i < s.size(); ++i) {
            if (!box[s[i]]) {
                while (stk.size() && s[i] < stk.back() && lastIndex[stk.back()] > i) {
                    box[stk.back()] = false;
                    stk.pop_back();
                }
                stk += s[i];
                box[s[i]] = true;
            }
        }
        return stk;
    }
};
```

## 321-330

## 331-340
## 341-350

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

#### 

## 351-360

## 361-370



## 371-380

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



## 381-390
## 391-400



