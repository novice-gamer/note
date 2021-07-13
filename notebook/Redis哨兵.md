> 操作系统：CentOS Linux release 7.6.1810 (Core) 
>
> Redis版本：redis-6.2.4

- 机器地址：

| IP                     | Redis端口 | sentinel端口 |
| ---------------------- | --------- | ------------ |
| 192.168.174.7(master)  | 6379      | 26379        |
| 192.168.174.8(slave-1) | 6379      | 26379        |
| 192.168.174.9(slave-2) | 6379      | 26379        |



+ 配置主从

三个配置文件基本一样，不同之处如下：

Master :

```shell
# redis.conf
requirepass 123456
bind 192.168.174.7
```

Slave-1：

```shell
# redis.conf
bind 192.168.174.8
requirepass 123456
masterauth 123456
replica-read-only yes
replica-priority 100
replicaof 192.168.174.7 6379
```

Slave-2：

```shell
# redis.conf
bind 192.168.174.9
requirepass 123456
masterauth 123456
replica-read-only yes
replica-priority 100
replicaof 192.168.174.7 6379
```

查看master的info信息：

```shell
# Replication
role:master
connected_slaves:2
slave0:ip=192.168.174.8,port=6379,state=online,offset=308,lag=0
slave1:ip=192.168.174.9,port=6379,state=online,offset=308,lag=0
master_failover_state:no-failover
master_replid:0ee0e53ee92953e79cbf6e00ef96c49d2f5e9fa8
master_replid2:0000000000000000000000000000000000000000
master_repl_offset:308
second_repl_offset:-1
repl_backlog_active:1
repl_backlog_size:1048576
repl_backlog_first_byte_offset:1
repl_backlog_histlen:308
```



+ 哨兵模式

Master 配置文件

```shell
port 26379
daemonize no
pidfile /var/run/redis-sentinel.pid
logfile "/application/redis-6.2.4/log/redis-sentinel.log"
dir /tmp
sentinel monitor mymaster 192.168.174.7 6379 2
sentinel auth-pass mymaster 123456
sentinel down-after-milliseconds mymaster 30000
acllog-max-len 128
sentinel parallel-syncs mymaster 1
sentinel failover-timeout mymaster 180000
sentinel deny-scripts-reconfig yes
SENTINEL resolve-hostnames no
SENTINEL announce-hostnames no
```

```
参数解释：

sentinel monitor <master-name> <ip> <port> <quorum>：

master-name：监控的名字（任意）
ip port：master节点ip端口，不需要配置从节点信息
quorum：代表要判定主节点最终不可达所需要的票数

sentinel down-after-milliseconds <master-name> <times>：
每个Sentinel节点都要通过定期发送ping命令来判断Redis数据节点和其余Sentinel节点是否可达，如果超过了down-after-milliseconds配置的时间且没有有效的回复，则判定节点不可达，<times>（单位为毫秒）

sentinel parallel-syncs <master-name> <nums>：
故障转移后每次向新的主节点发起复制操作的从节点个数

sentinel failover-timeout <master-name> <times>：
故障转移超时时间

sentinel auth-pass <master-name> <password>：
master节点的密码

sentinel notification-script <master-name> <script-path>：
在故障转移期间，当一些警告级别的Sentinel事件发生（指重要事件，例如-sdown：客观下线、-odown：主观下线）时，会触发对应路径的脚本，并向脚本发送相应的事件参数

sentinel client-reconfig-script <master-name> <script-path>：
在故障转移结束后，会触发对应路径的脚本，并向脚本发送故障转移结果的相关参数。和notification-script类似
```

Slave-01配置文件：

```shell
port 26379
daemonize no
pidfile /var/run/redis-sentinel.pid
logfile "/application/redis-6.2.4/log/redis-sentinel.log"
dir /tmp
sentinel monitor mymaster 192.168.174.7 6379 2
sentinel auth-pass mymaster 123456
sentinel down-after-milliseconds mymaster 30000
acllog-max-len 128
sentinel parallel-syncs mymaster 1
sentinel failover-timeout mymaster 180000
sentinel deny-scripts-reconfig yes
SENTINEL resolve-hostnames no
SENTINEL announce-hostnames no
```

Slave-02配置文件：

```shell
port 26379
daemonize no
pidfile /var/run/redis-sentinel.pid
logfile "/application/redis-6.2.4/log/redis-sentinel.log"
dir /tmp
sentinel monitor mymaster 192.168.174.7 6379 2
sentinel auth-pass mymaster 123456
sentinel down-after-milliseconds mymaster 30000
acllog-max-len 128
sentinel parallel-syncs mymaster 1
sentinel failover-timeout mymaster 180000
sentinel deny-scripts-reconfig yes
SENTINEL resolve-hostnames no
SENTINEL announce-hostnames no
```

启动命令：

```shell
# 依次启动
./src/redis-sentinel ./sentinel.conf
```



注意：当master下线后，在上线时注意配置文件主从配置已重置，需要重新配置。

