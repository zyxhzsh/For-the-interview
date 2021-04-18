1.什么是Spring框架

2.列举一些重要的Spring模块

3.@RestController vs @Controller

4.Spring IOC

5.spring的第一个程序

6.基于xml的DI

7.基于注解的DI

8.注解与XML的对比

9.Spring AOP

10.AspectJ

11.Spring bean

12.Spring MVC

13.Spring框架中用到了哪些设计模式?

14.Spring事务

15.JPA

16.spring是单例还是多例，如何设置为多例

1.什么是Spring框架

Spring是一种轻量级开发框架，旨在提高开发人员的开发效率以及系统的可维护性。

我们一般说 Spring 框架指的都是 Spring Framework，它是很多模块的集合，使用这些模块可以很方便地协助我们进行开发。这些模块是:核心容器、数据访问/集成,、Web、AOP(面向切面 编程)、工具、消息和测试模块。比如:Core Container 中的 Core 组件是Spring 所有组件的核心，Beans组件和Context组件是实现IOC和依赖注入的基础，AOP组件用来实现面向切面编程。

Spring 官网列出的 Spring 的 6 个特征:

核心技术 :依赖注入(DI)，AOP，事件(events)，资源，i18n，验证，数据绑定，类型转 换，SpEL。

测试 :模拟对象，TestContext框架，Spring MVC 测试，WebTestClient。

数据访问 :事务，DAO支持，JDBC，ORM，编组XML。

Web支持 : Spring MVC和Spring WebFlux Web框架。

集成 :远程处理，JMS，JCA，JMX，电子邮件，任务，调度，缓存。 

语言 :Kotlin，Groovy，动态语言。

2.列举一些重要的Spring模块
```
Spring Core: 基础,可以说 Spring 其他所有的功能都需要依赖于该类库。主要提供 IoC 依 赖注入功能。
Spring Aspects : 该模块为与AspectJ的集成提供支持。
Spring AOP :提供了面向切面的编程实现。
Spring JDBC : Java数据库连接。
Spring JMS :Java消息服务。
Spring ORM : 用于支持Hibernate等ORM工具。 Spring Web : 为创建Web应用程序提供支持。
Spring Test : 提供了对 JUnit 和 TestNG 测试的支持。
```

3.@RestController vs @Controller

（1）Controller返回一个⻚面。单独使用@Controller不加@ResponseBody的话一般使用在要返回一个视图的情况，这种情况属于比􏰀传统的Spring MVC的应用，对应于前后端不分离的情况。

（2）@RestController返回JSON或XML形式数据。@RestController只返回对象，对象数据直接以 JSON 或 XML 形式写入 HTTP 响应 (Response)中，这种情况属于 RESTful Web服务，这也是目前日常开发所接触的最常用的情况 (前后端分离)。

（3）如果你需要在Spring4之前开发RESTful Web服务的话，你需要使用@Controller并结合@ResponseBody注解，也就是说 @Controller + @ResponseBody = @RestController(Spring 4 之后新加的注解)。

@ResponseBody 注解的作用是将 Controller 的方法返回的对象通过适当的转换器转换为指定的格式之后，写入到HTTP响应(Response)对象的body中，通常用来返回JSON或者XML数据，返回JSON数据的情况比􏰀多。

4.Spring IOC

控制反转是一种思想，指将传统上由程序代码直接操控的对象调用权交给容器，通过容器来实现对象的装配和管理。控制反转就是对对 象控制权的转移，从程序代码本身反转到了外部容器。通过容器实现对象的创建，属性赋值，依赖的管理。

IoC容器是Spring用来实现IoC的载体，IoC容器实际上就是个Map(key，value),Map中存放的是各种对象。

依赖:classA 类中含有 classB 的实例，在 classA 中调用 classB 的方法完成功能，即 classA 对 classB 有依赖。

依赖注入(Dependency Injection)：程序代码不做定位查询，这些工作由容器自行完成。程序运行过程中，若需要调用另一个对象协助时，无须在代码中创建
被调用者，而是依赖于外部容器，由外部容器创建后传递给程序。

Spring框架使用依赖注入(DI)实现IoC。Spring容器负责创建、管理所有的Java对象，这些Java对象被称为Bean。Spring 使用“依赖注入”的方式 来管理 Bean 之间的依赖关系。使用 IoC 实现对象之间的解耦和。

5.spring的第一个程序

（1）创建maven项目，引入maven依赖pom.xml
```
<dependency> 
    <groupId>org.springframework</groupId> 
    <artifactId>spring-context</artifactId> 
    <version>5.2.5.RELEASE</version>
</dependency>

<build>
    <plugins>
        <plugin> 
            <artifactId>maven-compiler-plugin</artifactId> 
            <version>3.1</version>
            <configuration>
                <source>1.8</source>
                <target>1.8</target> 
            </configuration>
      </plugin>
    </plugins>
</build>
```
（2）定义接口与实体类(如果使用spring创建非自定义类对象，就不需要这步)
```
package com.bjpowernode.service;

public interface SomeService {
    void doSome();
}
——————————————————————————————————————————————————————————————————————
package com.bjpowernode.service;

public class SomeServiceImpl implements SomeService {

    /**
     * spring默认调用无参数构造方法创建对象。
     * 如果没有无参数构造方法，报错：No default constructor found
     */
    public SomeServiceImpl() {
        System.out.println("SomeSeviceImpl的无参数构造方法");
    }

    @Override
    public void doSome() {
        System.out.println("====SomeServiceImpl业务方法doSome=====");
    }
}
```

