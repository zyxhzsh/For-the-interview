### DML语言

数据操作语言，对表当中的数据进行增删改。

[向表中插入数据](#向表中插入数据)

[将查询结果插入到一张表中](#将查询结果插入到一张表中)

[修改表中的数据](#修改表中的数据)

[删除表中的数据](#删除表中的数据)

### 向表中插入数据

语法格式：

**写法1**

要求：字段的数量要等于值的数量，字段的顺序可以任意，但数据类型要对应相同。

```
insert into 表名
	(字段名1,字段名2,字段名3,....) 
values
	(值1,值2,值3,....),#可以一次插入多行数据
	...
	(值1,值2,值3,....);
```

即可以写全部字段，也可以写部分字段。写部分字段时,没有写出的字段的值为默认值。如果建表时没指定某字段的默认值，其默认值就为null。如果指定了其默认值就是指定的值。

**写法2**

这种格式必须按顺序写出所有字段的值。

```
insert into 表名 
values
	(这种格式必须按顺序写出所有字段的值),#可以一次插入多行数据
	...
	(所有字段的值);
```

```
#写全部字段
insert into t_student(no,name,sex,classno,birth)#写法1
values(1,'zhangsan','1','gaosan1ban','1950-10-12');

insert into t_student values(2,'jack','0','gaosan2ban','1996-07-23');#写法2

#写部分字段
insert into t_student(name) values ('trye');
+------+----------+------+------------+------------+
| no   | name     | sex  | classno    | birth      |
+------+----------+------+------------+------------+
|    1 | zhangsan | 1    | gaosan1ban | 1950-10-12 |
| NULL | trye     | 1    | NULL       | NULL       |
+------+----------+------+------------+------------+

#一次插入多行数据
insert into t_student
		(no,name,sex,classno,birth) 
values
		(3,'rose','1','gaosi2ban','1952-12-14'),
		(4,'laotie','1','gaosi2ban','1955-12-14');

```

当一条insert语句执行成功之后，表格当中必然会多一行记录，即使多的这一行记录当中某些字段是NULL，后期也没有办法通过执行insert语句对这该记录中的字段插入数据了，只能使用update进行更新。

### 将查询结果插入到一张表中

查询结果 和 插入的表的字段数要相同。

```
insert into 表名 select语句;#将查询结果插入到一张表中

#EX
insert into emp1 select * from emp;
```

### 修改表中的数据

若没有条件整张表数据全部更新。

```
update 表名 set 字段名1=值1,字段名2=值2... where 条件;

#EX 将部门10的LOC修改为SHANGHAI，将部门名称修改为RENSHIBU
update dept1 set loc = 'SHANGHAI',dname = 'RENSHIBU' where deptno = 10;
```

### 删除表中的数据

若没有条件，整张表数据全部删除。

delete的执行效率非常慢，因为没有释放数据的真实存储空间，回滚的时候要用，后悔了还可以回滚。

```
delete from 表名 where 条件;
```

若确定表中数据真的不要了，不需要回滚，可以使用truncate删除大表。表被截断不可回滚，永久丢失，只剩下表头，没有记录。

```
truncate table emp1
```
