# Mysql知识总结

1.特点：性能较好、开源、可靠性好、易用，功能方面比不上其他的商用/开源数据库

2.物理文件组成：

> 错误日志：Error Log

错误日志记录了MyQL Server运行过程中所有较为严重的警告和错误信息，以及MySQL Server每次启动和关闭的详细信息。错误日志功能默认是关闭的，错误信息被输出到标准错误输出（stderr）。

> 二进制日志：Binary Log & Binary Log Index

MySQL会将所有修改数据库数据的Query以二进制形式记录到日志文件中。当然，日志中并不仅限于Query语句这么简单，还包括每一条Query所执行的时间，所消耗的资源，以及相关的事务息，所以binlog是事务安全的。

> 更新日志：update log

更新日志是MySQL在较老的版本上使用的，其功能和binlog基本类似，只不过不是以二进制格式来记录，而是以简单的文本格式记录内容。自从MySQL增加了binlog功能之后，就很少使用更新日志了。从版本5.0开始，MySQL已经不再支持更新日志。

> 查询日志：query log

查询日志记录MySQL中所有的Query，可通过“--log[=fina_name]”来打开该日志。

> 慢查询日志：slow query log

慢查询日志中记录的是执行时间较长的Query，也就是我们常说的Slow Query，通过设--log-slow-queries[=file_name]来打开该功能并设置记录位置和文件名，默认文件名为hostname-slow.log，默认目录也是数据目录。

> InnoDB的在线REDO日志：InnoDB REDO Log

InnoDB是一个事务安全的存储引擎，其事务安全性主要是通过在线REDO日志和记录在表空间的UNDO信息来保证的。REDO日志中记录了InnoDB所做的所有物理变更和事务信息，通过REDO日志和UNDO信息，InnoDB保证了在任何情况下的事务安全性。

> 数据文件

在MySQL中，每一个数据库都会在定义好（或者默认）的数据目录下存在一个以数据库名字命名的文件夹，用来存放该数据库中的各种表数据文件。不同的MySQL存储引擎有各自不同的数据文件，存放位置也有区别。多数存储引擎的数据文件都存放在和MyISAM数据文件位置相同的目录下，但是每个数据文件的扩展名却各不一样。如MyISAM用“.MYD”作为扩展名，InnoDB用“.ibd”，Archive用“.arc”，CSV用“.CSV”，等等。

“.frm”文件

与表相关的元数据（meta）信息都存放在“.frm”文件，包括表结构的定义信息等。不论是什么存储引擎，每一个表都会有一个以表名命名的“.frm”文件。所有的“.frm”文件都存放在所属数据库的文件夹下面。

“.MYD”文件

“.MYD”文件是MyISAM存储引擎专用的，存放MyISAM表的数据。每一个MyISAM表都会有一个“.MYD”文件与之对应，它同样存放于所属数据库的文件夹下，和“.frm”文件在一起。

“.MYI”文件

“.MYI”文件也是专属于MyISAM存储引擎的，主要存放MyISAM表的索引相关信息。对于MyISAM存储来说，可以被缓存（cache）的内容主要就是来源于“.MYI”文件中。每一个MyISAM表对应一个“.MYI”文件，其存放的位置和“.frm”及“.MYD”一样。

“.ibd”文件和ibdata文件

这两种文件都是存放InnoDB数据的文件，之所以用两种文件来存放InnoDB的数据（包括索引），是因为InnoDB的数据存储方式能够通过配置来决定是使用共享表空间存放存储数据，还是用独享表空间存放存储数据。独享表空间存储方式使用“.ibd”文件来存放数据，且每个表一个“.ibd”文件，文件存放在和MyISAM数据相同的位置。如果选用共享存储表空间来存放数据，则会使用ibdata文件，所有表共同使用一个（或者多个，可自行配置）ibdata文件。

> Replication(主从复制)相关文件

master.info文件

master.info文件存在于Slave端的数据目录下，里面存放了该Slave的Master端的相关信息，包括Master的主机地址、连接用户、连接密码、连接端口、当前日志位置、已经读取到的日志位置等信息。

relay log和relay log index

mysql-relay-bin.xxxxxn文件用于存放Slave端的I/O线程从Master端所读取的Binary Log
信息，然后由Slave端的SQL线程从该relay log中读取并解析相应的日志信息，转化成Master所执行的Query语句，接着在Slave端应用。mysql-relay-bin.index文件的功能类似于mysql-bin.index，同样是记录日志存放位置的绝对路径，只不过它所记录的不是Binary Log，而是Relay Log。

relay-log.info文件

