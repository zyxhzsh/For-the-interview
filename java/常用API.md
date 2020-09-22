API(Application Programming Interface)，应用程序编程接口。Java API是一本程序员的字典 我们使用的类的说明文档。这些类将底层的代码实现封装了起来，我们不需要关心这些类是如何实现的，只需要学 习这些类如何使用即可。所以我们可以通过查询API的方式，来学习Java提供的类，并得知如何使用它们。JDK提供的类也是在一个个的包中。

[Scanner](#Scanner类)
[Random类](#Random类)
[](#)
[](#)
[](#)
[](#)

#### 引用数据类型使用步骤

1.导包

在类的所有代码之前导包，引入要使用的数据类型，java.lang包下的所有类无需导入。 
```
import 包名.类名;
```    
2.创建对象

使用该类的构造方法，创建一个该类的对象。
```
数据类型 变量名 = new 数据类型(参数列表);
```
3.调用方法

调用该类的成员方法，完成指定功能，格式。
```
变量名.方法名();
```

#### Scanner类

能够解析基本类型和字符串的简单文本扫描器。

比较常用的是：实现键盘输入数据到程序中。

```java
//1导包
import java.util.Scanner;

public class Demo01Scanner {
    public static void main(String[] args) {
        //2创建,System.in表示从键盘输入
        Scanner sc = new Scanner(System.in);

        //3调用方法
        //获取键盘输入的一个int数字
        int num = sc.nextInt();
        System.out.println("输入的int数字是："+num);

        //获取键盘输入的一个字符串
        String str = sc.next();
        System.out.println("输入的字符串是："+str);
    }
}
```
#### Random类

构造方法
```
Random()
创建一个新的随机数生成器。

Random(long seed)
使用单个long种子创建一个新的随机数生成器。
```
成员方法
```
nextInt()
从这个随机数生成器的序列返回下一个伪随机、均匀分布的整数值。

nextInt(int bound)
返回从该随机数生成器的序列中提取的、介于[0,bound)的伪随机、均匀分布的整数值。bound必须为正整数
```
```java
//导包
import java.util.Random;

public class Demo01Random {
    public static void main(String[] args) {
        //创建
        Random r = new Random();
        //使用
        int num1 = r.nextInt();//范围是int所有范围
        System.out.println("随机数是" + num1);
    }
}
```
**ex:**获取1-n之间的随机数，包含n
```java
    public static void main(String[] args) {
        randomN(new Scanner(System.in).nextInt());
    }

    private static void randomN(int n){
        Random r = new Random();
        int res;
        for (int i = 0; i < 100; i++) {
            res = r.nextInt(n)+1;//如果把+1写在里面，那么范围包括了0，不符合题目要求
            System.out.println("随机数字是"+res);
        }
    }
```
**ex:**猜数字，范围在1-100。**
```java
import java.util.Random;
import java.util.Scanner;

public class Demo01Random {
    public static void main(String[] args) {
        Random r = new Random();
        int randomNum = r.nextInt(100)+1;//生成一个随机数作为答案
        guessNum(randomNum);
    }

    private static void guessNum(int randomNum){
        int guess;
        Scanner sc = new Scanner(System.in);
        System.out.println("你一共能猜10次。请输入你要猜的数字：");
        int count=0;
        while(true){
            count++;
            guess=sc.nextInt();
            if(guess==randomNum){
                System.out.println("正确答案为："+ randomNum);
                System.out.println("你猜对了");
                break;
            }
            else if(guess>randomNum){
                System.out.println("猜大了");
            }
            else{
                System.out.println("猜小了");
            }
            if(count==10){
                System.out.println("你已用完十次机会，游戏失败");
                break;
            }
        }
    }
}
```
#### ArrayList

对象数组可以存储对象数据，但数组的长度是固定的，无法适应数据变化的需求。Java提供了另一个容器java.util.ArrayList集合类,让我们可以更便捷的存储和操作对象数据。

ArrayList是List接口的大小可变数组的实现。ArrayList是泛型集合，也就是装在集合中的元素都是统一的某种引用类型。泛型只能是引用类型，不能是基本类型。

##### 构造方法
```
ArrayList()构造一个初始容量为10的空列表。

ArrayList(int initialCapacity)构造一个具有指定初始容量的空列表。

```
##### 创建ArrayList集合：
```
ArrayList<String>  list = new ArrayList<>();
从jdk7开始，右侧尖括号内部可以不写内容，但是尖括号本身还是要写的。
```
直接打印ArrayList集合，得到的不是地址而是内容。如果内容为空，得到的是空的中括号[]。

##### 常用的成员方法

添加元素
```
public boolean add(E e)将指定的元素添加到列表的尾部，参数的类型和泛型一致,
返回值表示添加是否成功。对于ArrayList来说，添加是一定成功的，所以返回值可以不用。
但对于其他集合来说，添加动作不一定成功。
    boolean success = list.add("first");
    System.out.println("添加是否成功"+ success);

public void add(int index, E element)将指定的元素插入到列表的指定位置，参数的类型和泛型一致。
```
获取元素
```
public E get(int index)获取列表中指定位置的元素，参数是索引编号，返回值是对应位置的元素。
```
删除元素
```
public E remove(int index)删除列表中指定位置的元素，参数是索引编号，返回值是被删的元素。
```
集合的长度
```
public int size()返回列表中元素个数。
```

##### 遍历ArrayList集合
```java
    for (int i = 0; i < list.size(); i++) {
        System.out.println(list.get(i));
    }
```

##### ArrayList集合存储基本类型数据

集合里保存的是地址值，但是基本类型的数据没有地址值。如果希望向ArrayList当中存储基本类型，必须存储基本类型对应的包装类。 
```
基本类型    包装类（引用类型，包装类都位于java.lang包下）
byte        Byte
short       Short
int         Integer     【特殊】
long        Long
float       Float
double      Double
char        Character   【特殊】
boolean     Boolean

从JDK 1.5+开始，支持自动装箱、自动拆箱。

自动装箱：基本类型 自动变成--> 包装类型
自动拆箱：包装类型 自动变成--> 基本类型
```
