## Redis客户端

#### [Jedis](http://tool.oschina.net/uploads/apidocs/redis/clients/jedis/Jedis.html)

> *Jedis：使用阻塞的I/O，且其方法调用都是同步的，程序流需要等到sockets处理完I/O才能执行，不支持异步。Jedis客户端实例不是线程安全的，所以需要通过连接池来使用Jedis。全面的redis命令*
>
> **JedisPool 连接池配置**
>
> - maxTotal：最大连接数
> - maxIdle：允许最大空闲连接数
> - minIdle：确保最小空闲连接数
> - blockWhenExhausted：连接资源用完，是否等待
> - maxWaitMillis：连接池用完，调用者最大等待时间
>
> 最大连接池数： 业务QPS / 单连接QPS 

#### [redisson](https://redisson.org/)

> *基于Netty框架的事件驱动的通信层，其方法调用是异步的。Redisson的API是线程安全的，所以可以操作单个Redisson连接来完成各种操作,实现了分布式和可扩展的Java数据结构*。
>
> https://www.cnblogs.com/kiko2014551511/p/11527108.html

#### [lettuce](https://github.com/redisson/redisson)

> *基于Netty框架的事件驱动的通信层，其方法调用是异步的。Lettuce的API是线程安全的，所以可以操作单个Lettuce连接来完成各种操作*



