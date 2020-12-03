### DDL语言

数据定义语言，对表结构的增删改。

表在数据库中一般建议以：t_或者tbl\_开始。（数据库中还有很多别的对象：视图、索引等）。

[表的创建](#表的创建)

[表的删除](#表的删除)

[表结构的修改](#表结构的修改)

[约束](#约束)

### 表的创建

建表语句的语法格式：

```
create table 表名(
			字段名1 数据类型 default 默认值, #没有用default指定默认值时，默认值为null
			字段名2 数据类型 约束,#在创建表的时候，可以给表的字段添加相应的约束，添加约束的目的是为了保证表中数据的
	合法性、有效性、完整性。
			字段名3 数据类型,
			....
);
```

常见的数据类型

```
int			整数型(java中的int)
bigint	长整型(java中的long)
float		浮点型(java中的float double)
char		定长字符串(String)
varchar	可变长字符串(StringBuffer/StringBuilder)
date		日期类型 （对应Java中的java.sql.Date类型）
BLOB		二进制大对象（存储图片、视频等流媒体信息） Binary Large OBject （对应java中的Object）
CLOB		字符大对象（存储较大文本，比如，可以存储4G的字符串。） Character Large OBject（对应java中的Object）
```

**char和varchar怎么选择？**

当某个字段中的数据长度不发生改变的时候，是定长的，例如：性别、生日等都是采用char。（生日也可以用date）

当一个字段的数据长度不确定，例如：简介、姓名等都是采用varchar。varchar可以根据实际存入的数据长度动态地分配空间，但是数据长度不能超过varchar()括号里的上限。

**BLOB和CLOB类型的使用。**

一般是把电影放到云盘或某个硬盘，把存储路径放到数据库里，路径用char或者varchar即可。有些团队可能会把很小的图片放到数据库中（用户头像很小），好处是便于维护。放在硬盘上的图片如果换了位置，数据库就找不到图片了。

（1）创建学生表

学生信息包括：学号、姓名、性别、班级编号、生日

```
学号：bigint
姓名：varchar
性别：char
班级编号：int
生日：char
```

```
create table t_student(
  no bigint,
  name varchar(255),
  sex char(1) default 1,
  classno varchar(255),
  birth char(10)
);
```

### 表的删除

```
drop table if exist 表名;
```

### 表结构的修改

采用alter table来增加/删除/修改表结构，不影响表中的数据

alter table 表名 add 字段名 数据类型	#添加字段

alter table 表名 modify 字段名 数据类型	#修改字段的数据类型

alter table 表名 modify 字段名 新的字段名 数据类型	#修改字段的名称

alter table 表名 drop 字段名	#删除字段

### 约束

在创建表的时候，可以给表的字段添加相应的约束，添加约束的目的是为了保证表中数据的合法性、有效性、完整性。

常见的约束有哪些呢？

非空约束(not null)：约束的字段不能为NULL

唯一约束(unique)：约束的字段不能重复

主键约束(primary key)：约束的字段既不能为NULL，也不能重复（简称PK）

外键约束(foreign key)：...（简称FK）

检查约束(check)：注意Oracle数据库有check约束，但是mysql没有，目前mysql不支持该约束。
