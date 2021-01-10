这种开发方式，不同的方法有很多重复的操作，只是sql语句的唯一标识和调用的方法不同。

Dao的实现类其实并没有干什么实质性的工作，它仅仅就是通过SqlSession的相关API定位到sql映射文件中相应id的SQL语句，真正对DB进行操作的工作其实是由框架通过mapper中的SQL完成的。

Mapper动态代理方式无需程序员实现Dao接口。接口是由MyBatis结合映射文件自动生成的动态代理实现的。这种对Dao的实现方式称为Mapper的动态代理方式。

### 传统Dao开发方式

（1）定义实现类,实现Dao接口的方法。

```java
package org.example.dao;

import org.apache.ibatis.session.SqlSession;
import org.example.domain.Student;
import org.example.utils.MyBatisUtils;

import java.util.List;

public class StudentDaoImp implements StudentDao{

    @Override
    public List<Student> selectStudents() {
        SqlSession sqlSession = MyBatisUtils.getSqlSession();
        String sqlId = "org.example.dao.StudentDao.selectStudents";
        List<Student> students = sqlSession.selectList(sqlId);
        sqlSession.close();
        return students;

    }

    @Override
    public int insertStudent(Student student) {
        SqlSession sqlSession = MyBatisUtils.getSqlSession();
        String sqlId = "org.example.dao.StudentDao.insertStudent";

        int nums = sqlSession.insert(sqlId, student);
        sqlSession.commit();
        sqlSession.close();
        return nums;


    }

    @Override
    public int updateStudent(Student student) {
        SqlSession sqlSession = MyBatisUtils.getSqlSession();
        String sqlId = "org.example.dao.StudentDao.updateStudent";
        int nums = sqlSession.update(sqlId, student);
        sqlSession.commit();
        sqlSession.close();
        return nums;
    }

    @Override
    public int deleteStudent(int id) {
        SqlSession sqlSession = MyBatisUtils.getSqlSession();
        String sqlId = "org.example.dao.StudentDao.deleteStudent";
        int nums = sqlSession.delete(sqlId, id);
        sqlSession.commit();
        sqlSession.close();
        return nums;
    }
}
```

（2）创建实现类的实例，调用其方法实现crud。
```java
package org.example;
import org.example.dao.StudentDaoImp;
import org.example.domain.Student;
import org.junit.Test;

import java.util.List;

public class MyTest {

    @Test
    public void testSelect(){
        StudentDaoImp studentDaoImp = new StudentDaoImp();
        List<Student> students = studentDaoImp.selectStudents();
        for(Student stu:students){
            System.out.println(stu);
        }
    }

    @Test
    public void testInsert(){
        StudentDaoImp studentDaoImp = new StudentDaoImp();
        Student student = new Student();
        student.setId(1007);
        student.setName("徐彬");
        student.setAge(24);
        student.setEmail("xubin@163.com");
        int num = studentDaoImp.insertStudent(student);
        System.out.println(num);
    }

    @Test
    public void testUpdate(){
        StudentDaoImp studentDaoImp = new StudentDaoImp();
        Student student = new Student();
        student.setId(1003);
        student.setAge(32);
        int num = studentDaoImp.updateStudent(student);
        System.out.println(num);
    }

    @Test
    public void testDelete(){
        StudentDaoImp studentDaoImp = new StudentDaoImp();
        Student student = new Student();
        int id = 1007;
        int num = studentDaoImp.deleteStudent(id);
        System.out.println(num);
    }
}

```
