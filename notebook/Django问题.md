**问题1：**启动Django报错’str’ object has no attribute ‘decode’

![image-20210415181016811](https://raw.githubusercontent.com/novice-gamer/picture/master/img/image-20210415181016811.png)

解决方法：

进入operations.py文件，修改提示行数

```
query = query.decode(errors='replace') 修改成 query = query.encode(errors='replace')
```

注意：这是修改源码，可能会出现提示，直接确认修改。

**问题2：** mysqlclient 1.4.0 or newer is required; you have 0.9.3

![](https://raw.githubusercontent.com/novice-gamer/picture/master/img/image-20210415185640471.png)

解决方法：

在`settings.py`同文件夹下的`_init_.py`加入以下内容

```
import pymysql
pymysql.version_info = (1, 4, 13, "final", 0)
pymysql.install_as_MySQLdb()
```

