---
title: Redis笔记
author: 不二
img: >-
  https://cdn.jsdelivr.net/gh/weiyouwozuiku/weiyouwozuiku.github.io@src/source/_posts/PageImg/KV存储/Redis学习笔记.png
mathjax: true
date: 2024-09-08 19:08:18
tags: Redis
categories: KV存储

---

## 常见应用

### 分布式锁

`set key value ex 秒数 nx`

#### 客户端加锁失败

三种方式: 

- 直接抛出异常,通知用户稍后重试
- sleep一会重试
- 将请求转移到延时队列,一会儿再重试

#### 超时问题

不要用于较长时间的任务.

set指令的时候value设置为一个随机值.释放锁的时候判断随机数是否一致,再删除key.为了保证这个一个原子操作,需要变成lua脚本:
```lua
# delifequals
if redis.call("get",KEYS[1])==ARGV[1] then
    	return redis.call("del",KEYS[1])
    else
    	return 0
    end
```

#### 可重入

对客户端的set进行重写,使用线程的Threadlocal变量存储当前持有锁的计数.

如果没有当前锁的计数则赋值1.锁未释放再次加锁,计数+1.lock操作相反.但是更精确需要考虑内存锁计数的过期时间.不推荐使用.

## 异步队列

使用list实现.左进右出或者左出右进.

使用sleep处理队列为空的情况,会导致队列出现延迟.推荐使用阻塞读`blpop/brpop`.没有数据时会立即休眠,数据到达时会理解醒来.需要注意长期阻塞,redis客户端的连接就成空闲链接,当服务器断开连接减少闲置资源占用时会**导致`blpop/brpop`抛出异常**,需要捕获异常及其重试.

## 延时队列

使用zset实现,消息体序列化为value,score设置为到期处理时间,多线程轮训zset获取到期任务处理.通过`zrangebyscore(,0,time.time().start=0,num=1)`.每次获取一个进行处理,多线程同时竞争通过`zrem`成功作为抢到的唯一属性.**处理逻辑需要异常捕获,避免执行问题导致循环退出**.

优化:多个线程获取到同一个任务浪费cpu,可以将`zrangebyscore`和`zrem`放在lua中进行原子操作.

## 位图

就是利用字符串实现,通过`get`/`set`/`getbit`/`setbit`实现.

`bitcount`统计指定位置范围内1的个数

`bitpos查找指定单位内出现的第一个0或1`

这两个命令的start/end是字节索引,**必须是8的倍数**.

多位操作使用`bitfield`,形如:

```redis
bitfield w get u4 1 # 从第2位开始取4个位,第一位不当作符号位
bitfield w get i3 2 # 从第3位开始取3个位,第一位当作符号位
```

有符号数最多获取64位,无符号最多63位(受限于redis协议汇中integer是有符号数).

`bitfield`使用`incrby` 时,默认溢出策略是折返,可以使用`overflow`设置策略,**但仅对之后的第一条指令生效**.

溢出策略:

- warp:折返
- fail:失败报错不执行
- sat:饱和截断

## HyperLogLog

提供类似set的能力,使用场景是统计大量数据下set中元素的个数.标准误差在0.81%.

`pfadd`/`pfcount`/`pfmerge`

占用一定12k的存储空间

但计数较小时采用稀疏矩阵存储,超过阈值才会一次性转换为稠密矩阵.

## 布隆过滤器

通过插件实现在redis.需要使用特定版本的redis.

`bf.add`和`bf.exists`.  

## Tips

Redis所有的数据结构都可以设置过期时间.到时Redis会自动删除对应对象.如果一个字符串设置了过期时间,调用set修改时,过期时间会消失.



