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

// 如果说一旦所有的 slave，数据复制和同步的延迟都超过了 10 秒钟，那么这个时候，master 就不会再接收任何请求了。
```

PS: [more by: Redis 哨兵机制实现集群高可用](https://github.com/doocs/advanced-java/blob/master/docs/high-concurrency/redis-sentinel.md)