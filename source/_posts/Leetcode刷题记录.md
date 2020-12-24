---
title: Leetcode刷题记录
author: 不二
mathjax: true
date: 2020-11-10 00:14:48
tags: C++
img:
categories: 算法
---

## 前言

本文记录本人刷Leetcode过程的收获和代码。语言选择C++。

## 1～100

### 1 两数之和

```cpp
class Solution {
public:
    vector<int> twoSum(vector<int>& nums, int target) {
        unordered_map<int,int> heap;
        int r;
        for(int i=0;i <nums.size();i++){
            r=target-nums[i];
            //heap.count(key)返回的是key的下标
            if(heap.count(r)) return {heap[r],i};
            else{heap[nums[i]]=i;}
        }
        //防止报错
        return {};
    }
};
```