类似于master.info，relay-log.info文件存放通过Slave的I/O线程写入本地的relay log相关信息，以便Slave端的SQL线程及某些管理操作随时能够获取当前复制的相关信息。

> 其他文件

system config file

MySQL的系统配置文件一般都是在“my.cnf”，Unix/Linux环境下默认存放在“etc”目录下的，Windows环境一般存放在“c: windows”目录下。“my.cnf”文件中包含多种参数选项组（group），每一种参数组都通过中括号给了固定的组名，如“[mysqld]”组中包括了mysqld服务启动时候的初始化参数，“[client]”组中包含着客户端工具程序可以读取的参数，此外还有其他针对各个客户端软件的特定参数组，如mysql程序使用的“[mysql]”，mysqlchk使用的“[mysqlchk]”，等等。如果读者编写了某个客户端程序，也可以自己设定一个参数组名，将相关参数配置在里面，然后调用mysql客户端api程序中的参数读取api相关参数。

pid文件

pid文件是mysqld应用程序在Unix/Linux环境下的一个进程文件，和许多其他Unix/Linux服务端程序一样，它存放着自己的进程id。

Socket文件

Socket文件也是在Unix/Linux环境下才有的，用户在Unix/Linux环境下客户端连接可以不通过TCP/IP网络而直接使用Unix Socket来连接MySQL。

3.系统结构

