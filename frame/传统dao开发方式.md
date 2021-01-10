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
