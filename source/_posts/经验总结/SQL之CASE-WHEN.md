---
title: SQL之CASE WHEN
author: 不二
img: >-
  https://cdn.jsdelivr.net/gh/weiyouwozuiku/weiyouwozuiku.github.io@src/source/_posts/PageImg/经验总结/SQL之CASE-WHEN.jpg
mathjax: true
date: 2021-12-24 09:28:38
tags: SQL
categories: 经验总结
---

## 用法

**case函数只返回第一个符合条件的值，剩下的case部分将会被自动忽略。如果省略了ELSE子句，则返回NULL。**

### 简单case函数

```sql
--简单case函数
case sex
  when '1' then '男'
  when '2' then '女’
  else '其他' end
```

###  case搜索函数

```sql
case when sex = '1' then '男'
     when sex = '2' then '女'
     else '其他' end  
     
CASE WHEN score IS NULL THEN '缺席考试' ELSE '正常' END
```

### sum和case结合使用

```sql
select
  2    sum(case u.sex when 1 then 1 else 0 end)男性,
  3    sum(case u.sex when 2 then 1 else 0 end)女性,
  4    sum(case when u.sex <>1 and u.sex<>2 then 1 else 0 end)性别为空
  5  from users u;
 
        男性         女性       性别为空
---------- ---------- ----------
         3          2          0
```



## 参考资料

1. 博客园，[SQL之case when then用法](https://www.cnblogs.com/Richardzhu/p/3571670.html)，2014
2. 知乎，[SQL之CASE WHEN用法详解](https://zhuanlan.zhihu.com/p/63333847)，2019
