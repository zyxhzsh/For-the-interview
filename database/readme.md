[mysql概述](#mysql概述)

[DQL语句](DQL语句.md)

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
