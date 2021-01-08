### mybatis主要类介绍

(**1**) **Resources** 类

Resources 类，用于读取资源文件。该类有很多方法，通过加载并解析资源文件，返回不同类型的 IO 流对象。

(**2**) **SqlSessionFactoryBuilder** 类

SqlSessionFactory对象的创建，需要使用 SqlSessionFactoryBuilder 对象的 build()方法。

由于 SqlSessionFactoryBuilder 对象在创建完工厂对象后，就完成了其历史使命，即可被销毁。所以，一般会将SqlSessionFactoryBuilder 对象创建为一个方法内的局部对象，方法结束，对象销毁。

(**3**) **SqlSessionFactory** 接口

SqlSessionFactory 接口对象是一个重量级对象(系统开销大)，是线程安全的，所以一个应用只需要一个该对象即可。

SqlSessionFactory接口的实现类是DefaultSqlSessionFactory。

创建 SqlSession对象需要使用SqlSessionFactory接口的openSession()方法。

```
openSession(default true)

参数默认为true，创建一个有自动提交功能的SqlSession；参数为false时创建一个非自动提交功能的SqlSession，需手动提交。
```

(**4**) **SqlSession** 接口

SqlSession接口定义了操作数据库的方法，实现类是DefaultSqlSession。

SqlSession接口对象用于执行持久化操作。一个SqlSession对象对应着一次数据库会话，一次会话以SqlSession对象的创建开始，以SqlSession对象的关闭结束。

SqlSession接口对象是线程不安全的，所以每次数据库会话结束前，需要马上调用其 close()方法，将其关闭。再次需要会话，再次创建。 

因此SqlSession需要在方法内部创建和使用，使用完毕后关闭。

### 创建mybatis工具类
