## PL/SQL(存储过程/函数)

> [PL/SQL](https://baike.baidu.com/item/PL%2FSQL/8564979)也是一种[程序语言](https://baike.baidu.com/item/程序语言/10696489)，叫做过程化[SQL语言](https://baike.baidu.com/item/SQL语言/4801972)（Procedural Language/SQL）。PL/SQL是[Oracle数据库](https://baike.baidu.com/item/Oracle数据库/3710800)对SQL语句的扩展。在普通SQL语句的使用上增加了编程语言的特点，所以PL/SQL把数据操作和查询语句组织在PL/SQL代码的过程性单元中，通过逻辑判断、循环等操作实现复杂的功能或者计算。
>
> #### **PL/SQL结构**
>
> - 声明部分(DECLARE)
>
> > 定义变量,常量,游标
>
> - 执行部分(BEGIN)
> - 异常处理部分(EXCEPTION)
> - PL/SQL块中定义的子程序不能在其他PL/SQL中调用
>
> ![image-20211128221626813](image-20211128221626813.png) 
>
> #### **PLSQL语言基础**
>
> > 定义变量及赋值变量
> >
> > > ![image-20211129211130409](image-20211129211130409.png) 
> >
> > 变量作用域及标签
> >
> > > ![image-20211129211338693](image-20211129211338693.png) 
> > >
> > > ![image-20211129211434948](image-20211129211434948.png) 
>
> **PLSQL中SQL**
>
> > - 使用SELECT INTO 初始化变量
> >
> > > ~~~sql
> > > -- 将平均值cost赋值给var_name
> > > select to_char(AVG(cost),'$9,999.99') into var_name where course;
> > > ~~~
> >
> > - 使用Oracle序列
> >
> > > CURRVAL 返回该序列当前值
> > >
> > > NEXTVAL 递增序列并返回新值
> >
> > - PLSQL中事务控制
> >
> > > COMMIT
> > >
> > > ROLLBACK
> > >
> > > SAVEPOINT
>
> #### **PLSQL流程控制语句**
>
> > IF - THEN END IF
> >
> > IF - THEN - ELSE - END IF
> >
> > IF - THEN - **ELSIF** - THEN - ELSE -  END  IF
> >
> > CASE - WHEN - THEN  - ELSE - END  CASE
> >
> > CASE WHEN - THEN - ELSE - END CASE
> >
> >  NULLIF(A,B) 表达式A不等于表示式B 则返回表达式A (相等返回NULL)
> >
> > COALESCE(A,B,C,...) 函数   表达式A为NULL计算表达式B.所有都为NULL返回NULL
>
> **PLSQL迭代控制**
>
> > - 简单循环 LOOP   EXIT WHEN -  END LOOP  
> > - WHILE - LOOP - END LOOP
> > - 数字FOR   FOR VAR_NAME IN start..end LOOP END LOOP
> > - 游标FOR

