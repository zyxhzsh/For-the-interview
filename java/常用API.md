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
