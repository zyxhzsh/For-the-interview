### Collections

java.utils.Collections是集合工具类，用来对集合进行操作。

[常用方法](#常用方法)

[Comparator比较器](#Comparator比较器)

[Comparable和Comparator的区别](#Comparable和Comparator接口的区别)

### 常用方法

public static <T> boolean addAll(Collection<T> c, T... elements):往Collection集合中添加一些元素。相当于集合对象依次调用add方法。是否有序取决于集合是否是有序的集合。

public static void shuffle(List<?> list) :打乱List集合顺序。

public static <T> void sort(List<T> list):将List集合中元素按照默认规则排序。（重写Comparable接口中的compareTo方法来定义默认规则）

public static <T> void sort(List<T> list，Comparator<? super T> ):将List集合中元素按照指定规则排序。

**注意**：排序时，如果按照排序规则，有两个元素一样大，那么两个元素的相对顺序不会变。
```java
public class fuction {
    public static void main(String[] args) {
        ArrayList<String> list = new ArrayList<String>();
        Collections.addAll(list,"abc","wow", "ice","sn");//ArrayList有序，所以按输入顺序有序存储
        System.out.println(list);
        Collections.sort(list);//默认正序排序
        System.out.println(list);
        Collections.sort(list, new Comparator<String>(){//自定义按第一个字符逆序排序
            public int compare(String o1, String o2){
                return o2.charAt(0)-o1.charAt(0);
            }
        });
        System.out.println(list);
    }
}
结果：
[abc, wow, ice, sn]
[abc, ice, sn, wow]
[wow, sn, ice, abc]
```

### Comparator比较器

public static <T> void sort(List<T> list):将集合中元素按照默认规则排序。

该方法使用的是默认的规则完成字符串的排序，那么默认规则是怎么定义出来的呢？

说到排序了，简单的说就是两个对象之间比较大小，那么在JAVA中提供了两种比较实现的方式：

（1）一种是比较死板的采用java.lang.Comparable接口去实现。通过重写Comparable接口的compareTo方法来自定义排序规则。
标准库中的类，用户不能修改类里的compareTo方法。只有自定义的类，用户才能重写compareTo方法，来自定义比较的规则。
但这样把规则写死了，每次想修改排序规则，就要修改类的源码中的compareTo方法。

```java
public class fuction {
    public static void main(String[] args) {
        ArrayList<MyPerson> list =  new ArrayList<>();
        //按照输入有序存储
        Collections.addAll(list,new MyPerson("张三",18),
                new MyPerson("李四",20),new MyPerson("王五",15));

        System.out.println(list);
        Collections.sort(list);//按照重写的CompareTo排序，重写的是按年龄逆序。
        System.out.println(list);
    }
}
结果如下：
[MyPerson{name='张三', age=18}, MyPerson{name='李四', age=20}, MyPerson{name='王五', age=15}]
[MyPerson{name='李四', age=20}, MyPerson{name='张三', age=18}, MyPerson{name='王五', age=15}]
public class MyPerson implements Comparable<MyPerson>{
    private String name;
    private int age;

    public MyPerson() {
    }

    public MyPerson(String name, int age) {
        this.name = name;
        this.age = age;
    }

    @Override
    public int compareTo(MyPerson o) {
        //return 0;//认为元素都是相同的
        //自定义比较的规则,比较两个人的年龄
        //return this.age-o.age;////年龄升序排序
        return o.age-this.age;////年龄降序排序
    }

    @Override
    public String toString() {
        return "MyPerson{" +
                "name='" + name + '\'' +
                ", age=" + age +
                '}';
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

}
```
public static <T> void sort(List<T> list)这个方法，被排序的类型需要实现Comparable接口完成比较的功能。例在String类型上如下：

String类实现了这个接口，并完成了比较规则的定义，但是这样就把这种规则写死了。
```java
public final class String implements java.io.Serializable, Comparable<String>, CharSequence {
```
如果想要按照自定义的规则排序，不可能去修改String的源代码。

（2）一种灵活的方法是：当我需要做排序的时候再去选择java.util.Comparator接口来完成。

public static <T> void sort(List<T> list，Comparator<? super T> )方法需要实现Comparator接口，排序是comparator能实现的功能之一,该接口代表一个比较器，比较的方法就是compare(T o1, ST o2)。T是泛型类型参数名。

```
public interface Comparator<T> {
    ...
    public int compare(T o1, ST o2)：比较其两个参数的顺序。
    ...
}
```
> 两个对象比较的结果有三种：大于，等于，小于。
> 如果要按照升序排序，
  > 则o1小于o2时返回负数，相等返回0，大于返回正数。
> 如果要按照降序排序
  > 则o1小于o2时返回正数，相等返回0，大于返回负数。

```java
public class Demo03Sort {
    public static void main(String[] args) {
    
        ArrayList<Student> list02 = new ArrayList<>();
        list02.add(new Student("a迪丽热巴",18));
        list02.add(new Student("古力娜扎",20));
        list02.add(new Student("杨幂",17));
        list02.add(new Student("b杨幂",18));
        System.out.println(list02);

        /*Collections.sort(list02, new Comparator<Student>() {
            @Override
            public int compare(Student o1, Student o2) {
                //按照年龄升序排序
                return o1.getAge()-o2.getAge();
            }
        });*/

        //扩展：先按照年龄升序排序，若年龄相同，再使用姓名的第一个字比较
        Collections.sort(list02, new Comparator<Student>() {
            @Override
            public int compare(Student o1, Student o2) {
                //按照年龄升序排序
                int result =  o1.getAge()-o2.getAge();
                //如果两个人年龄相同,再使用姓名的第一个字比较
                if(result==0){
                    result =  o1.getName().charAt(0)-o2.getName().charAt(0);
                }
                return  result;
            }

        });

        System.out.println(list02);
    }
}
结果如下：
[Student{name='a迪丽热巴', age=18}, Student{name='古力娜扎', age=20}, Student{name='杨幂', age=17}, Student{name='b杨幂', age=18}]
[Student{name='杨幂', age=17}, Student{name='a迪丽热巴', age=18}, Student{name='b杨幂', age=18}, Student{name='古力娜扎', age=20}]


public class Student {
    private String name;
    private int age;

    public Student() {
    }

    public Student(String name, int age) {
        this.name = name;
        this.age = age;
    }

    @Override
    public String toString() {
        return "Student{" +
                "name='" + name + '\'' +
                ", age=" + age +
                '}';
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
}
```
### Comparable和Comparator接口的区别

**Comparable**：必须重写compareTo方法时，强行对实现它的每个类的对象进行整体排序。这种排序被称为类的自然排序，类的compareTo方法被称为它的自然比较方法。只能在类中实现compareTo()一次，不能经常修改类的代码实现自己想要的排序。实现此接口的List对象列表（和数组）可以通过Collections.sort（和Arrays.sort）进行自动排序，对象可以用作有序映射中的键或有序集合中的元素，无需指定比较器。

**Comparator**强行对某个对象进行整体排序。可以将Comparator 传递给sort方法（如Collections.sort或 Arrays.sort），从而允许在排序顺序上实现精确控制。还可以使用Comparator来控制某些数据结构（如有序set或有序映射）的顺序，或者为那些没有自然顺序的对象collection提供排序。

个人理解：

使用Comparable接口，进行排序的类本身是Comparable接口的实现类；

使用Comparator接口，进行排序的类使用Comparable接口的匿名内部类作为参数，进行排序的类本身不是Comparator接口的实现类。
