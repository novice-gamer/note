[toc]

# 1. 环境准备

```
系统版本：CentOS Linux release 7.5.1804 (Core)
软件版本：redis-5.0.0.tar.gz
安装目录：/application/redis
下载路径：/server/tools
```

# 2. 安装

1. 下载软件包

    ```shell
    wget https://download.redis.io/releases/redis-5.0.0.tar.gz
    ```

2. 解压到指定目录

    ```shell
    tar xf redis-5.0.0.tar.gz -C /application/
    
    # 修改名称
    mv redis-5.0.0 redis
    ```

3. 编译

    ```shell
    cd /application/redis
    make
    
    # make完后src目录下会出现redis-server、redis-cli等文件
    ```

4. 修改配置文件

    ```shell
    备份原始redis.conf
    cp redis.conf{,.bak}
    
    # 主要修改bind配置，可以远程访问，不只是本地访问
    ```

5. 启动

    ```shell
    src/redis-server ./redis.conf
    ```

    

