---
title: 算法小Tip
author: 不二
img: >-
  https://cdn.jsdelivr.net/gh/weiyouwozuiku/weiyouwozuiku.github.io@src/source/_posts/PageImg/算法/算法小Tip.jpg
mathjax: true
date: 2023-01-07 15:39:40
tags: Cpp 
categories: 算法
---

## c++的io

### cin和scanf速度

cin、cout之所以效率低是因为先把要输出的东西存入缓冲区，再输出，导致效率降低，而``std::ios::sync_with_stdio(false);``可以来打消iostream的输入输出缓存，可以节省许多时间，使效率与scanf与printf相差无几。这样做了之后要注意不要同时混用cout和printf 之类。

应注意的是scanf与printf使用的头文件应是stdio.h而不是 iostream。

在默认的情况下cin绑定的是cout，每次执行 << 操作符的时候都要调用flush，这样会增加IO负担。可以通过tie(0)（0表示NULL）来解除cin与cout的绑定，进一步加快执行效率。

`cin`和`scanf`的效率差距在一百万量级下比较明显，少于这个量级这二者没明显区别，随便用。

### sync_with_stdio

这个函数是一个“是否兼容stdio”的开关，C++为了兼容C，保证程序在使用了std::printf和std::cout的时候不发生混乱，将输出流绑到了一起。

### tie

tie是将两个stream绑定的函数，空参数的话返回当前的输出流指针。如`std::cin.tie()`，默认返回cout，数据类型为std::ostream。

## java的io

 java.util.Scanner类是一个简单的文本扫描类，它可以解析基本数据类型和字符串。它本质上是使用正则表达式去读取不同的数据类型。

Java.io.BufferedReader类为了能够高效的读取字符序列，从字符输入流和字符缓冲区读取文本。

Java的标准输入串是System.in。System.in提供的read方法是通过字节来读取数据的，不好处理。

java.util包中的Scanner读取数据是按空格符（这其中包括空格键，Tab键，Enter键）来分割数据的。只要遇到其中之一，Scanner的方法就会返回下一个输入（当然nextLine()方法的结束符为换行符，它    return 0;
}会返回换行符之前的数据）

BufferReader位于java.io包中，使用BufferReader就相对来说没有那么多方法来让你选择！读取数据比较固定，这样格式也就相对来说比较单一，只要记住就这一个读取数据的方法。`BufferedReader br = new BufferedReader (newInputStreamReader(System.in));`。这个BufferReader对象通过readLine()；方法来读取数据，readLine()是按Enter回车来读取一行数据的，只要在回车键之前的都会被readLine()方法返回。

readLine()方法返回的是字符串，因此要使用BufferReader输入一些字符之外的类型的数据，就要相对比较麻烦，需要通过一些XXXX.parseXxx()；来转换相应的数据类型，（例如：int类型的用Integer.parseInt（需要转换的字符串））。虽然麻烦一些，但通过BufferReader读入的效率要比Scanner高一倍，这个差距可想而知，而且读取的数据越多，效果就越明显。需要注意的是使用BufferReader对象的readLine()方法必须处理java.io.IOException异常。

### 对比

- Scanner提供了一系列nextXxx()方法，当我们确定输入的数据类型时，使用Scanner更加方便。也正是因为这个BufferedReader相对于Scanner来说要快一点，因为Scanner对输入数据进行类解析，而BufferedReader只是简单地读取字符序列
- BufferedReader是支持同步的，而Scanner不支持。如果我们处理多线程程序，BufferedReader应当使用。
- Scanner和BufferedReader都设置了缓冲区，Scanner有很少的缓冲区(1KB字符缓冲)相对于BufferedReader(8KB字节缓冲)，但是这是绰绰有余的。
- Scanner输入的一个问题：在Scanner类中如果我们在任何7个nextXXX()方法之后调用nextLine()方法，这nextLine()方法不能够从控制台读取任何内容，并且，这游标不会进入控制台，它将跳过这一步。nextXXX()方法包括nextInt()，nextFloat()， nextByte()，nextShort()，nextDouble()，nextLong()，next()。在BufferReader类中就没有那种问题。这种问题仅仅出现在Scanner类中，由于nextXXX()方法忽略换行符，但是nextLine()并不忽略它。如果我们在nextXXX()方法和nextLine()方法之间使用超过一个以上的nextLine()方法，这个问题将不会出现了；因为nextLine()把换行符消耗了。

## 稳定性

通俗地讲就是能保证排序前两个相等的数据其在序列中的先后位置顺序与排序后它们两个先后位置顺序相同。即：如，如果$A_i==A_j$，$A_i$原来在$A_j$位置前，排序后$A_i$仍然是在$A_j$位置前。

## 短码

判断i不等于-1：`if (~i)`

判断i不能0：`if(i)`

## 反推算法复杂度

一般ACM或者笔试题的时间限制是1秒或2秒。在这种情况下，C++代码中的操作次数控制在$10^710^8$为最佳。

下面给出在不同数据范围下，代码的时间复杂度和算法该如何选择：

