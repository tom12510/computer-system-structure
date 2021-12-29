## Redis操作

> - 设置键的生存时间或过期时间
>   - expire key number (单位s)
>   - pexpire key number (单位毫秒)
>   - 当number为时间戳时,则设置过期时间
> - 查看失效时间
>   - ttl key (距离这个key被删除还有多长时间)
> - **过期键删除策略**
>   - 定时删除(创建key并创建timer) 占用CPU资源
>   - 惰性删除(被动删除,使用时候检查key时候过期) 占用内存
>   - 定期删除(每隔一段时间,进行检查key)  确定删除频率
>
> **AOF,RDB 和复制功能对过期键处理**
>
> - RDB 在执行SAVE或RDB命令时会检查key时候过期
> - 

