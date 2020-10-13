[队列](#队列)

[算术左移、右移以及逻辑右移的区别](#算术左移_右移以及逻辑右移的区别)

[](#)

[](#)

[](#)

[](#)

[](#)

[](#)


### 队列

ArrayList是顺序结构，所以定位很快，但插入，删除数据慢。

LinkedList 是链表结构，定位慢，但插入，删除数据快。

ArrayList实现了List接口，常见方法有：

add(); contains(); get(); indexOf():定位对象所处的位置; remove(); size(); toArray(); toString();//转换为字符串

LinkedList也实现了List接口外，可以实现上述ArrayList中的常用方法，此外：

1.LinkedList还实现了双向链表结构Deque，可以很方便的在头尾插入删除数据。

LinkedList<class> link = new LinkedList<>();

常用方法：addFirst(); addLast(); getFirst(); getLast(); removeFirst(); removeLast();

2.LinkedList除了实现了List和Deque外，还实现了Queue接口(队列),Queue是先进先出队列 FIFO。

Queue<class> queue = new LinkedList<>();

常用方法：poll()取出第一个元素; peek()查看第一个元素; offer()在最后添加元素,可用add()替换;

先进后出FILO Stack栈：

Stack<class> stack = new Stack<>();

常用方法：push();可用add();代替 pop();输出末尾的元素相当于LinkedList中的removeLast(); peek();查看最后一个元素，相当于getLast();

### 算术左移_右移以及逻辑右移的区别

算数右移高位补符号位的数字，逻辑右移高位补0。

逻辑左移=算数左移，右边统一补0。

算术左移<<和算术右移>>主要用来进行有符号数的倍增、减半；

逻辑右移>>>主要用来进行无符号数的减半；

Java中是没有无符号数据类型的，负数是以补码形式存在的。

Integer.toBinaryString()可将整数转化为二进制；
