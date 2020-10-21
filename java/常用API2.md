### Object类

`java.lang.Object`类是Java语言中的根类，即所有类的父类。它中描述的所有方法子类都可以使用。在对象实例化的时候，最终找的父类就是Object。

如果一个类没有特别指定父类，	那么默认则继承自Object类。例如：

```java
public class MyClass /*extends Object*/ {
  	// ...
}
```

* `public String toString()`：返回该对象的字符串表示。
* `public boolean equals(Object obj)`：指示其他某个对象是否与此对象“相等”。

### toString方法

* `public String toString()`：返回该对象的字符串表示。

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
