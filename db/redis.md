# 简介

## 与rdbms

> mysql 是通过硬盘来存储信息的，并且还要解析并执行 sql 语句，这些决定了它会成为性能瓶颈。

* 存储硬件：硬盘
* SQL语言的解析

计算机领域最长使用的性能优化手段就是缓存。

那么能不能把结果缓存到内存中，以供下次使用呢？

内存和硬盘的速度差距还是很大的：

![img](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/fc9adf9ac5ec440db7f975e70f39df65~tplv-k3u1fbpfcp-zoom-in-crop-mark:3024:0:0:0.awebp?)

所以做后端服务的时候，我们不会只用 mysql，一般会结合内存数据库来做缓存，最常用的是 redis。

## 由来

我们的需求就是希望能缓存不同类型的数据，所以 Redis 设计是 key、value 的键值对形式。

并且值的类型有很多：字符串（string）、列表（list）、集合（set）、有序集合（sorted set)、哈希表（hash）、地理信息（geospatial）、位图（bitmap）等。

# 开始

## docker 启动 Redis

参考：

* https://stackoverflow.com/questions/43341143/how-to-save-a-docker-redis-container

* https://yeasy.gitbook.io/docker_practice/appendix/repo/redis

命令：

```bash
$ docker run -v /Users/zhangbinbinb28199/my-data/redis/data:/data --name=redis-test1 --user=1000:1000 -p 8096:6379 -d redis redis-server --requirepass qazplm
```

* 数据存在 /data 下
* `--requirepass` 可以加密码

## 使用 redis-cli 连接 Redis

* redis-cli 也就是 Redis 操作的客户端

```bash
$ redis-cli -h 127.0.0.1 -p 8093
```

## 数据类型

Redis支持五种数据类型：string（字符串），hash（哈希），list（列表），set（集合）及zset(sorted set：有序集合)。



## 简单试用

以下都是参考官方文档。

### 常用命令

#### 查看所有 key

```bash
keys *
# keys pattern
```

### string

String 是 Redis 最基本的类型

> string 类型是二进制安全的。意思是 redis 的 string 可以包含任何数据。比如jpg图片或者序列化的对象。

* 也就是不会转换任何二进制数据
* string 类型是 Redis 最基本的数据类型，string 类型的值最大能存储 512MB。

string类型，参考：https://redis.io/docs/data-types/strings/

```bash
set key value

# 获取
get key

# 模糊匹配keys
keys 'bin*'

# 递增，不存在这个key就从0开始计算
incr key
```

#### 过期时间

从 Redis 2.6.12 版本开始， SET 命令的行为可以通过一系列参数来修改：

EX second ：设置键的过期时间为 second 秒。 SET key value EX second 效果等同于 SETEX key second value 。
PX millisecond ：设置键的过期时间为 millisecond 毫秒。 SET key value PX millisecond 效果等同于 PSETEX key millisecond value 。
NX ：只在键不存在时，才对键进行设置操作。 SET key value NX 效果等同于 SETNX key value 。
XX ：只在键已经存在时，才对键进行设置操作。

### list

参考：https://redis.io/docs/data-types/lists/

redis list 是字符串连接起来的列表。经常被用来作：

* 实现堆栈或者队列
* 为后台工作系统提供队列管理

#### 基础命令

都是 list 的 l 开头的

```bash
# 左添加，头部添加
lpush list1 111

# 右添加
rpush list1 555

# 左删除
lpop list1

# 右删除
rpop list1

# 长度，可以作为查询所有的参数
llen list1

# 查看列表内容，0 ~ 10，闭区间
lrange 0 10
# 查看所有
lrange 0 -1 
```

### Sets

和js中的 Set 差不多，都是保存唯一值。

#### 大小限制

最大 2^32 - 1 个成员

#### 命令

```bash
# 插入
sadd set1 value

# 是否是成员，成功返回1
SISMEMBER set1 value

# 查看成员
SINTER set1
```

* set 只能去重，不能排序。如果需要排序需要使用 sorted set 数据结构

### sorted set - zset

每个数据都需要添加分数信息

```bash
# 添加 
zadd zset1 3 test2

# 返回排名
zrank zset1 test1

# 返回数据
zrange zset1 0 -1
```

### hash

* 和我们使用的map是一样的

```bash
# 新增
$ hset hash1 key2 value2

# 获取某个值
$ hget hash1 key2

# 获取所有值
$ HGETALL hash1
```



### geo

* 经纬度信息，根据距离计算周围的人用的。

#### 命令

```bash
# 添加经纬度信息
geoadd loc 13.123 38.115 'test1' 15.087 37.502 'test2' 

# 计算两个点之间的距离
geodist loc test1 test2

# 查找范围，某个表、经度、维度、半径、单位。经度每隔0.001度，距离相差约1000米
GEORADIUS loc 13 37 150 km
```

* 查找附近的人可以用这个实现，附近的店家

### sub/pub 发布订阅系统

```bash
# 订阅一个频道
$ subscribe my-channel

# 发布一些数据
$ publish my-channel 'message'

# 查看所有发布订阅频道
$ pubsub channels

# 释放订阅
$ UNSUBSCRIBE my-channel
```

## redis 删除

```bash
$ DEL KEY_NAME
```

## 查看数据类型

```bash
$ type key_name
```

## 事务

> 单个 Redis 命令的执行是原子性的，但 Redis 没有在事务上增加任何维持原子性的机制，所以 Redis 事务的执行并不是原子性的。
>
> 事务可以理解为一个打包的批量执行脚本，但批量指令并非原子化的操作，中间某条指令的失败不会导致前面已做指令的回滚，也不会造成后续的指令不做。

* redis 的事务并不是原子性的，也就说并不是里面一个命令失败其他的都不会执行，而是被看成一个批量执行的策略！

## Redis 集合分类

Redis一般通过使用特殊的key来实现数据的分类，比如：`group1:class1:name1`，用`:`分割来实现 `->` 递进的效果，实现层级分类。



# node 使用 Redis

## redis 包

* 官方推荐的包

### 使用

* 所有的 redis 命令都对应相应的方法（都是异步的）

```typescript
// 测试redis包操作Redis
import { createClient } from 'redis'

const client = createClient({
  socket: {
    host: '127.0.0.1',
    port: 8093,
  },
  password: 'qazplm',
})

client.on('error', err => {
  console.log(err, '---err')
})

const main = async () => {
  await client.connect()

  // 新建一个 hash 表
  await client.hSet('hash2', 'key1', 'value2')
  const hashVal = await client.hGet('hash2', 'key1')
  console.log(hashVal)

  const value = await client.keys('*')
  console.log(value, '---keys')

  await client.disconnect()
}

main()
```

