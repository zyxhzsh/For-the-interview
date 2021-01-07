[入门案例](#入门案例)

**JDBC**

JDBC编程有大量的代码重复，核心亮点只有一个sql语句，开发效率低。

需要加载驱动、创建连接、创建 Statement和ResultSet，需要创建对象，关闭连接，资源等繁杂的过程。业务逻辑和数据库操作混合在一起，代码的维护难度较大。

**缺陷**

1. 重复的代码比较多，开发效率低

2. 需要关注 Connection ,Statement, ResultSet 对象的创建和销毁。

3. 对 ResultSet 查询的结果，需要自己封装为 List

4. 业务代码和数据库操作混在一起，代码的维护难度较大。

### mybatis

mybatis是一个基于 Java 的持久层框架，包括SQL Maps 和 Data Access Objects(DAOs)。

```
（1）sql mapper:sql映射
可以把数据库表中的一行数据映射为一个java对象。
一行数据可以看做是一个java对象。操作这个对象，就相当于操作表中的数据。
（2）Data Access Objects(DAOs)数据访问对象，对数据库执行增删改查。
```

mybatis内部封装了 jdbc，开发者只需要关注sql语句本身，而不需要加载驱动、创建连接、创建 Statement和ResultSet，创建对象，关闭连接，资源等繁杂的过程。

MyBatis通过xml或注解两种方式配置sql 语句，并通过 java 对象和 sql 的动态参数进行映射生成最终执行的 sql 语句，最后由 mybatis 框架执行 sql 并将结果映射为java对象并返回。

**开发人员做的是：提供sql语句。至于怎么执行语句，怎么得到List集合或java对象（表中的数据），都由mybatis来完成。**

**MyBatis解决的主要问题**

减轻使用 JDBC 的复杂性，不用编写创建 Connetion , Statement，ResultSet对象的代码; 不用加载驱动、创建和关闭连接。不用编写关闭资源的代码。

直接使用 java 对象，表示结果数据。让开发者专注 SQL 的处理。 

**MyBatis可以完成**

1.注册数据库的驱动，例如 Class.forName(“com.mysql.jdbc.Driver”))

2.创建 JDBC 中必须使用的 Connection ， Statement，ResultSet 对象 

3.从配置文件或者注解中获取 sql，并执行 sql 语句。把 ResultSet 结果转换 成java 对象，并且给属性赋值。

```java
List<Student> list = new ArrayLsit<>();
ResultSet rs = state.executeQuery(“select * from student”); 
while(rs.next){
   Student student = new Student(); 			
   student.setName(rs.getString(“name”)); 
   student.setAge(rs.getInt(“age”));  
   list.add(student);
} 
```

4.关闭资源

ResultSet.close() , Statement.close() , Conenection.close()

**总结**

mybatis是一个sql映射框架，提供了数据库操作，是增强的JDBC。mybatis让开发人员只需要写sql就可以了，不需要关心Connection,Statement,ResultSet的创建和销毁，sql的执行，将sql结果转换为java对象。
