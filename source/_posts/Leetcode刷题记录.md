---
title: Leetcode刷题记录
author: 不二
mathjax: true
date: 2020-11-10 00:14:48
tags: Cpp
img:
categories: 算法
---

## 前言

本文记录本人刷Leetcode过程的收获和代码。语言选择C++。

## 1～1000

### 1～500

####1～100

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

[61. 旋转链表](https://leetcode-cn.com/problems/rotate-list/)

本题的移动位置k会给的很大，需要进行取余操作。

本题的解题步骤分为四步：

1. 将n-k-1处的节点的next指向NULL
2. 将整个链表的尾部指向头节点
3. 返回原本n-k处的节点

```cpp
class Solution {
public:
    ListNode *rotateRight(ListNode *head, int k) {
        if (!head || k == 0) return head;
        //获取链表长度
        int len = 0;
        for(auto p=head;p;p=p->next) len+=1;
        k%=len;
        //判断取余后的k是否为0,如果是直接返回
        if(!k) return head;
        else{
            //找到第n-k-1节点
            auto p=head;
            for(int i=0;i<len-k-1;i++) p=p->next;
            //找到尾节点
            auto tail=head;
            while (tail->next) tail=tail->next;
            //链接
            tail->next=head;
            head=p->next;
            p->next=NULL;
        }
        return head;
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

[90. 子集 II](https://leetcode-cn.com/problems/subsets-ii/)

```cpp
class Solution {
private:
    //key为数字，value为出现次数
    unordered_map<int, int> cnt;
    //结果集合
    vector<vector<int>> ans;
    //路径
    vector<int> path;
public:
    vector<vector<int>> subsetsWithDup(vector<int> &nums) {
        //如果只有一个数字，直接返回一个空集与当前数字
        if (nums.size() == 1) {
            ans.push_back(vector<int>{});
            ans.push_back(vector<int>{nums[0]});
        } else {
            for (auto itor:nums) cnt[itor] += 1;
            //这题范围较小，直接枚举
            dfs(-10);
        }
        return ans;
    }

    void dfs(int n) {
        //超过边界将结果保存
        if (n > 10) ans.push_back(path);
        else {
            //这里处理数字出现的次数，从0到cnk[n]次
            for (int i=0;i<cnt[n]+1;i++){
                dfs(n+1);
                path.push_back(n);
            }
            //清理现场
            for (int i=0;i<cnt[n]+1;i++) path.pop_back();
        }
    }
};
```



#### 101~200

[173. 二叉搜索树迭代器](https://leetcode-cn.com/problems/binary-search-tree-iterator/)

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

[190. 颠倒二进制位](https://leetcode-cn.com/problems/reverse-bits/)

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



#### 201~300

#### 301~400

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



#### 401~500

### 501~1000

#### 501~600

#### 601~700

#### 701~800

[781. 森林中的兔子](https://leetcode-cn.com/problems/rabbits-in-forest/)

这题的难点在于分析兔子的种类数量与同一种类中兔子的数量。因此分为两步计算：

假设有x只兔子说有y只兔子同色。

1. 计算兔子种类：$\lceil\frac{x}{y+1}\rceil$
2. 计算同一种类兔子的数量：$y+1$
3. 二者相乘就是本题答案

注意代码中公式的顺序，因为整数除法涉及小数舍去。

代码中$\lceil\frac{x}{y+1}\rceil*(y+1)$通过$\frac{x+y}{y+1}*(y+1)$实现。

下面证明 $\lceil\frac{n}{x+1}\rceil = \frac{n +x}{x+1}$ 。

![](https://cdn.jsdelivr.net/gh/weiyouwozuiku/buerlog_img/BlogImage/Leetcode%E5%88%B7%E9%A2%98%E8%AE%B0%E5%BD%95_#781.png)

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

#### 801~900

#### 901~1000

