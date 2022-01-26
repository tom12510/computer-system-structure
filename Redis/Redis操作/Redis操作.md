## Redis操作

- ####  **[Redis API ](https://redis.io/commands/bgrewriteaof)**

  > **ACL用户权限**
  >
  > - ACL CAT 查看Redis所有操作类目
  > - ACL DELUSER  删除所有指定的 ACL 用户并终止所有通过此类用户身份验证的连接
  > - ACL GENPASS 生成可靠的密码（返回64字节字符串）
  > - ACL GETUSER 获取用户定义
  > - ACL LIST  返回当活跃的ACL  （登录用户）
  > - ACL LOAD 
  > - ACL LOG（登录日志）
  > - ACL SAVE 
  > - ACL SETUSER 
  > - ACL USERS 
  > - ACL WHOAMI

- #### **RedisServer**

  > **读写键空间时的维护操作**
  >
  > 1. 读取键之后，服务器根据键统计命中次数和不命中次数，并更新LRU（最后使用时间）
  > 2. 服务器读取键发现改建已过期，先删除这个过期键在处理
  > 3. 

