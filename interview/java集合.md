1.集合框架

2.List,Set,Map三者的区别

3.Arraylist，LinkedList与Vector的区别

4.RandomAccess接口

5.ArrayList的扩容机制

6.HashMap和Hashtable的区别

7.HashMap和HashSet区别

8.HashSet如何检查重复

9.HashMap的底层实现

10.HashMap的⻓度为什么是2的幂次方

11.HashMap多线程操作导致死循环问题

12.HashMap扩容

13.ConcurrentHashMap和Hashtable的区别

14.ConcurrentHashMap

15.HashSet、LinkedHashSet和TreeSet三者的异同

1.集合框架

```
Collection单列集合，存储一个个对象(size())
    List接口：有序，元素可重复。（add,remove,set,indexOf,get）
    Set接口：无序，元素不可重复。(add,remove,contains)
    Queue接口：模拟队列的一种数据结构。提供offer()和poll()方法。

List（add,remove,set,indexOf,get）
    ArrayList实现类：底层是数组，查询快，增删慢，线程不安全。
    LinkedList实现类：底层是链表，查询慢，增删快，线程不安全。(同时实现了List和Deque，它既有索引，是一个list，同时也是一个双端队列)
                    addFirst(),addLast(),getFirst(),getLast(),removeFirst(),removeLast()。
    Vector实现类：底层是数组，查询快，增删慢，线程安全。

Set (add,remove,contains)
    HashSet实现类（无序）：底层是HashMap，线程不安全，可以存储null值。
    LinkedHashSet实现类（有序）：HashSet的子类，底层是LinkedHashMap，能够按照添加的顺序遍历。
    TreeSet实现类（有序）：底层使用红黑树，能够按照添加元素的顺序进行遍历或者自定义排序遍历。

Queue(offer(),poll(),peek())
    PriorityQueue实现类：优先级队列，底层是二叉堆。默认为小根堆。对于大根堆，就要定义comparator来实现。
    Dueue接口：双端队列。LinkedList是Dueue的实现类。
```

```
Map双列集合，存储键值对。(put,remove,clear,get,containsKey,containsValue,Set keySet(),Collection values(),Set entrySet())
    HashMap：线程不安全,可以存储null的key和value。jdk8之前：数组+链表；之后：数组+链表+红黑树。
    LinkedHashMap：HashMap的子类，保证在遍历map元素时，按照添加的顺序遍历。对每个数据维护了两个引用，从而可以知道前后添加的元素。
    TreeMap：底层使用红黑树。保证按照添加的Key-value的顺序进行排序，也可以自定义排序遍历。
    Hashtable：线程安全，效率低。不能存储null的key和value。
    Properties：用来处理配置文件，key和value都是String类型。
```

2.List,Set,Map三者的区别

List：存储的元素是有序的，可重复的。

Set：存储的元素是无序的，不可重复的。

Map：使用键值对存储，它是双列集合。Key是无序的、不可重复的，value是无序的、可重复的。

3.Arraylist，LinkedList与Vector的区别

ArrayList实现类：底层是Object数组，查询快，增删慢，线程不安全。

LinkedList实现类：底层是双向链表，查询慢，增删快，线程不安全。JDK1.7之前是循环链表。

Vector实现类：底层是数组，查询快，增删慢，线程安全。

（1）LinkedList不支持高效的随机元素访问，而ArrayList可以。

（2）ArrayList 的空间浪费主要体现在list列表的结尾会预留一定的容量空间，而LinkedList的空间花费则体现在它的每一个元素都需要消耗比ArrayList 更多的空间。

（3）ArrayList有最大容量的，为Integer的最大值。LinkedList实际大小用的是int类型，这也说明了LinkedList不能超过Integer的最大值。

（4）ArrayList 会先计算并决定是否扩容，然后把新增的数据直接赋值到数组上，而LinkedList仅仅只需要改变插入节点和其前后节点的指
向位置关系即可。

4.RandomAccess接口

用来标识实现这个接口的类具有随机访问功能。

在binarySearch􏰁方法中，它要判断传入的list是否为RamdomAccess的实例，如果是，调用indexedBinarySearch方法，如果不是，那么调用 iteratorBinarySearch方法。

