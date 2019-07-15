关于阿里云RocketMQ踩坑
==

``` text
服务开通, SDK调用什么的都没什么好说的, 傻瓜式就完了
这里要说的是关于阿里云RocketMQ tag和groupId的坑
之前没用过阿里云的MQ, 按我的构想是一个topic一个功能, 老大说一topic一个模块, 功能/环境可以用tag区分
然后就步入了踩坑大军
首先, tag确实可以区分消息, 不过坑的是, 每个groupId只能消费一个tag, 之前完全不知道有这个东西
在即将上线的时候, 由于测试服务器的消费者deamon是一直执行的
在预发布环境, 打开消费者程序, 死活消费不到消息, 找原因找了两个多小时
最后想到可能是groupId的原因, 赶紧让老大重新开了个groupId, 完美执行

PS: 这个坑, 阿里云文档也没说, 网上找到的相关教程都是傻瓜式的, 啥啥啥开通服务, 下载SDK, 调用SDK
写在这里只是希望, 后面踩坑的人能看到, 少踩点, 也是给自己一个提醒
PS2: 整的本来不用加班上线的功能, 整到晚上10点多才正式上线, 不知道如何吐槽了
```

> PS3: 还有一个就是关于Yii2守护进程, "MySQL server gone" 的问题  
> 这个只要在执行消费者程序前, 调用一下相关的 `db ping`(`SELECT 1`) 就完事了  
> 详细的在 [mysql-connect-gone.md](https://github.com/RandalTeng/static-page/tree/master/docs/PHP/mysql-connect-gone.md)