1. $n\leq 30$，指数级别，dfs+剪枝，状态压缩DP
2. $n\leq 100 \rightarrow O(n^2)$，floyd,dp,高斯消元
3. $n\leq 1000 \rightarrow O(n^2),O(n^2logn)$，dp,二元，朴素版Dijkstra，朴素版Prim，Bellman-Ford
4. $n\leq 10000 \rightarrow O(n*\sqrt{n})$，块状链表，分块，莫队
5. $n\leq 100000 \rightarrow O(nlogn)$，各种sort,线段树，树状数组，set/map,heap,拓朴排序，dijkstra+heap，prim+heap,spfa,求凸包，求半平面交，二分，CDQ分治，整体二分
6. $n\leq 1000000 \rightarrow O(n)$，以及常数较小的$O(nlogn)$算法，单调队列，hash,双指针扫描，并查集，kmp,AC自动机，常数较小的$O(nlogn)$的做法：sort,树状数组，heap,dijkstra,spfa
7. $n\leq 10000000 \rightarrow O(n)$，双指针扫描，kmp,AC自动机，线性筛素数
8. $n \leq 10^9 \rightarrow O(\sqrt{n})$，判断质数
9. $n \leq 10^{18} \rightarrow O(logn)$，最大公约数，快速幂
10. $n \leq 10^{1000} \rightarrow O((logn)^2)$，高精度加减乘除
11. $n \leq 10^{100000} \rightarrow O(logk * loglogk)$，k表示位数，高精度加减，FFT/NTT

## accumulate函数

accumulate定义在#include<numeric>中，作用有两个，一个是累加求和，另一个是自定义类型数据的处理。

### 累加求和

```cpp
int total = accumulate(nums.begin(), nums.end(), 0);
```

accumulate带有三个形参：头两个形参指定要累加的元素范围，第三个形参则是累加的初值。

可以实现将`vector<string>`类型的元素链接起来。

```cpp
string sum = accumulate(v.begin() , v.end() , string(" "));  
```

这个函数调用的效果是：从空字符串开始，把vec里的每个元素连接成一个字符串。

### 自定义数据类型的处理

对于自定义数据类型，我们就需要自己动手写一个回调函数来实现自定义数据的处理，然后让它作为accumulate()的第四个参数，accumulate()的原型为

```javascript
template<class _InIt, class _Ty,  class _Fn2> 
inline _Ty _Accumulate(_InIt _First, _InIt _Last, _Ty _Val, _Fn2 _Func)  
{   // return sum of _Val and all in [_First, _Last), using _Func  
    for (; _First != _Last; ++_First)  
        _Val = _Func(_Val, *_First);  
    return (_Val);  
} 
```

例如：

```cpp
struct Grade  
{  
    string name;  
    int grade;  
};  
  
int main()  
{  
    Grade subject[3] = {  
        { "English", 80 },  
        { "Biology", 70 },  
        { "History", 90 }  
    };  
  
    int sum = accumulate(subject, subject + 3, 0, [](int a, Grade b){return a + b.grade; });  
}
```

## 题解技巧

### 字符读取

有点时候需要去读表示操作的单字符串，为了避免`scanf("%c")`中可能读取到换行或者空格，推荐直接`scanf("%s")`，读取字符串会忽略换行之类的操作。然后获取`op[0]`获取操作。

### strcmp

`strcmp(str1,str2)`需要引入<string>头文件。其作用是对两个字符串作比较。

返回值：

- str1小于str2，返回负数或者-1
- str1等于str2，返回0
- str1大于str2，返回正数或者1

### puts

puts函数可以一次输出一个字符串指针对应的元素，并末尾追加换行。

## 无穷大的表示[^1]

**以下说法只适用于算法题，实际工程中不可这么用**。

如果问题中各数据的范围明确，那么无穷大的设定不是问题，在不明确的情况下，很多程序员都取0x7fffffff作为无穷大，因为这是32-bit int的最大值。如果这个无穷大只用于一般的比较（比如求最小值时min变量的初值），那么0x7fffffff确实是一个完美的选择，但是在更多的情况下，0x7fffffff并不是一个好的选择。

- 很多时候我们并不只是单纯拿无穷大来作比较，而是会运算后再做比较，例如在大部分最短路径算法中都会使用的松弛操作：

  `if (d[u]+w[u][v]<d[v]) d[v]=d[u]+w[u][v];`
  我们知道如果u,v之间没有边，那么`w[u][v]=INF`，如果我们的INF取0x7fffffff，那么`d[u]+w[u][v]`会溢出而变成负数，我们的松弛操作便出错了，更一般的说，0x7fffffff不能满足“无穷大加一个有穷的数依然是无穷大”，它变成了一个很小的负数。

- 除了要满足加上一个常数依然是无穷大之外，我们的常量还应该满足“无穷大加无穷大依然是无穷大”，至少两个无穷大相加不应该出现灾难性的错误，这一点上0x7fffffff依然不能满足我们。

