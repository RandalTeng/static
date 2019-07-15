High Available
==

Redis Cluster
--

> Redis自带集群配置  
> Master-Slave自带主从配置  
> Sentinel哨兵机制(集群监控/消息通知/故障转移/配置中心)  
> RDB/AOF持久化

Sentinel相关配置:

``` configure
min-slave-to-write 1 // 至少1个从
min-slave-max-lag 10 // 数据复制延迟不能超过10秒, 减少"脑裂"(双master)带来的数据不一致

// 如果说一旦所有的 slave, 数据复制和同步的延迟都超过了 10 秒钟, 那么这个时候, master 就不会再接收任何请求了。
```

PS: [more by: Redis 哨兵机制实现集群高可用](https://github.com/doocs/advanced-java/blob/master/docs/high-concurrency/redis-sentinel.md)

记一次线上Redis使用事故
--

``` text
事故简记:
    使用阿里云REDIS的时候, 配置更新为集群配置, 16G 8个分片, 每个分片2G, 今天(05/26/2019)早上发现不可  
    写, 报 `OOM command not allowed when used memory > 'maxmemory'`, 经过一些列操作, 查询到大VALUE缓  
    存占用某个分片全部内存, 导致对应的写复制无法写入, 整个集群不可用
分析:
    人为导致的集群不可用, 没有对大VALUE缓存做限制, 合进行理拆分
处理:
    预估缓存空间大小使用情况, 对大VALUE做限制, 进行子KEY拆分
    考虑是否可以对分片进行缩小, 提高可以容忍的大VALUE缓存最大值
```