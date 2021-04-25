mybatis是一个sql映射框架，提供了数据库操作，是增强的JDBC。mybatis让开发人员只需要写sql就可以了，不需要关心Connection,Statement,ResultSet的创建和销毁，sql的执行，将sql结果转换为java对象。

1.#{}和${}的区别是什么

2.Xml映射文件中，除了常⻅的select|insert|updae|delete标签之外，还有哪些标签?

3.通常一个 Xml 映射文件，都会写一个 Dao 接口 与之对应，请问，这个 Dao 接口的工作原理是什么?Dao 接口里的 方法，参数不同时，方法能重载吗?

4 Mybatis 是如何进行分⻚的?分⻚插件的原理是什么?

5.Mybatis 是如何将 sql 执行结果封装为目标对象并返回的?都有哪些映射形式?

6.Mybatis 执行批量插入，能返回数据库主键列表吗?

7.Mybatis 动态 sql 是做什么的?都有哪些动态 sql?能简述一 下动态 sql 的执行原理不?

8.Mybatis 能执行一对一、一对多的关联查询吗?都有哪些实现方式，以及它们之间的区别。

9.Mybatis 是否支持延迟加载?如果支持，它的实现原理是什么?

10.Mybatis 的 Xml 映射文件中，不同的 Xml 映射文件，id 是 否可以重复?

11.Mybatis 中如何执行批处理?

12.Mybatis 都有哪些 Executor 执行器?它们之间的区别是什么?

13.Mybatis 是否可以映射 Enum 枚举类?

14.Mybatis 映射文件中，如果 A 标签通过 include 引用了 B 标 签的内容，请问，B 标签能否定义在 A 标签的后面，还是说必须定 义在 A 标签的前面?

15.简述 Mybatis 的 Xml 映射文件和 Mybatis 内部数据结构之 间的映射关系?

16.为什么说 Mybatis 是半自动 ORM 映射工具?它与全自动的 区别在哪里?

17.Mybatis的一级、二级缓存

18.Mapper 编写有几种方式 ？

1.#{}和${}的区别是什么

#：占位符，告诉 mybatis 使用实际的参数值代替。使用 PrepareStatement 对象执行 sql 语句, #{...}代替sql 语句的“?”。这样做安全，避免sql注入。PrepareStatement 对象执行效率高。通常是首选做法。

$ ：字符串替换和连接，告诉 mybatis 使用$包含的“字符串”替换所在位置。使用 Statement 把 sql 语句和${}的内容连接起来。$效率低，数据安全性低，有可能造成sql注入。

2.Xml映射文件中，除了常⻅的select|insert|updae|delete标签之外，还有哪些标签?

```<resultMap>、<parameterMap>、<sql>、<include>、<selectKey>```，加上动态sql的9个标签，其中```<sql>```为 sql 片段标签，通过
```<include>```标签引入 sql 片段， ```<selectKey>```为不支持自增的主键生成策略标
签。

resultMap：将查询结果集中的列一一映射到实体类的各个属性上去，此处的这个映射关系，可以根据我们在“resultMap”的子标签中的配置来决定的
```
属性                            作用
id              指定查询结果集中的唯一标识，即主键，可配置多个
column          查询结果集中的列名，要和数据库字段名对应
property        和数据库字段对应的实体类的字段名
<resultMap id="ResultMap" type="Student">
    <id column="id" property="studentId"></id>
    <result column="name" property="studentName"></result>
    <result column="age" property="studentAge"></result>
</resultMap>
```
parameterMap可以用于指定实体类字段属性与数据库字段属性的映射关系,可以和result Map搭配使用。这样一来当传入参数实体类中的字段名和数据库的字段名名称上没有对应也能查询出想要的结果，这就是parameterMap的作用。
```
<parameterMap id="ParameterMap" type="Student">
    <parameter property="studentId" resultMap="ResultMap"></parameter>
    <parameter property="studentName" resultMap="ResultMap"></parameter>
    <parameter property="studentAge" resultMap="ResultMap"></parameter>
</parameterMap>

<resultMap id="ResultMap" type="Student">
    <id column="id" property="studentId"></id>
    <result column="name" property="studentName"></result>
    <result column="age" property="studentAge"></result>
</resultMap>
```

3.通常一个 Xml 映射文件，都会写一个 Dao 接口 与之对应，请问，这个 Dao 接口的工作原理是什么?Dao 接口里的 方法，参数不同时，方法能重载吗?

映射文件中的namespace的值，就是dao接口的全限定名称。映射文件中MappedStatement的id值，就是接口的方法名。接口方法的参数，就是传递给sql的参数。

接口全限定名称+方法名，可唯一定位一个MappedStatement。在 Mybatis 中，每一个 <select> 、 <insert> 、 <update> 、 <delete> 标签，都会被解析为一
个 MappedStatement 对象。

Dao 接口的工作原理是 JDK 动态代理，Mybatis 运行时会使用 JDK 动态代理为 Dao 接口生成代 理 proxy 对象，代理对象 proxy 会拦截接口方法，转而执行 MappedStatement 所代表的 sql，然后 将 sql 执行结果返回。

