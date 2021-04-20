环境准备：

```shell
系统版本：CentOS Linux release 7.5.1804 (Core)
软件版本：apache-maven-3.8.1-bin.zip
安装目录：/application/
```

安装：

下载安装包：

```shell
wget https://mirrors.bfsu.edu.cn/apache/maven/maven-3/3.8.1/binaries/apache-maven-3.8.1-bin.zip
```

解压到安装目录：

```shell
unzip apache-maven-3.8.1-bin.zip
mv apache-maven-3.8.1 /application/maven
```

添加环境变量：

```shell
vim /etc/profile
#添加
export PATH=/application/maven/bin:$PATH
# 使之生效
source /etc/profile
```

查看版本：

```shell
[root@VM-0-16-centos bin]# mvn -v
Apache Maven 3.8.1 (05c21c65bdfed0f71a2f2ada8b84da59348c4c5d)
Maven home: /application/maven
Java version: 1.8.0_281, vendor: Oracle Corporation, runtime: /usr/local/jdk1.8.0_281/jre
Default locale: zh_CN, platform encoding: UTF-8
OS name: "linux", version: "3.10.0-1127.19.1.el7.x86_64", arch: "amd64", family: "unix"
```



