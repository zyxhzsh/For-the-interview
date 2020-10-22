[Object类](#Object类)

[Objects类](#Objects类)

[Date类](#Date类)

[DateFormat类](#DateFormat类)

[Calendar类](#Calendar类)

[System类](#System类)

[StringBuilder类](#StringBuilder类)

[包装类](#包装类)

### 目标

能够说出Object类的特点

能够重写Object类的toString方法

能够重写Object类的equals方法

能够使用日期类输出当前日期

能够使用将日期格式化为字符串的方法

能够使用将字符串转换成日期的方法

能够使用System类的数组复制方法

能够使用System类获取当前毫秒时刻值

能够说出使用StringBuilder类可以解决的问题

能够使用StringBuilder进行字符串拼接操作

能够说出8种基本类型对应的包装类名称

能够说出自动装箱、自动拆箱的概念

能够将字符串转换为对应的基本类型

能够将基本类型转换为对应的字符串

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
还可以优化，提高效率

判空：if(obj==null)  return false;

判断参数obj是否为this本身：if(obj==this)  return true;

idea生成的代码:
```java
@Override
    public boolean equals(Object obj) {
        // 如果对象地址一样，则认为相同
        if (this == obj)
            return true;
        /* 如果参数为空，或者类型信息不一样，则认为不同
        getClass() != obj.getClass()使用反射技术，判断obj是否为Person类型。等效于obj instanceof Person*/
        if (obj == null || getClass() != obj.getClass())
            return false;
        // 转换为当前类型
        Person person = (Person) obj;
        // 要求基本类型相等，并且将引用类型交给java.util.Objects类的equals静态方法取用结果
        return age == person.age && Objects.equals(name, person.name);
    }
```
### Objects类

JDK7中添加了一个Objects工具类，它提供了一些方法来操作对象，它由一些静态的实用方法组成，这些方法是null-save（空指针安全的）或null-tolerant（容忍空指针的），用于计算对象的hashcode、返回对象的字符串表示形式、比较两个对象。

null是不能调用Object.equals()方法的。在比较两个对象的时候，Object的equals方法容易抛出空指针异常，而Objects类中的equals方法就优化了这个问题。

源码如下：
~~~java
public static boolean equals(Object a, Object b) {  
    return (a == b) || (a != null && a.equals(b));  
}
~~~

### 日期时间类

### Date类

java.util.Date类：表示日期和时间的类。表示特定的瞬间，精确到毫秒。1毫秒=1/1000秒。

毫秒值的作用：可以对时间和日期进行计算。把日期转换为毫秒进行计算，计算完毕再把毫秒转换为日期。

时间原点(0毫秒)：1970年1月1日00:00:00(时，分，秒)。把日期转换为毫秒就是计算当前日期到时间原点之间一共经历了多少毫秒。

**构造方法**

public Date()：获取当前系统的日期和时间。

public Date(long date)：传递毫秒值，把毫秒值转换为Date日期。

> tips: 由于我们处于东八区，所以我们的基准时间为1970年1月1日8时0分0秒。
```java
    public static void main(String[] args) {
        //System.out.println(System.currentTimeMillis());
        Date date1 = new Date();
        Date date2 = new Date(100L);
        System.out.println(date1);
        System.out.println(date2);
    }
输出：
Thu Oct 22 14:10:44 CST 2020
Thu Jan 01 08:00:00 CST 1970
/*
在使用println方法，会自动调用Date类中的toString方法。
Date类对Object类中的toString方法进行了覆盖重写，所以结果为指定格式的字符串。*/
```

**常用方法**

public long getTime()：把调用该方法的日期对象转换成对应的时间毫秒值。

### DateFormat类

java.text.DateFormat：是日期/时间格式化子类的抽象类，我们通过这个类可以帮我们完成日期和文本之间的转换,也就是可以在Date对象与String对象之间进行来回转换。

* **格式化**：按照指定的格式，从Date对象转换为String对象。

* **解析**：按照指定的格式，从String对象转换为Date对象。

### 构造方法

DateFormat是抽象类，不能直接使用，可以使用它的子类`java.text.SimpleDateFormat`。这个类需要一个模式（格式）来指定格式化或解析的标准。

构造方法为：

public SimpleDateFormat(String pattern)：用给定的模式和默认语言环境的日期格式符号构造SimpleDateFormat。

参数pattern是一个字符串，代表日期时间的自定义格式。

### 格式规则

常用的格式规则为：

| 标识字母（区分大小写） | 含义   |
| ----------- | ---- |
| y           | 年    |
| M           | 月    |
| d           | 日    |
| H           | 时    |
| m           | 分    |
| s           | 秒    |

模式中的字母不能更改，连接模式的符号可以改变。

创建SimpleDateFormat对象的代码如：

```java
import java.text.DateFormat;
import java.text.SimpleDateFormat;

public class Demo02SimpleDateFormat {
    public static void main(String[] args) {
        // 对应的日期格式如：2018-01-16 15:06:38
        DateFormat format = new SimpleDateFormat("yyyy-MM-dd HH:mm:ss");
    }    
}
```

### 常用方法

### format方法

格式化

public String format(Date date)：按照指定的模式，将Date对象格式化为符合模式的字符串。

使用步骤：

（1）创建SimpleDateFormat对象，构造方法中传递指定的模式。

（2）调用SimpleDateFormat对象中的format方法，按照构造方法中指定的模式，把Date日期格式化为符合模式的字符串。

```java
import java.text.DateFormat;
import java.text.SimpleDateFormat;
import java.util.Date;
/*
 把Date对象转换成String
*/
public class Demo03DateFormatMethod {
    public static void main(String[] args) {
        Date date = new Date();
        // 创建日期格式化对象,在获取格式化对象时可以指定风格
        DateFormat df = new SimpleDateFormat("yyyy年MM月dd日 HH时mm分ss秒");
        String str = df.format(date);
        System.out.println(date);
        System.out.println(str); 
    }
}
```

### parse方法

解析

public Date parse(String source)：把符合模式的字符串，解析为Date对象。

使用步骤：

（1）创建SimpleDateFormat对象，构造方法中传递指定的模式。

（2）调用SimpleDateFormat对象中的parse方法，把符合构造方法中模式的字符串，解析为Date对象。

注意：如果字符串和构造方法的模式不一样，那么程序就会抛出ParseException异常。

调用一个抛出了异常的方法，就必须处理这个异常，要么throws继续抛出这个异常，要么try catch自己处理。

如果主方法抛出异常，会把异常交给虚拟机处理。如果程序没有异常会正常执行，如果程序出现了问题，程序会终止并打印这个异常。
```java
import java.text.DateFormat;
import java.text.ParseException;
import java.text.SimpleDateFormat;
import java.util.Date;
/*
 把String转换成Date对象
*/
public class Demo04DateFormatMethod {
    public static void main(String[] args) throws ParseException {
        DateFormat df = new SimpleDateFormat("yyyy年MM月dd日 HH时mm分ss秒");
        Date date = df.parse("2020年10月22日 14时44分50秒");
        System.out.println(date); 
    }
}
```

### 计算出一个人已经出生了多少天

**思路：**

1.获取当前时间对应的毫秒值

2.获取自己出生日期对应的毫秒值

3.两个时间相减（当前时间–出生日期）

4.将毫秒值转换为天数。

**代码实现：**

```java
public class fuction {
    public static void main() throws Exception {
        System.out.println("请输入出生日期 格式 YYYY-MM-dd");
        // 获取出生日期,键盘输入
        String birthdayString = new Scanner(System.in).next();
        // 将字符串日期,转成Date对象
        // 创建SimpleDateFormat对象,写日期模式
        DateFormat df = new SimpleDateFormat("yyyy-MM-dd");
        // 调用方法parse,字符串转成日期对象
        Date birthdayDate = df.parse(birthdayString);
        // 获取今天的日期对象
        Date todayDate = new Date();
        // 将两个日期转成毫秒值然后相减
        long millisecond = todayDate.getTime() - birthdayDate.getTime();
        if (millisecond < 0){
            System.out.println("还没出生呢");
        } else {
            System.out.println(millisecond/1000/60/60/24);
        }
    }
}
```

### Calendar类

java.util.Calendar：是日历类，替换掉了许多Date的方法。

该类将所有可能用到的时间信息封装为静态成员变量，方便获取。日历类就是方便获取各个时间属性的。

它为特定瞬间与一组诸如YEAR, MONTH, DAT_OF_MONTH, HOUR等日历字段之间的转换提供了一些方法，并为操作日历字段提供了一些方法。
瞬间可用毫秒值表示，它时距历元的偏移量。

**获取方式**

Calendar为抽象类，Calendar类在创建对象时并非直接创建，而是通过静态方法getInstance()创建，该方法返回Calendar类的子类对象。该方法获取当前系统时间的一个日历。

public static Calendar getInstance()：使用默认时区和语言环境获得一个日历

```java
Calendar cal = Calendar.getInstance();//多态
```

### 常用方法

根据Calendar类的API文档，常用方法有：

- `public int get(int field)`：返回给定日历字段的值。
- `public void set(int field, int value)`：将给定的日历字段设置为给定值。
- `public void set(int year, int month, int date)`：同时设置年月日。
- `public abstract void add(int field, int amount)`：根据日历的规则，为给定的日历字段添加或减去指定的时间量。
- `public Date getTime()`：返回一个表示此Calendar时间值（从历元到现在的毫秒偏移量）的Date对象。

int field：日历类的字段，可以使用Calendar类的静态变量获取。

Calendar类中提供很多成员常量（静态成员变量），代表给定的日历字段：

| 字段值          | 含义                   |
| ------------ | -------------------- |
| YEAR         | 年                    |
| MONTH        | 月（从0开始，可以+1使用）       |
| DAY_OF_MONTH | 月中的天（几号）(也可用DATE)             |
| HOUR         | 时（12小时制）             |
| HOUR_OF_DAY  | 时（24小时制）             |
| MINUTE       | 分                    |
| SECOND       | 秒                    |
| DAY_OF_WEEK  | 周中的天（周几，周日为1，可以-1使用） |

注意：

西方星期的开始为周日，中国为周一。

在Calendar类中，月份的表示是以0-11代表1-12月。

日期是有大小关系的，时间靠后，时间越大。

#### get/set方法

get方法用来获取指定字段的值，set方法用来设置指定字段的值，代码使用演示：

public int get(int field)参数：传递指定的日历字段，返回值：日历字段代表的具体的值。

```java
import java.util.Calendar;

public class CalendarUtil {
    public static void main(String[] args) {
        Calendar cal = Calendar.getInstance();// 创建Calendar对象
        int year = cal.get(Calendar.YEAR); // 获取年 
        // 设置月
        int month = cal.get(Calendar.MONTH) + 1;
        // 设置日
        int dayOfMonth = cal.get(Calendar.DAY_OF_MONTH);
        System.out.print(year + "年" + month + "月" + dayOfMonth + "日");
    }    
}
```
public void set(int field, int value)：将给定的日历字段设置为给定值。
public void set(int year, int month, int date)：同时设置年月日。
```java
public class fuction {
    public static void main(String[] args) {
        Calendar cal = Calendar.getInstance();
        cal.set(Calendar.YEAR, 2000);
        System.out.println(cal.get(Calendar.YEAR));
        cal.set(9999,8,1);
        System.out.println(cal.get(Calendar.YEAR)+"年"+cal.get(Calendar.MONTH)+"月"+cal.get(Calendar.DATE)+"日");
    }
}
```

#### add方法

public abstract void add(int field, int amount)：把指定的字段增加或减少指定的值。

```java
public class fuction {
    public static void main(String[] args) {
        Calendar cal = Calendar.getInstance();
        cal.set(Calendar.YEAR, 2000);
        System.out.println(cal.get(Calendar.YEAR));
        cal.add(Calendar.YEAR, -100);//把年减少100年
        System.out.println(cal.get(Calendar.YEAR));
    }
}
```

#### getTime方法

Calendar中的getTime方法并不是获取毫秒时刻，而是拿到对应的Date对象。

把日历对象转换为日期对象。

```java
public class fuction {
    public static void main(String[] args) {
        Date date = new Date();
        System.out.println(date);
        Calendar cal = Calendar.getInstance();
        Date trans = cal.getTime();//把日历对象转换为日期对象。
        System.out.println(trans);
    }
}
```

### System类

`java.lang.System`类中提供了大量的静态方法，可以获取与系统相关的信息或系统级操作，常用的方法有：

- `public static long currentTimeMillis()`：返回以毫秒为单位的当前时间。
- `public static void arraycopy(Object src, int srcPos, Object dest, int destPos, int length)`：将数组中指定的数据拷贝到另一个数组中。

### currentTimeMillis方法

实际上，System.currentTimeMillis方法就是获取当前系统时间与1970年01月01日00:00点之间的毫秒差值

可以用来计算程序执行的时间。

例如：验证for循环打印数字1-9999所需要使用的时间（毫秒）

~~~java
public class fuction {
    public static void main(String[] args) {
        long a = System.currentTimeMillis();
        for(int i=1;i<=9999;i++){
            System.out.println(i);
        }
        long b = System.currentTimeMillis();
        System.out.println("共耗时："+(b-a)+"ms");
    }
}
~~~

### arraycopy方法

* `public static void arraycopy(Object src, int srcPos, Object dest, int destPos, int length)`：将数组中指定的数据拷贝到另一个数组中。

数组的拷贝动作是系统级的，性能很高。System.arraycopy方法具有5个参数，含义分别为：

| 参数序号 | 参数名称    | 参数类型   | 参数含义       |
| ---- | ------- | ------ | ---------- |
| 1    | src     | Object | 源数组        |
| 2    | srcPos  | int    | 源数组索引起始位置  |
| 3    | dest    | Object | 目标数组       |
| 4    | destPos | int    | 目标数组索引起始位置 |
| 5    | length  | int    | 复制元素个数     |

例题：

将src数组中前3个元素，复制到dest数组的前3个位置上复制元素前：src数组元素[1,2,3,4,5]，dest数组元素[6,7,8,9,10]复制元素后：src数组元素[1,2,3,4,5]，dest数组元素[1,2,3,9,10]

```java
public class Demo11SystemArrayCopy {
    public static void main(String[] args) {
        int[] src = new int[]{1,2,3,4,5};
        int[] dest = new int[]{6,7,8,9,10};
        System.arraycopy( src, 0, dest, 0, 3);
        /*代码运行后：dest数组中的元素发生了变化
         src数组元素[1,2,3,4,5]
         dest数组元素[1,2,3,9,10]
        */
    }
}
```
