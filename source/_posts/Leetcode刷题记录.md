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

[1. 两数之和](https://leetcode-cn.com/problems/two-sum/)

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

[82. 删除排序链表中的重复元素 II](https://leetcode-cn.com/problems/remove-duplicates-from-sorted-list-ii/)

```cpp
class Solution {
public:
    ListNode* deleteDuplicates(ListNode* head) {
        if(!head||!head->next) return head;
        ListNode* dummy=new ListNode(0);
        dummy->next=head;
        auto pre=dummy;
        while(pre->next){
            auto cur=pre->next;
            while(cur->next&&cur->val==cur->next->val){
                cur=cur->next;
            }
            if(pre->next==cur) pre=cur;
            else pre->next=cur->next;
        }
        
        return dummy->next;
    }
};
```

[83. 删除排序链表中的重复元素](https://leetcode-cn.com/problems/remove-duplicates-from-sorted-list/)

```cpp
class Solution {
public:
    ListNode* deleteDuplicates(ListNode* head) {
        auto cur=head;
        while(cur&&cur->next){
            if(cur->val==cur->next->val) cur->next=cur->next->next;
            else cur=cur->next;
        }
        return head;
    }
};
```



## 101~200

[191. 位1的个数](https://leetcode-cn.com/problems/number-of-1-bits/)

 函数`lowbit(x)`可以返回当前数字x二进制下最后1位1在第几位，实现方法为x&-x，在Cpp中-x=~x+1。本题解法就是这样的思路。

```cpp
class Solution {
public:
    int hammingWeight(uint32_t n) {
        int num=0;
        while(n){
            n-=n&-n;
            num++;
        }
        return num;
    }
};
```



## 201~300

## 301~400

[341. 扁平化嵌套列表迭代器](https://leetcode-cn.com/problems/flatten-nested-list-iterator/)

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



## 401~500

## 501~600

## 601~700

## 701~800

## 801~900

## 901~1000

