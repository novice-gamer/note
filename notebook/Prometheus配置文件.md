

默认配置文件：

```shell
# my global config
global:
	# 默认抓取周期，可用单位ms、s、m、h、d、w、y #设置每15s采集数据一次，默认1分钟
  scrape_interval:     15s # Set the scrape interval to every 15 seconds. Default is every 1 minute.
  # 估算规则的默认周期 # 每15秒计算一次规则。默认1分钟
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
```

默认配置文件中定义了4个YAML块：global、alerting、rule_files和scrape_configs。

## **global**

配置的第一部分是global，它包含了控制Prometheus服务器行为的全局配置。

scrape_interval用来指定应用程序或服务抓取数据的时间间隔（在示例中是15秒）。这个值是时间序列的颗粒度，即该序列中每个数据点所覆盖的时间段。

在从特定位置收集指标时，有可能会覆盖这个全局抓取间隔。强烈建议不要这么做！可以在服务器上保持一个全局抓取间隔，这会确保你的所有时间序列具有相同的颗粒度，并且可以组合在一起计算。但是，当使用了不同的数据间隔来收集数据时，则可能产生不合逻辑的结果。

参数evaluation_interval用来指定Prometheus评估规则的频率。目前主要有两种规则：记录规则（recording rule）和警报规则（alerting rule）。

记录规则：允许预先计算使用频繁且开销大的表达式，并将结果保存为一个新的时间序列数据。

警报规则：允许定义警报条件。根据这个参数，Prometheus将每隔15秒（重新）评估这些规则。

## **alerting**

配置的第二部分是alerting，它用来设置Prometheus的警报。正如我们在上一章中提到的，警报是由名为Alertmanager的独立工具进行管理的。Alertmanager是一个可以集群化的独立警报管理工具。

```shell
alerting:
  alertmanager:
  - static_configs:
    - target:
    # - alertmanager:9093
```

在默认配置中，alerting部分包含服务器的警报配置，其中alertmanagers块会列出Prometheus服务器使用的每个Alertmanager，static_configs块表示我们要手动指定在targets数组中配置的Alertmanager。

提示 Prometheus还支持Alertmanager的服务发现功能。例如，你可以通过查询外部源（如Consul服务器）来返回可用的Alertmanager列表，而不是单独指定每个Alertmanager。

## **Rule files**

rule_files指定了一组规则文件，可以包含记录规则或警报规则。

规则文件的语法是：

```shell
groups:
  [ - <rule_group> ]
```



## **scrape_configs**

配置的最后一部分是scrape_configs，用来指定Prometheus抓取的所有目标。Prometheus将它抓取的指标的数据源称为端点。为了抓取这些端点的数据，Prometheus定义了一个目标，这个目标里包含的信息是抓取数据所必需的，比如用到的标签、建立连接所需的身份验证，或者其他定义数据抓取的信息。若干目标构成的组称为作业，作业里每个目标都有一个名为实例（instance）的标签，用来唯一标识这个目标。

```shell
scrape_config:
  - job_name: 'prometheus'
  static_configs:
    - targets: ['localhost:9090']
```

默认配置中定义了一个作业prometheus，它的static_configs参数部分列出了抓取的目标，这些特定的目标被单独列出来，而不是通过自动服务发现。你也可以将静态配置理解为手动或人工服务发现。

作业prometheus只有一个监控目标：Prometheus服务器自身。它从本地的9090端口抓取数据并返回服务器的健康指标。Prometheus假设抓取的指标将返回到/metrics路径下，因此它会被追加到目标中然后抓取地址http://localhost:9090/metrics。

注意：在所有获取配置中<job_name>必须是唯一的。

### static_configs

```shell
  # 静态配置
  static_configs:
  # 指定要抓取的目标地址
  - targets: ['localhost:9090', 'localhost:9191']
    # 给抓取出来的所有指标添加指定的标签
    labels:
      my: label
      your: label
```





## 检查配置文件

```shell
# 检查配置文件是否正确
promtool check config prometheus.yml
```

检查通过如下：

```shell
[root@VM-0-16-centos prometheus]# ./promtool check config prometheus.yml
Checking prometheus.yml
  SUCCESS: 0 rule files found
```

