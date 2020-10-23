[集合框架](#集合框架)

[Collection接口](#Collection接口)

[Iterator接口](#Iterator接口)

[增强for循环](#增强for循环)

[泛型](#泛型)

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

在程序开发中，经常需要遍历集合中的所有元素。针对这种需求，JDK专门提供了一个接口`java.util.Iterator`，主要用于迭代访问（即遍历）`Collection`中的元素，因此`Iterator`对象也被称为迭代器。

* **迭代**：即集合元素的通用获取方式。在取元素之前先要判断集合中有没有元素，如果有，就把这个元素取出来，继续在判断，如果还有就再取出出来。一直把集合中的所有元素全部取出。这种取出方式专业术语称为迭代。

想要遍历Collection，首先要获取该集合的迭代器。Collection接口中有一个方法iterator()，这个方法返回的就是迭代器的实现类对象。

* `public Iterator iterator()`: 获取集合对应的迭代器，用来遍历集合中的元素的。

Iterator接口的常用方法如下：

* `public E next()`:返回迭代的下一个元素。
* `public boolean hasNext()`:如果仍有元素可以迭代，则返回 true。

### 迭代器的使用步骤

（1）使用集合中的方法iterator()返回迭代器的实现类对象，使用Iterator接口接收(多态)。

Iterator<E> iterator()迭代器的泛型跟着集合走，集合是什么泛型，迭代器就是什么泛型。

（2）使用Iterator接口中的方法hasNext判断还有没有下一个元素。

（3）使用Iterator接口中的方法next取出集合中的下一个元素。

注意：如果集合中已经没有元素了，还继续使用迭代器的next方法，将会抛出java.util.NoSuchElementException异常

```java
public class fuction {
    public static void main(String[] args) {
        Collection<String> coll = new ArrayList<>();// 使用多态方式 创建对象
        coll.add("妖梦");// 添加元素到集合
        coll.add("幻风");
        coll.add("旧梦");
        Iterator it1 = coll.iterator();//使用迭代器 遍历   每个集合对象都有自己的迭代器
        while(it1.hasNext()){//判断是否有迭代元素
            System.out.println(it1.next());//获取迭代出的元素
        }
        System.out.println("========================");
        for(Iterator it2 = coll.iterator();it2.hasNext();){
            System.out.println(it2.next());//获取迭代出的元素
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

### 泛型

集合中是可以存放任意对象，把对象存储进集合后，他们会被提升成Object类型。当我们取出每一个对象，并进行相应的操作时，必须采用类型转换。

```java
public class GenericDemo {
	public static void main(String[] args) {
		Collection coll = new ArrayList();
		coll.add("abc");
		coll.add("itcast");
		coll.add(5);//由于集合没有做任何限定，任何类型都可以给其中存放
		Iterator it = coll.iterator();
		while(it.hasNext()){
			//需要打印每个字符串的长度,就要把迭代出来的对象转成String类型
			String str = (String) it.next();
			System.out.println(str.length());
		}
	}
}
```

程序在运行时发生了问题**java.lang.ClassCastException**为什么会发生类型转换异常呢？

我们来分析下：由于集合中什么类型的元素都可以存储，致取出时强转引发运行时ClassCastException。

Collection虽然可以存储各种对象，但实际上通常Collection只存储同一类型对象。例如都是存储字符串对象。因此在JDK5之后，新增了泛型(Generic)语法，让你在设计API时可以指定类或方法支持泛型，这样我们使用API的时候也变得更为简洁，并得到了编译时期的语法检查。

**泛型**：泛型是一种未知的数据类型，当我们不知道使用什么数据类型的时候，可以使用泛型。通过泛型可以在类或方法中预支地使用未知的类型。

创建集合对象时，就会确定泛型的数据类型：把数据类型作为参数传递，赋值给泛型。

当没有指定泛型时，默认类型为Object类型。泛型也可以看成一个变量，用来接收数据类型。

### 使用泛型的好处

创建集合对象时，若不使用泛型，默认类型为Object类型，好处是可以存储任意类型的数据。弊端是不安全，可能会引发异常。

```java
public class fuction {
    public static void main(String[] args) {
        Collection coll = new ArrayList();// 使用多态方式 创建对象
        coll.add("妖梦");// 添加元素到集合
        coll.add(100);
        coll.add("旧梦");
        Iterator it1 = coll.iterator();//使用迭代器 遍历   每个集合对象都有自己的迭代器
        while(it1.hasNext()){
            Object obj = it1.next();//取出元素也是Object类型。
            System.out.println(obj);//获取迭代出的元素
            //想要使用String特有的方length获取字符串的长度，需要向下转型。
            /*会抛出ClassCastException，不能把Integer类型转换为String类型。100没法强转成字符串。
             */
            String s = (String)(obj);
            System.out.println(s.length());
        }
    }
}
```
使用泛型的好处：

(1) 将运行时期的ClassCastException，转移到了编译时期变成了编译失败。

当集合明确类型后，存放类型不一致就会编译报错。

(2）避免了类型强转的麻烦。

控制元素类型后，就不需要强转了。

泛型的弊端：泛型是什么类型，就只能存储什么类型的数据了，

```java
public class GenericDemo2 {
	public static void main(String[] args) {
        Collection<String> list = new ArrayList<String>();
        list.add("abc");
        list.add("itcast");
        // list.add(5);//当集合明确类型后，存放类型不一致就会编译报错
        // 集合已经明确具体存放的元素类型，那么在使用迭代器的时候，迭代器也同样会知道具体遍历元素类型
        Iterator<String> it = list.iterator();
        while(it.hasNext()){
            String str = it.next();
            //当使用Iterator<String>控制元素类型后，就不需要强转了。获取到的元素直接就是String类型
            System.out.println(str.length());
        }
	}
}
```

tips:泛型是数据类型的一部分，我们将类名与泛型合并一起看做数据类型。

### 泛型的定义与使用

我们在集合中会大量使用到泛型，用来灵活地将数据类型应用到不同的类、方法、接口当中。将数据类型作为参数进行传递。想要使用泛型就必须先定义它。

### 含有泛型的类

定义格式：

```
修饰符 class 类名<代表泛型的变量> {  }
```

```java
class ArrayList<E>{ 
    public boolean add(E e){ }

    public E get(int index){ }
   	....
}
```

使用泛型： 即什么时候确定泛型。

**在创建对象的时候确定泛型**

 例如，`ArrayList<String> list = new ArrayList<String>();`

此时，变量E的值就是String类型,那么我们的类型就可以理解为：

```java 
class ArrayList<String>{ 
     public boolean add(String e){ }

     public String get(int index){  }
     ...
}
```

自定义泛型类

```java
public class MyGenericClass<Generic> {
	//没有MVP类型，在这里代表 未知的一种数据类型 未来传递什么就是什么类型
	private Generic name;

    public Generic getName() {
        return name;
    }

    public void setName(Generic name) {
        this.name = name;
    }
}
```

使用:

```java
public class GenericClassDemo {
  	public static void main(String[] args) {		 
        // 创建一个泛型为String的类
        MyGenericClass<String> mg = new MyGenericClass();
        mg.setName("sad");//调用setname
        //创建一个泛型为Integer的类
        MyGenericClass<Integer> mg2 = new MyGenericClass();
        mg2.setName(11);	  
        Integer i2 = mg2.getName();
    }
}
```

###  含有泛型的方法

定义格式：

```
修饰符 <代表泛型的变量> 返回值类型 方法名(参数列表){}
```
```java
public class MyGenericMethod{
    //含有泛型的普通成员方法
    public <E> void method(E e){
        System.out.println(e);
    }

    //含有泛型的静态方法
    public static <E> E method2(E e){
        System.out.println(e);
        return e;
    }
}
```

使用格式：**调用方法时，确定泛型的类型**

```java
public static void main(String[] args){
    MyGenericMethod mg = new MyGenericMethod();
    mg.method("含有泛型的普通成员方法");
    MyGenericMethod.method2("含有泛型的静态方法");
}
```

### 含有泛型的接口

定义格式：

```
修饰符 interface接口名<代表泛型的变量> {  }
```
```java
public interface MyGenericInterface<E>{
	public abstract void add(E e);
	public abstract E getE();  
}
```

使用格式：

**（1）定义类时确定泛型的类型**

例如定义接口的实现类，指定接口的泛型。Scanner就实现了Iterator迭代器，指定接口的泛型为String。此时，泛型E的值就是String类型。所以Scanner重写的next方法泛型默认就是String。

```java
public final class Scanner implements Iterator<String>, Closeable {
    ...
    public String next() {
        ensureOpen();
        clearCaches();
        modCount++;
        while (true) {
            String token = getCompleteTokenInBuffer(null);
            if (token != null) {
                matchValid = true;
                skipped = false;
                return token;
            }
            if (needInput)
                readInput();
            else
                throwFor();
        }
    }
    ...
}
```

**（2）始终不确定泛型的类型，直到创建对象时，确定泛型的类型**

 接口使用什么泛型，实现类就使用什么泛型，实现类跟着接口走。相当于定义了一个含有泛型的类，创建对象的时候确定泛型的类。

例如，List接口有个泛型叫E，它的add方法使用了泛型E。

ArrayList实现了List接口，List接口用了泛型E，ArrayList用的也是E，泛型一样。而ArrayList的add方法用的泛型还是E。

```java
public class ArrayList<E> extends AbstractList<E>
        implements List<E>, RandomAccess, Cloneable, java.io.Serializable
{
    ...
}

public interface List<E> extends Collection<E> {
    ...
    boolean add(E e);
    ...
}

```


```java
public interface MyInterfaceE <E>{
    public abstract void method(E e);
}

//注意实现类的泛型变量只要和接口的名字一样即可，名字可以任意取，不需要和接口类定义时的泛型变量名相同。
public class MyEImpl<I> implements MyInterfaceE<I>{
    @Override
    public void method(I i) {
        System.out.println(i);
    }
}

public class Test {
    public static void main(String[] args){

        MyEImpl<Integer> me = new MyEImpl();
        me.method(5);

        MyEImpl<Double> me2 = new MyEImpl();
        me2.method(5.0);
    }
}
```

### 泛型通配符

代表任意的数据类型。不能创建对象使用，只能作为方法的参数使用。

当使用泛型类或者接口时，传递的数据中，泛型类型不确定，可以通过通配符<?>表示。但是一旦使用泛型的通配符后，只能使用Object类中的共性方法，集合中元素自身方法无法使用。

### 通配符基本使用

泛型的通配符:**不知道使用什么类型来接收的时候,此时可以使用?,?表示未知通配符。**

此时只能接收数据,不能往该集合中存储数据。

通配符在定义时不能使用，作为参数传递时可以使用。

注意：泛型没有继承概念。Collection<Object> list = new ArrayList<String>();这种是错误的。

```java
public class Test {
    public static void main(String[] args){

        ArrayList<Integer> list1 = new ArrayList<>();
        list1.add(1);
        ArrayList<String> list2 = new ArrayList<>();
        list2.add("good");
        printArray(list1);
        printArray(list2);
        //ArrayList<?> list03 = new ArrayList<?>();会报错，因为通配符在定义时不能使用。
    }

    //？代表可以接收任意类型
    public static void printArray(ArrayList<?> list){
    //泛型没有继承概念，所以这里不能写成Object，会报错。
        //使用迭代器遍历集合
        Iterator<?> it = list.iterator();
        while(it.hasNext()){
            //it.next()方法,取出的元素是Object,可以接收任意的数据类型
            Object o = it.next();
            System.out.println(o);
        }
    }
}

```

#### 通配符高级使用----受限泛型

之前设置泛型的时候，实际上是可以任意设置的，只要是类就可以设置。但是在JAVA的泛型中可以指定一个泛型的上限和下限。

**泛型的上限**：

* **格式**： `类型名称 <? extends 类 > 对象名称`
* **意义**： `只能接收该类型及其子类`

**泛型的下限**：

- **格式**： `类型名称 <? super 类 > 对象名称`
- **意义**： `只能接收该类型及其父类型`

比如：现已知Object类，String 类，Number类，Integer类，其中Number是Integer的父类

```java
public static void main(String[] args) {
    Collection<Integer> list1 = new ArrayList<Integer>();
    Collection<String> list2 = new ArrayList<String>();
    Collection<Number> list3 = new ArrayList<Number>();
    Collection<Object> list4 = new ArrayList<Object>();
    
    getElement(list1);
    getElement(list2);//报错
    getElement(list3);
    getElement(list4);//报错
  
    getElement2(list1);//报错
    getElement2(list2);//报错
    getElement2(list3);
    getElement2(list4);
  
}
// 泛型的上限：此时的泛型?，必须是Number类型或者Number类型的子类
public static void getElement1(Collection<? extends Number> coll){}
// 泛型的下限：此时的泛型?，必须是Number类型或者Number类型的父类
public static void getElement2(Collection<? super Number> coll){}
```
