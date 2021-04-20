[toc]

## Firewall介绍

> firewalld是CentOS 7.0新推出的管理netfilter的用户空间软件工具
> firewalld是配置和监控防火墙规则的系统守护进程。可以实iptables,ip6tables,ebtables的功能
> firewalld服务由firewalld包提供
> firewalld支持划分区域zone,每个zone可以设置独立的防火墙规则
>
> 相较于iptables防火墙而言，firewalld支持动态更新技术并加入了区域（zone）的概念。简单来说，区域就是firewalld预先准备了几套防火墙策略集合（策略模板），用户可以根据生产场景的不同而选择合适的策略集合，从而实现防火墙策略之间的快速切换。
>
> iptables每一个更改都需要先清除所有旧有的规则，然后重新加载所有的规则（包括新的和修改后的规则）；而firewalld任何规则的变更都不需要对整个防火墙规则重新加载。

**firewalld中常用的区域名称及策略规则**

| 区域（noze） | 默认策略规则                                                 |
| :----------- | :----------------------------------------------------------- |
| trusted      | 允许所有的数据包进出                                         |
| home         | 拒绝进入的流量，除非与出去的流量相关；而如果流量与ssh、mdns、ipp-client、amba-client与dhcpv6-client服务相关，则允许进入 |
| Internal     | 等同于home区域                                               |
| work         | 拒绝进入的流量，除非与出去的流量相关；而如果流量与ssh、ipp-client与dhcpv6-client服务相关，则允许进入 |
| public       | 拒绝进入的流量，除非与出去的流量相关；而如果流量与ssh、dhcpv6-client服务相关，则允许进入 |
| external     | 拒绝进入的流量，除非与出去的流量相关；而如果流量与ssh服务相关，则允许进入 |
| dmz          | 拒绝进入的流量，除非与出去的流量相关；而如果流量与ssh服务相关，则允许进入 |
| block        | 拒绝进入的流量，除非与出去的流量相关                         |
| drop         | 拒绝进入的流量，除非与出去的流量相关                         |

> 注意：firewalld默认出口是全放开的

Firewall 主要文件：

```shell
/etc/firewalld/		# 用户配置文件
├── firewalld.conf
├── helpers
├── icmptypes
├── ipsets
├── lockdown-whitelist.xml
├── services
└── zones
    ├── public.xml
    └── public.xml.old
    
/usr/lib/firewalld # 系统配置文件，预定义配置
```

firewalld有基于CLI（命令行界面）和基于GUI（图形用户界面)两种管理方式，即：firewall-cmd（终端管理工具）和firewall-config（图形管理工具）。

如果要使用firewall-config需要安装：

```shell
yum -y install firewall-config
```



## firewall-cmd操作

<details>   
  <summary><mark>展开查看</mark></summary>   
  <pre><code>
  System.out.println("Hello");   
  </code></pre>
</details>