Dao 接口里的方法，是不能重载的，因为是全限名+方法名的保存和寻找策略。

4 Mybatis 是如何进行分⻚的?分⻚插件的原理是什么?

MyBatis中可以使用RowBounds进行逻辑分页，它是基于结果集的内存分页。

如果想实现物理分页，可以在sql语句中实现（写带有物理分⻚的参数），或者使用MyBatis的分页插件。

分页插件的原理是：使用 Mybatis提供的插件接口，实现自定义插件，在插件的拦截方法内拦截待执行的 sql，然后重写 sql，根据 dialect 方言，添加对应的物理分⻚语句和物理分⻚参数。

5.Mybatis 是如何将 sql 执行结果封装为目标对象并返回的?都有哪些映射形式?

第一种是使用<resultMap>标签，逐一定义数据库列名和对象属性名之间的映射关系。

第二种是使用 sql 列的别名功能，将列的别名书写为对象属性名。列名不区分大小写，Mybatis会忽略列名大小写

有了列名与属性名的映射关系后，Mybatis通过反射创建对象，同时使用反射给对象的属性逐一赋值并返回，那些找不到映射关系的属性，是无法完成赋值的。

6.Mybatis 执行批量插入，能返回数据库主键列表吗?

能。

7.Mybatis 动态 sql 是做什么的?都有哪些动态 sql?能简述一 下动态 sql 的执行原理不?

Mybatis 动态 sql 可以让我们在 Xml 映射文件内，以标签的形式编写动态 sql，完成逻辑判断和动态拼接sql的功能，Mybatis提供了9种动态sql标签。

trim|where|set|foreach|if|choose|when|otherwise|bind。

其执行原理为，使用OGNL从sql参数对象中计算表达式的值，根据表达式的值动态拼接sql，以此来完成动态sql的功能。

8.Mybatis 能执行一对一、一对多的关联查询吗?都有哪些实现方式，以及它们之间的区别。

能，Mybatis 不仅可以执行一对一、一对多的关联查询，还可以执行多对一，多对多的关联查询。

多对一查询，其实就是一对一查询，只需要把 selectOne() 修改为 selectList() 即可;

多对 多查询，其实就是一对多查询，只需要把 selectOne() 修改为 selectList() 即可。

MyBatis 实现一对一有几种方式?
```
有联合查询和嵌套查询,联合查询是几个表联合查询,只查询一次, 通过在 resultMap 里面配置 association 节点配置一对一的类就可以完成;

嵌套查询是先查一个表，根据这个表里面的结果的 外键 id，去再另外一个表里面查询数据,也是通过 association 配置，但另外一个表的查询通过 select 属性配置。
```
MyBatis 实现一对多有几种方式,怎么操作的?
```
有联合查询和嵌套查询。联合查询是几个表联合查询,只查询一次,通过在 resultMap 里面 的 collection 节点配置一对多的类就可以完成

嵌套查询是先查一个表,根据这个表里面的 结果的外键 id,去再另外一个表里面查询数据,也是通过配置 collection,但另外一个表的查询 通过 select 节点配置。
```

那么问题来了，join 查询出来 100 条记录，如何确定主对象是 5 个，而不是 100 个?其去重复的 原理是 <resultMap> 标签内的 <id> 子标签，指定了唯一确定一条记录的 id 列，Mybatis 根据列 值来完成 100 条记录的去重复功能，<id>可以有多个，代表了联合主键的语意。主对象的关联对象，也是根据这个原理去重复的，尽管一般情况下，只有主对象会有重复记 录，关联对象一般不会重复。

9.Mybatis 是否支持延迟加载?如果支持，它的实现原理是什么?

Mybatis仅支持association关联对象和collection关联集合对象的延迟加载，association指的就是一对一，collection指的就是一对多查询。

它的原理是，使用 CGLIB 创建目标对象的代理对象，当调用目标方法时，进入拦截器方法，比如调用 a.getB().getName() ，拦截器 invoke() 方法发现 a.getB() 是 null 值，那么就会单独发送事先保存好的查询关联B对象的sql，把B查询上来，然后调用a.setB(b)，于是 a 的对象 b 属性 就有值了，接着完成 a.getB().getName() 方法的调用。这就是延迟加载的基本原理。

10.Mybatis 的 Xml 映射文件中，不同的 Xml 映射文件，id 是 否可以重复?

不同的 Xml 映射文件，如果配置了 namespace，那么 id 可以重复;如果没有配置 namespace，那么 id 不能重复;毕竟 namespace 不是必须的，只是最佳实践而已。

原因就是 namespace+id 是作为 Map<String, MappedStatement> 的 key 使用的，如果没有 namespace，就剩下 id，那么，id 重复会导致数据互相覆盖。有了 namespace，自然 id 就可以 重复，namespace 不同，namespace+id 自然也就不同。

11.Mybatis 中如何执行批处理?

使用BatchExecutor完成批处理。

12.Mybatis 都有哪些 Executor 执行器?它们之间的区别是什么?

