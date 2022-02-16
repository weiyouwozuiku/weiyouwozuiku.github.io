---
title: Leetcode算法题1-100
author: 不二
img: >-
  https://cdn.jsdelivr.net/gh/weiyouwozuiku/weiyouwozuiku.github.io@src/source/_posts/PageImg/算法/Leetcode算法题1-100.jpeg
mathjax: true
date: 2022-01-16 17:40:00
tags: Cpp
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

## 41-50

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



## 51-60

#### [51.N皇后](https://leetcode-cn.com/problems/n-queens)

#### [52.N皇后II](https://leetcode-cn.com/problems/n-queens-ii)

#### [53.](https://leetcode-cn.com/problems/maximum-subarray)

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