ArrayList实现了RandomAccess接口，就表明了他具有快速随机访问功能。 RandomAccess接口只是标识，并不是说ArrayList实现RandomAccess 接口才具有快速随机访问功能的。

5.ArrayList的扩容机制

ArrayList允许空值和重复元素。是一种变长的集合类，基于定长数组实现。

扩容底层使用的是 System.arraycopy 方法，会把原数组的数据全部拷贝到新数组上，所以性能消耗比较严重。
```
elementData是实际存放元素的数组。
静态常量DEFAULT_CAPACITY=10，是默认的初始化容量。
静态常量EMPTY_ELEMENTDATA是空数组，是在有参构造方法初始化一个空数组的时候使用。
静态常量DEFAULTCAPACITY_EMPTY_ELEMENTDATA是默认空数组，实在无参构造方法初始化时使用。当调用add方法添加第一个元素的时候，扩容至大小为DEFAULT_CAPACITY=10。
minCapacity：size+1
```
有参构造：若参数大于0，将elementData初始化为initialCapacity大小的数组；若参数等于0，elementData初始化为空数组；若参数小于0抛出异常。

无参构造：构造方法中将elementData初始化为空数组DEFAULTCAPACITY_EMPTY_ELEMENTDATA，当调用add方法添加第一个元素的时候，会扩容至大小为DEFAULT_CAPACITY=10。1.7之默认构造方法都是延迟初始化。

add扩容：newCapacity=oldCapacity+max(minCapacity - oldCapacity, oldCapacity/2)，也就是max(minCapacity,1.5oldCapacity)。

添加元素时，如果容量不足，就会先创建一个数组，容量为原来的1.5倍。若1.5倍的容量还小于minCapacity，就把容量设为minCapacity。

ArrayList最大容量为MAX_VALUE-8，主要是考虑到不同的JVM,有的JVM会加入一些数据头。但如果newCapacity>最大容量，那最大容量就变为Integer.MAX_VALUE。

6.HashMap和Hashtable的区别

（1）HashMap是非线程安全的，HashTable是线程安全的，HashMap比HashTable效率高一点。

（2）HashMap可以存储null的key和value，但null作为键只能有一个，null作为值可以有多个;HashTable不允许有null键和null值，否则会抛出 NullPointerException。

（3）Hashtable默认的初始大小为11，之后每次扩充，容量变为原来的2n+1。HashMap默认的初始化大小为16。之后每次扩充，容量变为原来的2倍。

如果给定了容量初始值，那么Hashtable会直接使用给定的大小，而HashMap会将其扩充为2的幂次方大小。(tableSizeFor()方法)

HashMap扩容后，数据要从原数组迁移到新数组。数据迁移时，数据要么在原来的位置，要么在原来位置+扩容长度。好处是不需要重新hash。如何判断是在原来位置还是新的位置：哈希值&旧数组容量，结果为0则在原位置，为1则在新位置。
```
    static final int tableSizeFor(int cap) {
        int n = cap - 1;
        n |= n >>> 1;
        n |= n >>> 2;
        n |= n >>> 4;
        n |= n >>> 8;
        n |= n >>> 16;
        return (n<0) ? 1 : (n >= MAXIMUM_CAPACITY) ? MAXIMUM_CAPACITY : n + 1;
    }
```
（4）JDK1.8以后的HashMap：链表⻓度大于阈值时(默认为8)，若当前数组的⻓度小于64，那么会先进行数组扩容。然后将链表转化为红黑树，以减少搜索时 间。Hashtable没有这样的机制。

7.HashMap和HashSet区别

HashSet底层是基于HashMap实现的，除了clone()、writeObject()、readObject()是HashSet自己不得不实现之外，其他方法都是直接调用 HashMap中的方法。

HashMap实现类Map接口，HashSet实现了Set接口。HashMap存储键值对，HashSet仅存储对象。

HashMap使用键(Key)计算hashcode；HashSet使用成员对象来计算hashcode值，对于两个对象来说hashcode可能相同，所以 equals() 方法用来判断对象的相等性。

8.HashSet如何检查重复

