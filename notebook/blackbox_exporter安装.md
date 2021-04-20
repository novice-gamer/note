> 介绍：blackbox_exporter是Prometheus的一个插件，可以提供 http、dns、tcp、icmp 的监控数据采集。

## **应用场景**

```shell
HTTP 测试
  定义 Request Header 信息
  判断 Http status / Http Respones Header / Http Body 内容
TCP 测试
  业务组件端口状态监听
  应用层协议定义与监听
ICMP 测试
  主机探活机制
  POST 测试
  接口联通性
SSL 证书过期时间
```

## **安装部署**

下载安装包

```shell
wget https://github.com/prometheus/blackbox_exporter/releases/download/v0.18.0/blackbox_exporter-0.18.0.linux-amd64.tar.gz
```

解压安装包到安装目录：

```shell
tar xf blackbox_exporter-0.18.0.linux-amd64.tar.gz -C /application/
```

进入安装目录，修改文件名(可选操作)：

```shell
mv blackbox_exporter-0.18.0.linux-amd64.tar.gz blackbox_exporter
```

验证是否安装成功：

```shell
[root@VM-0-16-centos application]# cd blackbox_exporter/
[root@VM-0-16-centos blackbox_exporter]# ./blackbox_exporter --version
blackbox_exporter, version 0.18.0 (branch: HEAD, revision: 60c86e6ce5a1111f7958b06ae7a08222bb6ec839)
  build user:       root@53d72328d93f
  build date:       20201012-09:46:31
  go version:       go1.15.2
```

加入systemctl服务：

```shell
vim /lib/systemd/system/blackbox_exporter.service

[Unit]
Description=blackbox_exporter
After=network.target

[Service]
User=root
Type=simple
ExecStart=/application/blackbox_exporter/blackbox_exporter --config.file=/application/blackbox_exporter/blackbox.yml
Restart=on-failure

[Install]
WantedBy=multi-user.target
```

启动服务：

```shell
systemctl daemon-reload
systemctl start blackbox_exporter.service
```

查看端口：

```shell
[root@VM-0-16-centos blackbox_exporter]# netstat -lntup | grep 9115
tcp6       0      0 :::9115                 :::*                    LISTEN      15781/blackbox_expo
```

## 加入Prometheus进行监控

示例：

```shell
# 监控主机存活状态
- job_name: node_status
    metrics_path: /probe
    params:
      module: [icmp]
    static_configs:
      - targets: ['127.0.0.1']
        labels:
          instance: node_status
          group: 'node'
    relabel_configs:
      - source_labels: [__address__]
        target_label: __param_target
      - target_label: __address__
        replacement: 127.0.0.1:9115
```

```shell
# 监控端口存活状态
- job_name: 'prometheus_port_status'
    metrics_path: /probe
    params:
      module: [tcp_connect]
    static_configs:
      - targets: ['127.0.0.1:80']
        labels:
          instance: 'port_status'
          group: 'tcp'
    relabel_configs:
      - source_labels: [__address__]
        target_label: __param_target
      - source_labels: [__param_target]
        target_label: instance
      - target_label: __address__
        replacement: 127.0.0.1:9115
```

```shell
# 监控网站状态
- job_name: web_status
    metrics_path: /probe
    params:
      module: [http_2xx]
    static_configs:
      - targets: ['http://www.baidu.com']
        labels:
          instance: user_status
          group: 'web'
    relabel_configs:
      - source_labels: [__address__]
        target_label: __param_target
      - target_label: __address__
        replacement: 127.0.0.1:9115
```

## 加入grafana

导入blackbox_exporter模板：

> 此模板为9965号模板，数据源选择Prometheus 模板下载地址 <https://grafana.com/grafana/dashboards/9965>

![image1](https://raw.githubusercontent.com/novice-gamer/picture/master/img/20210412165521.png)

![image2](https://raw.githubusercontent.com/novice-gamer/picture/master/img/20210412165903.png)

注意，此模板需要安装饼状图插件，否则会出现下面的问题：

![image3](https://raw.githubusercontent.com/novice-gamer/picture/master/img/20210412170353.png)

插件地址：<https://grafana.com/grafana/plugins/grafana-piechart-panel/>

```shell
[root@VM-0-16-centos bin]# ./grafana-cli plugins install grafana-piechart-panel
installing grafana-piechart-panel @ 1.6.1
from: https://grafana.com/api/plugins/grafana-piechart-panel/versions/1.6.1/download
into: /var/lib/grafana/plugins  # 注意：默认安装路径和安装方法不同，需要复制插件到安装目录的plugins-bundled

✔ Installed grafana-piechart-panel successfully

Restart grafana after installing plugins . <service grafana-server restart>
```

重启grafana
