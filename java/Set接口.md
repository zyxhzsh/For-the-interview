[Set接口](#Set接口)

[Set的遍历方式](#Set的遍历方式)

[Set的实现类](#Set的实现类)

[HashSet集合](#HashSet集合)

[LinkedHashSet集合](#LinkedHashSet集合)

### Set接口

`java.util.Set`接口与`Collection`接口中的方法基本一致，并没有对`Collection`接口进行功能上的扩充，只是比`Collection`接口更加严格了。

Set接口存取元素是无序的，元素不能重复，没有索引。

Set集合取出元素的方式可以采用：迭代器、增强for。

**Set接口特点**：

（1）元素是无序的。

（2）不能存储重复元素。

（3）没有所以。

### Set的遍历方式

（1）迭代器

（2）增强for循环
```
public class fuction {
    public static void main(String[] args) {
        Set<String> set = new HashSet<String>();
        set.add("5");
        set.add("6");
        //迭代器写法1
        Iterator<String> it1 = set.iterator();
        while (it1.hasNext()) {
            System.out.println(it1.next());
        }
        //迭代器写法2
        for(Iterator<String> it2 = set.iterator();it2.hasNext();){
            System.out.println(it2.next());
        }
        //增强for循环
        for(String str:set){
            System.out.println(str);
        }
    }
}
```

### Set的实现类

HashSet集合、LinkedHashSet集合

### HashSet集合

HashSet是根据对象的哈希值来确定元素在集合中的存储位置，因此具有良好的存取和查找性能。底层的实现其实是一个哈希表。（实际上是一个HashMap实例）

而Object类的hashCode方法可以获取对象的哈希值。哈希值是一个十进制整数，**默认**为由系统随机给出，就是对象的逻辑地址的十进制形式。重写后按重写后的方法返回。

**保证元素唯一性的方式依赖于**：hashCode与equals方法。先看待插入元素的哈希值在集合中是否出现过，若出现过，再用equals方法和与其哈希值相同的元素比较。

如果我们往集合中存放自定义的对象，为了保证其唯一，就必须复写hashCode和equals方法建立属于当前对象的比较方式。

### HashSet的遍历顺序

HashSet是无序的，无序是指：元素输入和输出的顺序不保证一样。

但是遍历输出该集合时，是按照存储的顺序遍历的。因此哈希表不变时，连续打印两次输出结果顺序一样。

### HashSet存储自定义类型的元素

给HashSet中存放自定义类型元素时，需要重写对象中的hashCode和equals方法，建立自己的比较方式，才能保证HashSet集合中的对象唯一

创建自定义MyPerson类，重写了toString方法。
```java
public class MyPerson {
    private String name;
    private int age;

    public MyPerson() {
    }

    public MyPerson(String name, int age) {
        this.name = name;
        this.age = age;
    }

    @Override
    public String toString() {
        return "MyPerson{" +
                "name='" + name + '\'' +
                ", age=" + age +
                '}';
    }
}
```

没重写hashCode与equals方法时。因为哈希值不同，所以p1和p2都存储进集合了，但两个对象的内容相同，不应该存两次。
```java
public class fuction {
    public static void main(String[] args) {
        HashSet<MyPerson> set = new HashSet<>();
        MyPerson p1 = new MyPerson("小美女",18);
        MyPerson p2 = new MyPerson("小美女",18);
        set.add(p1);
        set.add(p2);
        System.out.println(p1.hashCode());//2001049719
        System.out.println(p2.hashCode());//1528902577
        System.out.println(set);//[MyPerson{name='小美女', age=18}, MyPerson{name='小美女', age=18}]
    }
}
```
重写hashCode与equals方法后，两者哈希值相同，equals方法返回的是true，因此只会存一次。
~~~java
    @Override
    public boolean equals(Object o) {
        if (this == o) return true;
        if (o == null || getClass() != o.getClass()) return false;
        MyPerson myPerson = (MyPerson) o;
        return age == myPerson.age &&
                Objects.equals(name, myPerson.name);
    }

    @Override
    public int hashCode() {
        return Objects.hash(name, age);
    }
public class fuction {
    public static void main(String[] args) {
        HashSet<MyPerson> set = new HashSet<>();
        MyPerson p1 = new MyPerson("小美女",18);
        MyPerson p2 = new MyPerson("小美女",18);
        set.add(p1);
        set.add(p2);
        System.out.println(p1.hashCode());//734175839
        System.out.println(p2.hashCode());//734175839
        System.out.println(set);//[MyPerson{name='小美女', age=18}]
    }
}
~~~

### LinkedHashSet集合

HashSet保证元素唯一，可是元素存放进去是没有顺序的，那么我们要保证有序，怎么办呢？

LinkedHashSet是具有可预知迭代顺序的Set接口的哈希表和链表实现。与HashSet不同之处在于，后者维护着一个运行于所有条目的双重链表，此列表定义了迭代顺序，即按照元素插入到set中的顺序进行迭代。

LinkedHashSet底层是一个哈希表+链表。（多了一条链表记录元素的存储顺序）可以保证元素有序：元素输入和输出的顺序保证一样。

```java
public class fuction {
    public static void main(String[] args) {
       HashSet<String> set = new HashSet<>();
       set.add("a1");
       set.add("a213");
       set.add("b3");
       set.add("b313");
       System.out.println(set);
       LinkedHashSet<String> linkedHashSet = new LinkedHashSet<>();
       linkedHashSet.add("a1");
       linkedHashSet.add("a213");
       linkedHashSet.add("b2");
       linkedHashSet.add("b313");
       System.out.println(linkedHashSet);
    }
}
结果：
[a1, b3, b313, a213]
[a1, a213, b2, b313]
```
