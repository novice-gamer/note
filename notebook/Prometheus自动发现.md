Prometheus配置自动发现后以后更改监控项不必在重启服务。

在prometheus的配置文件的scrape_configs模块下添加一下内容：

```yaml
  - job_name: 'other_server'	#名称根据具体情况更改
    file_sd_configs:
      - files:
        - /application/prometheus/other_server.yml	# 可以写成*.yml，支持yml、json、yaml
        - /test/*.json
        refresh_interval: 10s		# 扫描文件时间间隔
```

创建文件

内容示例：

```json
# json
[
  {
    # 配置抓取目标
    "targets": [
      "http://s1.soulchild.cn",
      "http://s2.soulchild.cn"
    ],
    # 添加标签
    "labels": {
      "env": "test",
      "service": "app"
    }
  }
]
```

```yaml
# yaml
- targets:
  - http://s1.soulchild.cn
  - http://s2.soulchild.cn
  labels:
    env: test
    service: app
```

