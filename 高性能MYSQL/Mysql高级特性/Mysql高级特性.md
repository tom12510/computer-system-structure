## MYSQL高级特性

> - **分区表**
>
>   > *分区的主要目的将数据按照一个较粗的颗粒度存在不同表中，mysql表数据文件分为三部分，frm存放表结构，myd存放数据，myi存放索引，分区表文件使用#进行扩充，*
>   >
>   > - 一张表最多1024个分区
>   > - 分区表中不允许使用外键
>   > - 主键/索引分区必须包含全部(不支持部分分区)
>   > - 超大数据进行分区查询(数据量超大 B-Tree 无法进行优化)
>   > - 
>   >
>
> - **分区表类型**
>
>   > - RANGE(范围)分区  基于属于一个给定连续区间的列值，把多行分配给分区
>   > - LIST分区：类似于按RANGE分区，区别在于LIST分区是基于列值匹配一个离散值集合中的某个值来进行选择
>   > - HASH分区
>   > - KEY分区：类似于按HASH分区，区别在于KEY分区只支持计算一列或多
>   >
>   > ~~~sql
>   > create table sales (
>   > order_date datetime not null,
>   >  -- Other columns omitted
>   > ) engine = InnoDB PARTITION BY RANGE( YEAR(order_date))(
>   >  PARTITION p_2010 values less then (2010),
>   >      PARTITION p_2011 values less then (2011),
>   >      PARTITION p_2012 values less then (2012),
>   >       PARTITION p_catchall values less then MAXVALUE
>   > )
>   > ~~~
>   >
>   > - 索引列与分区类不匹配,会导致查询无法通过分区过滤(通过索引进行查询)
>
> #### **全文索引**
>
> > *针对于模糊查询索引(非精准值查询)*
> >
> > - MATCH() 返回关键字匹配相关度(浮点值）
>
> 