所以我们需要一个更好的家伙来顶替0x7fffffff，最严谨的办法当然是对无穷大进行特别处理而不是找一个很大很大的常量来代替它（或者说模拟它），但是这样会让我们的编程过程变得很麻烦。常见的方法是**采用0x3f3f3f3f**来表示无穷大。原因如下：

- 0x3f3f3f3f的十进制是1061109567，也就是$10^9$级别的（和0x7fffffff一个数量级），而一般场合下的数据都是小于$10^9$的，所以它可以作为无穷大使用而不致出现数据大于无穷大的情形。
- 另一方面，由于一般的数据都不会大于$10^9$，所以当我们把无穷大加上一个数据时，它并不会溢出（这就满足了“无穷大加一个有穷的数依然是无穷大”），事实上`0x3f3f3f3f+0x3f3f3f3f=2122219134`，这非常大但却没有超过32-bit int的表示范围，所以`0x3f3f3f3f`还满足了我们“无穷大加无穷大还是无穷大”的需求。
- 最后，0x3f3f3f3f还能给我们带来一个意想不到的额外好处：如果我们想要将某个数组清零，我们通常会使用memset(a,0,sizeof(a))这样的代码来实现（方便而高效），但是当我们想将某个数组全部赋值为无穷大时（例如解决图论问题时邻接矩阵的初始化），就不能使用memset函数而得自己写循环了（写这些不重要的代码真的很痛苦），我们知道这是因为memset是按字节操作的，它能够对数组清零是因为0的每个字节都是0，现在好了，如果我们将无穷大设为0x3f3f3f3f，那么奇迹就发生了，0x3f3f3f3f的每个字节都是0x3f！所以要把一段内存全部置为无穷大，我们只需要memset(a,0x3f,sizeof(a))。

## 优先队列的自定义排序规则[^2]

std::priority_queue 是C++标准库提供的优先队列（最大堆）实现，位于头文件

默认情况下要求元素有“小于”运算，取堆顶，返回最大值。

可以通过模板参数调整排序方式让其返回最小值，或者为自定义类型定义排序方式。

```cpp
template <class T, class Container = vector<T>, class Compare = less<typename Container::value_type> >
class priority_queue;
```

模板参数：

- T是数据类型
- Container是维护最大（小）堆使用的容器类型
- Compare是一个function object的类型，定义了排序方式

### 使用比较器类定义优先队列

```cpp
#include <queue>
#include <vector>
using namespace std;

struct Student { // 学生
    int id; // 学号
    int height; // 身高
};

int main()
{
    // 定义比较器
    struct Cmp {
        bool operator() (const Node& a, const Node& b) {
            return a.height < b.height;
        }
    };
    // 定义优先队列
    priority_queue<Student, vector<Student>, Cmp> PQ;
}
```

### 使用lambda表达式排序

```cpp
vector<Student> students = {...};
std::sort(students.begin(), students.end(), [](const Student& a, const Student& b){
	return a.height < b.height;
});

// 等价的
std::sort(students.begin(), students.end(), Cmp());
```

### 使用lambda表达式定义优先队列

实际上priority_queue有一个构造函数，可以传递一个比较对象，如果不传递就会用模板参数定义默认的比较对象。

```cpp
explicit priority_queue (const Compare& comp = Compare(), Container&& ctnr = Container());
```

```cpp
// 通过lambda表达式定义序
auto cmp = [](const Node& a, const Node& b) {
    return a.height < b.height;
};
priority_queue<Node, vector<Node>, decltype(cmp)> PQ(cmp);
```

**看上去和通过定义比较器定义优先队列似乎差不多，实际上lambda表达式的魅力在于可以访问当前上下文中的其他变量。**

例如：假设我们有一个 `vector<Student>`存储着学生信息，我们想定义一个存储学号的优先队列priority_queue，依然按照身高对其中学号排序：

```cpp
vector<Student> students = {...};
unordered_map<int, Student> id2stu;
for(auto& stu: students) {
	id2stu[stu.id] = stu;
}

// 我们可以很方便地把id2stu绑定到lambda表达式中用来排序
auto cmp = [&](int a, int b) {
    return id2stu[a].height < id2stu[b].height;
};
priority_queue<int, vector<int>, decltype(cmp)> PQ(cmp);
```

如果用定义比较器类的方式则需要通过构造函数传递id2stu的引用，然后绑定给成员变量。

```cpp
vector<Student> students = {...};
unordered_map<int, Student> id2stu;
for(auto& stu: students) {
	id2stu[stu.id] = stu;
}

struct Cmp {
    const unordered_map<int, Student>& id2stu;
	Cmp(const unordered_map<int, Student>& id2stu) : id2stu(id2stu) {}
    bool operator() (int a, int b) const {
	    return id2stu[a].height < id2stu[b].height;
    }
};
priority_queue<int, vector<int>, Cmp> PQ;
```

## 参考文献

[^1]: CSDN，[使用0x3f3f3f表示无穷大](https://blog.csdn.net/L_apple8/article/details/52525752) 

[^2]:[std::priority_queue + lambda表达式 + decltype](https://kyo86.com/2022/06/29/priority-queue-lambda-decltype/),2022