```shell
Status Options
  --state             				 			# 返回并打印防火墙状态
  --reload             							# 重新加载防火墙并保留状态信息
  --complete-reload    							# 重新加载防火墙并丢失状态信息
  --runtime-to-permanent						# 通过运行时配置创建永久文件
  --check-config       							# 检查永久性配置是否有错误
  
Log Denied Options
  --get-log-denied     							# 打印被拒绝的日志
  --set-log-denied=<value>					# 设置日志拒绝值

Automatic Helpers Options
  --get-automatic-helpers						# 打印自动助手值
  --set-automatic-helpers=<value>		# 设置自动助手值

Permanent Options
  --permanent          							# 永久设置一个选项（可用于标有[P]的选项）

Zone Options
  --get-default-zone   # 打印连接和接口的默认区域
  --set-default-zone=<zone>		# 设置默认区域
  --get-active-zones   # 打印当前活动区域
  --get-zones          # 打印预定义区域 [P]
  --get-services       # 打印预定义的服务 [P]
  --get-icmptypes      # 打印预定义的icmptypes [P]
  --get-zone-of-interface=<interface>	# 打印接口绑定到的区域名称 [P]
  --get-zone-of-source=<source>[/<mask>]|<MAC>|ipset:<ipset>	# 打印源绑定到的区域名称 [P]
  --list-all-zones     # 列出为所有区域添加或启用的所有内容 [P]
  --new-zone=<zone>    # 添加一个新区域 [P only]
  --new-zone-from-file=<filename> [--name=<zone>]		# 从文件中添加具有可选名称的新区域 [P only]
  --delete-zone=<zone> 	# 删除现有区域 [P only]
  --load-zone-defaults=<zone>		# 加载区域默认设置 [P only] [Z]
  --zone=<zone>        # 使用此区域设置或查询选项，否则使用默认区域 (可用于标有[Z]的选项)
  --get-target         # 获取区域目标 [P only] [Z]
  --set-target=<target>  # 设定区域目标 [P only] [Z]
  --info-zone=<zone>   # 打印有关区域的信息
  --path-zone=<zone>   # 打印区域的文件路径 [P only]

IPSet Options
  --get-ipset-types    # 打印支持的ipset类型
  --new-ipset=<ipset> --type=<ipset type> [--option=<key>[=<value>]]..  # 添加一个新的ipset [P only]
  --new-ipset-from-file=<filename> [--name=<ipset>]   # 从文件中添加具有可选名称的新ipset [P only]
  --delete-ipset=<ipset>   # 删除现有的ipset [P only]
  --load-ipset-defaults=<ipset>  # 加载ipset默认设置 [P only]
  --info-ipset=<ipset> # 打印有关ipset的信息
  --path-ipset=<ipset> # 打印ipset的文件路径 [P only]
  --get-ipsets         # 打印预定义的ipset
  --ipset=<ipset> --set-description=<description>  # 将新描述设置为ipset [P only]
  --ipset=<ipset> --get-description   # 打印ipset的描述 [P only]
  --ipset=<ipset> --set-short=<description>  # 将新的简短描述设置为ipset [P only]
  --ipset=<ipset> --get-short  # 打印ipset的简短描述 [P only]
  --ipset=<ipset> --add-entry=<entry> # 将新条目添加到ipset [P]
  --ipset=<ipset> --remove-entry=<entry> # 从ipset中删除条目 [P]
  --ipset=<ipset> --query-entry=<entry> # 返回ipset是否有条目 [P]
  --ipset=<ipset> --get-entries  # 列出一个ipset的条目 [P]
  --ipset=<ipset> --add-entries-from-file=<entry> # 将新条目添加到ipset [P]
  --ipset=<ipset> --remove-entries-from-file=<entry>  # 从ipset中删除条目 [P]
  
 IcmpType Options
  --new-icmptype=<icmptype>  # 添加新的icmptype [P only]
  --new-icmptype-from-file=<filename> [--name=<icmptype>] # 从具有可选名称的文件中添加新的icmptype [P only]
  --delete-icmptype=<icmptype> # 删除现有的icmptype [P only]
  --load-icmptype-defaults=<icmptype> # 加载icmptype默认设置 [P only]
  --info-icmptype=<icmptype>  # 显示有关icmptype的信息
  --path-icmptype=<icmptype>  # 打印icmptype的文件路径 [P only]
  --icmptype=<icmptype> --set-description=<description>  # 为icmptype设置新描述 [P only]
  --icmptype=<icmptype> --get-description  # 打印icmptype的描述 [P only]
  --icmptype=<icmptype> --set-short=<description>  #  为icmptype设置新的简短描述 [P only]
  --icmptype=<icmptype> --get-short  # 打印icmptype的简短描述 [P only]
  --icmptype=<icmptype> --add-destination=<ipv>  #  在icmptype中启用ipv的目标 [P only]
  --icmptype=<icmptype> --remove-destination=<ipv>  # 在icmptype中禁用ipv的目标 [P only]
  --icmptype=<icmptype> --query-destination=<ipv>  #  返回是否在icmptype中启用了目标ipv [P only]
  --icmptype=<icmptype> --get-destinations  #  列出icmptype中的目的地 [P only]
  
  
 Service Options
  --new-service=<service>  # 新增服务 [P only]
  --new-service-from-file=<filename> [--name=<service>] # 从文件中添加具有可选名称的新服务 [P only]
  --delete-service=<service>  # 删除现有服务 [P only]
  --load-service-defaults=<service>  # 加载icmptype默认设置 [P only]
  --info-service=<service>  #  打印有关服务的信息
  --path-service=<service> # 打印服务的文件路径 [P only]
  --service=<service> --set-description=<description>  # 设置新的服务描述 [P only]
  --service=<service> --get-description # 打印服务说明 [P only]
  --service=<service> --set-short=<description>   # 设置新的简短服务说明 [P only]
  --service=<service> --get-short #  打印服务简短说明 [P only]
  --service=<service> --add-port=<portid>[-<portid>]/<protocol>  # 为服务添加新端口 [P only]
  --service=<service> --remove-port=<portid>[-<portid>]/<protocol>   #  从服务中删除端口 [P only]
  --service=<service> --query-port=<portid>[-<portid>]/<protocol>     #  返回是否已添加端口以进行服务 [P only]
  --service=<service> --get-ports    #  列出服务端口 [P only]
  --service=<service> --add-protocol=<protocol>  #  向服务添加新协议 [P only]
  --service=<service> --remove-protocol=<protocol>  #  从服务中删除协议 [P only]
  --service=<service> --query-protocol=<protocol>   #  返回是否已为服务添加协议 [P only]
  --service=<service> --get-protocols   #  列出服务协议 [P only]
  --service=<service> --add-source-port=<portid>[-<portid>]/<protocol>    # 为服务添加新的源端口 [P only]
  --service=<service> --remove-source-port=<portid>[-<portid>]/<protocol>  #  从服务中删除源端口 [P only]
  --service=<service> --query-source-port=<portid>[-<portid>]/<protocol>  #  返回是否已经为服务添加了源端口 [P only]
  --service=<service> --get-source-ports    #  列出服务的源端口 [P only]
  --service=<service> --add-module=<module>  # 向服务添加新模块 [P only]
  --service=<service> --remove-module=<module>    # 从服务中删除模块 [P only]
  --service=<service> --query-module=<module>   #  返回是否已添加该模块进行维修 [P only]
  --service=<service> --get-modules  # 列出服务模块 [P only]
  --service=<service> --set-destination=<ipv>:<address>[/<mask>]   #  设置ipv的目标地址以启用服务 [P only]
  --service=<service> --remove-destination=<ipv>   # 禁用ipv i服务的目标 [P only]
  --service=<service> --query-destination=<ipv>:<address>[/<mask>]   # 返回是否为服务设置了目标ipv [P only]
  --service=<service> --get-destinations   # 列出服务中的目的地 [P only]
  
  Options to Adapt and Query Zones
  --list-all           # 列出为区域添加或启用的所有内容 [P] [Z]
  --list-services      # 列出为区域添加的服务 [P] [Z]
  --timeout=<timeval>  # 启用timeval时间选项，其中timeval为一个数字，后跟字母“ s”或“ m”或“ h”之一,可用于标有[T]的选项
  --set-description=<description>   #  为区域设置新的说明 [P only] [Z]
  --get-description    # 打印区域说明 [P only] [Z]
  --set-short=<description>   # 在区域中设置新的简短描述 [P only] [Z]
  --get-short          # 打印区域的简短描述 [P only] [Z]
  --add-service=<service>    # 为区域添加服务 [P] [Z] [T]
  --remove-service=<service>  # 从区域中删除服务 [P] [Z]
  --query-service=<service>  #  返回是否已为区域添加服务 [P] [Z]
  --list-ports         # 列出为区域添加的端口 [P] [Z]
  --add-port=<portid>[-<portid>]/<protocol>  #  添加区域的端口 [P] [Z] [T]
  --remove-port=<portid>[-<portid>]/<protocol>  # 从区域中删除端口 [P] [Z]
  --query-port=<portid>[-<portid>]/<protocol>  #  返回是否已为区域添加端口 [P] [Z]
  --list-protocols    # 列出为区域添加的协议 [P] [Z]
  --add-protocol=<protocol>   # 添加区域协议 [P] [Z] [T]
  --remove-protocol=<protocol>   #  从区域中删除协议 [P] [Z]
  --query-protocol=<protocol> #  返回是否已为区域添加协议 [P] [Z]
  --list-source-ports  # 列出为区域添加的源端口 [P] [Z]
  --add-source-port=<portid>[-<portid>]/<protocol>  #  添加区域的源端口 [P] [Z] [T]
  --remove-source-port=<portid>[-<portid>]/<protocol>   # 从区域中删除源端口 [P] [Z]
  --query-source-port=<portid>[-<portid>]/<protocol>  # 返回是否为区域添加了源端口 [P] [Z]
  --list-icmp-blocks   # 列出为区域添加的Internet ICMP类型阻止 [P] [Z]
  --add-icmp-block=<icmptype>    # 为区域添加ICMP块 [P] [Z] [T]
  --remove-icmp-block=<icmptype>   # 从区域中删除ICMP块 [P] [Z]
  --query-icmp-block=<icmptype>   # 返回是否已为区域添加ICMP块 [P] [Z]
  --add-icmp-block-inversion   # 启用区域的icmp块反转 [P] [Z]
  --remove-icmp-block-inversion  #  禁用区域的icmp块反转 [P] [Z]
  --query-icmp-block-inversion   # 返回是否已启用icmp块反转用于区域 [P] [Z]
  --list-forward-ports   # 列出为区域添加的IPv4转发端口 [P] [Z]
  --add-forward-port=port=<portid>[-<portid>]:proto=<protocol>[:toport=<portid>[-<portid>]][:toaddr=<address>[/<mask>]]   #  为区域添加IPv4转发端口 [P] [Z] [T]
  --remove-forward-port=port=<portid>[-<portid>]:proto=<protocol>[:toport=<portid>[-<portid>]][:toaddr=<address>[/<mask>]]   # 从区域中删除IPv4转发端口 [P] [Z]
  --query-forward-port=port=<portid>[-<portid>]:proto=<protocol>[:toport=<portid>[-<portid>]][:toaddr=<address>[/<mask>]]   # 返回是否为区域添加了IPv4转发端口 [P] [Z]
  --add-masquerade     # 启用区域的IPv4伪装 [P] [Z] [T]
  --remove-masquerade  # 禁用区域的IPv4伪装 [P] [Z]
  --query-masquerade   # 返回是否已为区域启用IPv4伪装 [P] [Z]
  --list-rich-rules    # 列出为区域添加的丰富语言规则 [P] [Z]
  --add-rich-rule=<rule>  # 为区域添加丰富的语言规则“规则” [P] [Z] [T]
  --remove-rich-rule=<rule> #  从区域中删除丰富语言规则“规则” [P] [Z]
  --query-rich-rule=<rule>   #  返回是否为区域添加了丰富的语言规则“规则”[P] [Z]
```





