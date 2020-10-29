---
title: MySQL 版本发布历史总结
tags: 'mysql'
categories: 'Databse'
top_img: '../../img/database.jpg'
---

* 在2000 年的时候，MySQL 公布了自己的源代码，并采用GPL（GNU General Public License）许可协议，正式进入开源世界。

* 2000年4月，MySQL对旧的存储引擎进行了整理，命名为MyISAM。

* 2001年，Heikiki Tuuri向MySQL提出建议，希望能集成他们的存储引擎InnoDB，这个引擎同样支持事务处理，还支持行级锁。所以在2001年发布的3.23 版本的时候，该版本已经支持大多数的基本的SQL 操作，而且还集成了MyISAM和InnoDB 存储引擎。MySQL与InnoDB的正式结合版本是4.0。

* 2003年12月，MySQL 5.0版本发布，提供了视图、存储过程等功能

* 2008年11月，MySQL 5.1发布，它提供了分区、事件管理，以及基于行的复制和基于磁盘的NDB集群系统，同时修复了大量的Bug。

* 2010年04月22 发布MySQL 5.5, MySQLcluster 7.1，其主要新特性包括半同步的复制及对SIGNAL/RESIGNAL的异常处理功能的支持，最重要的是InnoDB存储引擎终于变为当前MySQL的默认存储引擎。

* 2015年5月29日,发布了MySQL 5.6分支的最新的开发里程碑版本MySQL 5.6.25

* 2015年10月21日，MySQL 5.7.9 正式发布
* 2018年04月19日，  MySQL 8.0.11 
## 详细介绍
### 版本3.23（2001）
一般认为这个版本的发布是Mysql真正“诞生”的时刻，其开始获得广泛使用。在这个版本，Mysql依然只是一个在平面文件（Flat File） 上实现了 SQL 查询的系统。但一个重要的改进是引入 MyISAM 代替了老旧而且有诸多限制的 ISAM 引擎。InnoDB 引擎也已经可以使用，但没有包含在默认的二进制发行版中，因为它太新了。所以如果要使用 InnoDB，必须手工编译。版本 3.23 还引入了全文索引和复制。复制是 Mysql 成为互联网应用的数据库系统的关键特性。
### 版本4.0（2003）

* 支持新的语法，比如 UNION 和多表 DELETE 语法。

* 重写了复制，在备库使用了两个现成来实现复制，避免了之前一个线程所有复制工作的模式下任务切换导致的问题。

* InnoDB 成为标准配备，包括了全部的特性：行级锁、外键等。

* 引入了查询缓存（自那以后这部门改动不大），同时还支持通过 SSL 进行连接。
### 版本4.1（2005）

* 引入了更多新的语法，比如子查询和 INSERT ON DUPLICATE KEY UPDATE。

* 开始支持 UTF-8 字符集。

* 支持新的二进制协议和 prepared 语句。
### 版本5.0（2006）

* 这个版本出现了一些“企业级”特性：视图、触发器、存储过程和存储函数。

* 老的 ISAM 引擎的代码被彻底移除，同时引入了新的 Federated 等引擎。
### 版本5.1（2008）

* 这是 Sun 收购 MySQL AB 以后发布的首个版本，研发时间长达五年。

* 引入了分区、基于行的复制，以及 plugin API（包括可插拔存储引擎的 API）。


* 移除了 BerkeyDB 引擎，这是 MySQL 最早的事务存储引擎。

* 其他如 Federated 引擎也被放弃。

* 同时 Oracle 收购的 InnoDB Oy发布了 InnoDB plugin。
### 版本5.5（2010） 
性能提升

* 默认InnoDB plugin引擎。具有提交、回滚和crash恢复功能、ACID兼容。

* 行级锁(一致性的非锁定读 MVCC)。

* 表与索引存储在表空间、表大小无限制。

* 支持dynamic(primary key缓存内存 避免主键查询引起的IO )与compressed(支持数据及索引压缩)行格式。

* InnoDB plugin文件格式Barracuda、支持表压缩、节约存储、提供内存命中率、truncate table速度更快。

* 原InnoDB只有一个UndoSegment，最多支持1023的并发；现在有128个Segments，支持128K个并发（同样，解决高并发带来的事务回滚）。

