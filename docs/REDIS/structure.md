Redis structure
==

源码结构
--

``` text
server.c 初始化服务器, 注册事件机制, 启动事件循环, 初始化后台异步任务线程(flush/rdb/aof/master-slave)
server.h 定义各种基础结构 -- redisObject/robj
ae.c 事件循环处理
ae.h 定义事件驱动相关常量结构
ae_TYPE.c 实现底层相关I/O多路复用

t_COMMAND.c 外层基本数据结构实现

底层数据实现:

dist.c 通用类型dist实现
dist.h

intset.c 小型intset实现
intset.h

quicklist.c 快速列表实现
quicklist.h

sds.c 动态内存字符串实现(兼容C原始char字符串)
sds.h

ziplist.c 压缩列表实现
ziplist.h

rax.c reids tree实现
rax.h redis tree结构

高可用实现:
cluster.c 数据库集群实现
cluster.h
replication.c 数据分片实现
sentinel.c 哨兵机制实现

lazyfree.c 惰性释放键实现
aof.c 持久化实现
rdb.c
redis-check-aof.c 异步持久化处理实现
redis-check-rdb.c
bio.c 后台异步I/O实现
bio.h

```