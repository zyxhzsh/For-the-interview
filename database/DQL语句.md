### DQL语句

数据查询语句。第一列为书写顺序，第二列为执行顺序。

```
select		5
		..(as别名)
from			1	
		..
where		  2
		..
group by	3
		..
having		4
		..
order by	6
		..
```
[简单查询](#简单查询)

[条件查询](#条件查询)

[排序数据](#排序数据)

[分组函数](#分组函数)

[单行处理函数](#单行处理函数)

[分组查询](#分组查询)

[去重复记录](#去重复记录)

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

