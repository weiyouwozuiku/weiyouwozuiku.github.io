---
title: Python与数据库
author: 不二
mathjax: true
date: 2021-03-21 18:16:34
tags: Python
img:
categories:  程序设计
---

## Mysql

### 使用

- 安装mysql-connect的包

代码如下：

```Python
import mysql.connector

config={'host':'','port':3306,'user':"",'password':'','database':''}
try:
    con=mysql.connector.connect(**config)
	cursor=con.cursor()
	cursor.execute(sql)
	cursor.fetchone()或cursor.fetchall()
except Exception as e:
    print(e)
finally:
    if "con" in dir():
        con.close()
```

### 事务控制

`Connector`提供了简单的事务控制函数

```Python
con.start_transaction([事务隔离级别])
#事务的提交需要手动提交，不会自动触发
con.commit()
con.rollback()
```

### 数据库连接池

```Python
import mysql.connector.pooling
config={....}
pool=mysql.connector.pooling.MySQLConnetcionPool(**config,pool_size=10)
con=pool.get_connection()
```

## Tip

### SQL注入

由于SQL是解释型语言，存在SQL注入问题。

此时可以利用SQL预编译机制抵御注入攻击。

SQL预编译机制：数据库提前将SQL语句编译成二进制，避免了词法分析，这样反复执行同一条SQL效率提升。

SQL语句编译的过程中，关键字已经被解析过了，所以向编译后的SQL语句传入参数，都被当作字符串处理，数据库不会解析其中注入的SQL语句。

使用方式如下：

```Python
sql="select * from user where username=%s and password=%s"
cursor.execute(sql,(username,password))#此时两个数作为参数传入已经编译好了的sql语句中
```