（3）创建Spring配置文件，在src/main/resources/目录下，<建议名称为applicationContext class="xml"></建议名称为applicationContext>。spring配置中需要加入约束文件才能正常使用，约束文件是xsd扩展名。
```
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
       http://www.springframework.org/schema/beans/spring-beans.xsd">

       <bean id="service" class="com.bjpowernode.service.SomeServiceImpl" scope="singleton" /> 
</beans>
```

\<bean \/\>:声明java对象交给Spring创建和管理，一个实例对应一个bean元素。

id: 自定义的对象名称，该属性是Bean实例的唯一标识，程序通过id属性访问Bean，Bean与Bean间的依赖关系也是通过id属性关联的。

class: 类的全限定名称，不能是接口。

scope：指定bean对象的作用域。singleton,prototype,request,session

singleton：默认值，表示叫这个名称的对象在spring容器中只有一个；prototype,表示每次使用getBean()都创建一个新的对象。对于singleton ，Bean是在容器被创建时即被装配好了；对于prototype，Bean实例是在代码中使用该Bean实例时才进行装配的。

request：对于每次 HTTP 请求，都将会产生一个不同的 Bean 实例。session：对于每个不同的 HTTP session，都将产生一个不同的 Bean 实例。对于 scope 的值 request、session 只有在 Web 应用中使用 Spring 时，该作用域才有效。

（4）定义测试类

ApplicationContext 用于加载 Spring 的配置文件，在程序中充当“容器”的角色。其实现类有两个:ClassPathXmlApplicationContext和FileSystemXmlApplicationContext

ApplicationContext 容器，会在容器对象初始化时，将其中的所有对象一次性全部装配好。以后代码中若要使用到这些对象，只需从内存中直接获取即可。执行效率较高。但占用内存。

```
    @Test
    public void test01(){
    
        //指定Spring配置文件的位置和名称
        String config = "applicationContext.xml";

        //创建Spring的容器对象，根据Spring配置文件的位置，使用接口的不同实现类
        //1.如果Spring的配置文件是在类路径(classpath)，使用ClassPathXmlApplicationContext
        //2.如果Spring的配置文件是放在项目的根之下(与src、target同级目录)，使用FileSystemXmlApplicationContext
        //创建Spring容器，会读取配置文件中的bean标签，并执行对应类的无参构造器，创建对应的对象。
        ApplicationContext ctx = new ClassPathXmlApplicationContext(config);

        //从容器中获取对象，使用getBean("<bean>的id")方法
        SomeService service = (SomeService) ctx.getBean("someService");

        //调用对象的业务方法
        service.doSome();
    }

```

上面的例子只是创建了对象，没有给对象的属性赋值，赋值的方法有基于XML的DI和基于注解的DI两种。

bean实例在调用无参构造器创建对象后，就要对bean对象的属性进行初始化。初始化是由容器自动完成的，称为注入。

6.基于xml的DI

根据注入方式的不同，常用的有两类: set注入、构造注入。

（1）set注入也叫设值注入，通过setter方法传入被调用者的实例。

简单类型的属性赋值：在<bean/>标签下添加<property name="属性名" value="属性值" />

每一个property标签，完成一个属性的赋值。Spring执行property标签的原理，是执行name属性值对应的set方法。而并不关心set方法的具体实现和属性是否真的存在。
```xml
<property name="name" value="张三" />
```
引用类型的属性赋值：当指定bean的某属性值为另一bean的实例时，通过ref指定它们间的引用关系。ref的值必须为某bean的id值。
```java
package com.bjpowernode;

//创建一个学校类
public class School {
    private  String name;
    private  String address;

    public void setName(String name) {
        this.name = name;
    }

    public void setAddress(String address) {
        this.address = address;
    }

    @Override
    public String toString() {
        return "School{" +
                "name='" + name + '\'' +
                ", address='" + address + '\'' +
                '}';
    }
}

——————————————————————————————————————————————————————————————————————
package com.bjpowernode;

//创建一个学生类
public class Student {

    private String name;
    private int age;

    //引用类型
    private  School school;

    public Student() {
        System.out.println("Student的无参数构造方法");
    }

    public void setName(String name) {
        this.name = name.toUpperCase();
    }

    public void setAge(int age) {
        this.age = age;
    }

    public void setSchool(School school) {
        System.out.println("setSchool:"+school);
        this.school = school;
    }

    @Override
    public String toString() {
        return "Student{" +
                "name='" + name + '\'' +
                ", age=" + age +
                ", school=" + school +
                '}';
    }
}

```
配置文件
```
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
       http://www.springframework.org/schema/beans/spring-beans.xsd">

    <!--ref作为属性-->
    <bean id="myStudent" class="com.bjpowernode.Student" >
        <property name="name" value="李四" />
        <property name="age" value="20" />
        <property name="school" ref="mySchool" /> <!--setSchool(mySchool) -->
    </bean>

    <!--ref作为子标签-->
    <bean id="myStudent2" class="com.bjpowernode.Student">
        <property name="name" value="张三" />
        <property name="age" value="22" />
        <property name="school">
            <ref bean="mySchool"/>
        </property>
    </bean>

    <!--声明School-->
    <bean id="mySchool" class="com.bjpowernode.School">
        <property name="name" value="北京大学" />
        <property name="address" value="北京的海淀区" />
    </bean>
</beans>

```
（2）构造注入

