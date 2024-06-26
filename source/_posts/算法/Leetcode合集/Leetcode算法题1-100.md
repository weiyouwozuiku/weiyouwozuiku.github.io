---
title: Leetcode算法题1-100
author: 不二
img: >-
  https://cdn.jsdelivr.net/gh/weiyouwozuiku/weiyouwozuiku.github.io@src/source/_posts/PageImg/算法/Leetcode算法题1-100.jpeg
mathjax: true
date: 2022-01-16 17:40:00
tags: 
- Cpp
- Leetcode
categories: 算法
---

## 1-10

#### [1. 两数之和](https://leetcode-cn.com/problems/two-sum/)

```cpp
class Solution {
public:
    vector<int> twoSum(vector<int> &nums, int target) {
        //heap中的键存放数组元素，值存放数组元素的索引
        unordered_map<int, int> heap;
        for (int i = 0; i < nums.size(); i++) {
            int re=target-nums[i];
            //count函数中的值对应键值对中的key,检查是否有符合的key存在
            if(heap.count(re)) return {heap[re],i};
            heap[nums[i]]=i;
        }
        //防止报错
        return {};
    }
};
```

#### [2.两数相加](https://leetcode-cn.com/problems/add-two-numbers)

```cpp
class Solution {
public:
    ListNode *addTwoNumbers(ListNode *l1, ListNode *l2) {
        //dummy节点表示结果链表。用-1作为虚拟头节点，cur表示运算过程中的执行节点
        auto dummy = new ListNode(-1), cur = dummy;
        //t存储当前位相加的值
        int t=0;
        while (l1 || l2 || t) {
            if(l1) t+=l1->val,l1=l1->next;
            if(l2) t+=l2->val,l2=l2->next;
            //记得在更新cur->next的同时，更新cur已进行下一轮运算
            cur=cur->next=new ListNode(t%10);
            t/=10;
        }
        return dummy->next;
    }
};
```

#### [3.无重复字符的最长子串](https://leetcode-cn.com/problems/longest-substring-without-repeating-characters)

```cpp
/**
 * 本题的解题思路：
 * 双指针算法的主要优化点就是寻找题目中的单调性。假设变量i,j,i与j之间就是非重复元素的子串。
 * 则当i向后时，j最前的位置只能是当前位置或朝后的位置。可以采用反证法证明。
 */
class Solution {
public:
    int lengthOfLongestSubstring(string s) {
        unordered_map<char, int> heap;
        int res = 0;
        //注意这里不能写成i,j=0会出错
        for (int i = 0, j = 0; i < s.size(); i++) {
            heap[s[i]]++;
            while (heap[s[i]] > 1) heap[s[j++]]--;
            res = max(res, i - j + 1);
        }
        return res;
    }
};
```

#### [4.寻找两个正序数组的中位数](https://leetcode-cn.com/problems/median-of-two-sorted-arrays)

```cpp
class Solution {
public:
    double findMedianSortedArrays(vector<int> &nums1, vector<int> &nums2) {
        int total = nums1.size() + nums2.size();
        if (total % 2 == 0) {
            int left = findKthNumber(nums1, 0, nums2, 0, total / 2);
            int right = findKthNumber(nums1, 0, nums2, 0, total / 2 + 1);
            return (left + right) / 2.0;
        } else {
            //注意这里是totle/2+1
            return findKthNumber(nums1, 0, nums2, 0, total / 2 + 1);
        }
    }

    int findKthNumber(vector<int> &nums1, int i, vector<int> &nums2, int j, int k) {
        /**保证nums1数组的长度小于nums2,如果不是则反转过来。
         * 这样操作的原因在于可以减少考虑的情况。
         * 如果不约定nums1的长度小于nums2,需要考虑三种情况：
         * 1. n,m>=k/2
         * 2. m<k/2
         * 3. n<k/2
         * 约定后只需要考虑两种情况
         * 1. n,m>=k/2
         * 2. m<k/2
         */
        if (nums1.size() - i > nums2.size() - j) return findKthNumber(nums2, j, nums1, i, k);
        //这个判断需要放在k==1之前操作，因为i可能会在nums1中越界。这里判断的是nums1中没有数字的情况
        if (nums1.size() == i) return nums2[j + k - 1];
        if (k == 1) return min(nums1[i], nums2[j]);
        //因为这里保证了nums1小于nums2中需要考虑元素的数量，因此存在nums2中的数字远多于nums1中的数字，直接使用i+k/2会导致超过nums1中元素的数量。
        //因此需要使用min()保证不越界,这里与nums1.size()比较是因为i在不断增大，不能使用nums1.size()-k/2
        //这里的si和sj分别表示在nums1和nums2数组中第k/2位置的后一位。因此在后面比较的时候需要-1。
        //另外vector.size()返回的是无符号整形，因此需要强转int,而不能直接比较
        int si = min(i + k / 2, int(nums1.size())), sj = j + k / 2;
        //舍去无用子数组
        if (nums1[si - 1] > nums2[sj - 1]) {
            return findKthNumber(nums1, i, nums2, sj, k - (sj - j));
        } else {
            return findKthNumber(nums1, si, nums2, j, k - (si - i));
        }
    }
};
```

