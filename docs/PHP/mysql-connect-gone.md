关于"MySQL Server has gone"错误
==

> 在守护进程执行模式下的php程序, 第一次执行完SQL后, 一段时间没执行, 数据库连接会被自动断开
>>
>> 一段时间: 具体时间是PDO/mysqli配置的自动释放时长, 一些通过socket链接的, 就是socket的超时时间  
>> 这个我自己也没找到配置啥好的, 服务端的 wait_timeout时间设置28800s, 只能暂时略过
>
> 这里说的是通过重新连接MySQL达到规避这个错误的方法  
> 写一个try/catch, 在try里面执行 `SELECT 1`(其他数据库做类似的查询/ping操作)  
> catch里面捕获db/Exception, 如果try成功执行, 则说明数据库连接正常, 继续执行后续代码  
> 如果catch里面捕获到了异常, 则手动标记断开连接, 重新连接数据库, 执行后续代码

相关伪代码
--

``` code
... Before code. // waiting sth.
try
    db.connection.ping()
    or
    db.connection.executeSql("SELECT 1")
catch DbException
    db.close() // destory the connection and connect object
    db.open() // rebuild the object
... After code. // exec sql.
```