* Innodb_thread_concurrency默认为0，线程并发数无限制，可根据具体应用设置最佳值。

* Innodb_io_capacity可以动态调整刷新脏页的数量，改善大批量更新时刷新脏页跟不上导致的性能下降问题。Default：200，跟硬盘的IOPS有关。

* 充分利用CPU多核处理能力innodb_read_io_threads阈值：1-64innodb_write_io_threads 阈值：1-64根据数据库的读写比灵活设置，充分发挥多CPU、高性能存储设备的性能，不支持动态加载 。

* 自适应刷新脏页

* 热数据存活更久

* buffer pool多实例 ：innodb_buffer_pool_instances 参数增加innodb_buffer_pool实例个数，大大降低buffer pool的mutex争抢过热情况。

* Linux上实现异步IO

* 重新支持组提交
* 稳定性提升

* 支持半同步Replication。

* 增加Relay Log 自我修复功能。

* Crash recovery。

* 引入红-黑树做插入排序的中间数据结构，时间复杂度大大降低，减少恢复时间。

* Thread Pool 分组排队 限流
### 版本5.6（2012）

* 默认参数的改变

* Back_log  排队队列

* 支持全文索引

* 支持online DDL create,alter,drop

* 可以在建表时指定表空间位置
      create table external (x int unsigned not null primary key)data directory = '/volumes/external1/data';
      
* 新增参数innodb_page_size可以设置page大小

* 整合了memcached API，可以使用API来直接访问innodb表，并非SQL（减少SQL解析、查询优化代价）

* innodb只读事务，不需要设置TRX_ID字段，

* 减少内部数据结构开销，减少read view

* 仅仅非只读事务依然需要TRX_ID
* innodb改进点

* innodb表空间在线迁移(TransportableTablespaces)

* undo log可独立出系统表空间

* redo log最大可增长到512G

* innodb后台线程独立出来
* 优化器改进

* ICP
        可以在引擎层直接过滤数据，避免二次回表
        节省BP空间，提高查询性能
        
* BKA
        全称Batch Key Access：
        SQL通过辅助索引要访问表数据时候，将大量的随机访问放入缓存，交给MRR接口合并为顺序访问。
        
* MRR
        全称Multi Range Read：
        在BKA算法应用之后，通过MRR接口合并随机访问为顺序访问，再去检索表数据。
        变大量随机为顺序访问。在通过辅助索引检索大量数据时，性能提升明显
        磁头无需来回寻道，page只需读取一次，且较好利用了innodb线性预读功能（每次预读64个连续page）。
        
* 统计信息持久化，mysqld重启后不丢失

* explain语句支持insert，update，delete，replace语句，并且支持JSON格式

* 子查询优化提升。
### 版本5.7（2015年）
安全性

* 用户表 mysql.user 的 plugin字段不允许为空， 默认值是 mysql_native_password，而不是 mysql_old_password，不再支持旧密码格式；

* 增加密码过期机制，过期后需要修改密码，否则可能会被禁用，或者进入沙箱模式；

* 增加密码过期机制，过期后需要修改密码，否则可能会被禁用，或者进入沙箱模式；

* 提供了更为简单SSL安全访问配置，并且默认连接就采用SSL的加密方式。
灵活性

* MySQL数据库从5.7.8版本开始，也提供了对JSON的支持。

* 可以混合存储结构化数据和非结构化数据，同时拥有关系型数据库和非关系型数据库的优点

* 能够提供完整的事务支持

* generated column是MySQL 5.7引入的新特性，所谓generated column，就是数据库中这一列由其他列计算而得
易用性

* 在MySQL 5.7 之前，如果用户输入了错误的SQL语句，按下 ctrl+c ，虽然能够”结束”SQL语句的运行，但是，也会退出当前会话，MySQL 5.7对这一违反直觉的地方进行了改进，不再退出会话。

* MySQL 5.7可以explain一个正在运行的SQL，这对于DBA分析运行时间较长的语句将会非常有用。

