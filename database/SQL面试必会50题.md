[表的结构](#表的结构)

[建表和插入数据](#建表和插入数据)


1.查询课程编号为“01”的课程比“02”的课程成绩高的所有学生的学号（重点）


### 表的结构

学生表：

Student(s_id,s_name,s_birth,s_sex) –- 学生编号,学生姓名, 出生年月,学生性别

课程表：

Course(c_id,c_name,t_id) –– 课程编号, 课程名称, 教师编号

教师表：

Teacher(t_id,t_name) –- 教师编号,教师姓名

成绩表：

Score(s_id,c_id,s_s_score) –- 学生编号,课程编号,分数

### 建表和插入数据
```
-- 建表
-- 学生表
CREATE TABLE `Student`(
`s_id` VARCHAR(20),
`s_name` VARCHAR(20) NOT NULL DEFAULT '',
`s_birth` VARCHAR(20) NOT NULL DEFAULT '',
`s_sex` VARCHAR(10) NOT NULL DEFAULT '',
PRIMARY KEY(`s_id`)
);
-- 课程表
CREATE TABLE `Course`(
`c_id` VARCHAR(20),
`c_name` VARCHAR(20) NOT NULL DEFAULT '',
`t_id` VARCHAR(20) NOT NULL,
PRIMARY KEY(`c_id`)
);
-- 教师表
CREATE TABLE `Teacher`(
`t_id` VARCHAR(20),
`t_name` VARCHAR(20) NOT NULL DEFAULT '',
PRIMARY KEY(`t_id`)
);
-- 成绩表
CREATE TABLE `Score`(
`s_id` VARCHAR(20),
`c_id` VARCHAR(20),
`s_score` INT(3),
PRIMARY KEY(`s_id`,`c_id`)
);
```
```
-- 插入学生表测试数据
insert into Student values('01' , '赵雷' , '1990-01-01' , '男');
insert into Student values('02' , '钱电' , '1990-12-21' , '男');
insert into Student values('03' , '孙风' , '1990-05-20' , '男');
insert into Student values('04' , '李云' , '1990-08-06' , '男');
insert into Student values('05' , '周梅' , '1991-12-01' , '女');
insert into Student values('06' , '吴兰' , '1992-03-01' , '女');
insert into Student values('07' , '郑竹' , '1989-07-01' , '女');
insert into Student values('08' , '王菊' , '1990-01-20' , '女');
-- 课程表测试数据
insert into Course values('01' , '语文' , '02');
insert into Course values('02' , '数学' , '01');
insert into Course values('03' , '英语' , '03');

-- 教师表测试数据
insert into Teacher values('01' , '张三');
insert into Teacher values('02' , '李四');
insert into Teacher values('03' , '王五');

-- 成绩表测试数据
insert into Score values('01' , '01' , 80);
insert into Score values('01' , '02' , 90);
insert into Score values('01' , '03' , 99);
insert into Score values('02' , '01' , 70);
insert into Score values('02' , '02' , 60);
insert into Score values('02' , '03' , 80);
insert into Score values('03' , '01' , 80);
insert into Score values('03' , '02' , 80);
insert into Score values('03' , '03' , 80);
insert into Score values('04' , '01' , 50);
insert into Score values('04' , '02' , 30);
insert into Score values('04' , '03' , 20);
insert into Score values('05' , '01' , 76);
insert into Score values('05' , '02' , 87);
insert into Score values('06' , '01' , 31);
insert into Score values('06' , '03' , 34);
insert into Score values('07' , '02' , 89);
insert into Score values('07' , '03' , 98);
```

1.查询课程编号为“01”的课程比“02”的课程成绩高的所有学生的学号（重点）

想直接从成绩表里去查01课程分数比02课程分数高的所有学生的学号，不太容易。想办法构建这样一张表：
```
——————————————————————————————————————————————
s_id,       c_id_01_score,      c_id_02_score 
学生编号      课程01的分数         课程02的分数
——————————————————————————————————————————————

select * from table where c_id_01_score > c_id_02_score 
```
**方法**

先构建两个子查询，分别求出课程为01的成绩表a和课程为02的成绩表b，两个表只需要保存学号和分数两列。

然后把两个子查询内连接，选择学生编号那一列。

```
SELECT a.s_id FROM
(SELECT s_id, s_score FROM Score WHERE c_id = '01') as a
INNER JOIN
(SELECT s_id, s_score FROM Score WHERE c_id = '02') as b
ON a.s_id = b.s_id
WHERE a.s_score > b.s_score
```
（1）如果还想获取01和02课程的分数，select后面加上这两列
```
SELECT a.s_id, a.s_score as '01', b.s_score as '02' FROM
(SELECT s_id, s_score FROM Score WHERE c_id = '01') as a
INNER JOIN
(SELECT s_id, s_score FROM Score WHERE c_id = '02') as b
ON a.s_id = b.s_id
WHERE a.s_score > b.s_score
```
（2）如果还需要学生的姓名，再和学生表内连接
```
SELECT a.s_id, c.s_name, a.s_score as '01', b.s_score as '02' FROM
(SELECT s_id, s_score FROM Score WHERE c_id = '01') as a
INNER JOIN
(SELECT s_id, s_score FROM Score WHERE c_id = '02') as b
ON a.s_id = b.s_id
INNER JOIN Student as c ON a.s_id = c.s_id
WHERE a.s_score > b.s_score
```
