---
title: django2.2版本中pymysql报错
tags: 'django'
categories: 'python'
top_img: '../../img/frame.jpg'
---

ImproperlyConfigured: mysqlclient 1.3.13 or newer is required
## 1、问题背景：已经安装好了 pymysql，但是在命令行执行 python3 manage.py migrate 进行数据迁移时报如下错误：

（当然，你也可能是在执行其它操作时遇到类似错误）
```
......

  File "/Library/Frameworks/Python.framework/Versions/3.7/lib/python3.7/importlib/__init__.py", line 127, in import_module
    return _bootstrap._gcd_import(name[level:], package, level)
  File "/Library/Frameworks/Python.framework/Versions/3.7/lib/python3.7/site-packages/django/db/backends/mysql/base.py", line 36, in >module&gt;
    raise ImproperlyConfigured('mysqlclient 1.3.13 or newer is required; you have %s.' % Database.__version__)
django.core.exceptions.ImproperlyConfigured: mysqlclient 1.3.13 or newer is required; you have 0.9.3.
```
根据最后两行的提示，mysqlclient 版本不够新，但是我已经都安装的最新版本了呀。

## 2、解决方案一：
安装mysqlclient

mysqlclient==1.3.14

### pip 安装 mysqlclient 报错

https://pypi.org/project/mysqlclient/

```
yum install mysql-devel -y

pip install mysqlclient
```

## 3、解决方案二：

虽然本地已安装了 PyMySQL 驱动，但 Django 连接 MySQL 时仍默认使用 MySQLdb 驱动，但 MySQLdb 并不支持 Python3，所以需要手动在项目中进行配置。

在项目根目录下的 `__init__.py` 文件中添加如下代码即可：

import pymysql
pymysql.install_as_MySQLdb()

再次执行命令时，还是会报错，没关系，仔细看下报错的倒数第三行，已经告诉你是在 base.py 第 36 行报的错，根据你的提示路径打开 base.py，把 35、36 行前面加 # 注释掉就好了，就像下面这样：
```
 34 version = Database.version_info
 35 #if version > (1, 3, 13):
 36 #    raise ImproperlyConfigured('mysqlclient 1.3.13 or newer is required; you     have %s.' % Database.__version__)
```
现在再次执行命令，上面的报错已经没有了，但是又有了新的错误，看下面怎么解决吧。

AttributeError: 'str' object has no attribute 'decode'
解决了上面的问题后，又遇到下面这个错误：
```
   File "/Library/Frameworks/Python.framework/Versions/3.7/lib/python3.7/site-packages/django/db/backends/mysql/operations.py", line 146, in last_executed_query
    query = query.decode(errors='replace')
AttributeError: 'str' object has no attribute 'decode'
```
提示属性错误:“str”对象没有属性“decode”。

问题的原因是，在 Python3 里：

str 通过 encode() 转换成 bytes
bytes 通过 decode() 转换成 str
也就是说：str 只有 encode() 方法，bytes 只有 decode() 方法！
这个估计是 django 的 bug 了。

## 解决方法：
根据提示打开报错的文件 operations.py
找到 146 行，把 decode 改成 encode 即可，类似下面这样：
```
140     def last_executed_query(self, cursor, sql, params):
141         # With MySQLdb, cursor objects have an (undocumented) "_executed"
142         # attribute where the exact query sent to the database is saved.
143         # See MySQLdb/cursors.py in the source distribution.
144         query = getattr(cursor, '_executed', None)
145         if query is not None:
146             query = query.encode(errors='replace')\t# 这里把 decode 改为 encode
147         return query
```
此时，再次执行命令就不报错了，大功告成！

tips：

* str通过encode()方法可以编码为指定的bytes。
* 反过来，当从网络或磁盘上读取了字节流，那么读到的数据就是bytes。
* 要把bytes变为str，就需要用decode()方法。
* 反之，则使用encode()方法即可！




