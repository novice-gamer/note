[toc]

# 环境准备

```
系统版本：CentOS Linux release 7.5.1804 (Core)
软件版本：apache-tomcat-9.0.45.tar.gz
安装目录：/application/
```

# 安装

下载安装包：

```shell
wget https://mirrors.bfsu.edu.cn/apache/tomcat/tomcat-9/v9.0.45/bin/apache-tomcat-9.0.45.tar.gz
```

解压到安装目录并更改名称：

```shell
tar xf apache-tomcat-9.0.45.tar.gz -C /application/
mv apache-tomcat-9.0.45 tomcat

# 注意多个Tomcat根据端口号进行分别命名
```

启动

```shell
进入bin目录
./startup.sh
```

