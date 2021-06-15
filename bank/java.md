[深拷贝和浅拷贝](#深拷贝和浅拷贝)

[三元表达式](#三元表达式)

[lambda表达式](#lambda表达式)

### 深拷贝和浅拷贝

java的数据类型有基本类型和引用类型。

浅拷贝就是拷贝一份对象的引用，对新对象的修改，会改变原来的对象。因为两者指向的是同一个内存地址。

深拷贝就是创建一个新的对象，并复制其内容。新的对象会使用不同的内存地址，所以对新的对象的修改不会影响原来的对象。

Object中本地clone()方法，默认是浅拷贝。实现深拷贝有两种方式：

(1)实现Cloneable接口，重写clone方法。Cloneable仅仅是标识作用，如果没有实现这个接口，调用clone方法会抛出CloneNotSupportedException异常。

(2)通过序列化实现深拷贝。

层次调用clone方法可以实现深拷贝，但代码量实在太大，每个类都要重写clone方法太过繁琐。使用序列化实现深拷贝更好。

注意java中可以通过引用去改变对象的状态，但因为是值传递，所以无法修改对象的地址，因此无法指向另一个对象。

### 三元表达式

用来完成简单的选择逻辑：根据条件判断，从两个选择中选择一种执行。
```
条件表达式 ? 表达式A : 表达式B;

条件表达式为true，就执行表达式A，否则执行表达式B。
```

### lambda表达式

在数学中，函数就是有输入量和输出流的一套计算方案。

面对对象的思想是：找一个解决这个事情的对象，调用对象的方法。

相对来说，面向对象过分强调“必须通过对象的形式来做事情”。函数式思想就是尽量忽略面向对象的复杂语法，强调做什么，而不是以什么形式做。重视的是结果，不重视过程。

#### 例子

以通过实现Runnable接口来实现多线程为例，Thread类需要以Runnable接口作为参数，为了指定run的方法体，需要Runnable接口的实现类或者使用匿名内部类。

但是这两种方式都需要覆盖重写抽象方法run。只是为了将run方法体内的代码传递给Thread类,才不得不创建一个对象。而实际上只有方法体是关键的，传递一段代码才是我们的目的。

**使用lambda表达式可以简化匿名内部类的书写。同样的语义在lambda表达式中，要更加简单。**

**lambda表达式的标准格式**

```
(参数列表) -> {代码语句}

圆括号内是方法的参数，参数类型可以省略。

中间的箭头表示将前面的参数传递给后面的代码

大括号内是方法体，如果只有一条语句，可以把大括号，return，和语句的分号一起省略。
```

```java
public class DemoLambda {

    public static void main(String[] args) {

        //匿名内部类的方式，实现多线程
        new Thread(new Runnable() {
            @Override
            public void run() {
                System.out.println(Thread.currentThread().getName());
            }
        }).start();

        //lambda表达式，实现多线程
        new Thread(() -> {
            System.out.println(Thread.currentThread().getName());
        }).start();


        //lambda表达式，实现多线程
        new Thread(() -> System.out.println(Thread.currentThread().getName())).start();
    }
}
```

#### lambda的使用前提

使用lambda必须实现了接口，并且接口中有且仅有一个抽象方法。（有且仅有一个抽象方法的接口叫函数式接口）