执行类的有参构造，在构造对象的同时给属性赋值。

给Student类添加有参构造器
```
//定义有参数构造方法
    public  Student(String myname, int myage, School mySchool){
        System.out.println("Student有参数构造方法");
        //给属性完成赋值
        this.name = myname;
        this.age  = myage;
        this.school = mySchool;
    }
```
对xml配置文件进行相关配置
```
<!--使用name属性-->
    <bean id="myStudent" class="com.bjpowernode.Student" >
        <constructor-arg name="myage" value="22"/>
        <constructor-arg name="myname" value="张三" />
        <constructor-arg name="mySchool" ref="myXueXiao" />
    </bean>
    
<!--声明School-->
    <bean id="myXueXiao" class="com.bjpowernode.School">
      <property name="name" value="北京大学" />
      <property name="address" value="北京的海淀区" />
    </bean>
————————————————
```
<constructor-arg />标签中用于指定参数的属性有：

➢ name：指定参数名称,指的是构造方法中的形参。

➢ index：指明该参数对应着构造器的第几个参数，从0开始，可以省略。但要注意，若参数类型相同，或之间有包含关系，则需要保证赋值顺序要与构造器中的参数顺序一致。
```
 <!--使用index属性-->
    <bean id="myStudent2" class="com.bjpowernode.ba03.Student">
        <constructor-arg index="1" value="28" />
        <constructor-arg index="0" value="李四" />
        <constructor-arg index="2" ref="myXueXiao"/>
    </bean>

    <!--省略index-->
    <bean id="myStudent3" class="com.bjpowernode.ba03.Student">
        <constructor-arg value="周仓" />
        <constructor-arg value="24" />
        <constructor-arg ref="myXueXiao"/>
    </bean>

```

（3）引用类型的自动注入

对于引用类型属性的注入，也可不在配置文件中显示的注入。可以通过为<bean/>标签 设置 autowire属性值，为引用类型属性进行隐式自动注入。根据自动注入判断标准的不同，可以分为两种:byName:根据名称自动注入；byType: 根据类型自动注入。

byName：当配置文件中被调用者 bean 的 id 值与代码中调用者 bean 类的属性名相同时，可使用 byName 方式，让容器自动将被调用者 bean 注入给调用者 bean。容器是通过调用者的 bean 类的属性名与配置文件的被调用者 bean 的 id 进行比较而实现自动注入的。

byType：配置文件中被调用者 bean 的 class 属性指定的类， 要与代码中调用者 bean 类的某引用类型属性类型同源。即要么相同，要么有 is-a 关系(子 类，或是实现类)。但这样的同源的被调用 bean 只能有一个。多于一个，容器就不知该匹配 哪一个了。
```
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
       http://www.springframework.org/schema/beans/spring-beans.xsd">

    <!--byName自动注入，添加属性autowire="byName"-->
    <bean id="myStudent" class="com.bjpowernode.Student" autowire="byName">
        <property name="name" value="李四" />
        <property name="age" value="22"/>
        <!--引用类型-->
        <!--<property name="school" ref="myXueXiao" />-->
    </bean>
    
    <!--声明School-->
    <bean id="school" class="com.bjpowernode.School">
      <property name="name" value="清华大学" />
      <property name="address" value="北京的海淀区" />
    </bean>
    
</beans>
```
```
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
       http://www.springframework.org/schema/beans/spring-beans.xsd">

    <!--
        2.byType:按类型注入，java类中引用类型的数据类型和spring容器（xml配置文件）中<bean>的
                 class属性值是同源关系的，这样的bean能够赋值给引用类型
          同源关系：
           1.java类中引用类型的数据类型和<bean>的class值是一样的。
           2.java类中引用类型的数据类型和<bean>的class值是父子类关系的
           3.java类中引用类型的数据类型和<bean>的class值是接口和实现类关系

          指定byType
          <bean id="xx" class="yyy" autowire="byType">
             简单类型的赋值
          </bean>

          注意：在xml配置文件使用byType，符合条件的对象只能有一个，多余一个是报错的。
     -->

    <!--byType自动注入-->
    <bean id="myStudent" class="com.bjpowernode.Student" autowire="byType">
        <property name="name" value="周武" />
        <property name="age" value="22"/>
        <!--引用类型-->
        <!--<property name="school" ref="myXueXiao" />-->
    </bean>


    <!--符合条件的对象只能有一个-->
    <!--声明School-->
    <bean id="mySchool" class="com.bjpowernode.School">
      <property name="name" value="清华大学" />
      <property name="address" value="北京的海淀区" />
    </bean>

    <!--声明School的子类对象-->
    <!--<bean id="xiaoXueSchool" class="com.bjpowernode.ba05.XiaoXueSchool">
        <property name="name" value="中兴小学" />
        <property name="address" value="北京的大兴区"/>
    </bean>-->
</beans>

```
（4）为应用指定多个Spring配置文件

