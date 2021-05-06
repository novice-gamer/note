创建一个管理员账号：

```shell
python3.7 manage.py createsuperuser 

# 输出
Username (leave blank to use 'name'): admin	# 确认用户名
Email address: amdin@163.com		# 邮箱地址
Password: 				# 密码
Password (again): 		# 重复一遍密码
This password is too short. It must contain at least 8 characters.		# 密码简单提示
This password is too common.
This password is entirely numeric.
Bypass password validation and create user anyway? [y/N]: y			# 确认使用简单密码
Superuser created successfully.
```

访问浏览器：<http://127.0.0.1:8000/admin/>

![](https://raw.githubusercontent.com/novice-gamer/picture/master/img/20210421163800.png)

进入管理页面：

![](https://raw.githubusercontent.com/novice-gamer/picture/master/img/20210421164110.png)

将会看到几种可编辑的内容：组和用户。它们是由 [`django.contrib.auth`](https://docs.djangoproject.com/zh-hans/2.2/topics/auth/#module-django.contrib.auth) 提供的，这是 Django 开发的认证框架。

向其中添加数据库：`polls/admin.py`

```shell
from django.contrib import admin
from .models import Question
# Register your models here.

admin.site.register(Question)
```

这是刷新页面可以看到数据库已经显示在管理页面上：

![](https://raw.githubusercontent.com/novice-gamer/picture/master/img/20210421164633.png)

点击Questions，查看对象的change list,

![](https://raw.githubusercontent.com/novice-gamer/picture/master/img/20210421184640.png)

点击`what's up?`进行修改数据：

![](https://raw.githubusercontent.com/novice-gamer/picture/master/img/20210421184830.png)

> 注意事项：
>
> - 这个表单是从问题 `Question` 模型中自动生成的
> - 不同的字段类型（日期时间字段 [`DateTimeField`](https://docs.djangoproject.com/zh-hans/3.2/ref/models/fields/#django.db.models.DateTimeField) 、字符字段 [`CharField`](https://docs.djangoproject.com/zh-hans/3.2/ref/models/fields/#django.db.models.CharField)）会生成对应的 HTML 输入控件。每个类型的字段都知道它们该如何在管理页面里显示自己。4
> - 每个日期时间字段 [`DateTimeField`](https://docs.djangoproject.com/zh-hans/3.2/ref/models/fields/#django.db.models.DateTimeField) 都有 JavaScript 写的快捷按钮。日期有转到今天（Today）的快捷按钮和一个弹出式日历界面。时间有设为现在（Now）的快捷按钮和一个列出常用时间的方便的弹出式列表。

页面的底部提供了几个选项：

- 保存（Save） - 保存改变，然后返回对象列表。
- 保存并继续编辑（Save and continue editing） - 保存改变，然后重新载入当前对象的修改界面。
- 保存并新增（Save and add another） - 保存改变，然后添加一个新的空对象并载入修改界面。
- 删除（Delete） - 显示一个确认删除页面。

通过点击 “今天(Today)” 和 “现在(Now)” 按钮改变 “发布日期(Date Published)”。然后点击 “保存并继续编辑(Save and add another)”按钮。然后点击右上角的 “历史(History)”按钮。你会看到一个列出了所有通过 Django 管理页面对当前对象进行的改变的页面，其中列出了时间戳和进行修改操作的用户名：

![](https://raw.githubusercontent.com/novice-gamer/picture/master/img/20210421185612.png)

