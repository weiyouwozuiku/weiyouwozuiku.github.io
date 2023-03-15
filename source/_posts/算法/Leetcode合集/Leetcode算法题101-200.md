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

[102.二叉树的层序遍历](https://leetcode.cn/problems/binary-tree-level-order-traversal/description/)

```cpp
class Solution {
public:
    vector <vector<int>> levelOrder(TreeNode *root) {
        queue < TreeNode * > q;
        vector <vector<int>> res;
        if (!root) return {};
        else q.push(root);
        while (q.size()) {
            int size = q.size();
            vector<int> level;
            while (size--) {
                auto p = q.front();
                q.pop();
                level.push_back(p->val);
                if (p->left) q.push(p->left);
                if (p->right) q.push(p->right);
            }
            res.push_back(level);
        }
        return res;
    }
};
```

### [103.二叉树的锯齿形层序遍历](https://leetcode.cn/problems/binary-tree-zigzag-level-order-traversal/)

```cpp
// 本题的思路和二叉树层序遍历一致，只是在每隔一层反转level中的元素
class Solution {
public:
    vector <vector<int>> zigzagLevelOrder(TreeNode *root) {
        vector <vector<int>> res;
        queue < TreeNode * > q;
        if (root) q.push(root);
        else return {};
        bool flag = false;
        while (q.size()) {
            int size = q.size();
            vector<int> level;
            while (size--) {
                auto p = q.front();
                q.pop();
                level.push_back(p->val);
                if (p->left) q.push(p->left);
                if (p->right) q.push(p->right);
            }
            if (flag) reverse(level.begin(), level.end());
            res.push_back(level);
            flag = !flag;
        }
        return res;
    }
};
```



## 111-120

## 121-130

### [121. 买卖股票的最佳时机](https://leetcode.cn/problems/best-time-to-buy-and-sell-stock/description/)

```cpp
// 本题利用dp，最大的收益就是max(昨日的最大收益,今天价格-历史最低价)
class Solution {
public:
    int maxProfit(vector<int> &prices) {
        int res = 0;
        for (int i = 0, minp = INT_MAX; i < prices.size(); ++i) {
            res = max(res, prices[i] - minp);
            minp = min(prices[i], minp);
        }
        return res;
    }
};
```

### [124. 二叉树中的最大路径和](https://leetcode.cn/problems/binary-tree-maximum-path-sum/description/)

```cpp
 // 本题的解题思路是：
 // dp+dfs。本题可以拆解成最大路径就是ans=max(ans,root->val+left+right)
 // 需要注意的是返回值一共有三种可能，只走到root或者left+root或者right+root 因此返回root->val + max(left, right)
class Solution {
public:
    int ans;

    int maxPathSum(TreeNode *root) {
        ans = INT_MIN;
        dfs(root);
        return ans;
    }

    int dfs(TreeNode *root) {
        if (!root) return 0;
        int left = 0, right = 0;
        if (root->left) left = max(0, dfs(root->left));
        if (root->right) right = max(0, dfs(root->right));
        ans = max(ans, root->val + left + right);
        return root->val + max(left, right);
    }
};
```



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

#### [141. 环形链表](https://leetcode-cn.com/problems/linked-list-cycle/)

```cpp
class Solution {
public:
    bool hasCycle(ListNode *head) {
        while(head){
            if(head->val==INT_MAX) return true;
            else head->val=INT_MAX;
            head=head->next;
        }   
        return false;
    }
};
```

本题思路：

本题使用了快慢指针的方法来检测链表中环的存在。首先，已知当链表中出现环的时候遍历时会在第一个环中陷入死循环。基于此，采用双指针的思想，分别设置一个快指针、一个慢指针。快指针每次走两步，慢指针每次走一步。当慢指针移动到环的起始点时，快指针必然还在环中。在快慢指针都在环中时，就变成了慢指针与快指针之间的追击问题。

```cpp
class Solution {
public:
    bool hasCycle(ListNode *head) {
        // 没有节点或只有一个节点必然没有环
        if (!head || !head->next) return false;
        // fast指针每次走两步，slow指针每次走一步
        auto slow = head, fast = head->next;
        while (fast) {
            slow = slow->next, fast = fast->next;
            // fast指针指向最后的空节点
            if (!fast) return false;
            fast = fast->next;
            if (fast == slow) return true;
        }
        return false;
    }
};
```

### [142. 环形链表 II](https://leetcode.cn/problems/linked-list-cycle-ii/)

```cpp
// 本题的解题思路也是利用141中的快慢指针的思想，不同点在于因为fast指针走两步，slow走一步。
// 假定，b点是环的入口，即示例1中的2，快慢指针相遇到环中的位置为c。意味着慢指针从头结点到b的长度正好是从c点循环走若干圈到b的长度。
// 因此，当快慢指针相遇是，将慢指针复位到头指针，快指针一步一步走，当两者相遇时就是环入口
class Solution {
public:
    ListNode *detectCycle(ListNode *head) {
        if (!head || !head->next) return nullptr;
        auto slow = head, fast = head;
        while (fast) {
            slow = slow->next, fast = fast->next;
            if (!fast) return nullptr;
            fast = fast->next;
            if (!fast) return nullptr;
            if (slow == fast) break;
        }
        slow = head;
        while (slow != fast) slow = slow->next, fast = fast->next;
        return fast;
    }
};
```

### [143. 重排链表](https://leetcode.cn/problems/reorder-list/description/)

```cpp
class Solution {
public:
    void reorderList(ListNode *head) {
        if (!head || !head->next) return;
        int n = 0;
        for (auto q = head; q; q = q->next) n += 1;
        auto a = head;
        for (int i = 0; i < n / 2; ++i) a = a->next;
        auto b = a->next;
        a->next = nullptr;
        while (b) {
            auto c = b->next;
            b->next = a;
            a = b, b = c;
        }
        for (int i = 0; i < (n - 1) / 2; ++i) {
            auto c = a->next;
            a->next = head->next;
            auto d = head->next;
            head->next = a;
            head = d, a = c;
        }
    }
};
```



#### [146.LRU缓存](https://leetcode.cn/problems/lru-cache/)

```cpp
class LRUCache {
    // lru算法使用一个unordered_map存储key,value.再使用一个双链表标记使用的时间。最近使用则在双链表头部，最不常使用的则在双链表的尾部
public:
    struct Node {
        int key, val;
        Node *left, *right;

        Node(int _val, int _key) : val(_val), key(_key), left(nullptr), right(nullptr) {}
    } *l, *r;

    unordered_map<int, Node *> hash;

    int cap;

    LRUCache(int capacity) {
        cap = capacity;
        l = new Node(-1, -1), r = new Node(-1, -1);
        l->right = r;
        r->left = l;
    }

    int get(int key) {
        if (hash.count(key)) {
            auto p = hash[key];
            remove(p);
            insert(p);
            return p->val;
        } else {
            return -1;
        }
    }

    void put(int key, int value) {
        if (hash.count(key)) {
            auto p = hash[key];
            p->val = value;
            remove(p);
            insert(p);
        } else {
            if (hash.size() == cap) {
                auto p = r->left;
                remove(p);
                hash.erase(p->key);
                delete p;
            }
            auto p = new Node(value, key);
            hash[key] = p;
            insert(p);
        }
    }

    void remove(const Node *node) {
        node->left->right = node->right;
        node->right->left = node->left;
    }

    void insert(Node *node) {
        node->right = l->right;
        node->left = l;
        l->right->left = node;
        l->right = node;
    }
};
```



## 151-160

### [160. 相交链表](https://leetcode.cn/problems/intersection-of-two-linked-lists/description/)

```cpp
 // 本题的解题思路在于如果两个链表相交，则遍历一遍所在链表到另一个链表的长度是相同的。
 // 例如headA到相交点距离a，headB到相交点距离b，相交后的长度为c，因此a+c+b=b+c+a。因此本题可以用双指针解决
class Solution {
public:
    ListNode *getIntersectionNode(ListNode *headA, ListNode *headB) {
        auto a = headA, b = headB;
        while (a != b) {
            a = a ? a->next : headB;
            b = b ? b->next : headA;
        }
        return a;
    }
};
```



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

### [199. 二叉树的右视图](https://leetcode.cn/problems/binary-tree-right-side-view/description/)

```cpp
class Solution {
public:
    vector<int> rightSideView(TreeNode *root) {
        vector<int> res;
        if (!root) return {};
        queue < TreeNode * > q;
        q.push(root);
        while (q.size()) {
            int len = q.size();
            for (int i = 0; i < len; ++i) {
                auto p = q.front();
                q.pop();
                if (p->left) q.push(p->left);
                if (p->right) q.push(p->right);
                if (i == len - 1) res.push_back(p->val);
            }
        }
        return res;
    }
};
```



### [200.岛屿数量](https://leetcode.cn/problems/number-of-islands/description/)

```cpp
class Solution {
public:
    const int dx[4] = {1, -1, 0, 0}, dy[4] = {0, 0, 1, -1};

    int numIslands(vector <vector<char>> &grid) {
        int res = 0;
        for (int i = 0; i < grid.size(); ++i) {
            for (int j = 0; j < grid[i].size(); ++j) {
                if (grid[i][j] == '1') res += 1, dfs(grid, i, j);
            }
        }
        return res;
    }

    void dfs(vector <vector<char>> &grid, int x, int y) {
        grid[x][y] = '0';
        for (int i = 0; i < 4; ++i) {
            int di = x + dx[i], dj = y + dy[i];
            if (di >= 0 && dj >= 0 && di < grid.size() && dj < grid[di].size() && grid[di][dj] == '1') dfs(grid, di, dj);
        }
    }
};
