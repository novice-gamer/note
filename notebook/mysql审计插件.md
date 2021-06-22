[toc]

此次采用MariaDB的server_audit插件来记录

# 安装插件

查看MySQL插件存放目录：

```shell
mysql> show global variables like 'plugin_dir';
+---------------+-------------------------------------+
| Variable_name | Value                               |
+---------------+-------------------------------------+
| plugin_dir    | /data/application/mysql/lib/plugin/ |
+---------------+-------------------------------------+
1 row in set (0.03 sec)
```



下载MariaDB包提取插件：

```shell
wget https://mirrors.tuna.tsinghua.edu.cn/mariadb/mariadb-10.5.10/bintar-linux-x86_64/mariadb-10.5.10-linux-x86_64.tar.gz

tar xf mariadb-10.5.10-linux-x86_64.tar.gz
cp mariadb-10.5.10-linux-x86_64/lib/plugin/server_audit.so  /data/application/mysql/lib/plugin/

```

修改权限和用户属性

```shell
cd /data/application/mysql/lib/plugin/
chmod +x server_audit.so
chown mysql.mysql server_audit.so
```

MySQL安装插件：

```mysql
mysql> install plugin server_audit soname 'server_audit.so';
Query OK, 0 rows affected (0.01 sec)
也可以在my.cnf 设置 plugin_load = server_audit=server_audit.so    #载入审计插件，
# 查看插件安装情况
mysql> show plugins;
```



更改配置文件：

```shell
server_audit_logging = ON                 #开启日志记录，默认是关闭
server_audit = FORCE_PLUS_PERMANENT        #防止插件被卸载
server_audit_file_path = server_audit.log     #定义审计日志文件名
server_audit_file_rotate_now = OFF         #是否强制切割审计日志
server_audit_file_rotate_size = 1073741824   #定义切割审计日志的文件大小1073741824=1GB
server_audit_file_rotations = 0              #定义审计日志的轮询个数，0为不轮询
```

重启MySQL生效

或者临时启动

```shell
mysql> set  global  server_audit_logging=on;
mysql> set  global  server_audit_logging=off;
```



日志效果:

```shell
[root@VM-0-16-centos logs]# cat server_audit.log 
20210511 15:05:02,VM-0-16-centos,root,localhost,2,0,CONNECT,,,0
20210511 15:05:02,VM-0-16-centos,root,localhost,2,1,QUERY,,'select @@version_comment limit 1',0
20210511 15:05:05,VM-0-16-centos,root,localhost,2,2,QUERY,,'show databases',0
20210511 15:05:11,VM-0-16-centos,root,localhost,2,0,DISCONNECT,,,0
20210511 15:07:29,VM-0-16-centos,root,localhost,3,0,CONNECT,,,0
20210511 15:07:29,VM-0-16-centos,root,localhost,3,4,QUERY,,'select @@version_comment limit 1',0
20210511 15:07:35,VM-0-16-centos,root,localhost,3,5,QUERY,,'SELECT DATABASE()',0
20210511 15:07:35,VM-0-16-centos,root,localhost,3,7,QUERY,mysql,'show databases',0
20210511 15:07:35,VM-0-16-centos,root,localhost,3,8,QUERY,mysql,'show tables',0
20210511 15:07:38,VM-0-16-centos,root,localhost,3,40,QUERY,mysql,'show tables',0
20210511 15:07:46,VM-0-16-centos,root,localhost,3,41,QUERY,mysql,'select * from db',0
20210511 15:07:49,VM-0-16-centos,root,localhost,3,0,DISCONNECT,mysql,,0
```

# 参数说明

查看参数：

```mysql

mysql> show variables like "%server_audit%";
+-------------------------------+-----------------------------------------------+
| Variable_name                 | Value                                         |
+-------------------------------+-----------------------------------------------+
| server_audit_events           |                                               |
| server_audit_excl_users       |                                               |
| server_audit_file_path        | /data/application/mysql/logs/server_audit.log |
| server_audit_file_rotate_now  | OFF                                           |
| server_audit_file_rotate_size | 1073741824                                    |
| server_audit_file_rotations   | 0                                             |
| server_audit_incl_users       |                                               |
| server_audit_loc_info         |                                               |
| server_audit_logging          | ON                                            |
| server_audit_mode             | 1                                             |
| server_audit_output_type      | file                                          |
| server_audit_query_log_limit  | 1024                                          |
| server_audit_syslog_facility  | LOG_USER                                      |
| server_audit_syslog_ident     | mysql-server_auditing                         |
| server_audit_syslog_info      |                                               |
| server_audit_syslog_priority  | LOG_INFO                                      |
+-------------------------------+-----------------------------------------------+
16 rows in set (0.00 sec)
```

```shell
server_audit_events ：指定记录事件的类型，可以用逗号分隔的多个值
server_audit_excl_users ： 该列表的用户[行为]将不记录，connect信息将不受该设置影响
server_audit_file_path ：使用该变量设置存储日志的文件，可以指定目录，默认存放在数据目录的server_audit.log文件中
server_audit_file_rotate_now ：知否立即切割日志
server_audit_file_rotate_size ：限制日志文件的大小
server_audit_file_rotations ：指定日志文件的数量，如果为0日志将从不轮转
server_audit_incl_users ： 指定哪些用户的活动将记录，connect将不受此变量影响，该变量比server_audit_excl_users优先级高
server_audit_loc_info ：
server_audit_logging ：启动或关闭审计ON/OFF
server_audit_mode ：标识版本，用于开发测试
server_audit_output_type ：指定日志输出类型，可为SYSLOG或FILE，当为syslog时记录到/var/log/messages
server_audit_query_log_limit ：1024
server_audit_syslog_facility ：LOG_USER
server_audit_syslog_ident ：mysql-server_auditing
server_audit_syslog_info ：
server_audit_syslog_priority ：LOG_INFO
```



设置记录事件类型，默认记录全部。

server_audit_events = query,table,query_ddl,query_dml

```
事件类型

CONNECT：连接、断开连接和失败的连接，包括错误代码

QUERY：以纯文本形式执行的查询及其结果，包括由于语法或权限错误而失败的查询

TABLE：受查询执行影响的表

QUERY_DDL：与QUERY相同，但只筛选DDL类型的查询（create、alter、drop、rename和truncate语句，create/drop[procedure/function/user]和rename user除外（它们不是DDL）

QUERY_DML：与QUERY相同，但只筛选DML类型的查询（do、call、load data/xml、delete、insert、select、update、handler和replace语句）

QUERY_DCL：与QUERY相同，但只筛选DCL类型的查询（create user、drop user、rename user、grant、revoke和set password语句）

QUERY_DML_NO_SELECT：与QUERY_DML相同，但不记录SELECT查询。（从1.4.4版开始）（do、call、load data/xml、delete、insert、update、handler和replace语句）

```



# 卸载插件

清空my.cnf相关配置，清空后重启，之后在MySQL里卸载

```mysql
mysql>  UNINSTALL PLUGIN server_audit;
```