#### [5.最长回文子串](https://leetcode-cn.com/problems/longest-palindromic-substring)

```cpp
/**
 * 本题的回文串存在两种可能：
 * 1. 数量为奇数，两边对称
 * 2. 数量为偶数。从中向外，两两配对
 *
 * 解题的思路是选定一个i点，左边L点和R点分别是其回文串的左端点和右端点，则整个长度为(R-1)-(L+1)+1=R-L-1
 * 复杂度分析:枚举所有点作为i点为O(n)，枚举i点的LR点也是O(n)，因此整体为O(n^2)
 * 上述的解法较为通用，若是想追求更好的时间复杂度可以采用马拉车(Manacher)算法，将时间复杂度降至O(n)，但不具备通用性。
 */
class Solution {
public:
    string longestPalindrome(string s) {
        //最长回文串
        string res;
        //将字符串的每一个点作为i点进行分析
        for (int i = 0; i < s.size(); i++) {
            //假设此时i点的回文串字符数为奇数个
            int l = i - 1, r = i + 1;
            while (l >= 0 && r < s.size() && s[l] == s[r]) l--, r++;
            if (res.size() < r - l - 1) res = s.substr(l + 1, r - l - 1);
            //假设此时i点的回文串字符数为偶数个
            l = i, r = i + 1;
            while (l >= 0 && r < s.size() && s[l] == s[r]) l--, r++;
            if (res.size() < r - l - 1) res = s.substr(l + 1, r - l - 1);
        }
        return res;
    }
};
```

#### [6.Z字变换](https://leetcode-cn.com/problems/zigzag-conversion/)

```cpp
/**
 * 本题的解题思路：
 * 这道题本质上是一个找规律的题目，Z字变换指的是字符串的打印不是横着，而是横着的Z的结构进行打印
 * 本题共有三种情况进行分别考虑，n表示Z字变换的行数：
 * 1. 观察第一行与最后一行可知，其都是以2n-2为公差的等差数列
 * 2. 观察中间的行数可知，可以将打印的字符分为垂直和倾斜的两种数组的交替数组，且这两个数组依旧是以2n-2为公差的等差数组，
 *    而垂直与倾斜的二者之间存在这这样的约束关系：两两之间其和为2n-2
 * 3. 当2n-2中的n为1是，2n-2==0,会陷入死循环，因此将n==1的情况进行特判
 */

class Solution {
public:
    string convert(string s, int numRows) {
        //避免出现n==1导致的2n-2==0的出现进行特判
        if (numRows == 1) return s;
        string res;
        for (int i = 0; i < numRows; i++) {
            //首行或末行的情况
            if (i == 0 || i == numRows - 1) {
                for (int j = i; j < s.size(); j += 2 * numRows - 2) res += s[j];
            } else {
                //中间行的情况
                for (int j = i, k = 2 * numRows - 2 - i; j < s.size() || k < s.size(); j += 2 * numRows - 2, k += 2 * numRows - 2) {
                    if (j < s.size()) res += s[j];
                    if (k < s.size()) res += s[k];
                }
            }
        }
        return res;
    }
};
```

#### [7.整数反转](https://leetcode-cn.com/problems/reverse-integer/)

```cpp
/**
 * Tip：
 * Cpp中取余运算不同于数学中的取模，整数取余为整数，负数取余为负数
 * 这题使用long long就不需要考虑数据越界问题，直接比较生成数字是否越界即可
 * 但如果使用int，需要先判断给定的数是否越界。如果越界直接返回为0
 * 假定r是返回的反转数字，x是传入的原数字
 * 举例： 如果是正数越界，此时10*r+x%10>INT_MAX,进行不等式变换，可以得到r>(INT_MAX-x%10)/10
 * 即满足上述条件时，获得的反转数越界，可以直接返回0
 * 负数借助Cpp中取余的特性同理可得。
 *
 * long long的写法如下：
 */
// class Solution {
//public:
//    int reverse(int x) {
//        long long r=0;
//        while(x){
//            r=r*10+x%10;
//            x/=10;
//        }
//        if(r>INT_MAX) return 0;
//        if(r<INT_MIN) return 0;
//        return r;
//    }
//};

class Solution {
public:
    int reverse(int x) {
        int res = 0;
        while (x) {
            if (res > 0 && res > (INT_MAX - x % 10) / 10) return 0;
            if (res < 0 && res < (INT_MIN - x % 10) / 10) return 0;
            res = res * 10 + x % 10;
            x /= 10;
        }
        return res;
    }
};
```

