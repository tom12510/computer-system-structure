## SQL语言 

- #### **DCL** 数据控制语句

  > GRANT
  >
  > REVOKW

- #### **DDL** 数据定义语句

  > CREATE
  >
  > ALTER
  >
  > DROP
  >
  > RENAME
  >
  > TRUNCATE

- #### **DML** 数据操作语句

  > INSERT
  >
  > UPDATE
  >
  > DELETE
  >
  > MERGE

- #### **DQL** 数据查询语句

> - 根据查询结构创建表(当前副本)
>
>   ~~~sql
>   create table stu as select * from blade_region;
>
> - where 条件查询
>
>   > any :  等价于子语句单列结果集与条件 or 连接： A {condition} any (B)   
>   >
>   > all ：等价于子语句单列结果集与条件 and 连接   A {condition} all (B)   
>   >
>   > in： 等价于子语句单列结果集**等于条件** and 连接   A in (B)   
>   >
>   > exists： 等价于子语句单列结果集**等于条件** or 连接   A exists (B)   
>
> - 分组
>
>   > **ROLLUP**
>   >
>   > > group by rollup(A,B,C)
>   > >
>   > > - group by A,B,C
>   > >
>   > > - group by A,B
>   > >
>   > > - group by A
>   > >
>   > > - group by NULL
>   > >
>   > > group by A,rollup(A,B) 
>   > >
>   > > - group by A,B
>   > > - group by A
>   > > - group by A,NULL 
>   >
>   > **CUBE**
>   >
>   > > group by cube (A,B,C) 
>   > >
>   > > - group by A,B,C
>   > > - group by A,B
>   > > - group by A,C
>   > > - group by B,C
>   > > - group by B,C
>   > > - group by A
>   > > - group by B
>   > > - group by C
>   > > - group by NULL
>   > >
>   > > group by cube((A,B),C)
>   > >
>   > > - group by A,B,C
>   > > - group by A,B
>   > > - group by C
>   > > - group by NULL
>   >
>   > grouping()：判断该列是否通过rollup分组产生的
>
> - 排序
>
>   > row_number :  排序后生成的**行号**，结合子查询作条件查询
>   >
>   > 1. row_number() over(order by COL) ：  根据COL字段排序生成行号
>   >
>   > 2. row_number() over(partition by COL1 order by COL2)：以COL1分组后在组内通过COL2排序生成行号
>   >
>   >    
>   >
>   > rank ： 排名函数（跳过名次排序）
>   >
>   > dense_rank：连续名次排名
>
> - 多表查询
>
>   > 行结构相同
>   >
>   > intersect：返回两个结果集相同行
>   >
>   > union all：合并两个结果集行
>   >
>   > union：合并两个结果集行并去重
>   >
>   > minus：第一个结果集减去第二个结果集中数据返回剩余行
>   >
>   > ​    
>   >
>   > 外连接：
>   >
>   > ~~~sql
>   > -- 左连接
>   > select * from table1,table2 where table1.col = table2.col(+)
>   > -- 右连接
>   > select * from table1,table2 where table1.col(+) = table2.col
>   > -- 全外连接(左+右连接)
>   > select * from table1 full join table2 on table1.col = table2.col
>   > ~~~
>
> - 函数
>
>   > rownum 函数
>   >
>   > 1. 未排序时按insert顺序生成行号
>   > 2. 不适用与 = 与 > , >= 条件查询
>   > 3. 只有子查询才能设置别名
>   >
>   > <img src="img/image-20211126225347135.png" alt="image-20211126225347135" style="zoom: 80%;" /> 
>   >
>   > <img src="img/image-20211126225415576.png" alt="image-20211126225415576" style="zoom:80%;" /> 
>   >
>   > <img src="img/image-20211126225457254.png" alt="image-20211126225457254" style="zoom:80%;" /> 
>   >
>   
> - 索引
>
>   > 
>   
> - 特殊字符
>
>   > 1.  %  用来表示任意数量的字符，或者可能根本没有字符。
>   > 2.  _    任意单字符
>   > 3.  []  与特定范围或集合的任意单字符匹配
>   > 4.  ‘  描述字符串或日期
>   > 5.  “ 描述包含特殊字符或空格或日期
>   > 6.  escape 转义标识符  
>

- #### **PLSQL**

  > 
