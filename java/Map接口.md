[Map接口](#Map接口)

[Map常用子类](#Map常用子类)

[Map接口中的常用方法](#Map接口中的常用方法)

[Map集合遍历键找值的方式](#Map集合遍历键找值的方式)

[Map中的Entry对象](#Map中的Entry对象)

[Map集合遍历键值对的方式](#Map集合遍历键值对的方式)

[HashMap存储自定义类型键值](#HashMap存储自定义类型键值)

[LinkedHashMap](#LinkedHashMap)

[Hashtable](#Hashtable)

[Map集合练习](#Map集合练习)

[JDK9对集合添加的优化](#JDK9对集合添加的优化)

[Debug追踪](#Debug追踪)

[有序版本的斗地主](案例.md#有序版本的斗地主)

## 教学目标

- [ ] 能够说出Map集合特点
- [ ] 使用Map集合添加方法保存数据
- [ ] 使用”键找值”的方式遍历Map集合
- [ ] 使用”键值对”的方式遍历Map集合
- [ ] 能够使用HashMap存储自定义键值对的数据
- [ ] 能够使用HashMap编写斗地主洗牌发牌案例

### Map集合

现实生活中，我们常会看到这样的一种集合：IP地址与主机名，身份证号与个人，系统用户名与系统用户对象等，这种一一对应的关系，就叫做映射。

Java提供了专门的集合类用来存放这种对象关系的对象，即`java.util.Map`接口。Collection中的集合称为单列集合，Map中的集合称为双列集合。

Map中的集合，元素是成对存在的，每个元素由键与值两部分组成，键和值是一一对应的，通过键可以找到对应的值。Map中的集合不能包含重复的键，值可以重复；每个键只能对应一个值。

### Map常用子类

（1）HashMap<K,V>：采用哈希表结构存储数据，元素的存取顺序不能保证一致，是一个无序的集合。由于要保证键的唯一、不重复，需要重写键的hashCode()方法、equals()方法。

在**JDK1.8**之前，哈希表底层采用数组+链表实现，即使用链表处理冲突，同一hash值的链表都存储在一个链表里。但是当位于一个桶中的元素较多，即hash值相等的元素较多时，通过key值依次查找的效率较低。而JDK1.8中，哈希表存储采用数组+链表+红黑树实现，当链表长度超过阈值（8）时，将链表转换为红黑树，这样大大减少了查找时间。

数组结构把元素进行了分组，相同哈希值的元素是一组。链表/红黑树把相同哈希值的元素连接在一起。(链表长度超过8时，将链表转换为红黑树)。


（2）LinkedHashMap<K,V>：HashMap下有个子类LinkedHashMap，存储数据采用哈希表结构+链表结构。通过链表结构可以保证元素的存取顺序一致；通过哈希表结构可以保证键的唯一、不重复，需要重写键的hashCode()方法、equals()方法。

**tips**：Map接口中的集合都有两个泛型变量<K,V>,在使用时，要为两个泛型变量赋予数据类型。两个泛型变量<K,V>的数据类型可以相同，也可以不同。

**注意**：HashMap和LinkedHashMap，都可以将值为null的对象作为键或值。

### Map接口中的常用方法

* `public V put(K key, V value)`:  把指定的键与指定的值添加到Map集合中。注意如果K存在，添加的是新value，返回的是旧的value；如果K不存在，就返回null。
* `public V remove(Object key)`: 把指定的键所对应的键值对元素 在Map集合中删除，返回被删除元素的值。若不存在K就返回null。
* `public V get(Object key)` 根据指定的键，在Map集合中获取对应的值。没有这个key就返回null。
* `boolean containsKey(Object key)  ` 判断集合中是否包含指定的键。
* `public Set<K> keySet()`: 获取Map集合中所有的键，存储到Set集合中。
* `public Set<Map.Entry<K,V>> entrySet()`: 获取到Map集合中所有的键值对对象的集合(Set集合)。

```java
public class fuction {
    public static void main(String[] args) {
        Map<String, Integer> map = new HashMap<>();
        System.out.println(map.put("a", 100));//null
        System.out.println(map.put("a", 200));//100
        System.out.println(map);//{a=200}
        System.out.println(map.remove("a"));//200
        Integer i=map.remove("a");//null
        //注意，如果写成int i=map.remove("a");会抛出空指针异常，所以尽量使用包装类。
        System.out.println(i);
    }
}
```

### Map集合遍历键找值的方式

分析步骤：

1. 获取Map中所有的键，由于键是唯一的，所以返回一个Set集合存储所有的键。

调用方法public Set<K> keySet()，把map集合中所有的key存储到Set集合中。

2. 遍历键的Set集合，得到每一个键。

遍历Set集合有增强的for循环和迭代器两种方法。

3. 根据键获取对应的值：get(K key)

```java
public class fuction {
    public static void main(String[] args) {
        HashMap<String, String> map = new HashMap<String,String>();
        map.put("胡歌", "霍建华");
        map.put("郭德纲", "于谦");
        map.put("薛之谦", "大张伟");
        // 遍历键集 得到每一个键对应的值
        for (String key : map.keySet()) { //map.keySet()获取所有的键组成的集合
            String value = map.get(key);
            System.out.println(""+key+"对应的值是："+value);
        }
    }
}
```

### Map中的Entry对象

Map中存放的是两种对象，一种称为**key**(键)，一种称为**value**(值)，它们在在Map中是一一对应关系。

Map接口中有一个内部接口Entry。当Map集合创建时，就会在Map集合中创建Entry对象，用来记录键与值。

Entry将键值对的对应关系封装成了对象，因此Entry对象也称为键值对对象。这样在遍历Map集合时，就可以从每一个键值对对象（Entry对象）中获取对应的键与对应的值。

Entry表示了一对键和值，也同样提供了获取对应键和对应值得方法：

* `public K getKey()`：获取Entry对象中的键。
* `public V getValue()`：获取Entry对象中的值。

在Map集合中也提供了获取所有Entry对象的方法：

* `public Set<Map.Entry<K,V>> entrySet()`: 获取Map集合中所有的键值对对象，存储到一个Set集合中。

### Map集合遍历键值对的方式

通过集合中每个键值对(Entry)对象，获取键值对(Entry)对象中的键与值。

（1）获取Map集合中，所有的键值对(Entry)对象，存储到一个Set集合中。
，并返回这个Set集合。

public Set<Map.Entry<K,V>> entrySet()

（2）遍历包含键值对(Entry)对象的Set集合，得到每一个键值对(Entry)对象。

遍历Set集合有增强的for循环和迭代器两种方法。

（3）获取Entry对象中的键与值。

public K getKey()     

public V getValue()

```java
public class fuction {
    public static void main(String[] args) {
        // 添加元素到集合
        HashMap<String, String> map = new HashMap<String, String>(){
            {
                put("大音", "希声");
                put("大智", "若愚");
                put("大爱", "无疆");
            }
        };
        // 获取所有的entry对象  entrySet
        Set<Map.Entry<String,String>> entrySet = map.entrySet();
        //遍历得到每一个entry对象
        //(1)使用迭代器
        Iterator<Map.Entry<String,String>> it = entrySet.iterator();
        while(it.hasNext()){
            Map.Entry<String,String> entry = it.next();
            String key =  entry.getKey();
            String value =  entry.getValue();
            System.out.println(key+"+"+value);
        }
        System.out.println("----------------------");
        //(2)使用增强的for循环
        for(Map.Entry<String,String> entry: map.entrySet()){
            String key =  entry.getKey();
            String value =  entry.getValue();
            System.out.println(key+"+"+value);
        }
    }
}
```

**tips**：Map集合不能直接使用迭代器或者foreach进行遍历。但是转成Set之后就可以使用了。

### HashMap存储自定义类型键值

练习：每位学生（姓名，年龄）都有自己的家庭住址。那么，既然有对应关系，则将学生对象和家庭住址存储到map集合中。学生作为键, 家庭住址作为值。

> 注意，学生姓名相同并且年龄相同视为同一名学生。

**注意**

（1）当给HashMap中存放自定义对象时，如果自定义对象作为key存在，这时要保证对象唯一，必须复写对象的hashCode和equals方法(比较是否相同见HashSet存放自定义对象)。

（2）如果要保证map中存放的key和取出的顺序一致，可以使用LinkedHashMap集合来存放。

编写学生类：

```java
public class Student {
    private String name;
    private int age;

    public Student() {
    }

    public Student(String name, int age) {
        this.name = name;
        this.age = age;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public int getAge() {
        return age;
    }

    public void setAge(int age) {
        this.age = age;
    }

    @Override
    public boolean equals(Object o) {
        if (this == o)
            return true;
        if (o == null || getClass() != o.getClass())
            return false;
        Student student = (Student) o;
        return age == student.age && Objects.equals(name, student.name);
    }

    @Override
    public int hashCode() {
        return Objects.hash(name, age);
    }
}
```

编写测试类：

```java 
public class HashMapTest {
    public static void main(String[] args) {
        //1,创建Hashmap集合对象。
        Map<Student,String>map = new HashMap<Student,String>();
        //2,添加元素。
        map.put(new Student("lisi",28), "上海");
        map.put(new Student("wangwu",22), "北京");
        map.put(new Student("zhaoliu",24), "成都");
        map.put(new Student("zhouqi",25), "广州");
        map.put(new Student("wangwu",22), "南京");
        
        //3,取出元素。键找值方式
        Set<Student> keySet = map.keySet();
        for(Student key: keySet){
            Stringvalue = map.get(key);
            System.out.println(key.toString()+"....."+value);
        }
    }
}
```

### LinkedHashMap

HashMap保证成对元素唯一，并且查询速度很快，可是成对元素存放进去是没有顺序的，那么我们要保证有序，还要速度快怎么办呢？

在HashMap下面有一个子类LinkedHashMap，它的底层是哈希表组合+链表。链表用来记录元素的存储顺序，保证了元素有序。

**注意**：若put一个键值对时，哈希表中已经有该键，则更新value。此时该键值对在哈希表中的位置不会改变，仅仅是更新了value而已。
```java
//Student类见：HashMap存储自定义类型键值
public class LinkedHashMapDemo {
    public static void main(String[] args) {
        Map<Student,String>map = new LinkedHashMap<>();
        map.put(new Student("lisi",28), "上海");
        map.put(new Student("wangwu",22), "北京");
        map.put(new Student("zhaoliu",24), "成都");
        map.put(new Student("zhouqi",25), "广州");
        //(取出元素。键找值方式
        for(Student key: map.keySet()){
            String value = map.get(key);
            System.out.println(key.toString()+"....."+value);
        }
        map.put(new Student("wangwu",22), "南京");
        System.out.println("-------------------------------");
        for(Student key: map.keySet()){
            String value = map.get(key);
            System.out.println(key.toString()+"....."+value);
        }
    }
}

结果:
Student{name='lisi', age=28}.....上海
Student{name='wangwu', age=22}.....北京
Student{name='zhaoliu', age=24}.....成都
Student{name='zhouqi', age=25}.....广州
-------------------------------
Student{name='lisi', age=28}.....上海
Student{name='wangwu', age=22}.....南京
Student{name='zhaoliu', age=24}.....成都
Student{name='zhouqi', age=25}.....广州
```

### Hashtable

Hashtable在1.2版本之后被HashMap取代了。但Hashtable的子类Properties依然活跃在历史舞台。Properties是唯一一个和IO流相结合的集合。

实现了Map接口，底层是一个哈希表。与HashMap的区别：

（1）Hashtable不能将值为null的对象作为键或值。即不能存储null值，null键。；而HashMap可以。

若Hashtable存null值或null键，编译时不会报错，运行时会抛出空指针异常。

（2）Hashtable是线程安全的，也就是同步的，同步意味着它是单线程的集合。所以速度慢，效率低；而HashMap不是线程安全的，效率高。


### Map集合练习

计算一个字符串中每个字符出现次数。

**分析：**
```
1.获取一个字符串对象
2.创建一个Map集合，键代表字符，值代表次数。
3.遍历字符串得到每个字符。
4.判断Map中是否有该键，如果没有，将对应的值设为1；如果有，将对应的值加1。
5.打印最终结果
```
```java
public class Demo03 {
    public static void main(String[] args) {
        System.out.println("请输入一个字符串，按回车结束");
        String input = new Scanner(System.in).nextLine();
        System.out.println(countCharacter(input));
    }
    private static Map<Character, Integer> countCharacter(String str){
        Map<Character, Integer> map = new HashMap<>();
        for(int i=0;i<str.length();i++){
            map.put(str.charAt(i), map.getOrDefault(str.charAt(i),0)+1);
        }
        return map;
    }
}
```
### JDK9对集合添加的优化

 Java9添加了几种集合工厂方法，更方便创建少量元素的集合。新的List、Set、Map的静态工厂方法可以更方便地创建集合的不可变实例。

使用前提：当集合中存储的元素个数已经确定了，不再改变时使用。

~~~java
public class HelloJDK9 {  
    public static void main(String[] args) {  
        Set<String> str1=Set.of("a","b","c");  
        //str1.add("c");这里编译的时候不会错，但是执行的时候会报错，因为是不可变的集合  
        System.out.println(str1);  
        Map<String,Integer> str2=Map.of("a",1,"b",2);  
        System.out.println(str2);  
        List<String> str3=List.of("a","b");  
        System.out.println(str3);  
    }  
} 
~~~

**注意**：

1.of()方法只是Map，List，Set这三个接口的静态方法，其父类接口和子类实现并没有这类方法，比如HashSet，ArrayList等待；

2.返回的集合是不可变的；集合不能再使用add,put方法添加元素，会抛出异常。

3.Set接口和Map接口在调用of方法的时候，不能有重复的元素，否则会抛出异常

### Debug追踪

Debug调试程序可以让代码逐行运行，查看代码执行的过程，调试程序出现的bug

**使用IDEA的断点调试功能，查看程序的运行过程**

使用方式：

（1）在行号的右边，鼠标左键单击，添加断点（可以在每个方法的第一行添加，哪里bug添加到哪里）

（2）右键，选择Debug执行程序。程序会停留在添加的第一个断点处。

执行程序：

f8：逐行执行程序

f7：进入到方法中

shift+f8：跳出方法

f9：跳到下一个断点，如果没有下一个断点，就结束程序。

command+f2：退出Debug模式，停止程序。(红色的方块)

例如可以看for循环的执行流程
```java
public class Demo03Sort {
    public static void main(String[] args) {
/*断点*/      for(int i=0;i<3;i++){
            System.out.println(i);
        }

    }
}
```
