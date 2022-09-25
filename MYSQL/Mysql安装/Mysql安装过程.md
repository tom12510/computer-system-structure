## Mysql8.0安装过程

~~~shell
# 解压
tar -zxvf mysql-8.0.26-linux-glibc2.12-x86_64.tar.xz
# 重命名并移动到/usr/local应用目录中
mv mysql-8.0.26-linux-glibc2.12-x86_64 /usr/local/mysql
# 创建数据库目录
mkdir /usr/local/mysql/data
# 添加mysql用户组
group add mysql 
useradd -g mysql mysql 
# 将mysql目录所属权限赋值mysql用户
chown -R mysql.mysql /usr/local/mysql
# 初始化mysql数据库
/usr/local/mysql/bin/mysqld --user=mysql --basedir=/usr/local/mysql --datadir=/usr/local/mysql/data  --initialize 
# 在日志记入mysql临时密码

#创建mysql启动命令
cp -a /usr/local/mysql/support-files/mysql.server /etc/init.d/mysqld 
chmod +x /etc/rc.d/init.d/mysqld 
chkconfig --add mysqld
chkconfig --list mysqld
#设置mysql环境变量
vim /etc/profile
export PATH=$PATH:/usr/local/mysql/bin:/usr/local/mysql/lib
export PATH
#启动mysql服务
service mysql start
#进入mysql控制台更改临时密码
mysql -u root -p
ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY '123456';
#设置远程登陆
use mysql;
update user set host="%" where user='root' limit 1;
flush privileges;

~~~

