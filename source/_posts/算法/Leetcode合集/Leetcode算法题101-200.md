---
title: Leetcode算法题101-200
author: 不二
img: >-
  https://cdn.jsdelivr.net/gh/weiyouwozuiku/weiyouwozuiku.github.io@src/source/_posts/PageImg/算法/Leetcode算法题101-200.jpeg
mathjax: true
date: 2022-01-16 17:40:08
tags: 
- Cpp
- Leetcode
categories: 算法
---

## 101-110

## 111-120

## 121-130

## 131-140

#### [136. 只出现一次的数字](https://leetcode-cn.com/problems/single-number/)

本题利用位运算中异或的性质。

```cpp
class Solution {
public:
    int singleNumber(vector<int> &nums) {
        int n = 0;
        for (const auto &item: nums) {
            n ^= item;
        }
        return n;
    }
};
```



## 141-150
## 151-160
## 161-170
## 171-180

#### [172. 阶乘后的零](https://leetcode-cn.com/problems/factorial-trailing-zeroes/)

本题的难度都在于计算的逻辑，而非代码，属于数论的题目。

首先判断0会在什么情况下出现，即2和5为1~n之间元素的因数时，2与5相乘得10。因此本题可以转化为找到（2，5）对在1 ~ n之间有多少个数含有这两个因数。总所周知，2出现的概率低于5出现的概率，因此本题简化为寻找1~n中每个元素中可以分解为多少个5。

一个元素如果存在5这个因数可以表示为$x=y*5^k$。因此只需要检查1到n中有多少个$5^k$。于是答案如下：

```cpp
class Solution {
public:
    int trailingZeroes(int n) {
        int res = 0;
        while (n) res += n / 5, n /= 5;
        return res;
    }
};
```

#### [173. 二叉搜索树迭代器](https://leetcode-cn.com/problems/binary-search-tree-iterator/)

```cpp
class BSTIterator {
public:
    //构造栈，将根节点的左节点循环加入栈中，以此寻找相应子树的节点
    BSTIterator(TreeNode* root) {
        while (root){
            stk.push(root);
            root=root->left;
        }
    }

    //获取当前节点的信息。如果该节点存在右节点将右节点下的左节点压入栈中
    int next() {
        auto p=stk.top();
        stk.pop();
        int val=p->val;
        p=p->right;
        while(p){
            stk.push(p);
            p=p->left;
        }
        return val;
    }

    //检测是否还有元素没有遍历只需检测栈中是否还有节点
    bool hasNext() {
        return stk.size();
    }

private:
    //使用栈存储节点信息
    stack<TreeNode*> stk;
};
```

## 181-190

#### [190. 颠倒二进制位](https://leetcode-cn.com/problems/reverse-bits/)

这题最好的解法是分治算法，但是局限性很大。故本题给出基于循环处理位运算的解法。

本题可以分解为两个部分：

1. 首先获取当前位二进制值，n>>i&1
2. 将二进制数组装进结果，res<<1+当前位二进制值

```cpp
class Solution {
public:
    uint32_t reverseBits(uint32_t n) {
        uint32_t res = 0;
        for (int i = 0; i < 32; i++) res = (res << 1) + (n >> i & 1);
        return res;
    }
};
```



## 191-200

#### [191. 位1的个数](https://leetcode-cn.com/problems/number-of-1-bits/)

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

