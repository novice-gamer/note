+ Django版本2.2   3.2
+ Python版本3.7   3.8

第一步：在settings文件中配置多数据库

```shell
DATABASES = {
    # 'default': {
    #     'ENGINE': 'django.db.backends.sqlite3',
    #     'NAME': os.path.join(BASE_DIR, 'db.sqlite3'),
    # }
    'default': {
        'ENGINE': 'django.db.backends.mysql',
        'NAME': 'message',
        'USER': 'root',
        'PASSWORD': '123456',
        'HOST': '127.0.0.1',
        'POST': 3306,
    },
    'polls': {
        'ENGINE': 'django.db.backends.mysql',
        'NAME': 'poll',
        'USER': 'root',
        'PASSWORD': '123456',
        'HOST': '127.0.0.1',
        'POST': 3306,
    }
    
}
DATABASE_ROUTERS = ['ProjectName.db_router.DatabaseAppsRouter']	# ProjectName更换项目名称
DATABASE_APPS_MAPPING = {
    # example:
    # 'app_name':'DATABASES.key',
    'polls': 'polls',
    'service_monitor':  'default',
    # 下面4个默认即可
    'admin': 'default',
    'auth': 'default',
    'contenttypes': 'default',
    'sessions': 'default',

}
```

第二步：在settings文件的同级目录下创建数据库路由文件。

注意：文件名和settings文件中配置的一致（db_router）

```shell
# coding: utf-8
from django.conf import settings

DATABASE_MAPPING = settings.DATABASE_APPS_MAPPING

class DatabaseAppsRouter():
    """
       A router to control all database operations on models for different
       databases.

       In case an app is not set in settings.DATABASE_APPS_MAPPING, the router
       will fallback to the `default` database.

       Settings example:

       DATABASE_APPS_MAPPING = {'app1': 'db1', 'app2': 'db2'}
       """

    def db_for_read(self, model, **hints):
        """"Point all read operations to the specific database."""
        """将所有读操作指向特定的数据库。"""
        if model._meta.app_label in DATABASE_MAPPING:
            return DATABASE_MAPPING[model._meta.app_label]
        return None

    def db_for_write(self, model, **hints):
        """Point all write operations to the specific database."""
        """将所有写操作指向特定的数据库。"""
        if model._meta.app_label in DATABASE_MAPPING:
            return DATABASE_MAPPING[model._meta.app_label]
        return None

    def allow_relation(self, obj1, obj2, **hints):
        """Allow any relation between apps that use the same database."""
        """允许使用相同数据库的应用程序之间的任何关系"""
        db_obj1 = DATABASE_MAPPING.get(obj1._meta.app_label)
        db_obj2 = DATABASE_MAPPING.get(obj2._meta.app_label)
        if db_obj1 and db_obj2:
            if db_obj1 == db_obj2:
                return True
            else:
                return False
        else:
            return None

    def allow_syncdb(self, db, model):
        """Make sure that apps only appear in the related database."""
        """确保这些应用程序只出现在相关的数据库中。"""
        if db in DATABASE_MAPPING.values():
            return DATABASE_MAPPING.get(model._meta.app_label) == db
        elif model._meta.app_label in DATABASE_MAPPING:
            return False
        return None

    def allow_migrate(self, db, app_label, model=None, **hints):
        """Make sure the auth app only appears in the 'auth_db' database."""
        """确保身份验证应用程序只出现在“authdb”数据库中。"""
        if db in DATABASE_MAPPING.values():
            return DATABASE_MAPPING.get(app_label) == db
        elif app_label in DATABASE_MAPPING:
            return False
        return None

```

第三步：在models文件中指定app_name

```shell
from django.db import models
# Create your models here.
class Question(models.Model):
    question_text = models.CharField(max_length=200)
    pub_date = models.DateTimeField('date published')

    # 指定app名称
    class Meta:
        app_label = 'app_name'
```

第四步：执行`python3.7 manage.py makemigrations`

第五步：先执行除default之外的database.

```shell
python3.7 manage.py migrate --database='polls'
```

最后执行default库：

```shell
python3.7 manage.py migrate
```