#### [8.字符串转换整数 (atoi)](https://leetcode-cn.com/problems/string-to-integer-atoi/)

```cpp
/**
 * 本题的解题思路：
 * 1. 去掉字符串前面的空格字符
 * 2. 该字符串全是空格，直接返回0
 * 3. 判断是否有符号位,设置结果的符号位
 * 4. 将字符串转化为数字
 * 5. 判断是否越界
 */
class Solution {
public:
    int myAtoi(string s) {
        //整个字符串的索引位置
        int i = 0;
        //去掉字符串前面的空格字符
        while (s.size() && s[i] == ' ') ++i;
        //该字符串全是空格，直接返回0
        if (s.size() == i) return 0;
        //设置结果的符号位
        int flag = 1;
        if (s[i] == '-') {
            flag = -1;
            ++i;
        } else if (s[i] == '+') ++i;
        //防止出现'-+'等情况
        if (s[i] < '0' || s[i] > '9') return 0;
        //将字符串转化为数字
        long long res = 0;
        while (s[i] >= '0' && s[i] <= '9') {
            res = res * 10 + s[i++] - '0';
            //避免出现过长字符串
            if (res > INT_MAX) break;
        }
        res *= flag;
        //判断是否越界
        if (res > INT_MAX) res = INT_MAX;
        if (res < INT_MIN) res = INT_MIN;
        return res;
    }
};
```

#### [9.回文数](https://leetcode-cn.com/problems/palindrome-number/)

```cpp
class Solution {
public:
    //这是将x转化为string,并比较二者
    bool isPalindrome_1(int x) {
        //如果是负数，因为符号的存在，必然不是回文数
        if (x < 0) return false;
        //to_string函数为C++11引入
        string s = to_string(x);
        //使用反向迭代器将生成的字符串与与其相反的字符串对比其是否一致
        return s == string(s.rbegin(), s.rend());
    }

    bool isPalindrome(int x) {
        //如果是负数，因为符号的存在，必然不是回文数
        if (x < 0) return false;
        long long res = 0;
        //因为后面修改了x的值，所以这里需要将x的值进行保存以便进行比较y
        int y = x;
        while (x) {
            res = res * 10 + x % 10;
            x /= 10;
        }
        return res == y;
    }
};
```

#### [10.正则表达式匹配](https://leetcode-cn.com/problems/regular-expression-matching/)