以将 Spring 配置文件分解成多个配置文件。多个配置文件中有一个总文件，总配置文件将各其它子文件通过<import/>引入。在Java代码中只需要使用总配置文件对容器进行初始化即可。

也可使用通配符\*。但，此时要求父配置文件名不能满足\*所能匹配的格式，否则将出现 循环递归包含。
```
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
      xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
      xsi:schemaLocation="http://www.springframework.org/schema/beans
      http://www.springframework.org/schema/beans/spring-beans.xsd">

  <!--
      关键字 "classpath:" , 表示类路径，告诉spring到类路径中查找配置文件
  -->

   <!--包含其他的配置文件-->
   <!--
   <import resource="classpath:ba06/spring-school.xml"/>
   <import resource="classpath:ba06/spring-student.xml"/>
   -->
   <!--使用通配符，指定多个配置文件。
       通配符“*”，表示0或多个字符
       注意：总的文件名称（total.xml）,不能包含在通配符的范围内，不能叫做spring-total.xml
   -->
   <import resource="classpath:ba06/spring-*.xml"/>

</beans>

```

7.基于注解的DI

对于 DI 使用注解，将不再需要在 Spring 配置文件中声明 bean 实例。Spring 中使用注解， 需要在原有 Spring 运行环境基础上再做一些改变。

需要在 Spring 配置文件中配置组件扫描器，用于在指定的基本包中扫描注解。不建议使用顶级的父包，扫描的路径比较多，导致容器启动时间变慢。
```
    <!--声明组件扫描器(context:component)，指定注解所在的包名，让框架找到注解-->
    <!--
       base-package:指定注解在项目中的包名，框架会扫描这个包和子包中所有类的注解，
                    按照注解的功能，创建对象，赋值属性。
    -->
    <context:component-scan base-package="com.bjpowernode.ba01" />

    <!--指定扫描多个包-->
    <!--第一种，使用多次扫描器，分别指定不同的包-->
    <context:component-scan base-package="com.bjpowernode.ba01"/>
    <context:component-scan base-package="com.bjpowernode.ba02"/>

    <!--第二种，使用分隔符（,或者;）指定多个包-->
    <context:component-scan base-package="com.bjpowernode.ba01;com.bjpowernode.ba02"/>

    <!--第三种，指定父包-->
    <context:component-scan base-package="com.bjpowernode" />
```

（1）定义Bean的注解@Component

需要在类上使用注解@Component，该注解的 value 属性用于指定该 bean 的 id 值。若@Component 不指定 value 属性，bean 的 id 是类名的首字母小写。

例如：
```
@Component(value = "myStudent")等价于<bean id="myStudent" class="com.bjpowernode.ba01.Student"/>
```
Spring还提供了3个创建对象的注解:
```
➢ @Repository 用于对DAO实现类进行注解
➢ @Service 用于对Service实现类进行注解
➢ @Controller 用于对Controller实现类进行注解
```
这三个注解与@Component 都可以创建对象，但这三个注解还有其他的含义，@Service 创建业务层对象，业务层对象可以加入事务功能，@Controller 注解创建的对象可以作为处 理器接收用户的请求。

@Repository，@Service，@Controller 是对@Component 注解的细化，标注不同层的对象。即持久层对象，业务层对象，控制层对象。

（2）简单类型属性注入@Value

在属性上使用注解@Value，指定要注入的值。使用该注解完成属性注入时，类中无需setter。当然，若属性有setter，则也可将其加到setter上。
```
package com.bjpowernode.ba02;

import org.springframework.beans.factory.annotation.Value;
import org.springframework.stereotype.Component;

@Component("myStudent")
public class Student {

    /**
     * @Value: 简单类型的属性赋值
     *   属性： value 是String类型的， 表示简单类型的属性值
     *   位置：
     *       1.在属性定义的上面， 无需set方法，常用的方式
     *       2.在set方法上面
     */
    @Value("张三01")
    private String name;


    private int age;

    public void setName(String name) {
        this.name = name;
    }

    @Value("28")
    public void setAge(int age) {
        System.out.println("setAge:"+age);
        this.age = age;
    }

    @Override
    public String toString() {
        return "Student{" +
                "name='" + name + '\'' +
                ", age=" + age +
                '}';
    }
}
```

（3）byType自动注入@Autowired

需要在引用属性上使用注解@Autowired，该注解默认使用按类型自动装配 Bean 的方式。