把对象加入HashSet时，HashSet会先计算对象的hashcode值来判断对象加入的位置，同时与集合中其他对象的hashcode值作比较，如果没有相同的hashcode值，就说明没有重复的对象。若有相同的hashcode值，会调用equals()方法来检查对象是否真的相等。如果两者相同，HashSet就不会让加入操作成功。

hashCode() 的默认行为是对堆上的对象产生独特值。如果没有重写hashCode() ，则该class的两个对象内容
相同时也不会相等。

9.HashMap的底层实现

JDK1.8之前HashMap底层是数组和链表结合在一起使用也就是链表散列。HashMap通过key的hashCode经过扰动函数处理过后得到hash值，然后通过 (n - 1) & hash 判断当前元素存放的位置(这里的n指的是数组的⻓度，该方法为indexFor)，如果当前位置存在元素的话，就判断该元素与要存入的元素的hash值以及key是否相同，如果相同的话，直接覆盖，不相同就通过拉链法解决冲突。

扰动函数：HashMap的hash方法，可以减少碰撞。h^(h>>>16)&HASH_BITS（）

拉链法：创建一个链表数组，数组中每一格就是一个链表。若遇到哈希冲突，则将冲突的值加到链表中即可。

JDK1.8以后的HashMap：链表⻓度大于阈值时(默认为8)，若当前数组的⻓度小于64，那么会先进行数组扩容。只有链表长度大于8且数组长度大于等于64时，才会转换成红黑树，减少查找的时间。

红黑树是为了解决二叉查找树的缺陷，因为二叉查找树在某些情况下会退化成一个线性结构。

10.HashMap的⻓度为什么是2的幂次方

为了尽量􏰀少碰撞，就要尽量把数据分配均匀。Hash值的范围是-2^31到2^31-1。大概40亿的映射空间，只要哈希函数映射得比􏰀均匀松散，一般应用是很难出现碰撞的。但内存放不下40亿⻓度的数组，所以这个散列值是不能直接拿来用的，用之前还要先做对数组的⻓度取模运算， 得到的余数才能作为要存放的位置，也就是对应的数组下标。这个数组下标的计算方法是(n - 1)&hash。

取余(%)操作中如果除数是2的幂次，则等价于与其除数减一的与(&)操作。也就是hash%length==hash&(length-1)，前提是length是2的n次方。

（1）采用二进制位操作&，相对于%能够提高运算效率。

（2）2的幂次能充分利用数组的每个角标位，不会有取不到值的地方。

（3）方便扩容时的数据迁移。

这就解释了HashMap的⻓度为什么是2的幂次方。

11.HashMap多线程操作导致死循环问题

并发下的Rehash会造成元素之间会形成一个循环链表。不过，jdk1.8后解决了这个问题。

但是多线程下使用HashMap还是会存在其他问题比如数据丢失，并发环境下推荐使用ConcurrentHashMap。

12.HashMap扩容

什么时候扩容？

jdk1.7：判断装载因子是否达到阈值(0.75\*数组长度)，且是否产生哈希冲突。若size达到阈值且产生哈希冲突，则先扩容为2倍，再添加元素。

jdk1.8：先添加元素，然后判断是否达到阈值，若达到就扩容为2倍。

怎么扩容？

jdk1.7：单向链表的数据迁移，元素添加方式是头插法。外层循环遍历数组的角标位，内层循环遍历单向链表，计算新的角标位。然后将当前元素的next指向新的数组中的新角标位，然后新的数组中的新角标位存放当前元素。

jdk1.8：如果对应角标位，将单向链表进行数据迁移，元素添加方式是尾插法；如果对应角标是红黑树，将双向链表进行数据迁移。TreeNode形成链表时，既记录上一个，也记录下一个，就是一个双向链表。用双向链表去进行树化。如果扩容的时候直接去操作红黑树，过程非常麻烦，但是操作双向链表就很简单。

如果数据迁移后，双向链表的结构小于等于6，会将红黑树重新转为单链表的结构。

为什么默认的阈值为8：链表长度达到8的概率是很低的。链表尽可能不去进行树化，因为树化对性能的消耗是很高的，要同时维护双向链表和红黑树。红黑树的平衡涉及到变色和选择，而变色和旋转是非常慢的。

