# **谈谈MySQL的优化**

 一、SQL语句优化

​     （1）使用limit对查询结果的记录进行限定

​     （2）避免select *，将需要查找的字段列出来

​     （3）使用连接（join）来代替子查询

​     （4）拆分大的delete或insert语句

​     二、选择合适的数据类型

​     （1）使用可存下数据的最小的数据类型，整型 < date,time < char,varchar < blob

​     （2）使用简单的数据类型，整型比字符处理开销更小，因为字符串的比较更复杂。例如int类型存储时间类型，bigint类型转ip函数

​     （3）使用合理的字段属性长度，固定长度的表会更快。使用enum、char而不是varchar

​     （4）尽可能使用not null定义字段

​     （5）尽量少用text，非用不可最好分表

​     三、选择合适的索引列

​     （1）查询频繁的列，在where，group by，order by，on从句中出现的列

​     （2）where条件中<，<=，=，>，>=，between，in，以及like 字符串+通配符（%）出现的列

​     （3）长度小的列，索引字段越小越好，因为数据库的存储单位是页，一页中能存下的数据越多越好

​     （4）离散度大（不同的值多）的列，放在联合索引前面。查看离散度，通过统计不同的列值来实现，count越大，离散程度越高：

​     mysql> SELECT COUNT(DISTINCT column_name) FROM table_name;

​     四、使用命令分析

​     （1）SHOW查看状态

​     1.显示状态信息

​     mysql> SHOW [SESSION|GLOBAL] STATUS LIKE '%Status_name%';

​     session（默认）：取出当前窗口的执行

​     global：从mysql启动到现在

​     （a）查看查询次数（插入次数com_insert、修改次数com_insert、删除次数com_delete）

​     mysql> SHOW STATUS LIKE 'com_select';

​     （b）查看连接数(登录次数)

​     mysql> SHOW STATUS LIKE 'connections';

​     （c）数据库运行时间

​     mysql> SHOW STATUS LIKE 'uptime';

​     （d）查看慢查询次数

​     mysql> SHOW STATUS LIKE 'slow_queries';

​     （e）查看索引使用的情况：

​     mysql> SHOW STATUS LIKE 'handler_read%';

​     handler_read_key：这个值越高越好，越高表示使用索引查询到的次数。

​     handler_read_rnd_next：这个值越高，说明查询低效。

​     2.显示系统变量

​     mysql> SHOW VARIABLES LIKE '%Variables_name%';

​     3.显示InnoDB存储引擎的状态

​     mysql> SHOW ENGINE INNODB STATUS;

​     （2）EXPLAIN分析查询

​     mysql> EXPLAIN SELECT column_name FROM table_name;

​     explain查询sql执行计划，各列含义：

​     table：表名；

​     type：连接的类型

​    -const：主键、索引；

​    -eq_reg：主键、索引的范围查找；

​    -ref：连接的查找（join）

​    -range：索引的范围查找；

​    -index：索引的扫描；

​    -all：全表扫描；

​     possible_keys：可能用到的索引；

​     key：实际使用的索引；

​     key_len：索引的长度，越短越好；

​     ref：索引的哪一列被使用了，常数较好；

​          rows：mysql认为必须检查的用来返回请求数据的行数；

​     extra：using filesort、using temporary（常出现在使用order by时）时需要优化。

​    -Using filesort  额外排序。看到这个的时候，查询就需要优化了

​    -Using temporary 使用了临时表。看到这个的时候，也需要优化

​     （3）PROFILING分析SQL语句

​     1.开启profile。查看当前SQL执行时间

​     mysql> SET PROFILING=ON; 

​     mysql> SHOW profiles;

​     2.查看所有用户的当前连接。包括执行状态、是否锁表等

​     mysql> SHOW processlist;

​     （4）PROCEDURE ANALYSE()取得建议

​     通过分析select查询结果对现有的表的每一列给出优化的建议

​     mysql> SELECT column_name FROM table_name PROCEDURE ANALYSE();

​     （5）OPTIMIZE TABLE回收闲置的数据库空间

​     mysql> OPTIMIZE TABLE table_name;

