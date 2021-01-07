[搭建MyBatis开发环境](#搭建MyBatis开发环境)

[基本的CURD操作](#基本的CURD操作)

### 搭建MyBatis开发环境

实现第一个案例，查询表student中的数据。

实现步骤

```
1.创建mysql数据库ssm和表student
2.创建maven工程,pom.xml里加入mybatis依赖，加入mysql的驱动, 加入Resources
3.定义表对应的实体类。类名和表名一致，属性名和列名一致。
4.定义Dao接口，一个Dao接口对应一个sql映射文件。Dao接口的每个方法对应sql映射文件中的一个id。
5.为Dao接口创建一个sql映射文件(也叫mapper文件)，sql映射文件的namespace为接口的全限定名称,每个id值为方法名。sql语句写在sql映射文件中。
6.创建mybatis的主配置文件，提供数据库的连接信息和sql映射文件的位置信息。
7.使用mybatis的SqlSession对象，调用其方法访问数据库。
```

(**1**) 创建 mysql 数据库和表

数据库名 ssm ;表名 student

```mysql
CREATE TABLE `student` (
`id` int(11) NOT NULL ,
`name` varchar(255) DEFAULT NULL, `email` varchar(255) DEFAULT NULL, `age` int(11) DEFAULT NULL, PRIMARY KEY (`id`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8;
-- ----------------------------
-- Records of student
-- ----------------------------
BEGIN;
INSERT INTO `student` VALUES (1001, '李四', 'lisi@yandex.com', 20);
INSERT INTO `student` VALUES (1002, '张三', 'zs@qq.com', 28);
COMMIT;
```

(**2**) 创建 maven工程

pom.xml里加入mybatis依赖，加入mysql的驱动, 加入Resources。

maven默认会忽略java下的文件，所以要加入Resources。

```xml
	<dependencies>
    
    <dependency>
      <groupId>junit</groupId>
      <artifactId>junit</artifactId>
      <version>4.11</version>
      <scope>test</scope>
    </dependency>

    <!--mybatis依赖-->
    <dependency>
      <groupId>org.mybatis</groupId>
      <artifactId>mybatis</artifactId>
      <version>3.5.1</version>
    </dependency>

    <!--mysql驱动-->
    <dependency>
      <groupId>mysql</groupId>
      <artifactId>mysql-connector-java</artifactId>
      <version>8.0.21</version>
    </dependency>
    
  </dependencies>

  <build>
      <!--Resources配置-->
      <resources>
        <resource>
          <directory>src/main/java</directory><!--所在的目录-->
          <includes><!--包括目录下的.properties,.xml 文件都会扫描到-->
            <include>**/*.properties</include>
            <include>**/*.xml</include>
          </includes>
          <filtering>false</filtering>
        </resource>
	
	<!--如果不加这个，compile后target里不会有mybatis.xml，
	需要rebuild project来解决-->
        <resource>
          <directory>src/main/resources</directory><!--所在的目录-->
          <includes><!--包括目录下的.properties,.xml 文件都会扫描到-->
            <include>**/*.properties</include>
            <include>**/*.xml</include>
          </includes>
          <filtering>false</filtering>
        </resource>
      </resources>
    </build>
```

(**3**) 编写表student对应的实体类Student，用来保存表中的一行数据。类名和表名一致，属性名和列名保持一致。

生成getter/setter方法，重写toString方法。

```java
package org.example.domain;

public class Student {

    private Integer id;
    private Integer age;
    private String name;
    private String email;

    public Integer getId() {
        return id;
    }

    public void setId(Integer id) {
        this.id = id;
    }

    public Integer getAge() {
        return age;
    }

    public void setAge(Integer age) {
        this.age = age;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public String getEmail() {
        return email;
    }

    public void setEmail(String email) {
        this.email = email;
    }

    @Override
    public String toString() {
        return "Student{" +
                "id=" + id +
                ", age=" + age +
                ", name='" + name + '\'' +
                ", email='" + email + '\'' +
                '}';
    }
}
```

(**4**) 定义Dao接口，接口名一般为：表名+Dao。

一个方法对应一个SQL的执行，SQL写在sql映射文件中。

一般一个Dao接口对应一个sql映射文件，映射文件和Dao接口在同一目录下。

```java
package org.example.dao;

import org.example.domain.Student;

import java.util.List;

//Dao接口，操作Student表
public interface StudentDao {

    //查询Student表的所有数据。
    public List<Student> selectStudents();
}
```

(**5**) 为Dao接口创建一个sql映射文件，也叫mapper文件。文件名称和接口名称相同，文件的格式是xml。

sql映射文件是用来写sql语句的，mybatis会执行这些sql。

一般一个表有一个sql映射文件。

sql映射文件的namespace为接口的全限定名称，每个id值为接口中的一个方法名。

```xml
<?xml version="1.0" encoding="UTF-8" ?>

<!--
<!DOCTYPE mapper>用来指定约束文件，格式是固定的。
mybatis-3-mapper.dtd是约束文件的名称， 扩展名是dtd的。
约束文件作用：检查在当前文件中出现的标签，属性必须符合mybatis的要求。
-->
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<!--
mapper：当前文件的根标签，表示sql映射文件。
namespace：命名空间，是唯一值，可以是自定义的字符串。但实际中都使用dao接口的全限定名称。
-->
<mapper namespace="org.example.dao.StudentDao">
    <select id="selectStudents" resultType="org.example.domain.Student">
    select id, name, email, age from student order by id
  </select>
</mapper>
<!--
在当前文件中，可以使用特定的标签，表示数据库的特定操作。
   <select>:表示执行查询，select语句
   <update>:表示更新数据库的操作，在该标签中写的是update sql语句
   <insert>:表示插入，放的是insert语句
   <delete>:表示删除，执行的delete语句

   id: sql语句的唯一标识，mybatis会使用这个id的值来找到要执行的sql语句
       可以自定义，但实际上使用接口中的方法名称。

   resultType:表示结果类型，值写实体类的全限定名称。这里是查询操作，sql语句执行后得到ResultSet,遍历这个ResultSet得到的java对象的类型为resultType。
      
-->
```

(**6**) 创建mybatis的主配置文件。

一个项目只有一个主配置文件。主配置文件放在resources目录下。

**主配置文件提供了数据库的连接信息和sql映射文件的位置信息。**

没有开启日志时，不知道sql语句执行的详细信息，不知道这些语句的值是什么。

在mybatis的主配置文件中加入日志配置，可以在控制台输出执行的sql语句的参数。
```xml
<?xml version="1.0" encoding="UTF-8" ?>

<!--
<!DOCTYPE configuration>用来指定约束文件，格式是固定的。
mybatis-3-config.dtd是约束文件的名称，扩展名是dtd的。
约束文件作用：检查在当前文件中出现的标签，属性必须符合mybatis的要求。
-->
<!DOCTYPE configuration
        PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-config.dtd">

<!--
configuration:根标签，表示配置信息。
environments：配置环境：数据库的连接信息。default的值必须是某个environment的id值。
environment：一个数据库信息的配置，id表示数据源的名称。
transactionManager type：mybatis的事务类型
dataSource tyope：表示数据源的类型。数据源是用来连接数据库的。
property name="driver","url","username","password"配置连接数据库的具体信息。

mysql5:
    driver的value="com.mysql.jdbc.Driver"
    url的value="jdbc:mysql://localhost:3306/数据库名"

mysql8:
    driver的value="com.mysql.cj.jdbc.Driver"
    url的value="jdbc:mysql://localhost:3306/数据库名?useUnicode=true&amp;serverTimezone=UTC&amp;useSSL=false
-->
<configuration>
	
    <!--settings:控制mybatis全局行为-->
    <settings>
        <!--
        logImpl表示控制日志，STDOUT_LOGGING表示把日志输出到控制台上。
        -->
        <setting name="logImpl" value="STDOUT_LOGGING" />
    </settings>

    <environments default="development">
        <environment id="development">
            <!--配置mybatis的事务类型
            JDBC表示使用jdbc中的Connection对象的commit，rollback做事务处理。
            -->
            <transactionManager type="JDBC"/>
            <dataSource type="POOLED">
                <property name="driver" value="com.mysql.cj.jdbc.Driver"/>
                <property name="url" value="jdbc:mysql://localhost:3306/ssm?useUnicode=true&amp;serverTimezone=UTC&amp;useSSL=false"/>
                <property name="username" value="root"/>
                <property name="password" value="mysql1234"/>
            </dataSource>
        </environment>
    </environments>

    <!--提供sql映射文件的位置
    一个mapper标签指定一个文件的位置：从类路径开始的路径信息。（类路径target/classes/下面的路径）
    -->
    <mappers>
        <mapper resource="org/example/dao/StudentDao.xml"/>
    </mappers>
</configuration>
```

(**7**) 使用mybatis的SqlSession对象，调用其方法访问数据库，执行sql语句。

本案例是读取表student中的数据。

```
1.定义mybatis主配置文件的名称, 从类路径的根开始。
2.通过Resources.getResourceAsStream(config)读取配置文件，方法的返回类型为InputStream。
3.创建SqlSessionFactoryBuilder对象，调用该对象的build(InputStream in)方法，参数为配置文件。该方法返回一个SqlSessionFactory对象。
4.调用SqlSessionFactory对象的openSession()方法，来获取SqlSession对象。
5.指定要执行的sql语句的标识：通过namespace+id来找到。
6.调用sqlSession对象的selectList(sqlId)方法，参数为sqlId。selectList表示执行数据库查询，得到多条记录。该方法返回一个List对象，List中的元素类型为实体类，由标签中的ResultType指定。
7.关闭SqlSession对象
```

```
package org.example;

import org.apache.ibatis.io.Resources;
import org.apache.ibatis.session.SqlSession;
import org.apache.ibatis.session.SqlSessionFactory;
import org.apache.ibatis.session.SqlSessionFactoryBuilder;
import org.example.domain.Student;
import java.io.IOException;
import java.io.InputStream;
import java.util.List;

public class MyApp {
    public static void main(String[] args) throws IOException {
        //1.定义mybatis主配置文件的名称, 从类路径的根开始（target/clasess）
        String config = "mybatis.xml";
        //2.读取这个config表示的文件。读取后mybatis就会自动创建connection对象，然后连接数据库。
        InputStream in = Resources.getResourceAsStream(config);
        //3.创建SqlSessionFactoryBuilder对象
        SqlSessionFactoryBuilder builder = new SqlSessionFactoryBuilder();
        //4.创建SqlSessionFactory对象
        SqlSessionFactory factory = builder.build(in);
        //5.获取SqlSession对象
        SqlSession sqlSession = factory.openSession();
        //6.指定要执行的sql语句的标识。
        String sqlId = "org.example.dao.StudentDao.selectStudents";
        //7.执行sql语句，通过sqlId找到语句
        List<Student> studentList = sqlSession.selectList(sqlId);
        //8.输出结果
        studentList.forEach(stu -> System.out.println(stu));
        for(Student stu : studentList){
            System.out.println("查询的学生="+stu);
        }
        //9.关闭SqlSession对象
        sqlSession.close();
    }
}
```

### 基本的CURD操作