使用该注解完成属性注入时，类中无需 setter。当然，若属性有 setter，则也可将其加 到 setter 上。
```
package com.bjpowernode.ba03;

import org.springframework.beans.factory.annotation.Value;
import org.springframework.stereotype.Component;

@Component("mySchool")
public class School {
   @Value("人民大学")
   private  String name;
   @Value("北京的海淀")
   private  String address;
——————————————————————————————————
package com.bjpowernode.ba03;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.beans.factory.annotation.Value;
import org.springframework.stereotype.Component;

@Component("myStudent")
public class Student {

    @Value("张三")
    private String name;

    @Value("23")
    private int age;


    /**
     * 引用类型
     * @Autowired:引用类型的自动注入， 支持byName, byType.默认是byType
     *       位置:
     *         1.属性定义的上面，无需set方法，常用方式
     *         2.在set方法的上面
     */
    //byType
    @Autowired
    private School school;
    
    @Override
    public String toString() {
        return "Student{" +
                "name='" + name + '\'' +
                ", age=" + age +
                ", school=" + school +
                '}';
    }
}
```
（4）byName自动注入@Autowired与@Qualifier

需要在引用属性上联合使用注解@Autowired 与@Qualifier。@Qualifier 的 value 属性用 于指定要匹配的 Bean 的 id 值。类中无需 set 方法，也可加到 set 方法上。

@Autowired 还有一个属性 required，默认值为 true，表示当匹配失败后，会终止程序运行。若将其值设置为false，则匹配失败，将被忽略，未匹配的属性值为 null。
```
    @Autowired
    @Qualifier("mySchool")//与School类的@component注解value值相同
    private School school;
```
8.注解与XML的对比

注解优点是：方便、直观、高效。弊端是：以硬编码的方式写入到Java代码中，修改是需要重新编译代码的。

XML的优点是：配置和代码是分离的，在xml中做修改，无需编译代码，只需重启服务器即可将新的配置加载。缺点是：编写麻烦，效率低，大型项目过于复杂。

9.Spring AOP

面向切面编程：将交叉业务逻辑封装起来，便于减少系统的重复代码，降低模块间的耦合度，并有利于未来的可拓展性和可维护性。若不使用 AOP，则会出现代码纠缠，即交叉业务逻辑与主业务逻辑混合在一起。这样，会使主业务逻辑变的混杂不清。

例如，转账，在真正转账业务逻辑前后，需要权限控制、日志记录、加载事务、结束事 务等交叉业务逻辑，而这些业务逻辑与主业务逻辑间并无直接关系。但，它们的代码量所占 比重能达到总代码量的一半甚至还多。它们的存在，不仅产生了大量的“冗余”代码，还大 大干扰了主业务逻辑---转账。

交叉业务逻辑：通用的、与主业务逻辑无关的代码，如安全检查、事务、日志、缓存等。

AOP底层，就是采用动态代理模式实现的。采用了两种代理:JDK的动态代理，与CGLIB的动态代理。

（1）面向切面编程对有什么好处

减少重复代码，专注业务实现。

使用AOP之后我们可以把一些通用功能抽象出来，在需要用到的地方直接使用即可，这样大大简化了代码量。我们需要增加新功能时也方便，这样也提高了系统扩展性。日志功能、事务管理等等场景都用到了AOP 。

（2）AOP编程术语

切面(Aspect)：切面泛指交叉业务逻辑，实际就是对主业务逻辑的一种增强。

连接点(JoinPoint)：连接点指可以被切面织入的具体方法。通常业务接口中的方法均为连接点。

切入点(Pointcut)：切入点指声明的一个或多个连接点的集合。通过切入点指定一组方法。被标记为final的方法是不能作为连接点与切入点的。因为最终的是不能被修改的，不 能被增强的。

目标对象(Target)：目标对象指将要被增强的对象。即包含主业务逻辑的类的对象。

通知(Advice)：通知表示切面的执行时间，Advice也叫增强。换个角度来说，通知定义了增强代码切入到目标代码的时间点，是目标方 法执行之前执行，还是之后执行等。通知类型不同，切入时间不同。

切入点定义切入的位置，通知定义切入的时间。

（3）Spring AOP和AspectJ AOP有什么区别?

Spring AOP属于运行时增强，而AspectJ是编译时增强。Spring AOP基于代理(Proxying)，而AspectJ基于字节码操作(Bytecode Manipulation)。

如果我们的切面比􏰀少，那么两者性能差异不大。但是，当切面太多的话，最好选择AspectJ，它比Spring AOP快很多。Spring AOP 已经集成了AspectJ。

Spring AOP就是基于动态代理的，如果要代理的对象，实现了某个接口，那么Spring AOP会使用JDK Proxy，去创建代理对象，而对于没有实现接口的对象，就无法使用 JDK Proxy 去进行代理了，这时候Spring AOP会使用Cglib ，这时候Spring AOP会使用Cglib生成一个被代理对象的子类来作为代理。

10.AspectJ

AspectJ实现方式更简捷，使用更方便，而且还支持注解式开发。在Spring中使用AOP开发时，一般使用AspectJ的实现方式。

（1）AspectJ的通知类型

前置通知、后置通知、环绕通知、异常通知、最终通知

（2）AspectJ的切入点表达式

execution(modifiers-pattern? ret-type-pattern
declaring-type-pattern?name-pattern(param-pattern)
throws-pattern?)

execution(访问权限类型 返回值类型 全限定性类名 方法名(参数列表) 异常类型)

