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

[19. 二叉树的下一个节点](https://www.acwing.com/problem/content/31/)

这题主要想法就是判断当前节点是否拥有右节点。如果拥有右节点，则中序遍历的后一个节点为右节点子树的最左的节点。即，通过右节点循环找到子树的最左的节点；如果没有右节点，则中序遍历的后一个节点为该节点父节点的拐点。即上一次子树的根节点。

```cpp
class Solution {
public:
    TreeNode* inorderSuccessor(TreeNode* p) {
        if(!p) return nullptr;
        if(p->right){
            p=p->right;
            while(p->left)
                p=p->left;
            return p;
        }else{
            while(p->father&&p->father->right==p) p=p->father;
            return p->father;
        }
    }
};
```



[33. 链表中倒数第k个节点](https://www.acwing.com/problem/content/32/)

```cpp
class Solution {
public:
    ListNode* findKthToTail(ListNode* pListHead, int k) {
        int n=0;
        for(auto p=pListHead;p;p=p->next) n++;
        int step=n-k;
        if(step<0) return NULL;
        else{
            while(step--)pListHead=pListHead->next;
        }
        return pListHead;
    }
};
```

[35. 反转链表](https://www.acwing.com/problem/content/33/)

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

