### DDL语言

数据定义语言，对表结构的增删改。

表在数据库中一般建议以：t_或者tbl\_开始。（数据库中还有很多别的对象：视图、索引等）。

查询表结构：desc 表名;

查看表的字符集：show table status from mysql like 表名;

[表的创建](#表的创建)

[表的删除](#表的删除)

[表结构的修改](#表结构的修改)

[约束](#约束)

[主键约束](#主键约束)

[外键约束](#外键约束)

### 表的创建

建表语句的语法格式：

```
create table 表名(
			字段名1 数据类型 default 默认值, 		#没有用default指定默认值时，默认值为null
			字段名2 数据类型 约束,			 #在创建表的时候，可以在字段后添加相应的约束，称为列级约束
			....
			字段名k 数据类型,
			unique(字段m,字段n)			    #可以多个字段联合添加约束。称为表级约束
			
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
drop table if exist 表名;不通用，mysql支持，oracle不支持。
drop table 表名;#通用，如果表不存在会报错。
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

非空约束(not null)：约束的字段不能为NULL。非空约束没有表级定义方式，只有列级约束，只能加到字段后面。

唯一约束(unique)：约束的字段不能重复，但可以为null。null和null不算重复。

主键约束(primary key)：约束的字段既不能为NULL，也不能重复（简称PK）

外键约束(foreign key)：...（简称FK）

检查约束(check)：注意Oracle数据库有check约束，但是mysql没有，目前mysql不支持该约束。

### 主键约束

一张表的主键约束只能有1个。

主键相关的术语

		主键约束：primary key
		主键字段：id字段添加primary key之后，id叫做主键字段
		主键值：id字段中的每一个值都是主键值。

主键有什么作用？

这行记录在这张表中的唯一标识。第一范式就要求任何一张表都必须有主键。

**主键的分类**

根据主键字段的字段数量来划分：

单一主键 (推荐的，常用的。)

复合主键：多个字段联合起来添加一个主键约束(复合主键不建议使用，因为复合主键违背三范式。)
primary key(字段1,...,字段k)

根据主键性质来划分：

自然主键 ：主键值是一个和业务没有任何关系的自然数。(这种方式是推荐的)

业务主键 ：主键值和系统的业务挂钩，例如：拿着银行卡的卡号做主键、拿着身份证号做为主键。(不推荐使用)

最好不要拿着和业务挂钩的字段做为主键。因为以后的业务一旦发生改变的时候，主键也可能需要随着发生变化，但有的时候没有办法变化，因为变化可能会导致主键重复。

**主键值自增**

用户注册时不需要输入主键值。

```
drop table if exists t_user;
create table t_user(
	id int primary key auto_increment,  //id字段自动维护一个自增的数字，从1开始，以1递增。
	username varchar(255)
);
insert into t_user(username) values('a'); 
insert into t_user(username) values('b');
insert into t_user(username) values('c');
insert into t_user(username) values('d');
insert into t_user(username) values('e');
insert into t_user(username) values('f');
select * from t_user;

提示：Oracle当中也提供了一个自增机制，叫做：序列(sequence)对象。
```
### 外键约束

关于外键约束的相关术语

      外键约束：foreign key
      外键字段：添加有外键约束的字段
      外键值：外键字段中的每一个值。

语法：foreign key(外键字段) references 引用的字段所在的表(引用的字段)

**注意**

（1）外键值可以为null。

（2）外键字段引用其他表的某个字段的时候，被引用的字段必须是主键吗？

 被引用的字段不一定是主键，但至少是具有unique约束，具有唯一性，不可重复！


**业务背景**

请设计数据库表，用来维护学生和班级的信息？

第一种方案：一张表存储所有数据。缺点：冗余。【不推荐】
```
	  no(pk)          name          classno         classname
	  -----------------------------------------------------------
	   1               zs1            101          河南省平顶山市舞钢市垭口一高高三1班         
	   2               zs2            101          河南省平顶山市舞钢市垭口一高高三1班 
	   3               zs3            102          河南省平顶山市舞钢市垭口一高高三2班 
	   4               zs4            102          河南省平顶山市舞钢市垭口一高高三2班
	   5               zs5            102          河南省平顶山市舞钢市垭口一高高三2班 
```   
第二种方案：两张表(班级表和学生表)
```
	   t_class 班级表
	   cno(pk)         cname
	   -------------------------------------------------------------
            101           河南省平顶山市舞钢市垭口一高高三1班 
	    102           河南省平顶山市舞钢市垭口一高高三2班 

	    t_student 学生表
	    sno(pk)        sname          classno(该字段添加外键约束fk)
	    -----------------------------------------------------------
	     1              zs1              101
	     2              zs2              101
	     3              zs3              102
	     4              zs4              102
	     5              zs5              102
```
将以上表的建表语句写出来：

t_student中的classno字段引用t_class表中的cno字段，此时t_student表叫做子表。t_class表叫做父表。

添加数据的时候，先添加父表，再添加子表。
      
删除数据的时候，先删除子表，再删除父表。

创建表的时候，先创建父表，再创建子表。

删除表的时候，先删除子表，再删除父表。
```
drop table if exists t_student;
drop table if exists t_class;

create table t_class(
       cno int,
       cname varchar(255),
       primary key(cno)
);

create table t_student(
        sno int,
	sname varchar(255),
	classno int,
	primary key(sno),
	foreign key(classno) references t_class(cno)
);
    
insert into t_class values(101,'xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx');
insert into t_class values(102,'yyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyy');

insert into t_student values(1,'zs1',101);
insert into t_student values(2,'zs2',101);
insert into t_student values(3,'zs3',102);
insert into t_student values(4,'zs4',102);
insert into t_student values(5,'zs5',102);
insert into t_student values(6,'zs6',102);
```
```
select * from t_class;
select * from t_student;

insert into t_student values(7,'lisi',103);  //编译错误，引用的103，父表中没有该字段！
ERROR 1452 (23000) : Cannot add or update a child row :aforeign key constraint fails (bjpowernode INT YT......)
```