切入点表达式要匹配的对象就是目标方法的方法名。所以，execution 表达式中明显就是方法的签名。在其中可以使用以下符号：
```
*：0至多个任意字符
..： 用在方法参数中，表示任意多个参数；用在包名后，表示当前包及其子包
+：用在类名后，表示当前类及其子类；用在接口名后，表示当前接口及其实现类
```
```
modifiers-pattern] 访问权限类型
ret-type-pattern 返回值类型
declaring-type-pattern 包名类名 name-pattern(param-pattern) 方法名(参数类型和参数个数) throws-pattern 抛出异常类型
?表示可选的部分
```
（3）AspectJ基于注解的AOP实现

定义业务接口与实现类；

定义切面类；@Aspect。切面类中的方法要加上通知类型注解，其属性value是一个切入点表达式。通知方法可以包含一个JoinPoint类型参数，该类型的对象本身就是切入点表达式。

声明目标对象切面类对象；

注册 AspectJ 的自动代理；在定义好切面Aspect后，需要通知 Spring 容器，让容器生成“目标类+ 切面”的代理 对象。这个代理是由容器自动生成的。只需要在Spring 配置文件中注册一个基于 aspectj 的自动代理生成器，其就会自动扫描到@Aspect注解，并按通知类型与切入点，将其织入，并生成代理。

测试类中使用目标对象的id，从spring容器中获取目标对象，该对象就是经aspectj修改后的代理对象。

（4）@Before前置通知

在目标方法执行之前执行。

（5）@AfterReturning后置通知

在目标方法执行之后执行，所以可以获取到目标方法的返回值。该注解的returning属性就是用于指定接收方法返回值的变量名。该变量最好为Object类型，因为目标方法的返回值可能是任何类型。

（6）@Around环绕通知

在目标方法执行之前之后执行。被注解为环绕增强的方法要有返回值，Object 类型。并 且方法可以包含一个 ProceedingJoinPoint 类型的参数。接口 ProceedingJoinPoint 其有一个 proceed()方法，用于执行目标方法。若目标方法有返回值，则该方法的返回值就是目标方法 的返回值。最后，环绕增强方法将其返回值返回。该增强方法实际是拦截了目标方法的执行。

（7）@AfterThrowing异常通知

在目标方法抛出异常后执行。该注解的 throwing 属性用于指定所发生的异常类对象。 当然，被注解为异常通知的方法可以包含一个参数 Throwable，参数名称为 throwing 指定的 名称，表示发生的异常对象。

（8）@After 最终通知

无论目标方法是否抛出异常，该增强均会被执行。

（9）@Pointcut 定义切入点

当较多的通知增强方法使用相同的 execution 切入点表达式时，编写、维护均较为麻烦。 AspectJ 提供了@Pointcut 注解，用于定义 execution 切入点表达式。

其用法是，将@Pointcut 注解在一个方法之上，以后所有的execution的value属性值均可使用该方法名作为切入点。代表的就是@Pointcut定义的切入点。这个使用@Pointcut 注解 的方法一般使用 private 的标识方法，即没有实际作用的方法。

11.Spring bean

（1）Spring 中的 bean 的作用域有哪些

singleton : 唯一 bean 实例，Spring 中的 bean 默认都是单例的。

prototype : 每次请求都会创建一个新的 bean 实例。

request : 每一次HTTP请求都会产生一个新的bean，该bean仅在当前HTTP request内有效。

session : 对于每个不同的 HTTP session，都将产生一个不同的 Bean 实例。该bean仅在当前 HTTP session 内有 效。

global-session: 全局session作用域，仅仅在基于portlet的web应用中才有意义，Spring5已 经没有了。

（2）Spring 中的单例 bean 的线程安全问题了解吗

当多个线程操作同一个对象的时候，对这个对象的非静态成员变量的写操作 会存在线程安全问题。

常⻅的有两种解决办法:1. 在Bean对象中尽量避免定义可变的成员变量(不太现实)。2.在类中定义一个ThreadLocal成员变量，将需要的可变成员变量保存在ThreadLocal中(推
荐的一种方式)。

（3）@Component和@Bean的区别是什么?

作用对象不同: @Component注解作用于类，而@Bean注解作用于方法。

@Component 通常是通过类路径扫描来自动侦测以及自动装配到Spring容器中(我们可以使
用 @ComponentScan 注解定义要扫描的路径从中找出标识了需要装配的类自动装配到 Spring 的 bean 容器中)。@Bean注解通常是我们在标有该注解的方法中定义产生这个bean,@Bean告诉了Spring这是某个类的示例，当我需要用它的时候还给我。

@Bean 注解比 Component 注解的自定义性更强，而且很多地方我们只能通过@Bean注解来注册bean。比如当我们引用第三方库中的类需要装配到Spring容器时，则只能通过@Bean 来实现。

@Bean注解使用示例:
```
 
@Configuration
public class AppConfig {
    @Bean
    public TransferService transferService() {
        return new TransferServiceImpl();
}
——————————————————————————————————————————————————
上面的代码相当于下面的 xml 配置
 
<beans>
<bean id="transferService" class="com.acme.TransferServiceImpl"/>
</beans>

下面这个例子是通过 @Component 无法实现的。

@Bean
public OneService getService(status) {
    case (status)  {
        when 1:
            return new serviceImpl1();
        when 2:
            return new serviceImpl2();
        when 3:
            return new serviceImpl3();
    } 
}
```
（4）将一个类声明为Spring的 bean 的注解有哪些?