​     对于MyISAM表，当表上的数据行被删除时，所占据的磁盘空间并没有立即被回收，使用命令后这些空间将被回收，并且对磁盘上的数据行进行重排（注意：是磁盘上，而非数据库）。

​     对于InnoDB表，OPTIMIZE TABLE被映射到ALTER TABLE上，这会重建表。重建操作能更新索引统计数据并释放成簇索引中的未使用的空间。

​     只需在批量删除数据行之后，或定期（每周一次或每月一次）进行一次数据表优化操作即可，只对那些特定的表运行。

​     （6）REPAIR TABLE修复被破坏的表

​     mysql> REPAIR TABLE table_name;

​     （7）CHECK TABLE检查表是否有错误

​     mysql> CHECK TABLE table_name;

​     五、定位慢查询概述

​     开启慢查询日志，可以让MySQL记录下查询超过指定时间的语句，通过定位分析性能的瓶颈，才能更好的优化数据库系统的性能。以下是参数说明：

​     slow_query_log 慢查询开启状态

​     slow_query_log_file 慢查询日志存放的位置（这个目录需要MySQL的运行帐号的可写权限，一般设置为MySQL的数据存放目录）

​     long_query_time 查询超过多少秒才记录

​     pt-query-digest是用于分析mysql慢查询的一个工具，它可以分析binlog、General log、slowlog，也可以通过SHOWPROCESSLIST或者通过tcpdump抓取的MySQL协议数据来进行分析。可以把分析结果输出到文件中，分析过程是先对查询语句的条件进行参数化，然后对参数化以后的查询进行分组统计，统计出各查询的执行时间、次数、占比等，可以借助分析结果找出问题进行优化。

​     六、分区和分表概述

​     1.为什么要分区和分表？

​     日常开发中我们经常会遇到大表的情况，所谓的大表是指存储了百万级乃至千万级条记录的表。这样的表过于庞大，导致数据库在查询和插入的时候耗时太长，性能低下，如果涉及联合查询的情况，性能会更加糟糕。分表和表分区的目的就是减少数据库的负担，提高数据库的效率，通常点来讲就是提高表的增删改查效率。

​     2.什么是分区？

​     分区和分表相似，都是按照规则分解表。不同在于分表将大表分解为若干个独立的实体表，而分区是将数据分段划分在多个位置存放，可以是同一块磁盘也可以在不同的机器。分区后，表面上还是一张表，但数据散列到多个位置了。app读写的时候操作的还是大表名字，db自动去组织分区的数据。

​     3.什么是分表？

​     分表是将一个大表按照一定的规则分解成多张具有独立存储空间的实体表，我们可以称为子表，每个表都对应三个文件，MYD数据文件，.MYI索引文件，.frm表结构文件。这些子表可以分布在同一块磁盘上，也可以在不同的机器上。app读写的时候根据事先定义好的规则得到对应的子表名，然后去操作它。

​     4. 分区和分表有什么联系呢？

​     （1）都能提高mysql的性高，在高并发状态下都有一个良好的表现。

​     （2）分表和分区不矛盾，可以相互配合的，对于那些大访问量，并且表数据比较多的表，我们可以采取分表和分区结合的方式（如果merge这种分表方式，不能和分区配合的话，可以用其他的分表试），访问量不大，但是表数据很多的表，我们可以采取分区的方式等。

​     （3）分表技术是比较麻烦的，需要手动去创建子表，app服务端读写时候需要计算子表名。采用merge好一些，但也要创建子表和配置子表间的union关系。

​     （4）表分区相对于分表，操作方便，不需要创建子表。

​     七、配置优化概述

​     1、全局配置

（1）max_connections

（2）back_log

（3）key_buffer_size

（4）query_cache_size

（5）read_buffer_size

（6）read_rnd_buffer_size

（7）sort_buffer_size

（8）join_buffer_size

（9）table_open_cache

（10）max_heap_table_size

（11）tmp_table_size

（12）thread_cache_size

（13）interactive_timeout

（14）wait_timeout

​     2、InnoDB配置

（1）innodb_buffer_pool_size

（2）innodb_log_buffer_size  

（3）innodb_flush_log_at_trx_commit

（4）innodb_log_file_size

（5）innodb_additional_mem_pool_size

（6）innodb_buffer_pool_instances