jdk1.7扩容方式问题是：多线程的情况下，头插法可能会形成循环链表，导致内层循环死循环。例如线程1执行到next=e.next，然后失去了cpu。线程2执行完成，然后线程1继续往下执行。

jdk1.8扩容方式问题是：多线程情况下，尾插法可能产生数据丢失问题。例如线程1向1号角标存了10，线程2向1号角标存了12，10就被覆盖了。

13.ConcurrentHashMap和Hashtable的区别

ConcurrentHashMap和Hashtable的区别主要体现在实现线程安全的方式上不同。

底层数据结构：

JDK1.7的ConcurrentHashMap底层采用分段的数组+链表实现。JDK1.8的ConcurrentHashMap采用Node数组+链表/红黑二叉树实现。Hashtable采用数组+链表实现。

实现线程安全的方式：

JDK1.7的ConcurrentHashMap对整个桶数组进行了分割分段(Segment)，每一把锁只锁容器其中一部分数据，多线程访问 容器里不同数据段的数据，就不会存在锁竞争，提高并发访问率。segment继承自ReentrantLock。

JDK1.8用Node数组+链表+红黑树的数据结构来实现，采用CAS+Synchronized保证线程安全，尽量避免加锁。Node是最核心的内部类，它包装了key-value键值对，所有插入ConcurrentHashMap的数据都包装在这里面。它对value和next属性设置了volatile，锁住node来实现减小锁粒度。查询时间复杂度：从原来的遍历链表O(n)，变成遍历红黑树O(logN)。

Hashtable使用synchronized来保证线程安全，效率非常低。当一个线程访问同步方法时，其他线程也访问同步方法，可能会进入阻塞或轮询状态

14.ConcurrentHashMap

JDK1.7的CurrentHashMap的实现原理

Segment+链表实现。Segment类似于HashMap的结构，内部拥有一个HashEntry数组，数组中的每个元素又是一个链表,Segment继承了ReentrantLock。

所以Segment是一种可重入锁，扮演锁的⻆色。HashEntry用于存储键值对数据。

ConcurrentHashMap使用分段锁技术，将数据分成一段一段的存储，然后给每一段数据配一把锁，当一个线程占用锁访问其中一个段数据的时候，其他段的数据也能被其他线程访问，能够实现真正的并发访问。

ConcurrentHashMap定位一个元素的过程需要进行两次Hash操作，第一次Hash定位到Segment，第二次Hash定位到元素所在的链表的头部。

优点：写操作的时候可以只对元素所在的Segment进行加锁即可，不会影响到其他的Segment，这样，在最理想的情况下，ConcurrentHashMap可以最高同时支持Segment数量大小的写操作。

缺点：Hash的过程要比普通的HashMap要长。

JDK1.8的CurrentHashMap的实现原理

数组+链表+红黑树，采用CAS+Synchronized保证线程安全。synchronized只锁定当前链表或红黑二叉树的首节点，这样只要hash不冲突，就不会产生并 发。

cas是一种乐观锁，包含三个操作数：内存位置（V）、预期原值（A）和新值(B)。如果内存地址里面的值和A的值是一样的，就将内存里面的值更新成B。CAS是通过无限循环来获取数据的，如果在第一轮循环中，a线程获取地址里面的值被b线程修改了，那么a线程需要自旋，到下次循环才有可能机会执行。

JDK8中用Node保存key，value及key的hash值。其中value和next都用volatile修饰，保证并发的可见性。

JDK1.7采用segment的分段锁机制实现线程安全，其中segment继承自ReentrantLock。JDK1.8采用CAS+Synchronized保证线程安全。

原来是对需要进行数据操作的Segment加锁，现调整为对每个数组元素加锁（Node）。

查询时间复杂度：从原来的遍历链表O(n)，变成遍历红黑树O(logN)。

15.HashSet、LinkedHashSet和TreeSet三者的异同

HashSet是Set接口的主要实现类，HashSet的底层是HashMap，线程不安全的，可以存储null值;

LinkedHashSet是HashSet的子类，能够按照添加的顺序遍历。其内部是通过LinkedHashMap来实现的。

TreeSet底层使用红黑树，能够按照添加元素的顺序进行遍历，排序的方式有自然排序和自定义排序。
