> 系统版本：CentOS Linux release 7.5.1804 (Core)
>
> 防火墙：firewall(0.6.3)

+ **方法一：临时禁ping（重启失效）**

```shell
# 默认配置为0,表示启用Ping
[root@VM-0-16-centos ~]# cat /proc/sys/net/ipv4/icmp_echo_ignore_all
0

# 修改为1表示禁Ping
echo 1 > /proc/sys/net/ipv4/icmp_echo_ignore_all

# 修改为0表示启用Ping
echo 0 > /proc/sys/net/ipv4/icmp_echo_ignore_all
```



+ **方法二：修改配置文件（永久生效）**

```shell
# 修改/etc/sysctl.conf文件（没有配置项添加，有配置项直接修改值即可）
net.ipv4.icmp_echo_ignore_all = 1  # 表示禁Ping
net.ipv4.icmp_echo_ignore_all = 0  # 表示启用Ping

# 修改完后使用下面命令刷新配置
sysctl -p
```



+ **方法三：防火墙配置**

```shell
# 添加规则禁Ping
firewall-cmd --permanent --add-rich-rule='rule protocol value=icmp drop'
# 刷新防火墙
firewall-cmd --reload
# 查看规则
[root@VM-0-16-centos ~]# firewall-cmd --list-all
public
  target: default
  icmp-block-inversion: no
  interfaces:
  sources:
  services: dhcpv6-client ssh
  ports:
  protocols:
  masquerade: no
  forward-ports:
  source-ports:
  icmp-blocks:
  rich rules:
	rule protocol value="icmp" drop	 #可以看到规则已经添加
	
# 恢复Ping
firewall-cmd --permanent --remove-rich-rule='rule protocol value=icmp drop'
之后刷新防火墙即可
```