我们一般使用 @Autowired 注解自动装配 bean，要想把类标识成可用于 @Autowired 注解自动装配的 bean 的类,采用以下注解可实现:
```
@Component :通用的注解，可标注任意类为 Spring 组件。如果一个Bean不知道属于哪 个层，可以使用 @Component 注解标注。
@Repository : 对应持久层即 Dao 层，主要用于数据库相关操作。
@Service : 对应服务层，主要涉及一些复杂的逻辑，需要用到 Dao层。
@Controller : 对应 Spring MVC 控制层，主要用户接受用户请求并调用 Service 层返回数据给前端⻚面。
```
（5）Spring 中的 bean 生命周期

Bean 容器找到配置文件中 Spring Bean 的定义。

Bean 容器利用 Java Reflection API 创建一个Bean的实例。

如果涉及到一些属性值 利用 set() 方法设置一些属性值。

如果 Bean 实现了 BeanNameAware 接口，调用 setBeanName() 方法，传入Bean的名字。 如果 Bean 实现了 BeanClassLoaderAware 接口，调用 setBeanClassLoader() 方法，传入ClassLoader 对象的实例。

与上面的类似，如果实现了其他\*.Aware 接口，就调用相应的方法。 

如果有和加载这个Bean的Spring容器相关的BeanPostProcessor对象，执行postProcessBeforeInitialization()方法

如果Bean实现了InitializingBean接口，执行afterPropertiesSet()方法。

如果Bean在配置文件中的定义包含init-method属性，执行指定的方法。

如果有和加载这个 Bean的 Spring 容器相关的 BeanPostProcessor 对象，执行 postProcessAfterInitialization() 方法

当要销毁 Bean 的时候，如果 Bean 实现了 DisposableBean 接口，执行 destroy() 方法。 

当要销毁 Bean 的时候，如果 Bean 在配置文件中的定义包含 destroy-method 属性，执行指定的方法。

12.Spring MVC

Model1模式：整个 Web 应用几乎全部用 JSP ⻚面组成，只用少 量的 JavaBean 来处理数据库连接、访问等操作。这个模式下 JSP 即是控制层又是表示层。显而易⻅，这种模式存在很多问题。比如1将控制逻辑和表现逻辑混杂在一起，导致代 码重用率极低;2前端和后端相互依赖，难以进行测试并且开发效率极低; 

Model2模式：Java Bean(Model)+ JSP(View,)+Servlet(Controller)。Model:系统涉及的数据，也就是 dao 和 bean。View:展示模型中的数据，只是用来展 示。Controller:处理用户请求都发送给它，返回数据给JSP并展示给用户。

Model2的抽象和封装程度还远远不够，使用Model2进行开发时 不可避免地会重复造轮子，这就大大降低了程序的可维护性和复用性。于是很多JavaWeb开发相 关的 MVC 框架应运而生比如Struts2，但是 Struts2 比􏰀笨重。随着 Spring 轻量级开发框架的流 行，Spring 生态圈出现了 Spring MVC 框架， Spring MVC 是当前最优秀的 MVC 框架。相比于 Struts2 ， Spring MVC 使用更加简单和方便，开发效率更高，并且 Spring MVC 运行速度更快。

MVC 是一种设计模式,Spring MVC 是一款很优秀的 MVC 框架。Spring MVC 可以帮助我们进行 更简洁的Web层的开发，并且它天生与 Spring 框架集成。Spring MVC 下我们一般把后端项目分为Service层(处理业务)、Dao层(数据库操作)、Entity层(实体类)、Controller层(控制层，返回数据给前台⻚面)。

流程说明：
```
1.客户端(浏览器)发送请求给Spring前端控制器DispatcherServlet。
2.DispatcherServlet根据请求信息调用HandlerMapping ，获取请求对应的Handler对象及相关的对象，最后以HandlerExecutionChain对象的形式返回。
3.解析到对应的Handler(也就是我们平常说的 Controller 控制器)后，由DispatcherServlet选择一个合适的HandlerAdapter(处理器适配器)。
4.HandlerAdapter会根据Handler来调用真正的处理器开始处理请求，并处理相应的业务逻辑。
5.处理器处理完业务后，会返回一个ModelAndView 对象， Model是返回的数据对象， View是个逻辑上的 View 。
6.ViewResolver 会根据逻辑 View 查找实际的 View 。
7.DispaterServlet 把返回的 Model 传给 View (视图渲染)。 
8.把 View 返回给请求者(浏览器)
```
13.Spring框架中用到了哪些设计模式?

工厂设计模式 : Spring使用工厂模式通过 BeanFactory、ApplicationContext创建bean对象。

代理设计模式 : Spring AOP功能的实现。

包装器设计模式 : 我们的项目需要连接多个数据库，而且不同的客户在每次访问中根据需要 会去访问不同的数据库。这种模式让我们可以根据客户的需求能够动态切换不同的数据源。

观察者模式: Spring事件驱动模型就是观察者模式很经典的一个应用。

