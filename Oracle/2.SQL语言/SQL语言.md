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

  > 复合类型数据
  >
  > - 记录类型（相当某行数据）
  >
  >   ~~~sql
  >   declare 
  >   -- v_row_data 单行数据变量名，table1来源表，%ROWTYPE
  >   v_row_data table1%ROWTYPE;
  >   -- 行中某列数据
  >   declare v_name table1.col%TYPE;
  >   ~~~
  >
  > - 表类型（数组类型）
  >
  >   ~~~sql
  >   declare 
  >    -- 声明数据类型集合
  >    type str_arr is varray(3) of varchar2(30);
  >    -- 声明记录类型集合
  >    type object is recode(
  >     v_code table1.col%type,
  >     v_name table1.col%type,
  >    );
  >     v_str_arr  str_arr;
  >   ~~~
  >
  > - 数组属性和相关函数
  >
  >   | 属性         | 描述                                                         |
  >   | ------------ | ------------------------------------------------------------ |
  >   | count        | 返回集合中元素的个数                                         |
  >   | delete       | 删除集合中 **所有** 的元素及 **extend**                      |
  >   | delete(x)    | 删除元素下标为 `x` 的元素（`对 varry 非法`）                 |
  >   | delete(x,y)  | 删除元素下标从 `x 到 y` 的元素（`对 varry 非法`）            |
  >   | trim         | 从集合`末端`开始删除`一个`元素（`对 index by 非法)           |
  >   | trim(x)      | 从集合`末端`开始删除 `x` 个元素 （`对 index by 非法`）       |
  >   | exists(x)    | 如果集合元素 x 已经 初始化(extend) ，则返回 true，否则返回 false |
  >   | extend       | 在集合 末尾 添加一个元素（对 index by 非法）                 |
  >   | extend(x)    | 在集合 末尾 添加 x个元素 （对 index by 非法）                |
  >   | extend(x, n) | 在集合 末尾 添加元素 x 个下标为n 的 副本（对 index by 非法） |
  >   | first        | 返回集合中第一个元素的下标号，对 varry 集合 始终 返回 1（除非 未初始化 则为 空） |
  >   | last         | 返回集合中最后一个元素的下标号，对 varry 集合 值始终 等于 count （除非 未初始化 则为 空） |
  >   | limit        | 返回 varry 集合的最大的元素个数，对 index by 无效            |
  >   | next(x)      | 返回在第 x 个元素之后紧挨着它的元素下标(x+1)，若 x 是最后一个元素，则返回 null |
  >   | prior(x)     | 返回在第x个元素之前紧挨着它的 元素下标(x-1)，如果 x 是第一个元素，则返回 null |
  >
  > 
  >
  > 流程控制语句
  >
  > - IF-THEN-END IF
  >
  > - IF-THEN-ELSE-END IF
  >
  > - IF-ELSIF-THEN-END IF
  >
  > - CASE - WHEN - THEN - ELSE - END CASE
  >
  > - CASE WHEN - THEN - ELSE - END CASE
  >
  > - LOOP --EXIT--END LOOP
  >
  >   > ![image-20221019233255723](img\image-20221019233255723.png) 
  >
  > - LOOP --EXIT WHEN -- END LOOP
  >
  > - WHILE -- LOOP -- END  LOOP
  >
  > - WHILE -- LOOP -- EXIT -- END LOOP
  >
  > - FOR - IN -- LOOP END LOOP
  >
  > - FOR - IN REVERSE -- LOOP END LOOP
  >
  >  
  >
  > 游标
  >
  > - 隐式游标(Oracle执行SQL语句自动声明的，不能控制游标信息)
  >
  >   > 游标属性
  >   >
  >   > ~~~sql
  >   > -- 返回一个布尔类型的值，如果游标指向的数据不为空，那么返回true，否则返回false
  >   > %found
  >   > %notfound
  >   > -- 它可以表示游标指向的缓冲区（结果集）的数据条数
  >   > %rowcount
  >   > -- 返回一个布尔类型的值，判断当前游标是否打开，如果是打开的返回true，否则返回false
  >   > %isopen
  >   > ~~~
  >
  > - 显示游标
  >
  >   > 游标生命周期
  >   >
  >   > 1. declare
  >   >
  >   >    ~~~sql
  >   >    -- 定义游标  SQL任意Select语句都可以定义游标
  >   >    CURSOR c_cursor_name is SQL;
  >   >    -- 声明游标
  >   >    v_cursor_name  c_cursor_name;
  >   >    ~~~
  >   >
  >   > 2. open
  >   >
  >   > 3. fetch
  >   >
  >   >    ~~~sql
  >   >    -- 循环读取
  >   >    fetch v_cursor_name INTO  v_name;
  >   >    ~~~
  >   >
  >   > 4. close
  >   >
  >   > ~~~sql
  >   > -- 游标循环，赋值
  >   > declare 
  >   >   cursor c_region is select * from blade_region_copy where parent_code = '1101';
  >   >   v_region c_region%ROWTYPE;
  >   > begin
  >   >   open c_region;
  >   > 	loop 
  >   > 		fetch c_region into v_region;
  >   > 		exit when c_region%NOTFOUND;
  >   > 		dbms_output.put_line('city name is ' || v_region.name);
  >   > 		end loop;
  >   > 		close c_region;
  >   > end;
  >   > 
  >   > 
  >   > -- 参数游标
  >   > declare 
  >   >   cursor c_region (param in blade_region_copy.parent_code%Type) is 
  >   > 	select * from blade_region_copy where parent_code = param ;
  >   > begin
  >   >   -- Test statements here
  >   >   for item in c_region('1201')
  >   > 		loop dbms_output.put_line(item.name);
  >   > 		end LOOP;
  >   > end;
  >   > -- FOR UPDATE  行锁（当前事务提交或回滚前其他事务不允许更改被锁的数据）
  >   > -- FOR UPDATE OF COL 字段锁（锁定当前行的列不允许其他事务更改）
  >   > -- WHERE CURRENT OF 
  >   > ~~~
  >
  > 
  >
  > 异常处理
  >
  > - Exception WHEN -- THEN 捕获异常
  >
  > - 用户定义的异常
  >
  >   > ~~~sql
  >   > -- 定义异常
  >   > DECLARE exception_name EXCEPTION;
  >   > -- 抛出异常
  >   > RAISE exception_name;
  >   > -- 捕获异常 （内层块发生错误时，异常会立即传播到外层）
  >   > EXCEPTION WHEN exception_name THEN 
  >   > ~~~
  >
  > - 内置异常
  >
  >   |        异常        |                             描述                             |
  >   | :----------------: | :----------------------------------------------------------: |
  >   |  ACCESS_INTO_NULL  |           当一个空对象被自动分配一个值时会引发它。           |
  >   |   CASE_NOT_FOUND   | 当没有选择`CASE`语句的`WHEN`子句中的任何选项时，会引发这个错误，并且没有`ELSE`子句。 |
  >   | COLLECTION_IS_NULL | 当程序尝试将`EXISTS`以外的集合方法应用于未初始化的嵌套表或`varray`时，或程序尝试将值分配给未初始化的嵌套表或`varray`的元素时，会引发此问题。 |
  >   |  DUP_VAL_ON_INDEX  |     当尝试将重复值存储在具有唯一索引的列中时引发此错误。     |
  >   |   INVALID_CURSOR   | 当尝试进行不允许的游标操作(例如关闭未打开的游标)时会引发此错误。 |
  >   |   INVALID_NUMBER   |    当字符串转换为数字时失败，因为字符串不代表有效的数字。    |
  >   |    LOGIN_DENIED    |     当程序尝试使用无效的用户名或密码登录到数据库时引发。     |
  >   |   NO_DATA_FOUND    |         当`SELECT INTO`语句不返回任何行时会引发它。          |
  >   |   NOT_LOGGED_ON    |             当数据库调用没有连接到数据库时引发。             |
  >   |   PROGRAM_ERROR    |                当PL/SQL遇到内部问题时会引发。                |
  >   |  ROWTYPE_MISMATCH  |        当游标在具有不兼容数据类型的变量中获取值时引发        |
  >   |    SELF_IS_NULL    |       当调用成员方法时引发，但对象类型的实例未初始化。       |
  >   |   STORAGE_ERROR    |             当PL/SQL用尽内存或内存已损坏时引发。             |
  >   |   TOO_MANY_ROWS    |             当`SELECT INTO`语句返回多行时引发。              |
  >   |    VALUE_ERROR     |    当发生算术，转换，截断或者`sizeconstraint`错误时引发。    |
  >   |    ZERO_DIVIDE     |                  当尝试将数字除以零时引发。                  |
  >
  
- **触发器**

  >> 当数据满足某种条件时,执行某种操作(DML,DDL系统事件,用户事件)
  >>
  >> - trigger_name  触发器名称
  >> - fires     before  | after | instead of
  >> - event   inset | update | delete 
  >> - table or view 创建表
  >> - statement level 
  >>
  >> ~~~plsql
  >> -- 创建触发器
  >> create or replace trigger region_trigger
  >>   before insert
  >>   on energy_region 
  >> declare
  >>   -- local variables here
  >> begin
  >>   ;
  >> end region_trigger;
  >> -- 禁用触发器
  >> alter trigger trigger_name DISABLE;
  >> alter trigger trigger_name ENABLE;
  >> -- 执行触发器顺序
  >> follows/precedes 
  >> -- 行触发器 (触发器执行次数与行改变有关)
  >> for each row 
  >> -- 语句触发器 只执行一遍
  >> ~~~
  >>
  >> **触发器应用于**
  >>
  >> - 执行不能利用完整性约束来定义复杂的业务规则
  >> - 自动生成派生列的值
  >> - 收集关于表的统计
  >> - 防止无效事务
  >> - **触发器内所有的操作在一个事务,不能使用事务控制**
  >>
  >> **INSTEAD OF 触发器(基于视图触发器)**
  >>
  >> > 用trigger的内容替换时间本身的动作
  >> >
  >> > ~~~plsql
  >> > create or replace trigger instead_trigger
  >> > instead of update on my_view for each row
  >> > ~~~
  >>
  >> #### **变异表与复合触发器**
  >>
  >> 变异表: 针对正在DML操作的表,为变异表,**触发器不能读取或修改变异表(适用于行级触发器)**
  >>
  >> 解决方案
  >>
  >> - 采用自主事务控制
  >> - 采用两个触发器(一个行级触发器,一个表级触发器)和一个包解决
  >>
  >> **复合触发器**
  >>
  >> - 在触发器执行**之前**的触发器
  >> - 触发器语句影响每一行**之前**的触发器
  >> - 在触发语句影响每一行**之后**触发的行触发器
  >> - 在触发语句**之后**触发的语句触发器
  >
  >
