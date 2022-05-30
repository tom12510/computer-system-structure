## **Shell 常用命令**

- alias  设置命令别名

  ~~~shell
    alias md='mkdir'
    alias dir='ls-al'
  ~~~

- ar  建立或修改档案文件

  > ar [-dmpqrtx] [cfosSuvV] [a<成员文件>] [b<常用文件>] [i<成员文件>] [档案文件] [成员文件]
  >
  >  <img src="img\image-20220530154843998.png" alt="image-20220530154843998" style="zoom:67%;" /> 

- bg 将查询放到后台执行

  > bg [工作编号]，与&作用相同，没指定工作编号，会将当前的工作移动到后台
  >
  > jobs 查看后台程序
  >
  > fg 移动到当前shell执行
  >
  > ctrl + z  挂起进程并放入后台 (sigstop 信号)
  >
  > ctrl + c  杀死进程

- cat 连接多个文件，并将它们的内容输出到标准输出设备

  > cat [-AbeEnstTuv] [--help] [--version] [...file]
  >
  > *不指定任何文件名称，或指定文件名为 - ，则cat命令会从标准输入设备读取到数据到输出设备，也可以使用>,和>> 将多个文件合并成一个文件*
  >
  > <img src="img\image-20220530160641819.png" alt="image-20220530160641819" style="zoom:67%;" /> 

- chgrp 更改文件或目录的所属组

  > chgrp [-cfhRv]  [所属组] [文件或目录]
  >
  > ![image-20220530160922891](img\image-20220530160922891.png) 

- chmod 更改文件目录权限

  > chmod [-cfRv]  [权限范围] [文件或目录]
  >
  > 参数
  >
  > ![image-20220530160922891](img\image-20220530160922891.png)
  >
  > 范围表示
  >
  > ![image-20220530161343626](img\image-20220530161343626.png) 

- chown 

  > chown [-cfhRv] [--dereference] [--help] 
  >
  > 同上

- cmp 比较两个文件是否有差异

  > cmp [-clsv] [-i<字符数目>] [文件1] [文件2]

- cp 复制文件

  > cp [-abdfilprRsuvx] [-S <备份字尾字符串>] [来源文件或目录] [目的文件或目录]
  >
  > - -b 删除覆盖目的文件之前先备份
  > - -f 强制复制文件，忽略目标文件是否已存在
  > - -i 覆盖文件之间先询问
  > - -l 对来源文件建立硬连接(非复制)
  > - -p 保留源文件目录属性
  > - -r 递归处理（不包含目录和符号连接）
  > - -R 递归处理（指定目录下所有文件及子目录）
  > - -s 创建软连接
  > - -S 备份字符与参数-b使用
  > - -v 显示执行过程

- cpio 备份文件

  > cpio [-0aABcKLovV] [-C<输入/输出大小>] [-F<备份文件>] [-H <备份格式>]

- crontab 设置计时器

  > *让用户在指定时间内执行默认的命令或程序*
  >
  > crontab [-u <用户名称>] [设置文件]
  >
  > 参数
  >
  > ![image-20220530163601114](img\image-20220530163601114.png) 
  >
  > ![image-20220530163424534](img\image-20220530163424534.png) 

- cu 连接另一个系统主机

  > cu [-dehnotv] [-a <通信端口>] 

- df 显示磁盘的文件与使用情况

  > df [-ahHiklmPT] [-t <文件系统类型>] [-x <文件系统>]
  >
  > <img src="img\image-20220530164927243.png" alt="image-20220530164927243" style="zoom:67%;" />  

- diff 比较文件差异

  > diff [-abBcdefHilmNpPqrstTuvxy] [-<行数>] [-C <行数>]

- du 显示目录或文件大小

  > du [-abcDhHklmsSx] 

- echo 显示文本

  > echo [-ne] [...字符串] 
  >
  > ![image-20220530165336610](img\image-20220530165336610.png) 

- exit 退出当shell

  > exit [状态值]

- export  设置或显示环境遍历

  > export [-fn] [变量名称="变量值"]
  >
  > ![image-20220530165607367](img\image-20220530165607367.png) 

- fg 将程序或命令切换到前台执行

  > ![image-20220530165839935](img\image-20220530165839935.png) 

- file 判断文件类型

  > file [-bcLsvz] [-f<文件名>] [-m<魔法数字>] [文件或目录]
  >
  > ![image-20220530170022268](img\image-20220530170022268.png) 

- find 查找文件或目录

  > find [... 目录] [-amin<分钟>] [-anewer <参考文件或目录>]  [-cmin <分钟>] [-depth] [-empty] [-exec<执行命令>] 
  >
  > [-follow] 排除符号链接 
  >
  > ![image-20220530170752678](img\image-20220530170752678.png) 
  >
  > ![image-20220530170823169](img\image-20220530170823169.png) 
  >
  > ![image-20220530170843804](img\image-20220530170843804.png) 
  >
  > ![image-20220530170902421](img\image-20220530170902421.png) 
  >
  > ![image-20220530170925691](img\image-20220530170925691.png) 
  >
  > ![image-20220530170946452](img\image-20220530170946452.png) 
  >
  > ![image-20220530171010930](D:\shell\img\image-20220530171010930.png) 

- free 查看内存状态

  > free [-bkmotV] [-s<间隔秒数>]
  >
  > ![image-20220530171205595](img\image-20220530171205595.png) 

- 