适配器模式 :Spring AOP 的增强或通知(Advice)使用到了适配器模式、spring MVC 中也是用到了适配器模式适配Controller 。
单例设计模式 : Spring中的Bean默认都是单例的。

14.Spring事务

事务原本是数据库中的概念，在 Dao 层。但一般情况下，需要将事务提升到业务层， 即 Service 层。这样做是为了能够使用事务的特性来管理具体的业务。

在 Spring 中通常可以通过以下两种方式来实现对事务的管理: (1)使用 Spring的事务注解管理事务(2)使用AspectJ的AOP配置管理事务

（1）Spring事务管理API

1.1PlatformTransactionManager：要用于完成事务的提交、回滚，及获取事务的状态信息。有两个常用的实现类:

DataSourceTransactionManager:使用JDBC或MyBatis进行数据库操作时使用。 

HibernateTransactionManager:使用Hibernate进行持久化数据时使用。

Spring 事务的默认回滚方式是:发生运行时异常和 error 时回滚，发生受查(编译)异常时提交。不过，对于受查异常，程序员也可以手工设置其回滚方式。

1.2TransactionDefinition：定义了事务描述相关的三类常量:事务隔离级别、事务传播行为、事务默认超时时限，及对它们的操作。

定义了五个事务隔离级别常量：这些常量均是以 ISOLATION_开头，如ISOLATION_XXX。
```
➢ DEFAULT:采用 DB 默认的事务隔离级别。MySql 的默认为 REPEATABLE_READ; Oracle 默认为 READ_COMMITTED。
➢ READ_UNCOMMITTED:读未提交。未解决任何并发问题。
➢ READ_COMMITTED:读已提交。解决脏读，存在不可重复读与幻读。
➢ REPEATABLE_READ:可重复读。解决脏读、不可重复读，存在幻读
➢ SERIALIZABLE:串行化。不存在并发问题。
```
定义了七个事务传播行为常量：所谓事务传播行为是指，处于不同事务中的方法在相互调用时，执行期间事务的维护情况。事务传播行为是加在方法上的。事务传播行为常量都是以 PROPAGATION_ 开头
```
PROPAGATION_REQUIRED:指定的方法必须在事务内执行。若当前存在事务，就加入到当前事务中;若当前没有事务，则创建一个新事务。这种传播行为是最常见的选择，也是Spring 默认的事务传播行为。如该传播行为加在 doOther()方法上。若 doSome()方法在调用 doOther()方法时就是在事 务内运行的，则 doOther()方法的执行也加入到该事务内执行。若 doSome()方法在调用 doOther()方法时没有在事务内执行，则 doOther()方法会创建一个事务，并在其中执行。

PROPAGATION_REQUIRES_NEW:总是新建一个事务，若当前存在事务，就将当前事务挂起，直到新事务执行完毕。

PROPAGATION_SUPPORTS:指定的方法支持当前事务，但若当前没有事务，也可以以非事务方式执行。

PROPAGATION_MANDATORY 
PROPAGATION_NESTED 
PROPAGATION_NEVER 
PROPAGATION_NOT_SUPPORTED
```
定义了默认事务超时时限:常量 TIMEOUT_DEFAULT定义了事务底层默认的超时时限，sql语句的执行时长。注意，事务的超时时限起作用的条件比较多，且超时的时间计算点较复杂。所以，该

（2）使用Spring的事务注解管理事务

通过@Transactional 注解方式，可将事务织入到相应 public 方法中，实现事务管理。

2.1声明事务管理器

2.2开启注解驱动

2.3业务层public方法加入事务属性

（3）使用AspectJ的AOP配置管理事务

使用XML配置事务代理的方式的不足是，每个目标类都需要配置事务代理。当目标类 较多，配置文件会变得非常臃肿。

使用XML配置顾问方式可以自动为每个符合切入点表达式的类生成事务代理。其用法很简单，只需将前面代码中关于事务代理的配置删除，再替换为如下内容即可。

3.1加入aspectj的依赖坐标

3.2在容器中添加事务管理器

3.3配置事务通知

为事务通知设置相关属性。用于指定要将事务以什么方式织入给哪些方法。

3.4配置增强器

指定将配置好的事务通知，织入给谁。

15.JPA

如何使用JPA在数据库中非持久化一个字段?

假如我们有有下面一个类:
```
 
Entity(name="USER") public class User {
@Id
@GeneratedValue(strategy = GenerationType.AUTO) @Column(name = "ID")
private Long id;
@Column(name="USER_NAME") private String userName;
@Column(name="PASSWORD") private String password;
    private String secrect;
}
```
如果我们想让 secrect 这个字段不被持久化，也就是不被数据库存储怎么办?我们可以采用下面几种方法:一般使用后面两种方式比􏰀多
```
static String transient1; // not persistent because of static
final String transient2 = “Satish”; // not persistent because of final 
transient String transient3; // not persistent because of transient @Transient
String transient4; // not persistent because of @Transient
```
16.spring是单例还是多例，如何设置为多例

Spring中声明一个bean时，需要声明bean的作用域，默认是singleton。

如何设置为多例，有两种方式

（1）类上加注解@Scope("prototype")

（2）在配置文件中的<bean>标签里加scope=scope="prototype"。
