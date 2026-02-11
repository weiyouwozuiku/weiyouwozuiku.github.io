---
title: Redis笔记
author: 不二
img: https://cdn.jsdelivr.net/gh/weiyouwozuiku/weiyouwozuiku.github.io@src/source/_posts/PageImg/KV存储/Redis学习笔记.png
mathjax: true
date: 2024-09-08 19:08:18
tags:
  - Redis
categories: KV存储
---

 Redis基础数据类型
 > [!PDF|red] [[Redis深度历险：核心原理与应用实践.pdf#page=13&selection=91,12,115,0&color=red|Redis深度历险：核心原理与应用实践, p.13]]
> > string (字符串)、list (列表)、set (集合)、hash (哈希) 和 zset (有序集合)
> 
> 字符串最大512M.字符串长度小于1M时,扩容加倍现有空间.超过1M,扩容时一次只多扩1M.
## 常见命令
### string

set\get\ setnx \setex exists del
自增范围是有符号long 的最值.
### list
**是链表不是数组**.插入删除快,索引定位慢.
当列表弹出了最后一个元素之后,队列内容为空,但是依然存在.
异侧收发->队列,同侧收发->栈
ltrim实现定长链表
列元素较少时结构为`ziplist`,当数据量大时:`quicklist`.
### hash
渐进式rehash: 保留两个hash结构,同时查询两个结构,并循序渐进将旧hash迁移到新hash
### set
sadd\smembers\sismember\scard\spop
### zset
set+map value唯一,value对应的score排序
zadd\zrange(按 score 排序列出，参数区间为排名范围)\zrerange(按 score 逆序列出，参数区间为排名范围)\zcard(相当于 count())\zscore(获取指定 value 的 score)\zrank(排名)\zrangebyscore(根据分值区间遍历 zset)\zrem
内部通过**跳表**实现.

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

```Redis
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

`bf.add`和`bf.exists`,`bf.madd`,`bf.mexiets`.  

通过bf.reserve显式创建,三个参数:key,error_rate,initial_size.当实际数量超出数值时,误判率上升.默认error_rate:0.01,initial_size:100.

位数组相对越长,错误率越低.
位数组相对越长,hash函数需要的最佳数量也越多.

## 简单限流

1. **记录行为（****ZADD****）：** 首先，将当前行为的时间戳作为 `score` 和 `value` 加入到 ZSET 中。

    ◦ `pipe.zadd(key, now_ts, now_ts)`。

2. **清理过期元素（****ZREMRANGEBYSCORE****）：** 移除时间窗口之外的旧行为记录。

    ◦ `pipe.zremrangebyscore(key, 0, now_ts - period * 1000)`。

3. **统计当前数量（****ZCARD****）：** 获取当前时间窗口内（包括刚刚添加的元素）的行为数量。

    ◦ `Response<Long> count = pipe.zcard(key)`。

4. **设置过期时间（****EXPIRE****）：** 更新 ZSET 的过期时间，避免冷用户持续占用内存。

    ◦ `pipe.expire(key, period + 1)`。

5. **执行并检查：** 批量执行以上所有命令，然后将统计到的 `current_count` 与限制值 `max_count` 进行比较。

    ◦ `return count.get() <= maxCount`。
这里通过pipeline来保证了操作的原子性和io操作,牺牲了精确性.
有两个副作用:
- 占用空间大:该方案需要记录时间窗口内所有的行为记录。如果限流的次数（`max_count`）和时间窗口（`period`）都很长，ZSET 可能会变得非常庞大，消耗大量存储空
- 如果统计数量（`current_count`）超过限制（`max_count`），虽然返回“不能执行”，但由于 `ZADD` 已经先被执行了，这个被拒绝的请求仍然占据了 ZSET 的容量，从而导致在时间窗口内，**实际被允许执行的操作次数可能小于设定的** **max_count**
## 漏斗限流

内存实现的 `Funnel` 类，其核心在于状态变量和 `make_space` 方法。

状态变量 (Funnel Class Attributes)

每个限流对象（例如针对某个用户或某个行为）都对应一个 `Funnel` 实例，维护以下四个状态变量：

• `capacity`：**漏斗的固定容量**。

• `leakingRate`：**漏嘴的流水速率**（配额/秒），用于计算单位时间流出的量。

• `left_quota`：**漏斗当前剩余的空间**（初始值等于 `capacity`）。

• `leakingTs`：**上一次漏水（即状态更新）的时间戳**。

核心方法：`make_space()`（腾出空间）

`make_space()` 方法是漏斗算法的核心，它在每次尝试处理新请求（灌水）之前被调用，以确保漏斗的状态是最新的：

1. **计算时间差：** 获取当前时间 `now_ts`，并计算自上次漏水时间 `leaking_ts` 以来过去的时间 `delta_ts`。

2. **计算腾出配额：** 根据时间差和流失速率计算出可以腾出的空间：`delta_quota = delta_ts * leaking_rate`。

3. **更新剩余空间：** 将计算出的 `delta_quota` 加到 `left_quota` 上，并更新 `leaking_ts` 为当前时间。

4. **容量限制：** 如果更新后的 `left_quota` 超过了 `capacity`，则必须将其限制为 `capacity`，因为漏斗不能装超过其容量的水。

5. **最小单位限制：** 如果计算出的 `delta_quota` 小于 1（即腾出的空间太小），则不进行状态更新，避免不必要的浮点运算。

核心方法：`watering(quota)`（灌水，即处理请求）

`watering(quota)` 方法用于处理实际的请求配额（通常为 1）：

1. **先腾空间：** 首先调用 `make_space()` 来更新漏斗的最新状态。

2. **判断是否足够：** 检查当前的 `left_quota` 是否大于或等于请求所需的 `quota`。

3. **允许/拒绝：** 如果空间足够，则减少 `left_quota` 并返回 `True`（允许执行）；如果空间不足，则返回 `False`（拒绝执行）。

在单机实现中，所有的漏斗对象（`Funnel` 实例）都存储在一个内存字典或哈希表（`funnels`）中，通过 `(user_id, action_key)` 组成的键来获取或创建相应的漏斗实例。

### 分布式漏斗算法的挑战与 Redis-Cell 解决方案

虽然单机漏斗算法逻辑清晰且高效，但在分布式环境下，若要使用 Redis 的基本数据结构（如 Hash）实现漏斗限流，会面临原子性问题：

1. **非原子操作序列：** 必须先从 Redis Hash 中取出漏斗状态（容量、剩余配额、上次时间戳），然后在客户端内存中执行 `make_space` 和 `watering` 的计算逻辑，最后再将新的状态写回 Redis。

2. **并发问题：** 在高并发环境中，多个客户端同时执行这个“取值-计算-回填”的三步操作，可能导致竞态条件，数据不一致。

3. **加锁的代价：** 解决原子性问题通常需要引入分布式锁，但这会使代码复杂度升高，并因加锁和重试机制导致性能显著下降。

Redis-Cell 模块提供的原子化解决方案

为了解决上述问题，Redis 4.0 引入了 **Redis-Cell 模块**，该模块实现了**原子化的漏斗限流指令**，从而避免了客户端加锁和非原子操作序列。

该模块提供的指令是 `cl.throttle`，它将漏斗算法的计算逻辑全部迁移到了 Redis 服务器端，保证了操作的原子性。

**cl.throttle** **指令格式：**

```
cl.throttle key capacity max_rate period
```

该指令允许用户设置漏斗容量 (`capacity`)，以及在指定时间 (`period`) 内最多允许通过的次数 (`max_rate`)，用两个参数来表达漏水速率，比使用单个浮点数更直观。

**cl.throttle** **返回值：** 该指令执行后会原子性地更新漏斗状态，并返回一个包含 5 个整数的数组，这些信息对客户端处理限流结果至关重要：

1. **(integer) 0 或 1：** **限流结果**。0 表示允许该请求通过，1 表示请求被拒绝。

2. **(integer) 漏斗容量：** 当前漏斗的最大容量 (`capacity`)。

3. **(integer) 漏斗剩余空间：** 当前漏斗中剩余的配额 (`left_quota`)。

4. **(integer) 重试时间（Retry After）：** 如果请求被拒绝，客户端需要等待多长时间（单位：秒）后可以重试。

5. **(integer) 完全清空时间：** 漏斗完全清空（剩余空间达到容量）所需的时间（单位：秒）。

通过利用 `cl.throttle` 返回的第四个值（重试时间），客户端可以精确地进行 `sleep` 或安排异步任务重试，极大地简化了分布式限流的逻辑和复杂性。

## Tips

Redis所有的数据结构都可以设置过期时间.到时Redis会自动删除对应对象.如果一个字符串设置了过期时间,调用set修改时,过期时间会消失.