![Leetcode刷题记录_10.png](https://cdn.jsdelivr.net/gh/weiyouwozuiku/weiyouwozuiku.github.io@src/source/_posts/算法/Leetcode合集/Leetcode算法题1-100/Leetcode刷题记录_10.png)

```cpp
/**
 * 本题的解题思路：
 * 本题是一个动态规划问题,思路详见笔记
 */
class Solution {
public:
    bool isMatch(string s, string p) {
        //获取匹配串与模式串的字符长度以便于计算
        int n = s.size(), m = p.size();
        //将第一位空出便于后面从下标1开始运算
        s = ' ' + s, p = ' ' + p;
        //创建状态转移矩阵
        vector <vector<bool>> f(n + 1, vector<bool>(m + 1));
        //初始化第一个状态,即什么都没有
        f[0][0] = true;
        //这里从0开始是因为即使匹配串为空也可能符合模式串的要求
        for (int i = 0; i <= n; i++) {
            for (int j = 1; j <= m; j++) {
                //遇到下一位是*的此位直接跳过，将此位与后面的*一起处理
                //防止越界，需要多加一个判断
                if (j + 1 <= m && p[j + 1] == '*') continue;
                if (i && p[j] != '*') {
                    f[i][j] = f[i - 1][j - 1] && (s[i] == p[j] || p[j] == '.');
                } else if (p[j] == '*') {
                    //因为这里需要看下标为i-1的值因此需要i不为零
                    //因为这里的j下标指向的是*，所以需要与j-1下标的进行匹配
                    f[i][j] = f[i][j - 2] || i && f[i - 1][j] && (s[i] == p[j - 1] || p[j - 1] == '.');
                }
            }
        }
        return f[n][m];
    }
};
```

## 11-20

#### [11. 盛最多水的容器](https://leetcode-cn.com/problems/container-with-most-water/)

本题思路：

首先遇到这种问题，优先考虑双指针算法，双指针需要寻找其规律性简化计算逻辑。因此难点转化为如何移动指针，基于以下两点共识：

- 相同情况下两边的距离越远越好，因此其实从两个端点开始。
- 面积受限于较短边。因此当两边中一边较短时移动较短边。

可以使用反证法证明答案的正确性：本题存在最优解的两个边。假设右边已经到达右侧最优解所在边，左侧还需要移动时，左侧的边必然低于右侧的边。因为当左侧最优解还没到时，此时如果左侧边高于左侧最优解，那么就有更大的面积，存在矛盾，可证。

```cpp
class Solution {
public:
    int maxArea(vector<int> &height) {
        int res = 0;
        for (int i = 0, j = height.size() - 1; i < j;) {
            res = max(res, min(height[i], height[j]) * (j - i));
            height[i] > height[j] ? --j : ++i;
        }
        return res;
    }
};
```



#### [15. 三数之和](https://leetcode-cn.com/problems/3sum/)

本题优化在用一个双指针优化了两层循环，将其从$O(n^2)$降低到$O(2n)$，因此整体时间复杂度为$O(n^2)$。

基于单调性，可以使用试探法，先判断下一个是否满足，满足直接去下一个数，不然用上一个数检验。

针对重复情况，使用形如`j > i + 1 && nums[j] == nums[j - 1]`这样的方式，避免相同位上相同的元素。	

```cpp
class Solution {
public:
    vector <vector<int>> threeSum(vector<int> &nums) {
        vector <vector<int>> res;
        sort(nums.begin(), nums.end());
        for (int i = 0; i < nums.size(); ++i) {
            // 如果起始为就大于0，则之后的数字必然超过0，非题目解
            if (nums[i] > 0) break;
            // 排除重复情况
            if (i && nums[i] == nums[i - 1]) continue;
            for (int j = i + 1, k = nums.size() - 1; j < k; ++j) {
                // 排除重复情况
                if (j > i + 1 && nums[j] == nums[j - 1]) continue;
                // 试探法，如果下一个数满足就用下一个数
                while (j < k - 1 && nums[i] + nums[j] + nums[k - 1] >= 0) --k;
                if (nums[i] + nums[j] + nums[k] == 0) res.push_back({nums[i], nums[j], nums[k]});
            }
        }
        return res;
    }
};
```

#### [18. 四数之和](https://leetcode-cn.com/problems/4sum/)

另外需要注意虽然排序完成后整体为单调递增，但是target为负数时，不能通过`nums[i]>target`进行常数级优化。

```cpp
class Solution {
public:
    vector <vector<int>> fourSum(vector<int> &nums, int target) {
        vector <vector<int>> res;
        sort(nums.begin(), nums.end());
        for (int i = 0; i < nums.size(); ++i) {
            if (i && nums[i] == nums[i - 1]) continue;
            for (int j = i + 1; j < nums.size(); ++j) {
                if (j > i + 1 && nums[j] == nums[j - 1]) continue;
                for (int k = j + 1, l = nums.size() - 1; k < l; ++k) {
                    if (k > j + 1 && nums[k] == nums[k - 1]) continue;
                    while (k < l - 1 && nums[i] + nums[j] >= target - nums[l - 1] - nums[k]) --l;
                    if (nums[i] + nums[j] - target == -nums[k] - nums[l]) {
                        res.push_back({nums[i], nums[j], nums[k], nums[l]});
                    }
                }
            }
        }
        return res;
    }
};
```

### [20. 有效的括号](https://leetcode.cn/problems/valid-parentheses/)

```cpp
class Solution {
public:
    bool isValid(string s) {
        stack<int> q;
        unordered_map<char, char> heap{{']', '['},
                                       {')', '('},
                                       {'}', '{'}};
        for (auto &it: s) {
            if(it=='['||it=='{'||it=='(') q.push(it);
            else{
                if(q.size()&&heap[it]==q.top()) q.pop();
                else return false;
            }
        }
        if(q.size()) return false;
        else return true;
    }
};
```

## 21-30

#### [21.合并两个有序链表](https://leetcode-cn.com/problems/merge-two-sorted-lists/)

```cpp
/**
 * 本题的解题思路：
 * 本题就是简单的二路归并问题
 */
class Solution {
public:
    ListNode *mergeTwoLists(ListNode *l1, ListNode *l2) {
        //结果链表的头节点设置为虚拟节点避免
        auto dummy = new ListNode(-1), tail = dummy;
        while (l1 && l2) {
            if (l1->val < l2->val) {
                tail = tail->next = l1;
                l1 = l1->next;
            } else {
                tail = tail->next = l2;
                l2 = l2->next;
            }
        }
        if (l1) tail->next = l1;
        if (l2) tail->next = l2;
        return dummy->next;
    }
};
```

#### [22.括号生成](https://leetcode-cn.com/problems/generate-parentheses/)

```cpp
/**
 * 本题的解题思路：
 * 满足要求的小括号集合必然满足以下两个性质：
 * 1. 任意前缀中'('数量必然>=')'的数量
 * 2. 左右括号数量相等
 * 如果本题不是需要输出所有方案，而是只求数量的话，可以通过卡特兰数进行计算
 */
class Solution {
public:
    vector <string> ans;

    vector <string> generateParenthesis(int n) {
        dfs(n, 0, 0, "");
        return ans;
    }

    void dfs(int n, int lc, int rc, string seq) {
        if (lc == n && rc == n) ans.push_back(seq);
        else {
            if (lc < n) dfs(n, lc + 1, rc, seq + '(');
            if (rc < n && lc > rc) dfs(n, lc, rc + 1, seq + ')');
        }
    }
};
```

#### [23.合并K个升序链表](https://leetcode.cn/problems/merge-k-sorted-lists/)

```cpp
class Solution {
public:
    ListNode *mergeKLists(vector<ListNode *> &lists) {
        auto dummy = new ListNode(-1), tail = dummy;
        auto cmp = [](const ListNode *a, const ListNode *b) { return a->val > b->val; };
        priority_queue < ListNode * , vector < ListNode * >, decltype(cmp) > heap(cmp);
        for (auto it: lists) if (it) heap.push(it);
        while (heap.size()) {
            auto it = heap.top();
            heap.pop();
            tail = tail->next = it;
            if (it->next) heap.push(it->next);
        }
        return dummy->next;
    }
};
```

#### [25. K 个一组翻转链表](https://leetcode-cn.com/problems/reverse-nodes-in-k-group/)

```cpp
class Solution {
public:
    ListNode *reverseKGroup(ListNode *head, int k) {
        // 保存整个链表的头指针
        auto dummy = new ListNode(-1);
        dummy->next = head;

        for (auto p = dummy;;) {
            // 检查是否存在k个元素，不存在则退出循环
            auto q = p;
            for (int i = 0; i < k && q; i++) q = q->next;
            if (!q) break;

            // 交换中间元素的next
            auto a = p->next, b = a->next;
            for (int i = 0; i < k - 1; i++) {
                auto c = b->next;
                b->next = a;
                a = b, b = c;
            }
            // 前后元素旋转
            auto c = p->next;
            p->next = a, c->next = b;
            p = c;
        }
        return dummy->next;
    }
};
```

#### [28. 实现 strStr()](https://leetcode-cn.com/problems/implement-strstr/)

```cpp
class Solution {
public:
    vector<int> ne;

    void build(const string &needle) {
        int n = needle.length();
        // 这里最后多出来的元素用来标记匹配成功的情况
        ne.resize(n + 1);
        for (int i = 0, j = ne[0] = -1; i < n; ne[++i] = ++j) {
            // ~j表示j为-1的情况
            while (~j && needle[i] != needle[j]) j = ne[j];
        }
    }

    int strStr(string haystack, string needle) {
        if (!needle.length()) return 0;
        int res = -1, m = haystack.length(), n = needle.length();
        build(needle);
        for (int i = 0, j = 0; i < m; ++i) {
            while (j > 0 && haystack[i] != needle[j]) j = ne[j];
            if (haystack[i] == needle[j]) ++j;
            if (j == n) {
                res = i - n + 1;
                break;
                // 如果是查找多个
                // 只需要j=ne[j];即可，ne[n]==0，即从匹配串的第0位开始重新匹配
            }
        }
        return res;
    }
};
```



## 31-40

### [31. 下一个排列](https://leetcode.cn/problems/next-permutation/)

```cpp
// 本题的解题思路在于利用两次遍历，找到转折点。
// 首先是，先从后往前找第一个非递增的点，如果一直扫描到索引0，说明当前整体逆序，此时需要直接反转整个数组
// 否则，需要在找到的索引j到nums.size()-1之间找到大于nums[j-1]的最小值，交换nums[i-1]和nums[j-1]，再反转nums[i]之后的所有数
class Solution {
public:
    void nextPermutation(vector<int> &nums) {
        int i = nums.size() - 1;
        while (i > 0 && nums[i] <= nums[i - 1]) i -= 1;
        if (!i) reverse(nums.begin(), nums.end());
        else {
            int j = i;
            while (i < nums.size() && nums[i] > nums[j - 1]) i += 1;
            swap(nums[j - 1], nums[i - 1]);
            reverse(nums.begin() + j, nums.end());
        }
    }
};
```



[33.搜索旋转排序数组](https://leetcode.cn/problems/search-in-rotated-sorted-array/description/)

```cpp
// 本质上还是两个升序的数组，因此先二分找到分界点，然后在二分找到target位置
class Solution {
public:
    int search(vector<int> &nums, int target) {
        if (nums.empty()) return -1;
        int l = 0, r = nums.size() - 1;
        while (l < r) {
            int mid = l + r + 1 >> 1;
            if (nums[mid] >= nums[0]) l = mid;
            else r = mid - 1;
        }
        if (target >= nums[0]) l = 0;
        else l = r + 1, r = nums.size() - 1;
        while (l < r) {
            int mid = l + r >> 1;
            if (nums[mid] >= target) r = mid;
            else l = mid + 1;
        }
        if (nums[r] == target) return r;
        return -1;
    }
};
```

[34. 在排序数组中查找元素的第一个和最后一个位置](https://leetcode.cn/problems/find-first-and-last-position-of-element-in-sorted-array/)

```cpp
// 本题需要注意二分条件的选择，起点>=，终点<=以及查询到之后left需要保存下来，以免之后二分修改。
// 避免mid越界，可以l+(r-l)/2
class Solution {
public:
    vector<int> searchRange(vector<int> &nums, int target) {
        int len = nums.size(), l = 0, r = nums.size() - 1, mid = 0;
        if (len == 0) return {-1, -1};
        while (l < r) {
            mid = l + r >> 1;
            if (nums[mid] >= target) r = mid;
            else l = mid + 1;
        }
        if (nums[r] != target) return {-1, -1};
        int left = l;
        r = nums.size() - 1;
        while (l < r) {
            mid = l + r + 1 >> 1;
            if (nums[mid] <= target) l = mid;
            else r = mid - 1;
        }
        return {left, r};
    }
};
```



## 41-50

### [42. 接雨水](https://leetcode.cn/problems/trapping-rain-water/description/)

```cpp
// 本题使用了单调栈实现，last存上一个点的高度，需要注意的是这里stk存的是索引，而不是高度
class Solution {
public:
    int trap(vector<int> &height) {
        int res = 0;
        stack<int> stk;
        for (int i = 0; i < height.size(); ++i) {
            int last = 0;
            while (stk.size() && height[i] >= height[stk.top()]) {
                res += (height[stk.top()] - last) * (i - stk.top() - 1);
                last = height[stk.top()];
                stk.pop();
            }
            if (stk.size()) res += (height[i] - last) * (i - stk.top() - 1);
            stk.push(i);
        }
        return res;
    }
};
// 双指针
class Solution {
public:
    int trap(vector<int>& height) {
        int l=0,r=height.size()-1,res=0,pre_max=0,suf_max=0;
        while(l<=r){
            pre_max=max(pre_max,height[l]);
            suf_max=max(suf_max,height[r]);
            if(pre_max<suf_max){
                res+=pre_max-height[l];
                l+=1;
            }else{
                res+=suf_max-height[r];
                r-=1;
            }
        }
        return res;
    }
};
```



#### [43. 字符串相乘](https://leetcode-cn.com/problems/multiply-strings/)

```cpp
class Solution {
public:
    string multiply(string num1, string num2) {
        vector<int> A, B;
        int n = num1.size(), m = num2.size();
        vector<int> C(n + m);
        for (int i = n - 1; i >= 0; i--) A.push_back(num1[i] - '0');
        for (int i = m - 1; i >= 0; i--) B.push_back(num2[i] - '0');
        for (int i = 0; i < n; i++) {
            for (int j = 0; j < m; j++) {
              // 注意这里的是+=，因为这里C[i+j]的值可能需要被多次计算
                C[i + j] += A[i] * B[j];
            }
        }

        //将结果集中数据进行进位与取模
        for (int i = 0, t = 0; i < C.size(); i++) {
            t += C[i];
            C[i] = t % 10;
            t /= 10;
        }
        //去前导0
        int k = C.size() - 1;
        while (k > 0 && !C[k]) k--;
        string res = "";
        while (k >= 0) res += C[k--] + '0';
        return res;
    }
};
```

### [46. 全排列](https://leetcode.cn/problems/permutations/description/)

```cpp
// 本题单纯的dfs没办法生成path，因为索引i可能不是path存放的索引，所以需要u来标记path中的存放位置
class Solution {
public:
    vector <vector<int>> ans;
    vector<int> path;
    vector<bool> st;

    vector <vector<int>> permute(vector<int> &nums) {
        path = vector<int>(nums.size());
        st = vector<bool>(nums.size(), false);
        dfs(nums, 0);
        return ans;
    }

    void dfs(vector<int> &nums, int u) {
        if (u == nums.size()) {
            ans.push_back(path);
            return;
        }
        for (int i = 0; i < nums.size(); ++i) {
            if (!st[i]) {
                st[i] = true;
                path[u] = nums[i];
                dfs(nums, u + 1);
                st[i] = false;
            }
        }
    }
};
```



## 51-60

#### [51.N皇后](https://leetcode-cn.com/problems/n-queens)

![n皇后截距计算.jpg](https://cdn.jsdelivr.net/gh/weiyouwozuiku/weiyouwozuiku.github.io@src/source/_posts/算法/Leetcode合集/Leetcode算法题1-100/n皇后截距计算.jpg)

由图可知，当y=x+b是存在截距为负数的情况，而数组下表不存在负数。因此+n，保证其为正数。

```cpp
class Solution {
public:
    int n;
    vector<bool> col, dg, udg;
    vector <string> path;
    vector <vector<string>> ans;

    vector <vector<string>> solveNQueens(int _n) {
        n = _n;
        col = vector<bool>(n);
        dg = udg = vector<bool>(2 * n);
        path = vector<string>(n, string(n, '.'));
        dfs(0);
        return ans;
    }

    void dfs(int u) {
        if (u == n) ans.push_back(path);
        else {
            for (int i = 0; i < n; i++) {
                if (!col[i] && !dg[u - i + n] && !udg[u + i]) {
                    col[i] = dg[u - i + n] = udg[u + i] = true;
                    path[u][i] = 'Q';
                    dfs(u + 1);
                    path[u][i] = '.';
                    col[i] = dg[u - i + n] = udg[u + i] = false;
                }
            }
        }
    }
};
```

#### [52.N皇后II](https://leetcode-cn.com/problems/n-queens-ii)

```cpp
class Solution {
public:
    int n;
    vector<bool> col, dg, udg;
    int res;

    int totalNQueens(int _n) {
        n = _n;
        res = 0;
        col = vector<bool>(n);
        dg = udg = vector<bool>(2 * n);
        dfs(0);
        return res;
    }

    void dfs(int u) {
        if (u == n) {
            ++res;
        } else {
            for (int i = 0; i < n; ++i) {
                if (!col[i] && !dg[u - i + n] && !udg[u + i]) {
                    col[i] = dg[u - i + n] = udg[u + i] = true;
                    dfs(u + 1);
                    col[i] = dg[u - i + n] = udg[u + i] = false;
                }
            }
        }
    }
};
```

#### [53.最大子数组和](https://leetcode-cn.com/problems/maximum-subarray)

```cpp
// 这里采用了动态规划的方式。f(i)表示以nums[i]为最后一个元素的数组和
// f(i)可以分为两类只有一个元素的和多个元素累加的
// 一个元素的就是nums[i]，另一个就是f[i-1]+nums[i]
// 两种情况都包含nums[i]，提取出来就成了nums[i]+max(0,f(i-1))
class Solution {
public:
    int maxSubArray(vector<int> &nums) {
        int res = INT_MIN;
        for (int i = 0,last=0; i < nums.size(); ++i) {
            last=nums[i]+max(0,last);
            res=max(last,res);
        }
        return res;
    }
};
```

```cpp
// 分治的思想实现，将整个数组拆成两个子数组，分别计算其中的前缀和
class Solution {
public:
    struct Node{
        int sum,s,ls,rs;
    };
    Node build(vector<int>& nums,int l,int r){
        if(l==r) {
            int v=max(nums[l],0);
            return {nums[l],v,v,v};
        }
        int mid=l+r>>1;
        Node res;
        auto L=build(nums,l,mid),R=build(nums,mid+1,r);
        res.sum=L.sum+R.sum;
        res.s=max(max(L.s,R.s),L.rs+R.ls);
        res.ls=max(L.ls,L.sum+R.ls);
        res.rs=max(R.rs,R.sum+L.rs);
        return res;
    }
    int maxSubArray(vector<int>& nums) {
        // 特判全是负数的情况，因为结果中不允许空数组
        int tmp=INT_MIN;
        for(auto &it:nums){
            tmp=max(it,tmp);
        }
        if(tmp<0) return tmp;
        auto res=build(nums,0,nums.size()-1);
        return res.s;
    }
};
```

### [54. 螺旋矩阵](https://leetcode.cn/problems/spiral-matrix/description/)

```cpp
class Solution {
public:
    vector<int> spiralOrder(vector <vector<int>> &matrix) {
        vector<int> res;
        int n = matrix.size();
        if (!n) return {};
        int m = matrix[0].size();
        int dx[] = {0, 1, 0, -1}, dy[] = {1, 0, -1, 0};
        for (int i = 0, x = 0, y = 0, d = 0; i < n * m; ++i) {
            res.push_back(matrix[x][y]);
            matrix[x][y]=INT_MAX;
            st[x][y] = true;
            int a = x + dx[d], b = y + dy[d];
            // 需要注意这里的n和m
            if (a < 0 || a >= n || b < 0 || b >= m || matrix[a][b]==INT_MAX) {
                d = (d + 1) % 4;
                a = x + dx[d], b = y + dy[d];
            }
            x = a, y = b;
        }
        return res;
    }
};
```



#### [56. 合并区间](https://leetcode-cn.com/problems/merge-intervals/)

```cpp
class Solution {
public:
    vector <vector<int>> merge(vector <vector<int>> &intervals) {
        if (intervals.size() == 0) return intervals;
        vector <vector<int>> ans;
        // 所有区间按照左端点排序
        sort(intervals.begin(), intervals.end());
        int st = intervals[0][0], end = intervals[0][1];
        // 排序完之后，之后出现三种情况
        for (auto item: intervals) {
            // 1. 起始点就与上一个区间没有重叠
            if (item[0] > end) {
                ans.push_back({st, end});
                st = item[0];
                end = item[1];
            } else {
                // 2. 区间结束点在当前区间之外
                if (end < item[1]) end = item[1];
                // 3. 区间结束点在当前区间内，不用处理
            }
        }
        // 将最后的区间压入ans队列
        ans.push_back({st, end});
        return ans;
    }
};
```



## 61-70

#### [61. 旋转链表](https://leetcode-cn.com/problems/rotate-list/)

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

#### [69. Sqrt(x)](https://leetcode-cn.com/problems/sqrtx/)

```cpp
class Solution {
public:
    int mySqrt(int x) {
        if (x == 0) return 0;
        if (x == 1) return 1;
        int l = 1, r = x, mid;
        while (l < r) {
            // 这里直接写l+r可能会越界，但是这里将1替换成1ll会变成long long类型，避免越界
            // 或者写成l+(r-l)/2
            mid = l + 1ll + r >> 1;
            // 这里之所以写成这样是为了防止越界
            if (mid <= x / mid) {
                l = mid;
            } else {
                r = mid - 1;
            }
        }
        return l;
    }
};
```

## 71-80

[71. 简化路径](https://leetcode.cn/problems/simplify-path/)

```cpp
class Solution {
public:
    string simplifyPath(string path) {
        string res, name;
        // 改成同一个格式，便于处理
        if (path.back() != '/')path += '/';
        for (auto it: path) {
            if (it != '/') name += it;
            else {
                if (name == "..") {
                    while (res.size() && res.back() != '/') res.pop_back();
                    if (res.size()) res.pop_back();
                } else if (name != "." && name != "")
                    res += '/' + name;
                name.clear();
            }
        }
        if (res.empty()) res = "/";
        return res;
    }
};
```

[72. 编辑距离](https://leetcode.cn/problems/edit-distance/)

```cpp
class Solution {
    // 本题dp,分为几种情况：
    // 采用f(i,j)表示分别表示:i word1的字符串索引位置，j word2的字符串索引位置
    // 1. f(i-1,j):word1添加i位置的字符等于word2 word2删除j位置的字符等于word2
    // 2. f(i,j-1):word2添加j位置的字符等于word1 word1删除j位置的字符等于word2
    // 3. f(i-1,j-1): word1中[0,i-1]和word2[0,j-1]相同 word1中的i位置和word2中的j位置不同则+1，否则不加
public:
    int minDistance(string word1, string word2) {
        word1 = ' ' + word1, word2 = ' ' + word2;
        int n = word1.size(), m = word2.size();
        vector <vector<int>> f(n + 1, vector<int>(m + 1));
        for (int i = 0; i <= n; ++i) f[i][0] = i;
        for (int i = 0; i <= m; ++i) f[0][i] = i;
        for (int i = 1; i <= n; ++i) {
            for (int j = 1; j <= m; ++j) {
                f[i][j] = min(f[i - 1][j], f[i][j - 1]) + 1;
                int t = word1[i] != word2[j];
                f[i][j] = min(f[i][j], f[i - 1][j - 1] + t);
            }
        }
        return f[n][m];
    }
};
```



## 81-90

#### [82. 删除排序链表中的重复元素 II](https://leetcode-cn.com/problems/remove-duplicates-from-sorted-list-ii/)

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

#### [83. 删除排序链表中的重复元素](https://leetcode-cn.com/problems/remove-duplicates-from-sorted-list/)

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

### [88. 合并两个有序数组](https://leetcode.cn/problems/merge-sorted-array/description/)

```cpp
// 本题的解题难点在于怎么移动元素，因为采用的vector，移动需要使用额外空间
// 但是本题nums1长度是n+m-1，因此可以从后往前排序。
class Solution {
public:
    void merge(vector<int>& nums1, int m, vector<int>& nums2, int n) {
        int i=m-1,j=n-1,len=n+m-1;
        while(i>=0&&j>=0){
            if(nums1[i]>=nums2[j]) nums1[len--]=nums1[i--];
            else nums1[len--]=nums2[j--];
        }
        while(j>=0) nums1[len--]=nums2[j--];
    }
};
```



#### [90. 子集 II](https://leetcode-cn.com/problems/subsets-ii/)

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

## 91-100

### [92. 反转链表 II](https://leetcode.cn/problems/reverse-linked-list-ii/description/)

```cpp
class Solution {
public:
    ListNode *reverseBetween(ListNode *head, int left, int right) {
        auto dummy = new ListNode(-1), a = dummy;
        dummy->next = head;
        for (int i = 1; i < left; ++i) a = a->next;
        auto b = a->next, c = b->next;
        for (int i = 0; i < right - left; ++i) {
            auto d = c->next;
            c->next = b;
            b = c, c = d;
        }
        a->next->next = c, a->next = b;
        return dummy->next;
    }
};
```

