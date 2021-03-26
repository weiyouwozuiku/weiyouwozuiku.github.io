---
title: Acwing刷题记录
author: 不二
mathjax: true
date: 2021-03-23 09:35:22
tags: C++
img:
categories: 算法
---

## 前言

本文记录本人刷Acwing过程的收获和代码。语言选择C++。

## 1～100

### [35. 反转链表](https://www.acwing.com/problem/content/33/)

此题有两个版本，一个迭代，一个递归。

迭代版：

```cpp
class Solution {
public:
    ListNode* reverseList(ListNode* head) {
        //如果没有节点或者只有一个节点
        if(head==NULL||head->next==NULL) return head;
        auto current=head;
        ListNode* pre=NULL;
        while(current){
            auto next=current->next;
            current->next=pre;
            pre=current;
            current=next;
        }
        return pre;
    }
};
```

递归版：

```cpp

```

## 101~200

## 201~300

## 301~400

## 401~500

## 501~600

## 601~700

## 701~800

## 801~900

## 901~1000