* sys schema是MySQL 5.7.7中引入的一个系统库，包含了一系列视图、函数和存储过程， 该项目专注于MySQL的易用性。
例如：如何查看数据库中的冗余索引；如何获取未使用的索引；如何查看使用全表扫描的SQL语句。
可用性

* 在线设置 复制的过滤规则 不再需要重启MySQL，只需要停止SQLthread，修改完成以后，启动SQLthread。

* 在线修改buffer pool的大小。

* Online DDL MySQL 5.7支持重命名索引和修改varchar的大小，这两项操作在之前的版本中，都需要重建索引或表。

* 在线开启GTID ，在之前的版本中，由于不支持在线开启GTID，用户如果希望将低版本的数据库升级到支持GTID的数据库版本，需要先关闭数据库，再以GTID模式启动，所以导致升级起来特别麻烦。
性能

* 临时表的性能改进。

    临时表只在当前会话中可见
    临时表的生命周期是当前连接（MySQL宕机或重启，则当前连接结束）
        
* 只读事务性能改进。

    MySQL 5.7通过 避免为只读事务分配事务ID ，不为只读事务分配回滚段，减少锁竞争等多种方式，优化了只读事务的开销，提高了数据库的整体性能。

* 加速连接处理。

    在MySQL 5.7之前，变量的初始化操作（THD、VIO）都是在连接接收线程里面完成的，现在将这些工作下发给工作线程，以减少连接接收线程的工作量，提高连接的处理速度。这个优化对那些频繁建立短连接的应用，将会非常有用。

* 复制性能的改进 （支持多线程复制（Multi-Threaded Slaves, 简称MTS）

    MySQL的默认配置是库级别的并行复制，为了充分发挥MySQL 5.7的并行复制的功能，我们需要将slave-parallel-type配置成LOGICAL_CLOCK。

* 支持多源复制（Multi-source replication）
* 严格性改变

* 默认启用 STRICT_TRANS_TABLES 模式。

* 对 ONLY_FULL_GROUP_BY 模式实现了更复杂的特性支持，并且也被默认启用。

* 其他被默认启用的sql mode还有 NO_ENGINE_SUBSTITUTION。
* 默认参数的改变

* 默认binlog格式调整为ROW格式

* 默认binlog错误后的操作调整为ABORT_SERVER

    在先前的选项下（binlog_error_action=IGNORE_ERROR），如果一个错误发生，导致无法写入binlog，mysql-server会在错误日志中记录错误并强制关闭binlog功能。这会使mysql-server在不记录binlog的模式下继续运行，导致从库无法继续获取到主库的binlog。

* 默认开启mysql崩溃时的binlog安全。

* 默认调低slave_net_timeout。
* 安装不同

* mysql_install_db已经不再推荐使用了，建议改成mysqld --initialize 完成实例初始化。如果 datadir 指向的目标目录下已经有数据文件，则会有[ERROR] Aborting；

在初始化时如果加上 --initial-insecure，则会创建空密码的 root@localhost 账号，否则会创建带密码的 root@localhost 账号，密码直接写在 log-error 日志文件中；新用户登入后需要立刻修改密码，否则无法继续后续的工作。



### 版本8.0（2018
作为版本号突飞猛进的一个版本，在MySQL 8.0中新增了如下的特性：
<a href="https://learnku.com/laravel/t/10243/mysql-8-new-features-required-by-10-developers" target="_blank">10 个开发者必知的 MySQL 8.0 新功能</a>

* 用户角色
8.0中将会增强账号管理的功能，提供角色这一概念，即能组合权限，批量授权给某一用户。

* 增强的InnoDB
自增id会写入到redo log中，这一改动使得数据库重启之后的自增值能恢复到重启前的状态
增加了死锁检测开关innodb_deadlock_detect，可以在高并发系统中动态调整这一特性，提升性能

* 增强的JSON操作
增加了->>操作符，使用这一操作符等同于对JSON_EXTRACT的结果进行JSON_UNQUOTE操作，简化了SQL语句
增加了按JSON数据组织返回数据操作的两个方法：JSON_ARRAYAGG与JSON_OBJECTAGG。JSON_ARRAYAGG将某列的值按照一个JSON数据返回，而JSON_OBJECTAGG将列A作为键，列B作为值，返回一个JSON对象格式的数据