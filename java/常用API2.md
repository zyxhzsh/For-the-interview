### Object类

java.lang.Object类是Java语言中的根类，即所有类的父类。它中描述的所有方法子类都可以使用。在对象实例化的时候，最终找的父类就是Object。

如果一个类没有特别指定父类，那么默认则继承自Object类。例如：

```java
public class MyClass /*extends Object*/ {
  	// ...
}
```
常用方法

public String toString()：返回该对象的字符串表示。

public boolean equals(Object obj)：指示其他某个对象是否与此对象“相等”。

### toString方法

public String toString()

toString方法返回该对象的字符串表示，其实该字符串内容就是对象的类型+@+内存地址值。

由于toString方法返回的结果是内存地址，而在开发中，经常需要按照对象的属性得到相应的字符串表现形式，因此也需要重写它。

在我们直接使用输出语句输出对象名的时候,其实通过该对象调用了其toString()方法。因此看一个类是否重写了toString()，直接打印这个类的对象即可。如果没有重写那么打印的是对象的地址值。

如果不希望使用toString方法的默认行为，则可以对它进行覆盖重写。例如自定义的Person类：

```java
    public class Person {  
        private String name;
        private int age;

        @Override
        public String toString() {
            return "Person{" + "name='" + name + '\'' + ", age=" + age + '}';
        }

        // 省略构造器与Getter Setter
    }

   public static void main(String[] args) {
        Scanner sc = new Scanner(System.in);
        System.out.println(sc);//打印的不是地址，说明Scanner重写了toString()方法。

        ArrayList<Integer> list = new ArrayList<>();
        list.add(1);
        list.add(2);
        list.add(3);
        System.out.println(list);//打印的不是地址，说明Scanner重写了toString()方法。
    }
```

### equals方法

public boolean equals(Object obj)

判断其他某个对象是否与此对象“相等”。

==：基本数据类型比较的是值，引用数据类型比较的是两个对象的地址值。

调用成员方法equals并指定参数为另一个对象，则可以判断这两个对象是否是相同的。这里的“相同”有默认和自定义两种方式。

（1）默认方法

比较对象的地址，只要不是同一个对象，结果必然为false。

```java
### 默认的equals方法
public boolean equals(Object obj) {
    return (this == obj);
}
```
（2）对象内容比较

如果希望进行对象的内容比较，即所有或指定的部分成员变量相同就判定两个对象相同，则可以覆盖重写equals方法。

注意：equals的参数类型为Object，隐含着多态，无法使用子类特有的内容。例如：
```
Person p1 = new Person("张三",18);
Person p2 = new Person("李四",18);
p1.equals(p2)//形参Object obj=p2=new Person("李四",18);
```
可以使用向下转型把obj的类型转换为Person。为了防止类型转换异常，需要先进行判断。
```java
    @Override
    public boolean equals(Object obj) {
        if(obj instanceof Person){
            //使用向下转型，把obj转换为Person类型
            Person p =(Person)obj;
            //比较两个对象的属性
            return this.name.equals(p.name) && this.age==p.age;
        }else{
            return false;
        }
    }
```