![](https://i.imgur.com/2By6xrO.png)

MySQL可以看成是两层架构，第一层我们通常叫做SQL Layer，在MySQL数据库系统处理底层数据之前的所有工作都是在这一层完成的，包括权限判断、Query解析、执行计划优化、query cache的处理等；第二层就是存储引擎层，通常叫做Storage Engine Layer，也就是底层数据存取操作实现部分，由多种存储引擎共同组成。

SQL Layer子模块

> 初始化模块

初始化模块就是在MySQL Server启动的时候，对整个系统做各种各样的初始化操作，比如各种buffer、cache结构的初始化和内存空间的申请，各种系统变量的初始化设定，各种存储引擎的初始化设置，等等。

> 核心API

主要是提供一些需要非常高效的底层操作功能的优化实现，包括各种底层数据结构的实现，特殊算法的实现，字符串处理，数字处理，小文件I/O，格式化输出，以及最重要的内存管理部分。核心API模块的所有源代码都集中在mysys和strings文件夹下面

> 网络交互模块

底层网络交互模块抽象出底层网络交互所使用的接口api，实现底层网络数据的接收与发送，以方便其他各个模块调用，以及对这一部分的维护。所有源码都在vio文件夹下面。

> Client & Server交互协议模块

> 用户模块

主要包括用户的登录连接权限控制和用户的授权管理

> 访问控制模块

根据用户模块中各用户的授权信息，以及数据库自身特有的各种约束，来控制用户对数据的访问。用户模块和访问控制模块两者结合起来，组成了MySQL整个数据库系统的权限安全管理功能。

> 连接管理、连接线程和线程管理

连接管理模块负责监听对MySQL Server的各种请求，接收连接请求，转发所有连接请求到线程管理模块。每一个连接上MySQL Server的客户端请求都会被分配（或创建）一个连接线程为其单独服务。而连接线程的主要工作就是负责MySQL Server与客户端的通信，接受客户端的命令请求，传递Server端的结果信息等。线程管理模块则负责管理维护这些连接线程，包括线程的创建，线程的缓存（cache）等。

> Query解析和转发模块

**在MySQL中我们习惯将所有Client端发送给Server端的命令都称为Query**

主要工作就是将Query语句进行语义和语法的分析，然后按照不同的操作类型进行分类，接着做出针对性的转发

> Query Cache模块

主要功能是将客户端提交给MySQL的Select类Query请求的返回结果集缓存（cache）到内存中，与该Query的一个hash值做一个对应。在该Query所取数据的基表发生任何数据的变化之后，MySQL会自动
使该Query的缓存失效。在读写比例非常高的应用系统中，Query Cache对性能的提高是非常显著的。当然它对内存的消耗也是非常大的。

> Query优化器模块

优化客户端请求的Query，根据客户端请求的Query语句和数据库中的一些统计信息，在一系列算法的基础上进行分析，得出一个最优的策略

> 表变更管理模块

要是负责完成一些DML和DDL的Query，如：update、delte、insert、create table、alter table等语句的处理。

> 表维护模块

表的状态检查，错误修复，以及优化和分析等工作都是表维护模块须要做的事情

> 系统状态管理模块

负责在客户端请求系统状态的时候，将各种状态数据返回给用户，像DBA常用的各种show status命令、show variables命令等，其所得到的结果都是由这个模块返回的

> 表管理器

表管理器的工作主要就是维护表定义文件（*.frm文件），以及一个cache，该cache中的主要内容是各个表的结构信息。此外它还维护table级别的锁管理。

> 日志记录模块

日志记录模块主要负责整个系统级别的逻辑层的日志的记录，包括error log、binarylog、slow query log等。

> 复制模块

复制模块又可分为Master模块和Slave模块两部分，Master模块主要负责在Replication环境中读取Master端的binary日志，以及与Slave端的I/O线程交互等工作。Slave模块比Master模块所要做的事情稍多一些，在系统中主要体现在两个线程上面。一个是负责从Master请求和接受binary日志，并写入本地relay log中的I/O线程。另外一个是负责从relaylog中读取相关日志事件，然后将其解析成可以在Slave端正确执行并得到和Master端完全相同结果的命令，再交给Slave执行的SQL线程。

> 存储引擎接口模块

实现了MySql其底层数据存储引擎的插件式管理，它的本质上是一个抽象类，而正是因此它成功地将各种数据处理高度抽象化。

4.模块间的合作

![](https://i.imgur.com/1GJeCWS.png)

5.以下几类数据不适合存放在数据库中

二进制多媒体数据

将二进制多媒体数据存放在数据库中，一个问题是数据库空间资源耗用非常严重，另一个问题是这些数据的存储很消耗数据库主机的CPU资源。这种数据主要包括图片、音频、视频和其他一些相关的二进制文件。处理这些数据本不是数据库的优势，如果硬要将它们塞入数据库，肯定会造成数据库的处理资源消耗严重

流水队列数据

数据库为了保证事务的安全性（支持事务的存储引擎）及可恢复性，是须要记录所有变更的日志信息的。而流水队列数据的用途就决定了存放这种数据的表中的数据会不断地被INSERT、UPDATE和DELETE，而每一个操作都会生成与之对应的日志信息。在MySQL中，如果是支持事务的存储引擎，这个日志的产生量更是要翻倍。如果通过一些成熟的第三方队列软件来实现这个队列数据的处理功能，性能将会成倍的提升。

超大文本数据

对于5.0.3之前的MySQL版本，VARCHAR类型的数据最长只能存放255个字节，如果须要存储更长的文本数据到一个字段，就必须使用TEXT类型（最大可存放64KB）的字段，甚至是更大的LONGTEXT类型（最大4GB）。而TEXT类型数据的处理性能要远比VARCHAR类型数据的处理性能低下。从5.0.3版本开始，VARCHAR类型的最大长度被调整到64KB了，但使用的时候请注意字符集的差异造成的实际存储长度。

6.概念

DDL（data definition languages）数据定义语句，create、drop、alter

DML（data manipulation languages）数据操纵语句，insert、delete、update

DCL（data control languages）数据控制语句，grant、revoke

系统数据库：

- information_schema  

INFORMATION_SCHEMA provides access to database metadata, information about the MySQL server such as the name of a database or table, the data type of a column, or access privileges. Other terms that are sometimes used for this information are data dictionary and system catalog.
          
- mysql     

mysql存储了系统的用户权限信息
         
- performance_schema 

The MySQL Performance Schema is a feature for monitoring MySQL Server execution at a low level. 
     
- sys       

MySQL 8.0 includes the sys schema, a set of objects that helps DBAs and developers interpret data collected by the Performance Schema.


表连接

表连接分为内连接、外连接，内连接仅从两张表中选出互相匹配的记录，而外连接会选出其他不匹配的记录。

外连接又分为左连接和右连接

- 左连接：包含所有的左边表中的记录甚至是右边表中没有和它匹配的记录
- 右连接：包含所有的右边表中的记录甚至是左边表中没有和它匹配的记录

**注意：左连接与右连接出现差别一般是数据表出现空缺值的时候**

子查询

当查询的条件是另外一个查询的结果的时候，就需要用到子查询，关键字包含 in, not in, =, !=, exists, not exists

---

7.语法

	+-----+
	| CMD |
	+-----+

	show variables like '%char%';//查看系统的字符集设置情况
	
	set character_set_* = utf8;//设置指定的字符集（只对当前连接起作用）
	
	show table status from [database_name];//查询数据库中的表信息
	
	show full columns from [tabale_name];//查询表中字段信息
	
	//e.g.
	mysql> show full columns from student;
	+-------+-------------+--------------------+------+-----+---------+----------------+---------------------------------+---------+
	| Field | Type        | Collation          | Null | Key | Default | Extra          | Privileges                      | Comment |
	+-------+-------------+--------------------+------+-----+---------+----------------+---------------------------------+---------+
	| id    | int(11)     | NULL               | NO   | PRI | NULL    | auto_increment | select,insert,update,references |         |
	| name  | varchar(20) | utf8mb4_0900_ai_ci | NO   |     | NULL    |                | select,insert,update,references |         |
	| age   | int(11)     | NULL               | NO   |     | NULL    |                | select,insert,update,references |         |
	+-------+-------------+--------------------+------+-----+---------+----------------+---------------------------------+---------+
#
	+-----+
	| DDL |
	+-----+

	create database [database_name];
	
	drop database [database_name];
	
	use [database_name];
	
	desc [table_name];
	
	create table [table_name](
		[column_name_1] [column_type] [constraints],
		...
		primary key(column_name)
		foreight key [column_name] refrences [table_name]([column_name])
	)ENGINE=[engine_name(InnoDB[default])] AUTO_INCREMENT=[number] DEFAULT CHARSET=[charset] COLLATE=[collate_rule];

	drop table [table_name];

	alter table [table_name] modify (column) [column_name] [column_definition] (first|after [column_name]);//重新定义表字段

	alter table [table_name] add (column) [column_name] [column_definition] (first|after [column_name]);//增加表字段

	alter table [table_name] drop (column) [column_name];//丢弃表字段

	alter table [table_name] change (column) [old_column_name] [new_column_definition] (first|after [column_name]);//表字段改名，此时需要重新定义表字段

	//可知，modify和change均可以重新修改表定义，不同的是change可以修改字段名

	alter table [old_table_name] rename [to] [new_table_name];
#

	+-----+
	| DML |
	+-----+
	
	insert into [table_name]([cloumn_name1] ...) values([record1_column_value1] ...),([record2_column_value1] ...) ...;//可以一次性插入多条数据

	update [table_name] set [column_name1] = [column_value1], ... (where condition);//更新数据，注意使用where子句，否则会更新所有的行
	
	delete form [table_name] (where condition);//删除行，注意使用where子句，否则会删除所有记录

	delete [table_name1] ... from [table_name1] ... (where condition);//删除多个表的行，注意使用where子句，否则会删除所有记录

	select * from [table_name] (where condition);

	//查询不重复的记录，使用disdinct
	disdinct
	
	//查询排序（默认升序）
	order by （desc|asc）

	//限制查询的记录数目
	limit [number]
	
	//分类聚合
	group by
	
	//对分类聚合后的结果进行再汇总
	with rollup

	//对分类聚合后的结果进行条件的过滤
	having
		
	select [field1], [field2] ... from [table_name] [where condition] [group by ...][with rollup][having ...]
	
	//聚合操作
	sum,count,max,min

	//子查询
	in, not in, =, !=, exists, not exists

	//记录联合,将多次查询的结果集合并在一起，union会把重复的记录剔除，union all会保留所有的重复记录
	union、union all
	
#	
	+-----+
	| DCL |
	+-----+
	
	//e.g.
	grant select, insert on [database_name].* to [username]@[url(localhost)] identified by [password];

8.查看帮助

	//显示所有可供查询的分类
	? contents
	
	mysql> ? contents;
	You asked for help about help category: "Contents"
	For more information, type 'help <item>', where <item> is one of the following
	categories:
	   Account Management
	   Administration
	   Components
	   Compound Statements
	   Data Definition
	   Data Manipulation
	   Data Types
	   Functions
	   Functions and Modifiers for Use with GROUP BY
	   Geographic Features
	   Help Metadata
	   Language Structure
	   Plugins
	   Storage Engines
	   Table Maintenance
	   Transactions
	   User-Defined Functions
	   Utility

	//通过关键字查询，比如查询show命令的使用
	？ show
	
	mysql> ? show
	Name: 'SHOW'
	Description:
	SHOW has many forms that provide information about databases, tables,
	columns, or status information about the server. This section describes
	those following:
	
	SHOW {BINARY | MASTER} LOGS
	SHOW BINLOG EVENTS [IN 'log_name'] [FROM pos] [LIMIT [offset,] row_count]
	SHOW CHARACTER SET [like_or_where]
	SHOW COLLATION [like_or_where]
	SHOW [FULL] COLUMNS FROM tbl_name [FROM db_name] [like_or_where]
	SHOW CREATE DATABASE db_name
	SHOW CREATE EVENT event_name
	SHOW CREATE FUNCTION func_name
	SHOW CREATE PROCEDURE proc_name
	SHOW CREATE TABLE tbl_name
	SHOW CREATE TRIGGER trigger_name
	SHOW CREATE VIEW view_name
	SHOW DATABASES [like_or_where]
	......
	......

9.data types

[可选的数据类型](https://dev.mysql.com/doc/refman/8.0/en/data-types.html)
