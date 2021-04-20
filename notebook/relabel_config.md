**relabel_config**

> 重新标记是一个功能强大的工具，可以在目标的标签集被抓取之前重写它，每个采集配置可以配置多个重写标签设置，并按照配置的顺序来应用于每个目标的标签集。目标重新标签之后，以__开头的标签将从标签集中删除的。如果使用只需要临时的存储临时标签值的，可以使用_tmp作为前缀标识。

**action类型**

+ replace: 将"target_label"指定的标签的值替换为"replacement"指定的内容
+ keep: 删除与正则不匹配的目标
+ drop: 删除与正则匹配的目标
+ labelmap: 将正则与所有标签的"名称"匹配，然后用"replacement"指定的内容来替换源标签的名称，source_labels不用填写。一般用来去除标签前缀获得一个新的标签名称
+ labeldrop: 删除与正则匹配的label,labeldrop只需要写regex字段
+ labelkeep: 删除与正则不匹配的label,labelkeep只需要写regex字段



**测试action**

测试前配置文件：

```shell
# my global config
global:
  scrape_interval:     15s # Set the scrape interval to every 15 seconds. Default is every 1 minute.
  evaluation_interval: 15s # Evaluate rules every 15 seconds. The default is every 1 minute.
  # scrape_timeout is set to the global default (10s).
# Alertmanager configuration
alerting:
  alertmanagers:
  - static_configs:
    - targets:
      # - alertmanager:9093
# Load rules once and periodically evaluate them according to the global 'evaluation_interval'.
rule_files:
  # - "first_rules.yml"
  # - "second_rules.yml"
# A scrape configuration containing exactly one endpoint to scrape:
# Here it's Prometheus itself.
scrape_configs:
  # The job name is added as a label `job=<job_name>` to any timeseries scraped from this config.
  - job_name: 'prometheus'
    # metrics_path defaults to '/metrics'
    # scheme defaults to 'http'.
    static_configs:
    - targets: ['localhost:9090']

  - job_name: 'other_server'
    file_sd_configs:
      - files:
        - '/application/prometheus/other_server.yml'
        refresh_interval: 10s

```

```shell
[root@VM-0-16-centos prometheus]# cat other_server.yml
- targets:
  - "127.0.0.1:9090"
  labels:
    __hostname__: server03
    __businees_line__: "a1"
    __region_id__: "cn-beijing"
    __availability_zone__: "A1"
    __status__: "true"
- targets:
  - "127.0.0.1:9090"
  labels:
    __hostname__: server01
    __businees_line__: "a2"
    __region_id__: "cn-beijing"
    __availability_zone__: "A2"
    __status__: "false"
- targets:
  - "127.0.0.1:9090"
  labels:
    __hostname__: server02
    __businees_line__: "a3"
    __region_id__: "cn-beijing"
    __availability_zone__: "A3"
    __status__: "false"
```

此时查看target页面：

<img src="https://raw.githubusercontent.com/novice-gamer/picture/master/img/image-20210413142453756.png" alt="image-20210413142453756"  />

+ **replace**

```shell
#  __hostname__替换成name
    relabel_configs:
    - source_labels:	# 需要处理的源标签
      - "__hostname__"
      regex: "(.*)"			# 匹配__hostname__的内容，.*表示任意内容都匹配
      target_label: "name"	# 替换后的标签名
      action: replace			# replace(替换)动作
      replacement: "$1"		# 表示替换后标签(target_label)对应的值，$1表示匹配的内容
```

查看target页面：

![image-20210413143621741](https://raw.githubusercontent.com/novice-gamer/picture/master/img/image-20210413143621741.png)

将正则表达式更换一下：regex: "(server02)”,可以看到只有server02被替换了

![image-20210413144428862](https://raw.githubusercontent.com/novice-gamer/picture/master/img/image-20210413144428862.png)



还可以利用replace来让两个字段进行连接：

```shell
    relabel_configs:
    - source_labels:
      - "__region_id__"
      - "__availability_zone__"
      separator: "-"		# 标签值的间隔符，默认;
      target_label: "zone"
      regex: "(.*)"
      action: replace
      replacement: "$1"
```

![image-20210413150436584](https://raw.githubusercontent.com/novice-gamer/picture/master/img/image-20210413150436584.png)



+ **keep** 

```shell
    relabel_configs:
    - source_labels:
      - "__hostname__"
      regex: "(server01)"
      action: keep
```

利用keep来只匹配`__hostname__`值为server01的实例。

![image-20210413145200299](https://raw.githubusercontent.com/novice-gamer/picture/master/img/image-20210413145200299.png)



+ **drop**

```shell
    relabel_configs:
    - source_labels:
      - "__hostname__"
      regex: "(server01)"
      action: drop
```

利用drop 来实现不匹配`__hostname__`值为server01的实例

![image-20210413145550497](https://raw.githubusercontent.com/novice-gamer/picture/master/img/image-20210413145550497.png)



