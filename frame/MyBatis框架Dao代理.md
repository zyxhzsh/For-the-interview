[Dao动态代理](#Dao动态代理)

[mybatis是封装的jdbc操作](#mybatis是封装的jdbc操作)

---

[深入理解参数](#深入理解参数)

[获取简单类型的一个参数的值](#获取简单类型的一个参数的值)

[使用@Param](#使用Param)

[使用java对象传参](#使用java对象传参)

[使用位置传参](#使用位置传参)

[使用Map传参](#使用Map传参)

[\#和$](##和$)

[替换列名](#替换列名)

[sql注入](#sql注入)

---

封装**MyBatis**输出结果

[resultType](#resultType)

[resultMap](#resultMap)

[列名和属性名不同的两种解决方法](#列名和属性名不同的两种解决方法)

[自定义数据类型定义别名](#自定义数据类型定义别名)

---

[模糊like](#模糊like)

### Dao动态代理

获取SqlSession对象。调用SqlSession对象的 getMapper(dao接口.class)方法，即可获取指定接口的实现类对象。调用实现类对象的方法来执行mapper文件中对应的SQL语句。

```
这个模式中对象dao的数据类型是什么？
System.out.println("dao="+dao.getClass().getName());
dao=com.sun.proxy.$Proxy2 //jdk的动态代理
```

使用动态代理方式的规范和要求

```
1.dao接口和mapper文件放在同一个目录
2.dao接口和mapper文件名称一致
3.mapper文件中的namespace的值是dao接口的全限定名称
4.mapper文件中的标签<select>,<insert>,<update>,<delete>的id是接口中的方法名称。
5.dao接口中不能有重载方法。
```

```java
package org.example;
import org.apache.ibatis.session.SqlSession;
import org.example.dao.StudentDao;
import org.example.domain.Student;
import org.example.utils.MyBatisUtils;
import org.junit.Test;

import java.util.List;

public class MyTest {

    @Test
    public void testSelect(){
        SqlSession sqlSession = MyBatisUtils.getSqlSession();
        StudentDao dao = sqlSession.getMapper(StudentDao.class);
        List<Student> students = dao.selectStudents();
        for(Student stu:students){
            System.out.println(stu);
        }
    }

    @Test
    public void testInsert(){
        SqlSession sqlSession = MyBatisUtils.getSqlSession();
        StudentDao dao = sqlSession.getMapper(StudentDao.class);
        Student student = new Student();
        student.setId(1007);
        student.setName("徐彬");
        student.setAge(24);
        student.setEmail("xubin@163.com");
        int num = dao.insertStudent(student);
        sqlSession.commit();
        sqlSession.close();
        System.out.println(num);
    }

    @Test
    public void testUpdate(){
        SqlSession sqlSession = MyBatisUtils.getSqlSession();
        StudentDao dao = sqlSession.getMapper(StudentDao.class);
        Student student = new Student();
        student.setId(1003);
        student.setAge(77);
        int num = dao.updateStudent(student);
        sqlSession.commit();
        sqlSession.close();
        System.out.println(num);
    }

    @Test
    public void testDelete(){
        SqlSession sqlSession = MyBatisUtils.getSqlSession();
        StudentDao dao = sqlSession.getMapper(StudentDao.class);
        Student student = new Student();
        int id = 1007;
        int num = dao.deleteStudent(id);
        sqlSession.commit();
        sqlSession.close();
        System.out.println(num);
    }
}
```

### mybatis是封装的jdbc操作

使用#{}之后， mybatis执行sql是使用的jdbc中的PreparedStatement对象

以根据id获取一个学生的信息为例，

```
        由mybatis执行下面的代码：
         1. mybatis创建Connection ， PreparedStatement对象
            String sql="select id,name, email,age from student where id=?";
            PreparedStatement pst = conn.preparedStatement(sql);
            pst.setInt(1,1001);

         2. 执行sql封装为resultType="com.bjpowernode.domain.Student"这个对象
            ResultSet rs = ps.executeQuery();
            Student student = null;
            while(rs.next()){
               //从数据库取表的一行数据， 存到一个java对象属性中
               student = new Student();
               student.setId(rs.getInt("id));
               student.setName(rs.getString("name"));
               student.setEmail(rs.getString("email"));
               student.setAge(rs.getInt("age"));
            }

           return student;  //给了dao方法调用的返回值
```

PreparedStatement使用?做占位符，在mapper中一个#{}对应的就是一个?。用PreparedStatement对象执行效率高、速度快、更安全，没有sql注入的风险。

### 深入理解参数

从java代码中把数据传入到mapper文件的sql语句中。

mybatis把java的基本数据类型、包装类和String叫做简单类型。

### parameterType

接口中方法参数的类型，可以写全限定名称或别名。也可以不写，MyBatis可以通过反射机制得到接口方法的参数类型。

```
<--也可以写成int, Integer, integer-->
<select id="selectStudents" parameterType="java.lang.Integer" resultType="org.example.domain.Student">
        select id, name ,email, age from student order by id;
</select>
```

### 获取简单类型的一个参数的值

mapper文件获取简单类型的一个参数的值，使用#{任意字符}

ex：根据id获取一个学生的信息。

mapper

```
    <select id="selectStudentById" resultType="org.example.domain.Student">
        select id, name ,email, age from student where id=#{studentId};
    </select>
```

接口方法

```
public interface StudentDao {
	   Student selectStudentById(Integer id);
}
```

测试

```
    @Test
    public void testSelectStudentById(){
        SqlSession sqlSession = MyBatisUtils.getSqlSession();
        StudentDao dao = sqlSession.getMapper(StudentDao.class);
        System.out.println("dao="+dao.getClass().getName());
        Student stu = dao.selectStudentById(1003);
        System.out.println(stu);
    }
```

### 使用Param

当 Dao 接口方法多个参数，需要通过名称使用参数

在接口方法的形参前面加入@Param(“自定义参数名”)， mapper 文件使用#{自定义参数名}。

接口方法

```
    List<Student> selectMultiParam(@Param("personName") String name,
                                   @Param("personAge") int age);
```

mapper文件

```
    <select id="selectMultiParam"  resultType="org.example.domain.Student">
        select id, name ,email, age from student where name=#{studentName} or age=#{studentAge};
    </select>	
```

测试

```
    @Test
    public void testSelectMultiParam(){
        SqlSession sqlSession = MyBatisUtils.getSqlSession();
        StudentDao dao = sqlSession.getMapper(StudentDao.class);
        System.out.println("dao="+dao.getClass().getName());
        List<Student> students = dao.selectMultiParam("李四",22);
        for(Student stu:students){
            System.out.println(stu);
        }
    }
    
==>  Preparing: select id, name ,email, age from student where name=? or age=?; 
==> Parameters: 李四(String), 20(Integer)
<==    Columns: id, name, email, age
<==        Row: 1001, 李四, lisi@yandex.com, 20
<==        Row: 1006, 关羽, guanyu@163.com, 20
<==      Total: 2
Student{id=1001, age=20, name='李四', email='lisi@yandex.com'}
Student{id=1006, age=20, name='关羽', email='guanyu@163.com'}
```

### 使用java对象传参

接口中方法的参数是一个java对象。使用一个java对象传参，对象里有多个属性，这些属性值就是sql需要的参数值。

语法格式:  

javaType, jdbcType 的类型 MyBatis 可以检测出来，一般不需要设置。

```
#{property,javaType=java中数据类型名,jdbcType=数据类型名称}
```

参数对象类型的定义(也可以就用Student类型作为参数)

```
package org.example.vo;

public class QueryParam {
    private String paramName;
    private Integer paramAge;

    public String getParamName() {
        return paramName;
    }

    public void setParamName(String paramName) {
        this.paramName = paramName;
    }

    public Integer getParamAge() {
        return paramAge;
    }

    public void setParamAge(Integer paramAge) {
        this.paramAge = paramAge;
    }

    @Override
    public String toString() {
        return "QueryParam{" +
                "paramName='" + paramName + '\'' +
                ", paramAge=" + paramAge +
                '}';
    }
}
```

接口方法

```
    List<Student> selectMultiObject(QueryParam queryParam);
```

mapper文件

```
    <select id="selectMultiObject"  resultType="org.example.domain.Student">
        select id, name ,email, age from student where name=#{paramName} or age=#{paramAge};
    </select>
<!--
where name=#{queryName,javaType=string,jdbcType=VARCHAR} 
or 
age =#{queryAge,javaType=int,jdbcType=INTEGER}
-->
```

测试

```
    @Test
    public void testSelectMultiObject(){
        SqlSession sqlSession = MyBatisUtils.getSqlSession();
        StudentDao dao = sqlSession.getMapper(StudentDao.class);
        System.out.println("dao="+dao.getClass().getName());
        QueryParam queryParam = new QueryParam();
        queryParam.setParamAge(20);
        queryParam.setParamName("李四");
        List<Student> students = dao.selectMultiObject(queryParam);
        for(Student stu:students){
            System.out.println(stu);
        }
    }
```

### 使用位置传参

参数位置从 0 开始， 引用参数语法 #{argposition}。如#{arg0}表示第一个参数。

这种方法容易出错，所以不建议使用。

接口方法

```
    List<Student> selectMultiPosition(String name, Integer age);
```

mapper文件

```
    <select id="selectMultiPosition"  resultType="org.example.domain.Student">
        select id, name ,email, age from student where name=#{arg0} or age=#{arg1};
    </select>
```

测试

```
    @Test
    public void testSelectMultiPosition(){
        SqlSession sqlSession = MyBatisUtils.getSqlSession();
        StudentDao dao = sqlSession.getMapper(StudentDao.class);
        System.out.println("dao="+dao.getClass().getName());
        List<Student> students = dao.selectMultiPosition("李四",20);
        for(Student stu:students){
            System.out.println(stu);
        }
    }
```

### 使用Map传参

接口中方法定义看不出该方法有几个参数，每个参数的类型是什么。可读性很差。

接口方法

```
    List<Student> selectMultiMap(Map<String, Object> map);
```

mapper文件

```
    <select id="selectMultiMap"  resultType="org.example.domain.Student">
        select id, name ,email, age from student where name=#{myname} or age=#{myage};
    </select>
```

测试

```
    @Test
    public void testSelectMultiMap(){
        SqlSession sqlSession = MyBatisUtils.getSqlSession();
        StudentDao dao = sqlSession.getMapper(StudentDao.class);
        System.out.println("dao="+dao.getClass().getName());
        Map<String, Object> map = new HashMap<>();
        map.put("myname", "李四");
        map.put("myage", 20);
        List<Student> students = dao.selectMultiMap(map);
        for(Student stu:students){
            System.out.println(stu);
        }
    }
```

### #和$

**#**：占位符，告诉 mybatis 使用实际的参数值代替。使用 PrepareStatement 对象执行 sql 语句, #{...}代替sql 语句的“?”。这样做安全，避免sql注入。PrepareStatement 对象执行效率高。通常是首选做法。

**$** ：字符串替换和连接，告诉 mybatis 使用$包含的“字符串”替换所在位置。使用 Statement 把 sql 语句和${}的内容连接起来。$效率低，数据安全性低，有可能造成sql注入。

主要用在替换表名，列名，不同列排序等操作。

  \# 和 $的区别

```
	  1. #使用在sql语句中做占位的， 使用PreparedStatement执行sql，效率高
	  2. $不使用占位符，是字符串连接方式，使用Statement对象执行sql，效率低
	  3. $有sql注入的风险，缺乏安全性。#能够避免sql注入，更安全。
	  4. $:可以替换表名或者列名
```

接口方法

```
    Student selectStudentByName(@Param("studentName") String name);
```

mapper文件

```
    <select id="selectStudentByName" resultType="org.example.domain.Student">
        select id, name ,email, age from student where name=${studentName};
    </select>
```

测试

```
    @Test
    public void testSelectStudentByName(){
        SqlSession sqlSession = MyBatisUtils.getSqlSession();
        StudentDao dao = sqlSession.getMapper(StudentDao.class);
        Student stu = dao.selectStudentByName("'李四'");
        System.out.println(stu);
    }
```

### 替换列名

接口方法

```
    List<Student> selectStudentOrder(@Param("colName") String colName);
```

mapper文件

```
    <select id="selectStudentOrder" resultType="org.example.domain.Student">
        select id, name ,email, age from student order by ${colName};
    </select>
```

测试

```
    @Test
    public void testSelectStudentOrder(){
        SqlSession sqlSession = MyBatisUtils.getSqlSession();
        StudentDao dao = sqlSession.getMapper(StudentDao.class);
        String colName = "age";
        List<Student> students = dao.selectStudentOrder(colName);
        for(Student stu:students){
            System.out.println(stu);
        }
    }
```

### sql注入

```
        Student stu = dao.selectStudentByName("'李四' or true");
```

### 封装**MyBatis**输出结果

### resultType

执行sql得到的ResultSet转换成的类型，也可以看做是接口中方法的返回值。

注意如果返回的是集合，那应该设置为集合包含的类型，而不是集合本身。

如果是对象类型，一个对象表示一条记录。mybatis把ResultSet中的列值，赋值给java对象同名的属性。

使用类型的完全限定名或别名。建议使用全限定名称。

result的默认原则是同名的列赋值给同名的属性。

### resultType为map

列名为key，列值为value。

Map作为接口返回值，sql语句的查询结果最多只能有一条记录。大于一条记录就报错。

接口方法

```
    Map<Object,Object> selectReturnMap(int id);
```

mapper映射文件

```
    <select id="selectReturnMap"  resultType="java.util.HashMap">
        select id, name from student where id=#{StudentId};
    </select>
```

测试

```
   @Test
    public void selectReturnMap(){
        SqlSession sqlSession = MyBatisUtils.getSqlSession();
        StudentDao dao = sqlSession.getMapper(StudentDao.class);
        Map<Object, Object> returnMap = dao.selectReturnMap(1003);
        System.out.println(returnMap);
    }
```

### resultMap

resultMap 可以自定义 sql 的结果和 java 对象属性的映射关系。更灵活的把列值赋值给指定属性。 常用在列名和java对象属性名不一样的情况。

1.先在mapper文件中定义\<resultMap\>，指定列名和属性的对应关系。\<resultMap\>可以复用，每个查询都可以用使用。

 \<id\/result column="" property=""\/\>

主键字段使用\<id\>，非主键字段使用\<result\>

2.然后在\<select\>中把resultType的值设为自己定义的resultMap。

ex 把列name的值赋给Student对象的email，把列email的值赋给Student对象的name。


接口方法
```
    List<Student> selectAllResultMap();
```

mapper文件

```
    <resultMap id="StudentMap" type="org.example.domain.Student">
        <id column="id" property="id"/>
        <result column="name" property="email"/>
        <result column="email" property="name"/>
        <result column="age" property="age"/>
    </resultMap>

    <select id="selectAllResultMap"  resultMap="StudentMap">
        select id, name, email, age from student;
    </select>
```

测试

```
    @Test
    public void testSelectAllResultMap(){
        SqlSession sqlSession = MyBatisUtils.getSqlSession();
        StudentDao dao = sqlSession.getMapper(StudentDao.class);
        List<Student> students = dao.selectAllResultMap();
        for(Student stu:students){
            System.out.println(stu);
        }
    }
```

### 列名和属性名不同的两种解决方法

（1）使用列别名和\<resultType\>

select 列名 as 对象属性名

java实体类

```
public class PrimaryStudent {
	private Integer stuId;
	private String stuName;
	private Integer stuAge;
	// set , get方法
}
```

接口方法

```
    List<PrimaryStudent> selectAllPrimaryStudents();
```

mapper

```
    <select id="selectAllPrimaryStudents" resultType="org.example.domain.PrimaryStudent">
        select id as stuId, name as stuName,age as stuAge from student;
    </select>
```

测试

```
    @Test
    public void testselectAllPrimaryStudents(){
        SqlSession sqlSession = MyBatisUtils.getSqlSession();
        StudentDao dao = sqlSession.getMapper(StudentDao.class);
        List<PrimaryStudent> primaryStudents = dao.selectAllPrimaryStudents();
        for(PrimaryStudent priStu:primaryStudents){
            System.out.println(priStu);
        }
    }
```

（2）使用<resultMap>

见[resultMap](#resultMap )

### 自定义数据类型定义别名

不建议使用别名，有可能出现不同的类有相同的别名。

在mybatis的主配置文件中定义别名，使用标签<typeAliases>。有两种定义方法。

（1）\<typeAliase\>

可以指定一个类型一个自定义别名

type：自定义类型的全限定名称

alisas：别名

（2）\<package\>

name是包名，这个包中的所有类，类有名就是别名(不区分大小写)

```
    <typeAliases>
        <typeAlias type="org.example.domain.Student" alias="stu"/>
        <package name="org.example.domain"/>
    </typeAliases>
```

### 模糊like

模糊查询的实现有两种方式， 一是 java 代码中给查询数据加上“%” ; 二是在 mapper 文件 sql 语句的条件位置加上“%”。

(1) java 代码中给查询数据加上“%” 

接口方法

```
    List<Student> selectLikeJava(String studentName);
```

mapper文件

```
    <select id="selectLikeJava" resultType="Student">
        select id, name ,email, age from student where name like #{studentName};
    </select>
```

测试

```
    @Test
    public void selectLikeJava(){
        SqlSession sqlSession = MyBatisUtils.getSqlSession();
        StudentDao dao = sqlSession.getMapper(StudentDao.class);
        String studentName = "%四%";
        List<Student> students = dao.selectLikeJava(studentName);
        for(Student stu:students){
            System.out.println(stu);
        }
    }
```

（2）在 mapper 文件 sql 语句的条件位置加上“%”

接口方法

```
    List<Student> selectLikeMapper(String studentName);
```

mapper文件

```
    <select id="selectLikeMapper" resultType="Student">
        select id, name ,email, age from student where name like "%" #{studentName} "%";
    </select>
```

测试

```
    @Test
    public void selectLikeMapper(){
        SqlSession sqlSession = MyBatisUtils.getSqlSession();
        StudentDao dao = sqlSession.getMapper(StudentDao.class);
        String studentName = "四";
        List<Student> students = dao.selectLikeMapper(studentName);
        for(Student stu:students){
            System.out.println(stu);
        }
    }
```

