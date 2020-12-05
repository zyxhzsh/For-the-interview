[mysql概述](#mysql概述)

[DQL语言](DQL语言.md)

[DML语言](DML语言.md)

[DDL语言](DDL语言.md)

[存储引擎](#存储引擎)

[事务](事务.md)

[索引](索引.md)

### mysql概述

1.常用命令

```
（1）mysql服务的启动和关闭

启动：sudo mysql.server start

查看状态：sudo mysql.server status

停止：sudo mysql.server stop

登录mysql：

sudo mysql -uusername -p（隐藏密码）

sudo mysql -uusername -ppassword（显示密码）

查看mysql服务的端口号：mysql>  show global variables like 'port';(mysql命令)

退出mysql：\q、QUIT或EXIT

（2）查看mysql版本

(base) orange$mysql --version

(base) orange$mysql -V

MySQL程序选项具有以下两种通用形式：
长选项，由单词之前加两个减号组成。如--version
短选项，由单个字母之前加一个减号组成。如-V

或者进入mysql后：mysql> select version();

（3）数据库

查看有哪些数据库：mysql> show databases;(mysql命令)

创建数据库：create database database_name;

删除数据库：drop database database_name;

使用数据库：use database_name;（在数据库中建立表，因此创建表的时候必须要先use数据库。）

查询当前使用的数据库：select database();

初始化数据：use使用数据库，然后source命令执行sql脚本：source sql_file_path;

（4）表

查看当前数据库中有哪些表：show tables;

查看其它数据库中的表：show tables from database_name;

查看表的创建语句：show create table <table name>;

查看表的结构：desc table_name;

查看表格中的数据：select * from table_name;

（5）终止一条正在编写的语句：\c

（6）PID file的错误解决方法：

此时需要停止所有mysql进程。

（1）先查看有哪些关于mysql的进程：ps -ef | grep mysql

（2）停止相关进程：sudo kill “进程名”
```

2.DB_DBMS和SQL的关系：程序员写SQL语句，DBMS执行SQL语句来操作数据库中的数据。

3.对表的理解。

表是一种结构化的文件。

table是数据库的基本组成单元，所有的数据都以表格的形式组织，目的是可读性强。

一个表包括行和列：

    行：被称为数据/记录(data)
    
    列：被称为字段(column)

字符串：varchar

每一个字段应该包括哪些属性？

字段名、数据类型、相关的约束。

4、SQL语句分类。

    DQL（数据查询语言）：查询语句，凡是select语句都是DQL。
    DML（数据操作语言）：insert delete update，对表当中的数据进行增删改。
    DDL（数据定义语言）：create drop alter，对表结构的增删改。
    TCL（事务控制语言）：commit提交事务，rollback回滚事务。(TCL中的T是Transaction)
    DCL（数据控制语言）：grant授权、revoke撤销权限等。

5、导入数据的例子

    第一步：登录mysql数据库管理系统
        dos命令窗口：
            mysql -uroot -p
    第二步：查看有哪些数据库
        show databases; (这个不是SQL语句，属于MySQL的命令。)
        +--------------------+
        | Database           |
        +--------------------+
        | information_schema |
        | mysql              |
        | performance_schema |
        | test               |
        +--------------------+
    第三步：创建自己的数据库
        create database bjpowernode; (这个不是SQL语句，属于MySQL的命令。)
    第四步：使用bjpowernode数据
        use bjpowernode; (这个不是SQL语句，属于MySQL的命令。)
    第五步：查看当前数据库中有哪些表？
        show tables; (这个不是SQL语句，属于MySQL的命令。)
    第六步：初始化数据
        mysql> source /Users/orange/Downloads/resources/bjpowernode.sql
        
    注意：数据初始化完成之后，有三张表：
    +-----------------------+
    | Tables_in_bjpowernode |
    +-----------------------+
    | dept                  |
    | emp                   |
    | salgrade              |
    +-----------------------+
    第七步：查看表dept的表结构
    		desc emp;
    第八步：查看表dept中的数据
    		select * from emp;

5、bjpowernode.sql，这个文件以sql结尾，这样的文件被称为“sql脚本”。什么是sql脚本呢？

    当一个文件的扩展名是.sql，并且该文件中编写了大量的sql语句，我们称这样的文件为sql脚本。
    注意：直接使用source命令可以执行sql脚本。
    sql脚本中的数据量太大的时候，无法打开，请使用source命令完成初始化。

### sql基础知识

（1）任何一条sql语句以“;”结尾。

（2）sql语句不区分大小写。但是表中的数据区分大小写，这和sql语句无关。当表中的字符串和sql语句大小写不同时，mysql仍能查出记录但oracle不会查出记录。mysql不严谨。

（3）无论是任何数据库，只要数学表达式有null出现，结果一定是null。

（4）sql语句中字符串用单引号括起来。

### 存储引擎

存储引擎就是表的存储方式。不同的存储引擎会有不同的存储方式。

1、完整的建表语句

       CREATE TABLE `t_x` (
      `id` int(11) DEFAULT NULL
     ) ENGINE=InnoDB DEFAULT CHARSET=utf8; 
    
     注意：在MySQL当中，凡是标识符使用飘号括起来的。最好别用，不通用。
    
     建表的时候可以指定存储引擎，也可以指定字符集。
    
     mysql默认使用的存储引擎是InnoDB方式。
     默认采用的字符集是UTF-8。

2、什么是存储引擎呢？

存储引擎这个名字只有在mysql中存在。(Oracle中有对应的机制，但不叫做存储引擎。Oracle中没有特殊的名字，就是"表的存储方式")

mysql支持很多存储引擎，每个存储引擎都对应了一种不同的存储方式。每一个存储引擎都有自己的优缺点，需要在合适的时机选择合适的存储引擎。

3、查看当前mysql支持的存储引擎？

       show engines \G

4、常见的存储引擎？

节省空间：MyISAM。

安全：InnoDB。（常用）

查询速度快：MEMORY。

（1）MyISAM

```
       			Engine: MyISAM
         Support: YES
         Comment: MyISAM storage engine
    Transactions: NO
    	  XA: NO
      Savepoints: NO
    
       MyISAM这种存储引擎不支持事务。
       MyISAM是mysql最常用的存储引擎，但是这种存储引擎不是默认的。
       MyISAM采用三个文件组织一个表：
           格式文件：存储表结构的定义。xxx.frm
       数据文件：存储表行的内容。xxx.MYD
       索引文件：存储表上的索引。xxx.MYI
       优点：可被压缩，节省存储空间。可以转换为只读表，提高检索效率。灵活的AUTO_INCREMENT字段处理
       缺点:不支持事务。
```

（2）InnoDB

```
           Engine: InnoDB
         Support: DEFAULT
         Comment: Supports transactions, row-level locking, and foreign keys
    Transactions: YES
    	  XA: YES
      Savepoints: YES
       
       优点：支持事务、行级锁、外键等。这种存储引擎数据的安全得到保障。
    		
    	 它管理的表具有下列主要特征：
       表的结构存储在xxx.frm文件中
       提供一组用来记录事务性活动的日志文件
       用COMMIT(提交)、SAVEPOINT及ROLLBACK(回滚)支持事务处理
       InnoDB表空间tablespace被用于存储表的内容
       数据存储在tablespace这样的表空间中(逻辑概念)，无法被压缩，无法转换成只读。
       擎在MySQL数据库崩溃之后提供自动恢复机制。
       InoDB支持外键及引用的完整性，包括级联删除和级联更新。
```

（3）MEMORY

以前叫做HEPA引擎。

```
           Engine: MEMORY
         Support: YES
         Comment: Hash based, stored in memory, useful for temporary tables
    Transactions: NO
    	  XA: NO
      Savepoints: NO
    
      缺点：不支持事务，不支持回滚。数据容易丢失。因为所有数据和索引都是存储在内存当中的，断电就没了。
      优点：查询速度最快。
      
      它管理的表具有下列主要特征：
      –	在数据库目录内，每个表均以.frm格式的文件表示。
      –	表数据及索引被存储在内存中。
      –	支持表级锁机制。
      –	不能包含TEXT或BLOB字段。因为内存存储不了。
```


