## Oracle 体系结构

> - **实例（Oracle Instance）**
>
>   > 相当与应用，运行在物理机上，一个实例只有一个数据库，一个数据库可以有n个实例组成集群。不同实例可以建相同名字的用户。
>
> - **数据库（DataBase）**
>
>   > 基于磁盘的数据文件、控制文件、日志文件、参数文件和归档日志文件等组成的物理文件集合
>
> - **表空间**(逻辑划分)
>
>   > 表空间是 Oracle 对物理数据库上相关数据文件（ORA 或者 DBF 文件）的逻辑映射。一个数据库在逻辑上被划分成一到若干个表空间，每个表空间包含了在逻辑上相关联的一组结构。
>   >
>   > 段：表的逻辑空间(包含数据段，索引段，临时段和回滚段)
>   >
>   > 区：多行数据逻辑空间
>   >
>   > 数据块：单行数据空间，最小的I/O单位
>
> - **数据文件**(dbf,ora)
>
>   > 数据库的物理存储单位，一个表空间存在多个数据文件，一个数据文件只属于某个表空间
>
> - #### **表（Table）**
>
>   > Oracle表中字段可以是用户自定义的对象类型，嵌套表。
>   >
>   > 
>   >
>   > **Oracle数据类型**
>   >
>   > |      Oracle 内置数据类型       |                             说明                             |
>   > | :----------------------------: | :----------------------------------------------------------: |
>   > |           varchar2()           |         变长字符串，最大长度为4000，PLSQL中为32767字         |
>   > |          nvarchar2()           |                 变长字符串，最大长度32767kb                  |
>   > |              long              |                         变长字符数据                         |
>   > |              Clob              |       字符大型对象，最大长度为(4GB-1) * DB_BLOCK_SIZE        |
>   > |           char(size)           |                   定长字符串，最大为2000B                    |
>   > |          number(p,s)           | 数字类型，p表示精度（1~38）,s 表示小数点右边的数字长度(-84~127)<br />Number(p) ：表示存储总长度为p的整数 <br />Number(p,s) ：表示存储总长度为p的浮点数，小数位长度为s <br /> |
>   > |              date              |                    日期值 yyyy-MM-dd 格式                    |
>   > | timestamp/with Local Time Zone |                   时间戳/ 携带时区的时间戳                   |
>   > |   raw(size)、Long raw(size)    |               原始二进制数据，最大为2000B/2GB                |
>   > |              Blob              |      二进制大型对象，最大存储为(4GB-1) * DB_BLOCK_SIZE       |
>   >
>   >  
>   >
>   > **临时表** （非永久保存，基于session，或一个事务中有效）
>   >
>   > ~~~sql
>   > -- 创建临时表 (数据行只有在当前事务中可见,默认)
>   > CREATE GLOBAL TEMPORARY TABLE  tableName () on commit delete rows;
>   > --  创建临时表 (数据行仅在当前会话中可见)
>   > CREATE GLOBAL TEMPORARY TABLE  tableName () on commit preserve rows;
>   > ~~~
>   >
>   >  
>   >
>   > **索引组织表** （Index Organized Table)
>   >
>   >    *区别于表的存储方式（默认堆表，无组织的存储方式，存在可用空间就能存储），IOT表采用B-树索引结构存储表中的索引列（叶子节点存储该行的RowId）*
>   >
>   >  
>   >
>   > **对象表**
>   >
>   >    对象表具有自身就是对象或类型定义实例化的行。可通过对象ID（Object ID，OID）引用对象表中的行，这与关系表或普通表中的主键形成对比。然而，与关系表一样，对象表仍可以有主键和唯一键。
>   >
>   > ~~~sql
>   > create type PERS_TYP as object (
>   > )
>   > ~~~
>   >
>   > **外部表**
>   >
>   >    *不存在数据库中的表，通过Oracle数据字典描述外部表数据，外部表的数据只能进行select操作。*
>   >
>   >  ~~~sql
>   >  -- 创建外部表
>   >  create table example ( 
>   >      
>   >   ) ORGANIZATION EXTERNAL (
>   >       TYPE oracle_loader   -- 访问方式     
>   >       DEFAULT DIRECTORY oracle_loader_data_dir  -- 外部文件实际位置
>   >        ACCESS PARAMETERS (     
>   >           RECORDS DELIMITED BY NEWLINE CHARACTERSET US7ASCII  -- 记录用换行符分隔
>   >           SKIP 0 -- 跳过0行
>   >           BADFILE ‘ORACLE_LOADER_BAD_DIR’ : ‘example.bad’ -- 外表的坏文件
>   >           LOGFILE ‘ORACLE_LOADER_LOG_DIR’ : ‘example.log’ -- 外表的日志文件
>   >           FIELDS TERMINATED BY ‘,’ -- 以 , 终止字段，即以 , 分隔字段
>   >           LRTRIM --  删除首尾空白字符
>   >           MISSING FIELD VALUES ARE NULL -- 某些字段空缺值都设为NULL 
>   >           REJECT ROWS WITH ALL NULL FIELDS  ( -- 数据类型装换，与上面表字段名对应
>   >                CREATE_DATE DATE "YYYY/MM/DD HH24:MI:SS",
>   >                GNAME CHAR(200),
>   >           )
>   >        )
>   >       LOCATION (‘1.csv’,‘2.csv’) -- 数据文件名为 ‘1.csv’ 和 ‘2.csv’
>   >   )
>   >  PARALLEL                   -- 并行处理
>   >  REJECT LIMIT UNLIMITED;  -- 读入整个文件
>   >  ~~~
>
> - **约束(constraint)**
>
>   > - 空值约束  NOT NULL
>   > - 唯一列值  UNIQUE
>   > - 主键值  PAIMAEY KEY 
>   > - 引用完整值  FOREIGN KEY （外键：通过另一个表约束那些值可以填）
>   > - 复合内联完整性  CHECK (同过表内其他字段来约束该值)
>   > - 基于触发器的完整性
>
> - **索引**
>
>   > - 唯一索引（实现唯一约束）  B-Tree实现
>   > - 非唯一索引（提高查询速度） B-Tree实现
>   > - 反向键索引(减少新增之间数据冲突)
>   > - 基于函数索引（查询列通过函数值创建索引）
>   > - 位图索引（存储索引列每个可能值的位串），位串长度与索引表中行数相同
>
> - **视图** (存储查询)
>
>   > - 普通视图（不存储任何数据）
>   >
>   >   ~~~sql
>   >   create view view_name as SQL with read only;
>   >   ~~~
>   >
>   > - 物化视图（某个时刻的数据副本）
>   >
>   >   > 1. ON DEMAND （需要刷新时，与表数据同步）
>   >   > 2. ON COMMIT（基表提交时，刷新视图）
>   >   >
>   >   > ~~~sql
>   >   > create materialized view my_mv_cd
>   >   > build immediate refresh complete on demand
>   >   > as
>   >   > select p.pid as id, p.name, a.address from test_person p,test_address a
>   >   > where p.pid = a.aid;
>   >   > 
>   >   > ~~~
>
> - **PL/SQL**
>
>   > - 存储过程/函数（用于执行系列的SQL）
>   > - 程序包（函数与存储过程的应用）
>   > - 触发器（基于DML，DDL,数据库事件触发任务）
>
> - **重做日志文件 **(redo log file)
>
>   > 记入数据库中数据变化
>
> - **归档日志文件**
>
> - **警报和跟踪日志文件**
>
> - **备份文件**
>
>    

