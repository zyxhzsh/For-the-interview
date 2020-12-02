### DQL语句

数据查询语句。第一列为书写顺序，第二列为执行顺序。

```
select		5
		..(as别名)
from		1	
		..
where		  2
		..
group by	3
		..
having		4
		..
order by	6
		..
limit		7
```
### 单表查询

[简单查询](#简单查询)

[条件查询](#条件查询)

[排序数据](#排序数据)

[分组函数](#分组函数)

[单行处理函数](#单行处理函数)

[分组查询](#分组查询)

[去重复记录](#去重复记录)

### 多表查询

[连接查询](#连接查询)

[子查询](#子查询)

### union

[union](#union)

### limit

[limit](#limit)

---

### 简单查询

DQL（数据查询语言）：查询语句，凡是select语句都是DQL。

查询多个字段：select 字段1,字符2,...字段n  from 表名;

查询全部字段：select  *  from 表名;#不建议用\*，效率较低，需要先将\*转换成字段

（1）字段可以参与数学运算：

```
mysql> select ename,sal*12 from emp;#查询员工的年薪
+--------+----------+
| ename  | sal*12   |
+--------+----------+
| SMITH  |  9600.00 |
| ALLEN  | 19200.00 |
| WARD   | 15000.00 |
| JONES  | 35700.00 |
| MARTIN | 15000.00 |
| BLAKE  | 34200.00 |
| CLARK  | 29400.00 |
| SCOTT  | 36000.00 |
| KING   | 60000.00 |
| TURNER | 18000.00 |
| ADAMS  | 13200.00 |
| JAMES  | 11400.00 |
| FORD   | 36000.00 |
| MILLER | 15600.00 |
+--------+----------+
14 rows in set (0.00 sec)
```

给查询结果的列重命名：as可以省略

select ename, sal\*12 as yearsal from emp;

select ename, sal\*12  yearsal from emp;

别名中有中文：标准sql语句要求字符串必须用单引号括起来。虽然mysql支持双引号，尽量别用。因为oracle和sqlserver都不支持双引号。

### 条件查询

select 字段1,字段2... 

from 表名 

where 条件

执行顺序：from, where, select。

支持如下运算符

| 运算符           | 说明                                                         |
| ---------------- | :----------------------------------------------------------- |
| =                | 等于                                                         |
| <>或!=           | 不等于                                                       |
| <                | 小于                                                         |
| <=               | 小于等于                                                     |
| \>               | 大于                                                         |
| \>=              | 大于等于                                                     |
| between … and …. | 两个值之间,**等同于 >= and <=**  。也可用于字符串，此时表示左闭右开。 |
| is null          | 为`null`（is not null 不为空）                               |
| **and**          | 并且 and比or优先级高                                         |
| **or**           | 或者                                                         |
| in               | 包含。相当于多个or（not in表示不在这个范围中）               |
| not              | not可以取非，主要用在is 或in中                               |
| like             | like称为模糊查询，支持%或下划线匹配  %匹配任意个字符  下划线匹配一个字符 |

（1）找出工资不等于3000的员工？

		select ename, sal from emp where sal<>3000;
	
		select ename, sal from emp where sal!=3000;#两种都可以

（2）找出工资在1100和3000之间的员工，包括1100和3000？

		select ename,sal from emp where sal>=1000 and sal<=3000;
	
		select ename,sal from emp where sal between 1000 and 3000;#闭区间
	
		使用 between and，必须左小右大。因为between num1 and num2表示的是闭区间[num1, num2]

（3）找出哪些人津贴不为NULL？

```
select ename,sal,comm from emp where comm is not null;
```

（4）找出哪些人没有津贴？

```
select ename, sal, comm  from emp where comm is null or comm = 0;
```

（5）找出薪资大于2000的并且部门编号是20或30部门的员工。

```
select ename, sal, deptno from emp where sal>2000 and (deptno=20 or deptno=30);
select ename, sal, deptno from emp where sal>2000 and deptno in(20, 30);
两种方式效率没区别
```

（6）找出名字中第二个字母是A的？

```
select ename from emp where ename like '_A%';
```

（7）找出名字中有下划线的？

```
select name from t_user where name like '%_%';
```

### 排序数据

排序采用order by子句，排序字段可以放多个，采用逗号间隔。order by默认采用升序。

如果存在where子句那么order by必须放到where语句的后面。

```
select ... from ... where ... order by 字段1 asc/desc, ... ,字段k asc/desc;  
如果根据第i个字段排序相等，会根据第i+1个字段排序。
```

可以使用字段在被选择的字段中的位置代表该字段来排序，但不健壮。

（1）按照工资的降序排列，当工资相同的时候再按照名字的升序排列。

```
select ename, sal from emp order by sal desc, ename asc;
select ename, sal from emp order by 2 desc, 1 asc;#不健壮，不推荐。
```

（2）找出工作岗位是SALESMAN的员工，并且要求按照薪资的降序排列。

```
select ename, job, sal from emp where job='salesman' order by sal desc;
```

### 分组函数

分组函数对某一组数据进行操作。

分组函数也叫多行处理函数：输入多行，输出结果一行。

1.分组函数自动忽略null，不需要额外添加过滤条件。

2.分组函数不可直接出现自where子句中。因为group by在where之后执行，而分组函数是在group by之后执行。

3.count(*)一定统计的是总记录条数，count(字段名)统计的是该字段中不为null的数据总数量。

4.分组函数能组合起来使用

```
select count(*),sum(sal),avg(sal),max(sal),min(sal) from emp;
+----------+----------+-------------+----------+----------+
| count(*) | sum(sal) | avg(sal)    | max(sal) | min(sal) |
+----------+----------+-------------+----------+----------+
|       14 | 29025.00 | 2073.214286 |  5000.00 |   800.00 |
+----------+----------+-------------+----------+----------+
1 row in set (0.00 sec)
```

```
count 计数
sum 求和
avg 平均值
max 最大值
min 最小值

找出工资总和？
		select sum(sal) from emp;
找出最高工资？
		select max(sal) from emp;
找出最低工资？
		select min(sal) from emp;
找出平均工资？
		select avg(sal) from emp;
找出总人数？
		select count(*) from emp;
		select count(ename) from emp;#错
```

### 单行处理函数

ifnull(可能为null的数据，被当做...处理)

（1）计算每个员工的年薪。

```
select ename, (sal+ifnull(comm,0))*12 as yearsal from emp;
```

### 分组查询

group by...having

分组函数一般都会和group by联合使用，并且任何一个分组函数都是在group by语句执行结束之后才会执行。

若一条sql语句没有group by，会有一个缺省的group by，整张表的数据会自成一组。

1.当一条语句有group by时，select后面只能跟参加分组的字段和分组函数，别的字段没有意义。mysql不会报错，但是oracle会报错。

2.多个字段可以联合起来一起分组。groupby 字段1,...字段k。

3.having在group by之后执行，作用是对分组的数据再进行过滤。想使用having必须要有group by，group by不能缺省，缺省会报错。

where效率较高，能够用where尽量使用where。

（1）找出工资高于平均公司的员工。

```
select ename,sal from emp where sal >(select avg(sal) from emp);
```

（2）每个工作岗位最高薪资？

```
select max(sal), job from emp group by job;
```

（3）找出每个部门不同工作岗位的最高薪资。

```
select deptno,job, max(sal) from emp group by deptno,job;
```

（4）找出每个部门的最高薪资，要求显示薪资大于2000的数据。

```
select deptno,max(sal) from emp group by deptno having max(sal)>2000;#这种方式效率非常低。求出了最大值却丢弃了，不如早点丢弃，用where语句在分组前就过滤掉。
select deptno,max(sal) from emp where sal>2000 group by deptno;
```

（5）找出每个部门的平均薪资，要求显示薪资大于2000的数据。

```
select deptno,avg(sal) from emp group by deptno having max(sal)>2000;
```

### 去重复记录

distinct ..

distinct只能出现在所有字段的前方，表示后面所有字段联合起来去除重复记录。

（1）统计岗位的数量

```
select count(distinct job) from emp;
```
### 连接查询

在实际开发中查询数据，一般都是多张表联合查询取出最终的结果。多张表联合查询数据就叫连接查询。

**连接查询的分类**

根据语法出现的年代来划分的话，包括：

	SQL92（一些老的DBA可能还在使用这种语法。DBA：DataBase Administrator，数据库管理员）			
	
	SQL99（比较新的语法）	

根据表的连接方式来划分，包括：

	内连接：
		等值连接
		非等值连接
		自连接
				
	外连接：
		左外连接（左连接）
		右外连接（右连接）
		
	全连接（很少用）

**表的别名**

连接查询一定要给表起别名，因为两张表可能有字段名相同的情况。

表的别名执行效率高，可读性好。

**笛卡尔积现象**

当两张表进行连接查询的时候，如果没有任何条件进行限制，最终的查询结果条数是两张表记录条数的乘积。

怎么避免笛卡尔积现象？

加条件进行过滤。过滤不会减少记录的匹配次数，只不过显示的是有效记录。所以加了过滤程序的执行效率不会变。

[内连接](#内连接)

[外连接](#外连接)

[内连接和外连接的区别](#内连接和外连接的区别)

[三张以上表的连接](#三张以上表的连接)

### 内连接

**等值连接**

内连接之等值连接：连接条件中的关系是等量关系。

（1）找出每一个员工的部门名称，要求显示员工名和部门名。

99语法中，表连接的条件 和 连接后进行数据过滤的条件(where)分离了，结构更清晰。

```
select 
	e.ename, d.dname 
from 
	emp e, dept d 
where 
	e.deptno = d.deptno;#92语法，现已不用。

#99语法
select
	e.ename,d.dname
from
	emp e
inner join#inner可以省略，默认为内连接，带inner可读性
	dept d
on
	e.deptno = d.deptno;
```

**非等值连接**

内连接之非等值连接：连接条件中的关系是非等量关系。

（1）找出每个员工的工资等级，要求显示员工名、工资、工资等级。

```
select
	e.ename,e.sal,s.grade
from
	emp e
inner join
	salgrade s
on
  e.sal between s.losal and s.hisal;
```

**自连接**

内连接之自连接：一张表看作两张表，自己连自己。

（1）找出每个员工的上级领导，要求显示员工名和对应的领导名。

```
#弄清楚连接条件：员工的领导编号等于领导的员工编号
#king没有上级领导，所以不会包含king的记录。
select 
	a.ename as '员工名',b.ename as '领导名'
from
	emp a
inner join
	emp b
on
	a.mgr = b.empno;
```

### 外连接

**特点**：主表的数据无条件的全部查询出来。

左外连接（左连接）：表示左边的这张表是主表。a left outer join b

右外连接（右连接）：表示右边的这张表是主表。a right outer join b

左连接有右连接的写法，右连接也会有对应的左连接的写法。outer可以省略。

（1）找出每个员工的上级领导。

意思是要每个员工的记录，没有领导也要查出来。如果使用内连接不能匹配就不查询了，而外连接可以把员工表作为主表，主要是为了把所有员工都查出来，捎带查员工的上级领导。不能因为没有上级领导就把员工记录丢失了。

```
select
	a.ename,b.ename
from
	emp a
left outer join
	emp b
on
	a.mgr=b.empno;
```

（2）找出哪个部门没有员工？

```
select 
	d.*
from
	dept d
left outer join
	emp e
on
	e.deptno = d.deptno
where
	e.empno is null;
```

### 内连接和外连接的区别

内连接每个表示平等的，外连接表之间有主次之分。

	内连接：
		假设A和B表进行连接，使用内连接的话，凡是A表和B表能够匹配上的记录查询出来，这就是内连接。
		AB两张表没有主副之分，两张表是平等的。
	
	外连接：
		假设A和B表进行连接，使用外连接的话，AB两张表中有一张表是主表，一张表是副表，主要查询主表中
		的数据，捎带着查询副表，当副表中的数据没有和主表中的数据匹配上，副表自动模拟出NULL与之匹配。

### 三张以上表的连接

（1）找出每一个员工的部门名称以及工资等级。

```
select 
  e.ename,d.dname,s.grade
from 
  emp e
join
  dept d
on
  e.deptno=d.deptno
join 
  salgrade s
on 
  e.sal between s.losal and s.hisal;
```

（2）找出每一个员工的部门名称、工资等级、以及上级领导。

```
select 
  a.ename '员工',d.dname,s.grade,b.ename '领导'
from 
  emp a
join
  dept d
on
  a.deptno=d.deptno
join 
  salgrade s
on 
  a.sal between s.losal and s.hisal
left outer join 
  emp b
on a.mgr=b.empno;
```
### 子查询

子查询就是嵌套的select语句，可以理解为子查询是一张表

where后面嵌套子查询，是为了在where里使用分组函数作为查询条件。

from后嵌套子查询，是为了把查询结果当做临时表。

select后嵌套子查询，有时候看起来好理解。

### where后面嵌套子查询

分组函数不可直接出现自where子句中。

where后面嵌套子查询就是在where语句中加入select语句

（1）找出高于平均薪资的员工信息。

```
select * from emp where sal>(select avg(sal) from emp);
```

### from后嵌套子查询

（1）找出每个部门平均薪水的等级。

第一步：找出每个部门平均薪水（按照部门编号分组，求sal的平均值）

```
select deptno,avg(sal) avgsal from emp group by deptno;
```

第二步：将以上的查询结果当做临时表t，让t表和salgrade s表连接，条件是：t.avgsal between s.losal and s.hisal

```
select 
	t.*,s.grade
from
	(select deptno,avg(sal) as avgsal from emp group by deptno) t
join
	salgrade s
on
	t.avgsal between s.losal and s.hisal;
```

（2）找出每个部门平均的薪水等级。

第一步：找出每个员工的薪水等级。

```
select e.ename,e.sal,e.deptno,s.grade from emp e join salgrade s on e.sal between s.losal and s.hisal;
+--------+---------+--------+-------+
| ename  | sal     | deptno | grade |
+--------+---------+--------+-------+
| SMITH  |  800.00 |     20 |     1 |
| ALLEN  | 1600.00 |     30 |     3 |
| WARD   | 1250.00 |     30 |     2 |
| JONES  | 2975.00 |     20 |     4 |
| MARTIN | 1250.00 |     30 |     2 |
| BLAKE  | 2850.00 |     30 |     4 |
| CLARK  | 2450.00 |     10 |     4 |
| SCOTT  | 3000.00 |     20 |     4 |
| KING   | 5000.00 |     10 |     5 |
| TURNER | 1500.00 |     30 |     3 |
| ADAMS  | 1100.00 |     20 |     1 |
| JAMES  |  950.00 |     30 |     1 |
| FORD   | 3000.00 |     20 |     4 |
| MILLER | 1300.00 |     10 |     2 |
+--------+---------+--------+-------+
```

第二步：基于以上结果，继续按照deptno分组，求grade平均值。

```
select 
	e.deptno,avg(s.grade)
from 
	emp e 
join 
	salgrade s 
on 
	e.sal between s.losal and s.hisal
group by
	e.deptno;
```

### select后嵌套子查询

（1）找出每个员工所在的部门名称，要求显示员工名和部门名。

```
1.外连接的方法
select 
	e.ename,d.dname
from
	emp e
join
	dept d
on
	e.deptno = d.deptno;
	
2.select后嵌套子查询的方法

select 
	e.ename,(select d.dname from dept d where e.deptno = d.deptno) as dname#若不起别名，包括括号整个都会作为字段名。
from 
	emp e;
```

### union

union可以合并查询结果(相加)。

第一个查询结果的列数必须和第二个查询结果的列数相同。

若两张毫无相干的表查询结果想连接，就只能用union。

```
查询语句1
union
查询语句2
```
（1）找出工作岗位是SALESMAN和MANAGER的员工？
```
第一种：
select ename,job from emp where job = 'MANAGER' or job = 'SALESMAN';

第二种：
select ename,job from emp where job in('MANAGER','SALESMAN');

第三种：
select ename,job from emp where job = 'MANAGER'
union
select ename,job from emp where job = 'SALESMAN';
```
### limit

limit用于分页查询，是mysql中特有的。oracle有个相同的机制叫rownum。

limit的作用：取结果集中的部分数据。如果不分页用户体验不好，一次显示太多浏览器可能会崩溃。

```
limit startIndex, length
#startIndex可缺省，默认为0。
limit length
```

通用的分页标准：每页显示3条记录
```
第一页：0，3
第二页：3，3
第三页：6，3
```
第N页：(每页显示记录数)\*(N-1)，每页显示记录数）;

```java
java代码{
	int pageNo = 2; // 页码是2
	int pageSize = 10; // 每页显示10条
	
	limit (pageNo - 1) * pageSize, pageSize
}
```

（1）找出工资排名在第4到第9名的员工？（思路：降序取前4到9名）

```
select ename,sal from emp order by sal desc limit 4,6;#注意4到9名有6个人。
```
