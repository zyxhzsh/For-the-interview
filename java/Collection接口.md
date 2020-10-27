[集合框架](#集合框架)

[Collection接口](#Collection接口)

[Iterator接口](#Iterator接口)

[增强for循环](#增强for循环)

[集合综合案例](https://github.com/GrowTowardsSunlight/For-the-interview/blob/master/java/案例.md#集合综合案例)

## 教学目标

- [ ] 能够说出集合与数组的区别
- [ ] 说出Collection集合的常用功能
- [ ] 能够使用迭代器对集合进行取元素
- [ ] 能够说出集合的使用细节
- [ ] 能够使用集合存储自定义类型
- [ ] 能够使用foreach循环遍历集合
- [ ] 能够使用泛型定义集合对象
- [ ] 能够理解泛型上下限
- [ ] 能够阐述泛型通配符的作用

### 集合框架

集合按照其存储结构可以分为两大类，分别是单列集合`java.util.Collection`和双列集合`java.util.Map`

集合是java中提供的一种容器，可以用来存储多个数据。
    
集合和数组都是容器，它们有啥区别呢？

* 数组的长度是固定的。集合的长度是可变的。

* 数组中存储的是同一类型的元素，可以存储基本数据类型值，也可以存储对象。集合存储的都是对象。而且对象的类型可以不一致。在开发中一般当对象多的时候，使用集合进行存储。

### Collection接口

* **Collection**：单列集合类的根接口，定义的是所有单列集合中共性的方法，用于存储一系列符合某种规则的元素。Collection没有带索引的方法。

它有两个重要的子接口，分别是List和Set。

`List`的特点是：元素有序、元素可重复、有索引，可以使用普通的for循环遍历。

`Set`的特点是：元素不可重复、没有索引，不能使用普通的for循环遍历。

List接口的主要实现类有ArrayList和LinkedList。

Set接口的主要实现类有HashSet和TreeSet，这两个类都是无序的。而LinkedHashSet继承了HashSet类，实现了Set接口，且LinkedHashSet是有序的。

集合本身是一个工具，它存放在java.util包中。在`Collection`接口定义着单列集合框架中最最共性的内容。

### Collection 常用功能

Collection是所有单列集合的父接口，因此在Collection中定义了单列集合(List和Set)通用的一些方法，这些方法可用于操作所有的单列集合。方法如下：

* `public boolean add(E e)`：  把给定的对象添加到当前集合中 。
* `public void clear()` :清空集合中所有的元素。
* `public boolean remove(E e)`: 把给定的对象在当前集合中删除。
* `public boolean contains(E e)`: 判断当前集合中是否包含给定的对象。
* `public boolean isEmpty()`: 判断当前集合是否为空。
* `public int size()`: 返回集合中元素的个数。
* `public Object[] toArray()`: 把集合中的元素，存储到数组中。

### Iterator接口

在程序开发中，经常需要遍历集合中的所有元素。针对这种需求，JDK专门提供了一个接口`java.util.Iterator`，主要用于迭代访问（即遍历）集合中的元素，因此`Iterator`对象也被称为迭代器。

* **迭代**：即集合元素的通用获取方式。在取元素之前先要判断集合中有没有元素，如果有，就把这个元素取出来，继续在判断，如果还有就再取出出来。一直把集合中的所有元素全部取出。这种取出方式专业术语称为迭代。

想要遍历集合，首先要获取该集合的迭代器。例如Collection接口中有一个方法iterator()，这个方法返回的就是迭代器的实现类对象。

* `public Iterator<E> iterator()`: 获取集合对应的迭代器，用来遍历集合中的元素的。迭代器的泛型跟着集合走，集合是什么泛型，迭代器就是什么泛型。

Iterator接口的常用方法如下：

* `public E next()`:返回迭代的下一个元素。
* `public boolean hasNext()`:如果仍有元素可以迭代，则返回 true。

### 迭代器的使用步骤

（1）使用集合中的方法public Iterator<E> iterator()返回迭代器的实现类对象，使用Iterator接口接收(多态)。

迭代器的泛型跟着集合走，集合是什么泛型，迭代器就是什么泛型。
```
Iterator<集合对象的泛型> it = 集合对象.iterator();
```
若省略类型参数：Iterator it = 集合对象.iterator();不会报错，但会破坏使用类型参数的目的，并可能掩盖错误。如下面代码所示

若Iterator省略了类型参数，Set中的元素类型就变为Object，entry就是Object类型。而此处泛型K是String，entry是Object类型，无法调用getKey()方法。泛型V是String，entry是Object类型，无法调用getValue()方法。

**因此虽然没有报错，但是达不到获取Entry对象中的键与值的目的。所以迭代器的泛型不应该省略。**
```java
迭代器省略了泛型，不规范的写法
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
        //使用迭代器
        Iterator it = entrySet.iterator();//迭代器省略了泛型
        while(it.hasNext()){
            Object entry = it.next();
            String key =  entry.getKey();
            String value =  entry.getValue();
            System.out.println(key+"+"+value);
        }
    }
}
这种参数化类型的原始使用在Java中是有效的，但是会破坏使用类型参数的目的，并可能掩盖错误。此检查反映了javac的rawtypes警告。
Reports any uses of parameterized classes where the type parameters are omitted. Such raw uses of parameterized types are valid in Java, but defeat the purpose of using type parameters, and may mask bugs. This inspection mirrors the rawtypes warning of javac.
```
（2）使用Iterator接口中的方法hasNext判断还有没有下一个元素。

（3）使用Iterator接口中的方法next取出集合中的下一个元素。

注意：如果集合中已经没有元素了，还继续使用迭代器的next方法，将会抛出java.util.NoSuchElementException异常。

正确的写法，迭代器写上泛型。
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
        //使用迭代器
        Iterator<Map.Entry<String,String>> it = entrySet.iterator();
        while(it.hasNext()){
            Map.Entry<String,String> entry = it.next();
            String key =  entry.getKey();
            String value =  entry.getValue();
            System.out.println(key+"+"+value);
        }
    }
}
```

### 迭代器的实现原理

Iterator迭代器对象在遍历集合时，内部采用指针的方式来跟踪集合中的元素。

在调用Iterator的next方法之前，迭代器的索引位于第一个元素之前（集合的-1索引），不指向任何元素，当第一次调用迭代器的next方法后，迭代器的索引会向后移动一位，指向第一个元素并将该元素返回，当再次调用next方法时，迭代器的索引会指向第二个元素并将该元素返回，依此类推，直到hasNext方法返回false，表示到达了集合的末尾，终止对元素的遍历。

### 增强for循环

增强for循环(也称for each循环)是**JDK1.5**以后出来的一个高级for循环，专门用来遍历数组和集合的。它的底层是一个Iterator迭代器，所以在遍历的过程中，不能对集合中的元素进行增删操作。

用for循环的方式简化了迭代器的写法。

格式：

```java
for(元素的数据类型  变量 : Collection集合or数组){ 
  	//写操作代码
}
```

它只能遍历Collection和数组，只进行遍历元素，不能在遍历的过程中对集合元素进行增删操作。

> 注意: 增强for循环必须有被遍历的目标，目标只能是Collection或者是数组。新式for仅仅作为遍历操作出现。

