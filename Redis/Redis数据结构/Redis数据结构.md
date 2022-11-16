## Redis数据结构

> - #### **SDS(动态字符串)**
>
>   > - 实现字符串对象
>   > - 在Redis程序内部用作char*类型替代品(key值结构)
>   >
>   > ~~~c
>   > struct sdshdr {
>   >   // 记录buf数组中已使用字节数量
>   >   int len;
>   >   //记录buf数组中未使用字节数量
>   >    int free;
>   >   // 字节数组 保存字符串
>   >    char buf[];
>   > }
>   > ~~~
>   >
>   > *C语言使用长度+1的字符数组表示长度为N的字符串(使用\0表示字符结尾)*
>   >
>   > - free记录的是buf中未使用的空间,SDS实现空间预分配和惰性空间释放优化策略,使其操作字符时不必指定数组长度和动态扩容
>   >
>   >   - 空间预分配 (SDS中len长度小于1MB,len与free相同,即使用长度扩容一倍,当SDS长度大于1MB,free值扩容1MB未使用空间 SDS数组的实际长度= free + len + '/0')
>   >   - 惰性空间释放 (对字符进行删减时,将删减内容移动到free空间中,防止直接操作buf数组)
>   >
>   >   - 只要扩容的内容不超过free长度,不会对buf进行扩容
>   >
>   > **二进制安全**
>   >
>   > > *C字符串的特殊性(空格当做结尾),因此字符串内容中不允许存在空格,不能保存图片,视频二进制流转码的字符*
>   > >
>   > > SDS中API都是二进制安全的,所有的API都会以二进制方式处理buf数组中数据**(通过len长度判断字符结尾)**
>   >
>   > ![image-20211226150411195](img/image-20211226150411195.png) 
>   >
>   > **SDS API**
>   >
>   > ![image-20211226150456288](img/image-20211226150456288.png) 
>
> #### **链表**
>
> > *链表提供了高效的节点重排能力，以及顺序性的节点访问方式，并且可以通过增删节点来灵活地调整链表的长度。*
> >
> > **redis中链表使用场景** 
> >
> > -  发布与订阅、慢查询、监视器等功能
> > - 保存多个客户端的状态信息,以及使用链表来构建客户端输出缓冲区（outputbuffer）
> >
> > **redis中链表结构**
> >
> > ~~~c
> > typedef struct listNode{
> >   // 前置节点
> >   struct listNode * prev;
> >   //后置节点
> >   struct listNode * next;
> >   // 节点置
> >   void * value;  
> > } listNode;
> > // adlist.h/list持有链表
> > typedef struct list {
> >   // 表头
> >   listNode * head;
> >   // 表尾
> >   listNode * tail;
> >   // 链表包含的节点数量
> >   unsigned long len;
> >   // 节点值复制函数
> >   void *(*dup) (void * ptf);
> >   // 节点值释放函数
> >   void (*free) (void * ptf);
> >   //节点对比函数
> >   int (*match) (void *ptr , void *key);
> > } list;
> > ~~~
> >
> > ![image-20211226151229094](img/image-20211226151229094.png) 
> >
> > - **Redis链表特性**
> >   - 双端
> >   - 无环
> >   - 带表头指针和表尾指针
> >   - 长度计数器
> >   - 多态
> >
> > **Redis链表API**
> >
> > ![image-20211226151855637](img/image-20211226151855637.png) 
>
> #### **字典(symbol table)**
>
> > *字典，又称为符号表（symbol table）、关联数组（associative array）或映射（map），是一种用于保存键值对（key-value pair）的抽象数据结构。*
> >
> > - redis字典时哈希键的底层实现之一
> >
> > **字典实现**
> >
> > 一个哈希表里面可以有多个哈希表节点，而每个哈希表节点就保存了字典中的一个**键值对**。
> >
> > **哈希表**
> >
> > ~~~c
> > typedef struct dictht{
> >   // 哈希表数组 保存键值对
> >   dictEntry ** table;
> >   // 哈希表长度
> >   unsigned long size;
> >   // 哈希表大小掩码 用于计算索引值 size -1  与哈希值决定k-存放索引位置
> >   unsigned long sizemask;
> >   // 已使用节点数量
> >   unsigned long used;
> > } dictht;
> > // 哈希表节点
> > typedef struct dictEntry{
> >   // 键
> >   void *key;
> >   //值
> >   union{
> >     void *val;
> >     uint64_tu64;
> >     int64_ts64;
> >   } v;
> >   //指向下一节点 形成链表 防止哈希冲突 (同一个哈希有多个对应值)
> >   struct dictEntry *next;
> > } dictEntry;
> > // Redis中字典 dict.h/dict 结构表示
> > typedef struct dict {
> >   //类型特定函数
> >   dictType * type;
> >   //私有数据
> >   void * privdata;
> >   // 哈希表
> >   dictht ht[2];
> >   //rehash索引 (空为 -1 )
> >   in trehashidx ;
> > }dict;
> > 
> > ~~~
> >
> > ![image-20211226153232616](img/image-20211226153232616.png) 
> >
> > - type属性 和 privdata 属性针对不同类型的键值对,为创建多态字典设置的
> >   - type属性 指向dictType结构的指针
> >   - privdata可选类型参数
> >
> > ~~~c
> > typedef struct dictType{
> >   // 计算哈希值的函数
> >   unsigned int (*hashFunction)(const void *key);
> >   //复制键的函数
> >   void *(*keyDup)(void *privdata,const void *key);
> >   // 复制值得函数
> >   void *(*valDup)(void *privdata,const void *key);
> >   ....
> > }
> > ~~~
> >
> > #### **哈希算法**
> >
> > ~~~c
> > #使用字典设置的哈希函数,计算键key的哈希值
> > hash = dict -> type -> hashFunction(key);
> > #使用哈希表的sizemask属性和哈希值,计算出索引值
> > index  = hash & dict ->ht[x].sizemask;
> > 
> > ~~~
> >
> > **rehash(重新散列)**
> >
> > *对哈希表进行扩容和收缩*
> >
> > **哈希表扩容**
> >
> > 1. 为字典的ht[1]哈希表分配空间，**大小取决于当前操作和ht[0]的userd属性**
> > 2. 将保存在ht[0]的键值对**重新计算哈希值**存入ht[1]哈希表中
> > 3. ht[0]空间释放，ht[1]改为ht[0] ，并在ht[1]新创建一个空白的哈希表 为下一次rehsah准备
> >
> > ![image-20211227142537997](img/image-20211227142537997.png) 
> >
> > **哈希表扩容**
> >
> > 1. 哈希表的负载因子大于等于1(并且没写盘操作)
> >
> > - 哈希冲突两种解决方案
> >   1. 闭散列 开放定值法,单该key被占用时,存在占用key的下一个相邻位置
> >      - 负载因子定义 a = ht[0].used /  ht[0].size   (07~0.8 之间合适)
> >   2. 开散列 链接地址法
> >
> > **字典API**
> >
> > ![image-20211227143338844](img/image-20211227143338844.png)
>
> #### **跳跃表(skipList)**
>
> > SortedSet 数据结构(实现排序) 和集群节点中用作内部数据结构
> >
> > 跳跃表性能  O(logN)  ~ O(N) 查找
> >
> > ![image-20211227144004963](img/image-20211227144004963.png) 
> >
> > ![image-20211227144104006](img/image-20211227144104006.png) 
> >
> >  **跳跃表插入**
> >
> > ![image-20211227144201482](img/image-20211227144201482.png) 
> >
> > ![image-20211227144246667](img/image-20211227144246667.png) 
> >
> >  下图箭头指向元素对应上图红色箭头方向
> >
> > - header-> L1 -> L4 - > L2  
> >
> >   ![image-20211227155350977](img/image-20211227155350977.png)  
> >
> > **zskiplist结构**(左边)
> >
> > - header 跳跃表头节点
> > - tail 跳跃表尾节点
> > - level 跳跃表层数(不包含原链表)
> > - length 长度
> >
> > **zskiplistNode结构**(右边L1,L2,L3表示的层)
> >
> > - 层(level)  
> >   - 前进指针 (指向下一层级结点)
> >   - 跨度 记录两个节点之间距离(隔了多少元素) 指向NULL指针跨度为0
> > - 后退(backward)指针(BW标识结点) 指向当前节点的前一个节点
> > - 分值(score) 
> > - 成员对象(obj)
> >
> > 跳跃表结点(redis.h/zskiplistNode)结构定义
> >
> > ~~~c
> > typedef struct zskiplistNode{
> >   // 定义层结构
> >     struct zskiplistLevel{
> >          // 前进指针
> >         struct zskiplistNode * forward;
> >         //跨度
> >         unsigned int span;
> >     } level[];
> >     // 后退指针
> >     struct zskiplistNode * backward;
> >     // 分值
> >     double source;
> >     // 成员对象
> >     robj * obj;
> > } zskiplistNode;
> > typedef struct zskiplist{
> >     //表头结点和表尾节点
> >     struct zskiplistNode * header, *tail;
> >     //表中节点数量
> >     unsigned long length;
> >     //表中层数最大的节点层数
> >     int level;
> > }zskiplist
> >     
> > ~~~
> >
> > 跳跃表API
> >
> > ![image-20211227160102454](img/image-20211227160102454.png) 
>
> **整数集合(INTSET)** _保证集合中数值不会重复_
>
> > intset.h 与 intset 结构表示整数集合
> >
> > ~~~c
> > typedef struct intset {
> >     // 编码方式
> >     uint32_t encoding;
> >     // 集合包含的元素数量
> >     uint32_t length;
> >     //保存元素 从小到大有序排列 不会包含重复项
> >     int8_t contents[];
> > } intset;
> > ~~~
> >
> > ![image-20211227161002682](img/image-20211227161002682.png) 
>
> #### **压缩列表(zipList)**
>
> > 压缩列表是Redis为了节约内存而开发的，是由一系列特殊编码的连续内存块组成的顺序型（sequential）数据结构。一个压缩列表可以包含任意多个节点（entry），每个节点可以保存一个字节数组或者一个整数值。
> >
> > *当一个列表键只包含少量列表项，并且每个列表项要么就是小整数值，要么就是长度比较短的字符串，那么Redis就会使用压缩列表来做列表键的底层实现。*
> >
> > OBJECT ENCODING key
> >
> > ![image-20211227161437002](img/image-20211227161437002.png) 
>
> #### **对象**
>
> > ![image-20211227161632392](img/image-20211227161632392.png) 
> >
> > **Redis 对象系统**
> >
> > - 通过计数器方式回收引用内存和对象共享(一个key值引用同一个对象)
> >
> > **redisObject结构**
> >
> > ~~~c
> > typedef struct redisObject{
> >     // 类型
> >     unsigned type :4;
> >     // 编码
> >     unsigned encoding:4;
> >     //指向底层实现数据结构指针
> >     void * ptr;
> >     // ...
> > } robj;
> > ~~~
> >
> > **对应类型**
> >
> > ![image-20211227162107319](img/image-20211227162107319.png) 
> >
> > ![image-20211227162211311](img/image-20211227162211311.png) 
> >
> > ![image-20211227162244064](img/image-20211227162244064.png) 
> >
> > **字符串对象**
> >
> > 字符串对象编码可以是 int raw 或者 embstr
> >
> > embstr 编码 单例字符串长度小于或者等于32字节时
> >
> >  
> >
> > **列表对象**
> >
> > 对象编码 ziplist 或者linkedlist
> >
> > 当列表对象满足两个条件时可以使用压缩列表
> >
> > - 列表元素的字符长度小于64字节
> > - 列表对象元素个数小于512个
> >
> > *以上两个条件的上限值是可以修改的，具体请看配置文件中关于list-max-ziplist-value选项和list-max-ziplist-entries选项的说明*
> >
> >  
>
> #### **内存回收**
>
> > 引用计数器(reference counting) 
> >
> > ![image-20211227163345095](img/image-20211227163345095.png) 
>
> #### **对象共享**
>
> > 

