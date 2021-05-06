[toc]

# 1. 安装准备

[*官网地址*](https://nodejs.org/en/)

```
软件版本：node-v14.16.1-linux-x64.tar.xz
系统版本：CentOS Linux release 7.5.1804 (Core)
安装目录：/application/node/
下载目录：/server/tools/
```

下载安装包：

```shell
wget https://nodejs.org/dist/v14.16.1/node-v14.16.1-linux-x64.tar.xz
```

# 2. 安装

解压安装包：

```shell
tar xf node-v14.16.1-linux-x64.tar.xz -C /application/
```

更改名称：

```shell
mv node-v14.16.1-linux-x64 node
```

配置全局变量：

```shell
vim /etc/profile
PATH=/application/mysql/bin/:$PATH:/application/node/bin/
# 在最后加上node的路径，如果没有PATH这行就自行创建“PATH=/application/node/bin/:$PATH”
```

刷新配置文件：

```shell
source /etc/profile
```

验证：

```shell
[root@VM-0-16-centos bin]# node -v
v14.16.1
[root@VM-0-16-centos bin]# npm -v
6.14.12
```

# 3. 更新

npm更新：

```shell
npm install -g npm-check
```

node更新：

```shell
# 安装n模块
npm install -g n
# 升级
sudo n stable
```



