---
title: 数据库的备份、还原、导入及导出
tags: 'Mongodb'
categories: 'Databse'
top_img: '/img/database.jpg'
---

[mongodb 备份、还原、导入、导出简单操作](https://segmentfault.com/a/1190000006236494)

一般来说，进行整库导出导入时使用mongodump和mongorestore，这一对组合操作的数据是BSON格式，进行大量dump和restore时效率较高

进行单个集合导出导入时使用

* [mongoexport](https://docs.mongodb.com/manual/reference/program/mongoexport)（备份)

* [mongoimport](https://docs.mongodb.com/manual/reference/program/mongoimport)（还原）

这一对组合操作的数据是JSON格式，可读性较高。

##  mongodump（备份)
说明：

> mongodump是一个用于导出二进制数据库内容的实用工具，它导出的bson文档中只会包含着集合文档等信息，不包括索引信息（索引信息会单独导出），所以还原后，索引必须重建（这个不用担心，使用mongorestore会自动重建mongodump生成的索引信息）。3.4版本中添加了对只读视图的支持。

参数：
| 命令      |全称         |    默认值        |参考释义   |
|  ----    |  -----      | -------         | -----   |
|  ----    |  --help      | -------         | 查看mongodump命令的使用帮助   |
|  ----    |  --version      | -------         | 返回mongodump的版本号   |
|  -h    |  --host <hostname><:port>      | localhost:27017         | 指定mongod要连接的主机名及端口号   |
|  ----    |  --port <port>      | 27017         | 指定MongoDB实例监听客户连接的TCP端口号   |
|  -u    |  --username      | -------         | 指定用于向使用认证的MongoDB数据库认证的用户名，与--password和
--authenticationDatabase结合使用   |
|  -p    |  --password      | -------         | 指定用于向使用认证的MongoDB数据库认证的密码。与--username和 --
authenticationDatabase选项结合使用。   |
|  -d    |  --db      | -------         | 指定要备份的数据库。如果不指定，mongodump会将此实例中的所有数据库备份。   |
|  -c    |  --collection      | -------         | 指定要备份的集合。如果不指定，则会将指定数据库或实例中的所有集合备份。|
|      |  --gzip     | -------         | 3.2版本+，压缩输出，如果mongodump指定导出到目录，则该选项会将每个文件都压缩，并添加.gz后缀；如果mongodump指定导出到文档或标准输出流，则该选项会压缩到文档或输出流中|
|  -o    |  --out (path)      | -------         | 指定导出数据的目录路径，如不指定，则mongodump默认将文件输出到dump所在的工作目录中。该选项不能和--archive一起使用   |


例子：
随便进一目录：
```bash
mongodump -d mytest -c user -o  ./
```
会在当前目录生成mytest文件夹，里面有两个文件：
* user.bson
* user.metadata,json

> 整库备份：
```bash
mongodump -d k-blog -o ./
```
在备份完成后，系统自动在当前目录下建立一个k-blog目

##  mongorestore（还原）

说明： 

> mongorestore用来导入数据到MongoDB实例中，3.0.0版本以上支持通过标准输入流来导入数据。

参数：
| 命令      |全称         |    默认值        |参考释义   |
|  ----    |  -----      | -------         | -----   |
|  -d    |  --db (database)      | -------         | 指定要还原的数据库。如果不指定，restore将会还原dump记录的所有数据库，并会覆盖现有数据库数据|
|  -c    |  --collection      | -------         | 指定要还原的集合。如果不指定，mongorestore会从文件名中读取识别集合名称（如果有扩展名则会省略扩展名）   |
|  ----    |  --drop	      | -------         | 还原集合之前会先从目标数据库中删除集合，不会删除不在备份中的集合。   |
|  ----    |  --gzip      | -------         | 3.2版本+，从压缩文件中还原  |
|  ----    |  (path)      | -------         | 要还原的数据文件路径，该参数必须是mongorestore命令的最后一个参数   |
例子：

通过user.bson文件还原mytest数据库中的user集合，并在还原之前进行删除
```bash
mongorestore --drop -d k-blog -c user ./user.bson
```
> 整库恢复：
```bash
mongorestore  -d k-blog  ./
```
## mongoexport（导出）及mongoimport（导入）

## mongoexport（导出）

参数
| 命令      |全称         |    默认值        |参考释义   |
|  ----    |  -----      | -------         | -----   |
|  ----    |  --help      | -------         | 查看mongoexport的使用帮助   |
|  ----    |  --version      | -------         | 查看mongoexport的版本号   |
|  ----    |  --db &lt;database&gt;      | -------         | 指定要在哪个数据库上运行该命令   |
|  ----    |  --collection &lt;collection&gt;      | -------         | 指定要导出的集合   |
|  ----    |  --fields &lt;field1[,field2]&gt;      | -------         | 指定导出时只导出一个或多个字段，导出多个时，需要使用逗号分隔;当字段中有空格时，需要用英文引号括起来。   |
|  ----    |  --query &lt;JSON&gt;      | -------         | 提供查询文档作为导出数据源   |
|  ----    |  --type&lt;string&gt;      | -------         | 指定要导出的文件类型，可选值：json，csv   |
|  -o    |  --out &lt;file&gt;      | -------         | 指定要导出的文件路径（含文件名），如果不指定，则会导出为标准输出（例如stdout）   |
例子：
导出mytest数据库中的user集合到user.json文件中
``` bash
mongoexport -d mytest -c user -o ./user.json
```
会在当前目录中生成json文件<div>

## mongoimport（导入）
参数：
| 命令      |全称         |    默认值        |参考释义   |
|  ----    |  -----      | -------         | -----   |
|  ----    |  --ignoreBlanks      | -------         | 忽略要导入文件中的空字段，如果不指定该参数，则默认会读取空字段并创建   |
|  ----    |  --type &lt;json csv tsv&gt;  | json，csv，tsv   | 要导入的文件类型，另外支持tsv  |
|  ----    |  --headerline      | -------         | 使用第一行作为字段名称   |
|  ----    |  --mode &lt;insert upsert merge&gt;      | insert（插入），upsert（替换数据库中的文档），merge（合并         | 指定导入过程中，如何应对数据库文档与导入文件中的文档匹配（默认会使用_id字段对比）的情况   |

例子：
从user.json文件导入到mytest数据库中的user集合，并在之前进行删除
``` bash
mongoimport --drop -d mytest -c user --file ./user.json
```