Mybatis有三种基本的Executor执行器， SimpleExecutor 、 ReuseExecutor 、 BatchExecutor 。

SimpleExecutor :每执行一次 update 或 select，就开启一个 Statement 对象，用完立刻关闭 Statement 对象。

ReuseExecutor :执行 update 或 select，以 sql 作为 key 查找 Statement 对象，存在就使用， 不存在就创建，用完后，不关闭 Statement 对象，而是放置于 Map<String, Statement>内，供下 一次使用。简言之，就是重复使用 Statement 对象。

BatchExecutor :执行 update(没有 select，JDBC 批处理不支持 select)，将所有 sql 都添加到批处理中(addBatch())，等待统一执行(executeBatch())，它缓存了多个 Statement对象，每个 Statement 对象都是 addBatch()完毕后，等待逐一执行 executeBatch()批处理。与 JDBC 批处理相同。

作用范围:Executor 的这些特点，都严格限制在 SqlSession 生命周期范围内。

在Mybatis配置文件中，可以指定默认的ExecutorType执行器类型，也可以手动给DefaultSqlSessionFactory的创建SqlSession的方法传递ExecutorType类型参数。

13.Mybatis 是否可以映射 Enum 枚举类?

Mybatis 可以映射枚举类，Mybatis 可以映射任何对象到表的一列上。映射方式为自定义一个 TypeHandler ，实现 TypeHandler 的 setParameter() 和 getResult() 接口方法。 TypeHandler 有两个作用，一是完成从 javaType 至 jdbcType 的转换，二是完成 jdbcType 至 javaType 的转换，体现为 setParameter() 和 getResult() 两个方法，分别代表设置 sql 问号占位符参数和获取列查询结果。

14.Mybatis 映射文件中，如果 A 标签通过 include 引用了 B 标 签的内容，请问，B 标签能否定义在 A 标签的后面，还是说必须定 义在 A 标签的前面?

虽然 Mybatis 解析 Xml 映射文件是按照顺序解析的，但是，被引用的 B 标签依然可以定义 在任何地方，Mybatis 都可以正确识别。
原理是，Mybatis 解析 A 标签，发现 A 标签引用了 B 标签，但是 B 标签尚未解析到，尚不存 在，此时，Mybatis 会将 A 标签标记为未解析状态，然后继续解析余下的标签，包含 B 标签，待 所有标签解析完毕，Mybatis 会重新解析那些被标记为未解析的标签，此时再解析 A 标签时，B 标签已经存在，A 标签也就可以正常解析完成了。

15.简述 Mybatis 的 Xml 映射文件和 Mybatis 内部数据结构之 间的映射关系?

Mybatis 将所有 Xml 配置信息都封装到 All-In-One 重量级对象 Configuration 内部。在 Xml 映射文件中， <parameterMap> 标签会被解析为 ParameterMap 对象，其每个子元素会被解析为 ParameterMapping 对象。 <resultMap> 标签会被解析为 ResultMap 对象，其每个子元素会被解析为ResultMapping 对象。每一个 <select>􏰀<insert>􏰀<update>􏰀<delete> 标签均会被解析为MappedStatement对象，标签内的sql会被解析为BoundSql对象。

16.为什么说 Mybatis 是半自动 ORM 映射工具?它与全自动的区别在哪里?

Hibernate 属于全自动 ORM 映射工具，使用 Hibernate 查询关联对象或者关联集合对象时， 可以根据对象关系模型直接获取，所以它是全自动的。而 Mybatis 在查询关联对象或关联集合对 象时，需要手动编写 sql 来完成，所以，称之为半自动 ORM 映射工具。

17.Mybatis的一级、二级缓存

1)一级缓存: 基于 PerpetualCache 的 HashMap 本地缓存，其存储作用域为 Session，当 Session flush 或 close 之后，该 Session 中的所有 Cache 就将清空，默认打开一级缓存。

2)二级缓存与一级缓存其机制相同，默认也是采用 PerpetualCache的HashMap存储，不同在于其存储作用域为Mapper(Namespace)，并且可自定义存储源，如 Ehcache。默认不 打开二级缓存，要开启二级缓存，使用二级缓存属性类需要实现 Serializable 序列化接口 (可用来保存对象的状态),可在它的映射文件中配置<cache/> ;

3)对于缓存数据更新机制，当某一个作用域(一级缓存 Session/二级缓存 Namespaces)进行了 C/U/D 操作后，默认该作用域下所有select中的缓存将被 clear 掉并重新更新，如 果开启了二级缓存，则只根据配置判断是否刷新。

18.Mapper 编写有几种方式 ？

方式1：接口实现类继承SQLSessionDaoSupport，此方法需要编写mapper接口，mapper接口的实现类,mapper.xml文件。

方式2：使用org.mybatis.spring.mapper.MapperFactoryBean 此方法需要在SqlMapConfig.xml中配置mapper.xml的位置，还需定义mapper接口。

方式3：使用mapper扫描器 需要编写mapper.xml文件，需要mapper接口，配置mapper扫描器，使用扫描器从spring容器中获取mapper的实现对象。
