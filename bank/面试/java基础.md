1.深拷贝和浅拷贝

2.三元表达式

3.lambda表达式

4.死锁的条件，如何避免死锁

5.java表示各种进制

1.深拷贝和浅拷贝

java的数据类型有基本类型和引用类型。

浅拷贝就是拷贝一份对象的引用，对新对象的修改，会改变原来的对象。因为两者指向的是同一个内存地址。

深拷贝就是创建一个新的对象，并复制其内容。新的对象会使用不同的内存地址，所以对新的对象的修改不会影响原来的对象。

Object中本地clone()方法，默认是浅拷贝。实现深拷贝有两种方式：

(1)实现Cloneable接口，重写clone方法。Cloneable仅仅是标识作用，如果没有实现这个接口，调用clone方法会抛出CloneNotSupportedException异常。

(2)通过序列化实现深拷贝。

层次调用clone方法可以实现深拷贝，但代码量实在太大，每个类都要重写clone方法太过繁琐。使用序列化实现深拷贝更好。

注意java中可以通过引用去改变对象的状态，但因为是值传递，所以无法修改对象的地址，因此无法指向另一个对象。

2.三元表达式

用来完成简单的选择逻辑：根据条件判断，从两个选择中选择一种执行。
```
条件表达式 ? 表达式A : 表达式B;

条件表达式为true，就执行表达式A，否则执行表达式B。
```

3.lambda表达式

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

4.死锁的条件，如何避免死锁

死锁：多个线程循环等待资源，线程被无限期地阻塞，程序无法正常终止。

原因：资源的竞争，资源申请的顺序不合理。

死锁的四个必要条件
```
1. 互斥条件:该资源任意一个时刻只由一个线程占用。
2. 请求与保持条件:一个进程因请求资源而阻塞时，对已获得的资源保持不放。
3. 不剥夺条件:线程已获得的资源在末使用完之前不能被其他线程强行剥夺，只有自己使用完毕后才释放资源。
4. 循环等待条件:若干进程之间形成一种头尾相接的循环等待资源关系。
```
如何预防线程死锁
```
1. 破坏互斥条件 :这个条件我们没有办法破坏，因为我们用锁本来就是想让他们互斥的(临界 资源需要互斥访问)。
2. 破坏请求与保持条件 :一次性申请所有的资源。
3. 破坏不剥夺条件 :占用部分资源的线程进一步申请其他资源时，如果申请不到，可以主动释
放它占有的资源。
4. 破坏循环等待条件 :靠按序申请资源来预防。进程在申请资源时必须按照序号递增的顺序进行资源的申请，释放资源则反序释放。
破坏循环等待条件。
```

银行家算法:银行家算法是一种死锁避免算法，该算法允许进程动态申请资源。系统毎次在进行资源分配之前,先计算此次分配资源的安全性,若此次资源分不会导致系统进入不安全状态,则分配资源；
否则,不分配资源,让进程等待。银行家算法在避免死锁上非常有效，但是需要在进程运行前就知道其所需资源的最大值，且进程数也通常不是固定的。因此很难实现。

已分配给进程的资源：Allocation

进程总共需要的资源数：Claim

进程还需要的资源数：Need

资源池(空闲资源数)：Available

银行家算法分配系统资源的原则

(1)当一个进程对资源的最大需求量不超过系统中的资源数时可以接纳该进程。

(2)进程可以分期请求资源，但请求的总数不能超过最大需求量。

(3)当系统空闲的资源不能满足进程尚需资源数时，对进程的请求可以推迟分配，但总能使进程在有限的时间里得到资源。

(4)当系统空闲的资源能满足进程尚需资源数时，必须测试系统现存的资源能否满足该进程尚需的最大资源数，若能满足则按当前的申请量分配资源，否则也要推迟分配。

5.java表示各种进制

二进制0b：0b101

十六进制0x：0x101

八进制0：0101（java中八进制前缀只能用0，不能用0o）

十进制：不加任何前缀

十进制转二进制：

(1)api
```
String str =  Integer.toBinaryString(100);
```
(2)手动实现

正数，除以2求余数，余数倒序。

思路：判断是正数还是负数：

如果是正数：直接求

如果是负数：

（1）求出其对应的正数的二进制表示中，最左边连续的0的数量，记为a。然后求出负数对应的正数的二进制表示，并按位取反。

（2）新建一个StringBuilder对象，先存入a个字符'1'，然后再存入对应的正整数的二进制表示的按位取反。此时StringBuilder对象存的是该负数的反码。

（3）最后从低位到高位遍历StringBuilder对象，若当前元素为'1'，将当前元素设为'0'；若当前元素为'0'，将当前元素设为'1'并退出循环。循环结束后，StringBuilder对象存放的就是该负数的补码了。

```java
public class BaseConversion {


    public static String signedTenToOther(int num){

        if(num >= 0){
            return TenToOther(num);
        }else{
            int a = Integer.numberOfLeadingZeros(Math.abs(num));//负数对应的正数的二进制表示中，最左边开始连续的0的数量a
            StringBuilder sb = new StringBuilder();

            //先求反码，高位一定是连续的a个0
            for(int i=0;i<a;i++){
                sb.append('1');
            }
            //求出对应的正数的二进制表示并按位取反
            String str = TenToOther(Math.abs(num));
            for(int i=0;i<str.length();i++){
                if(str.charAt(i) == '1'){
                    sb.append('0');
                }else{
                    sb.append('1');
                }
            }
            //System.out.println("反码"+sb.toString());

            //此时sb中存的是该负数的反码，然后给他加1就得到补码了。
            for(int i=sb.length()-1;i>=0;i--){
                if (sb.charAt(i) - '0' == 0){
                    sb.setCharAt(i, '1');
                    break;
                }else{
                    sb.setCharAt(i, '0');
                }
            }
            return sb.toString();
        }
    }

    //正数求二进制
    public static String TenToOther(int num){

        int base = 2;
        StringBuilder sb = new StringBuilder();
         while(num != 0){
             sb.append(num%base);
             num = num/base;
         }
         sb.reverse();
         return sb.toString();
    }

    //用Integer.toBinaryString验证结果是否正确
    public static void main(String[] args) {

        System.out.println(Integer.numberOfLeadingZeros(101));
        String str1 = signedTenToOther(-101);
        String str2 =  Integer.toBinaryString(-101);
        System.out.println(str1);
        System.out.println(str2);
    }
}
```

