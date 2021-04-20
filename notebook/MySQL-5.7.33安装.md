[toc]

# 1. 环境说明

```
系统：CentOS Linux release 7.5.1804 (Core)
数据库版本：mysql-5.7.33-linux-glibc2.12-x86_64.tar.gz
软件存放目录：/server/tools/
软件安装目录：/application/
```

# 2. 安装准备

## 2.1 创建目录

```shell
mkdir -p /application /server/tools
```

## 2.2 下载软件包

```shell
cd /server/tools/
wget https://dev.mysql.com/get/Downloads/MySQL-5.7/mysql-5.7.33-linux-glibc2.12-x86_64.tar.gz
```

# 3. 安装

## 3.1 卸载MariaDB

安装之前先卸载CentOS 7 自带的MariaDB。

1. 检查MariaDB是否安装

    ```shell
    yum list installed | grep mariadb
    ```

2. 卸载

    ```shell
    yum -y remove mariadb*
    ```

## 3.2 安装MySQL

首先安装依赖包:

```shell
yum -y install ncurses-devel libaio-devel numactl
```

解压软件包到指定目录：

```shell
tar xf mysql-5.7.33-linux-glibc2.12-x86_64.tar.gz -C /application/
```

查看解压结果：

```shell
[root@VM-0-16-centos application]# ll
总用量 4
drwxr-xr-x 9 root root 4096 4月   2 17:59 mysql-5.7.33-linux-glibc2.12-x86_64
```

更改名称（可选）：

```shell
mv mysql-5.7.33-linux-glibc2.12-x86_64 mysql
```

创建MySQL用户：

```shell
useradd mysql -s /sbin/nologin -M
```

初始化数据库：

```shell
# 注意提前创建data目录
cd /application/mysql
./bin/mysqld --initialize --user=mysql --datadir=/application/mysql/data/ --basedir=/application/mysql

# 输出信息（注意生成的随机密码）
2021-04-07T06:08:32.380134Z 0 [Warning] TIMESTAMP with implicit DEFAULT value is deprecated. Please use --explicit_defaults_for_timestamp server option (see documentation for more details).
2021-04-07T06:08:32.769771Z 0 [Warning] InnoDB: New log files created, LSN=45790
2021-04-07T06:08:32.878632Z 0 [Warning] InnoDB: Creating foreign key constraint system tables.
2021-04-07T06:08:33.179879Z 0 [Warning] No existing UUID has been found, so we assume that this is the first time that this server has been started. Generating a new UUID: ae9bb898-9767-11eb-8083-5254004ea4fa.
2021-04-07T06:08:33.189863Z 0 [Warning] Gtid table is not ready to be used. Table 'mysql.gtid_executed' cannot be opened.
2021-04-07T06:08:34.198722Z 0 [Warning] CA certificate ca.pem is self signed.
2021-04-07T06:08:34.514763Z 1 [Note] A temporary password is generated for root@localhost: 5,ctwHmg/qv4
```

配置文件

> 注意：从 **5.7.18** 开始不在二进制包中提供 **my-default.cnf** 文件,需要自己去创建my.cnf文件。

说明：

my.cnf文件可以自定义位置，也可以放在默认位置，MySQL会自动读取。

默认位置：

| 文件名              | 说明                                           |
| ------------------- | ---------------------------------------------- |
| /etc/my.cnf         | 全局选项                                       |
| /etc/mysql/my.cnf   | 全局选项                                       |
| SYSCONFDIR/my.cnf   | 全局选项                                       |
| $MYSQL_HOME/my.cnf  | 服务器特定选项（仅限服务器）                   |
| defaults-extra-file | 指定的文件 `--defaults-extra-file`，如果有的话 |
| ~/.my.cnf           | 用户特定选项                                   |
| ~/.mylogin.cnf      | 用户特定的登录路径选项（仅限客户端）           |

my.cnf配置文件：

[MySQL配置文件](https://www.cnblogs.com/os-linux/p/11420601.html)

模板：

```shell
[client]
port = 3306
socket = /application/mysql/mysql.sock
default-character-set = utf8mb4

[mysqld]
port = 3306
socket = /application/mysql/mysql.sock
pid-file = /application/mysql/mysql.pid
basedir = /application/mysql
datadir = /application/mysql/data
tmpdir = /application/mysql/tmp
character_set_server = utf8mb4
user = mysql
log-error=/application/mysql/logs/error.log
max_connections = 1000
binlog_format = ROW

server-id = 1
log_slave_updates = 1
expire-logs-days = 15
max_binlog_size = 1024M
relay_log_info_repository = TABLE
master_info_repository = TABLE

[mysql]
auto-rehash
```



根据配置文件创建目录或文件：

```shell
mkdir /application/mysql/logs /application/mysql/tmp/
touch /application/mysql/mysql.pid
touch /application/mysql/mysql.sock
```

修改/application/mysql/support-files/mysql.server文件

```
basedir=/application/mysql
datadir=/application/mysql/data
```

授权目录

```shell
chown -R mysql.mysql /application/mysql
```

复制启动文件：

```shell
cp /application/mysql/support-files/mysql.server /etc/init.d/mysqld
```

添加系统服务

```shell
chkconfig --add mysqld
```

启动服务：

```shell
systemctl start mysqld.service
systemctl status mysqld.service

#显示结果
● mysqld.service - MySQL Server
   Loaded: loaded (/etc/systemd/system/mysqld.service; disabled; vendor preset: disabled)
   Active: active (running) since 三 2021-04-07 14:50:38 CST; 3s ago
     Docs: man:mysqld(8)
           http://dev.mysql.com/doc/refman/en/using-systemd.html
 Main PID: 9138 (mysqld)
   CGroup: /system.slice/mysqld.service
           └─9138 /application/mysql/bin/mysqld --defaults-file=/etc/my.cnf

4月 07 14:50:38 VM-0-16-centos systemd[1]: Started MySQL Server.
```

添加命令：

```shell
 echo 'PATH=/application/mysql/bin/:$PATH' >>/etc/profile
 source /etc/profile
```

更改密码及用户权限

```shell
mysql -uroot -p5,ctwHmg/qv4  #使用随机生成的密码

#修改密码
SET PASSWORD = PASSWORD('123456');
ALTER USER 'root'@'localhost' PASSWORD EXPIRE NEVER;
FLUSH PRIVILEGES;

# 执行完上面的命令后还是无法远程连接，还需要更改用户权限
use mysql                                            #访问mysql库
update user set host = '%' where user = 'root';      #使root能再任何host访问
FLUSH PRIVILEGES; 

```

