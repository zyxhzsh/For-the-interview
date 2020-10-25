[List集合](#List集合)

[ArrayList集合](#ArrayList集合)

[LinkedList集合](#LinkedList集合)

[Vector集合](#Vector集合)

### List集合

`java.util.List`接口继承自`Collection`接口，是单列集合的一个重要分支，习惯性地会将实现了`List`接口的对象称为List集合。

在List集合中允许出现重复的元素，所有的元素是以一种线性方式进行存储的，在程序中可以通过索引来访问集合中的指定元素。

另外，List集合还有一个特点就是元素有序，即元素的存入顺序和取出顺序一致。

**List接口特点**：

1. 元素存取有序的集合。例如，存元素的顺序是11、22、33。那么集合中，元素的存储就是按照11、22、33的顺序完成的）。

2. 带有索引的集合，通过索引就可以精确的操作集合中的元素（与数组的索引是一个道理）。

3. 集合中可以有重复的元素，通过元素的equals方法，来比较是否为重复的元素。

> tips:我们在基础班的时候已经学习过List接口的子类java.util.ArrayList类，该类中的方法都是来自List中定义。

### 常用方法

List接口不但继承了Collection接口中的全部方法，而且还增加了一些根据元素索引来操作集合的特有方法。

- `public void add(int index, E element)`: 将指定的元素，添加到该集合中的指定位置上。
- `public E get(int index)`:返回集合中指定位置的元素。
- `public E remove(int index)`: 移除列表中指定位置的元素, 返回的是被移除的元素。
- `public E set(int index, E element)`:用指定元素替换集合中指定位置的元素,返回值的更新前的元素。

注意：操作索引的时候，一定要防止索引越界异常。

### List的遍历方式

（1）普通的for循环

```
for (int i = 0; i < list.size(); i++) {
    System.out.println(list.get(i));
}
```
（2）使用迭代器
```java
Iterator it = list.iterator();
while(it.hasNext()){
    System.out.println(it.next());
}
```
（3）使用增强for循环
```java
for(String s:list){
    System.out.println(s);
}
```

### List的实现类

ArrayList、LinkedList、Vector

### ArrayList集合

ArrayList集合实现了动态的对象数组，它的底层是数组，元素增删慢，查找快。由于日常开发中使用最多的功能为查询数据、遍历数据，所以ArrayList是最常用的集合。

此实现不是同步的，即它时是多线程，意味着它的效率高，速度快。

缺点：先看add的源码

0 1 2 3
```
    public void add(int index, E element) {
        rangeCheckForAdd(index);
        modCount++;
        final int s;
        Object[] elementData;
        if ((s = size) == (elementData = this.elementData).length)
            elementData = grow();
        System.arraycopy(elementData, index,
                         elementData, index + 1,
                         s - index);
        elementData[index] = element;
        size = s + 1;
    }

当容量足够时，调用System.arraycopy在原数组上进行复制，把index后的都移一位，然后把indexs设为element；
当容量不够时调用grow()方法，而grow()方法调用了重载方法grow(int minCapacity)
grow(int minCapacity)
    private Object[] grow() {
        return grow(size + 1);
    }

    private Object[] grow(int minCapacity) {
        int oldCapacity = elementData.length;
        if (oldCapacity > 0 || elementData != DEFAULTCAPACITY_EMPTY_ELEMENTDATA) {
            int newCapacity = ArraysSupport.newLength(oldCapacity,
                    minCapacity - oldCapacity, /* minimum growth */
                    oldCapacity >> 1           /* preferred growth */);
            return elementData = Arrays.copyOf(elementData, newCapacity);
        } else {
            return elementData = new Object[Math.max(DEFAULT_CAPACITY, minCapacity)];
        }
    }

而grow(int minCapacity)中，当旧的容量不为0或旧的数组不为空时，调用了Arrays.copyOf(T[] original, int newLength)。
newCapacity=oldCapacity+max(minCapacity - oldCapacity, oldCapacity/2)。也就是max(minCapacity,1.5oldCapacity)这里oldCapacity/2更大，所以扩容为原来容量的1.5倍。
；否则分配DEFAULT_CAPACITY和minCapacity中的最大值。

    public static <T> T[] copyOf(T[] original, int newLength) {
        return (T[]) copyOf(original, newLength, original.getClass());
    }

Arrays.copyOf(T[] original, int newLength)方法，又调用了copyOf(参数列表)方法。

    public static <T,U> T[] copyOf(U[] original, int newLength, Class<? extends T[]> newType) {
        @SuppressWarnings("unchecked")
        T[] copy = ((Object)newType == (Object)Object[].class)
            ? (T[]) new Object[newLength]
            : (T[]) Array.newInstance(newType.getComponentType(), newLength);
        System.arraycopy(original, 0, copy, 0,
                         Math.min(original.length, newLength));
        return copy;
    }

copyOf(参数列表)方法调用了数组复制方法arraycopy(参数列表)。

public static native void arraycopy(Object src,  int  srcPos,Object dest, int destPos, int length);
```
因此添加元素时，如果容量不足，就会先创建一个数组，容量为原来的1.5倍。若1.5倍的容量还小于minCapacity，就把容量设为minCapacity。

然后把原数组的数据复制到新的数组中。所以容量不足时，增加元素效率低。而删除元素需要移动该元素之后的所有元素，效率也低。

结论：ArrayList更适合于快速的查找匹配，顺序添加很方便。删除和插入，容量不足时需要复制数组，然后要移动元素。

ArrayList最大容量为MAX_VALUE-8，主要是考虑到不同的JVM,有的JVM会在加入一些数据头。但如果newCapacity>最大容量，那最大容量就变为Integer.MAX_VALUE。

[ArrayList详解](https://www.cnblogs.com/fsmly/p/11283921.html)

[ArrayList面试题](https://blog.csdn.net/Fujie1997/article/details/105207966?utm_medium=distribute.pc_relevant.none-task-blog-BlogCommendFromMachineLearnPai2-3.add_param_isCf&depth_1-utm_source=distribute.pc_relevant.none-task-blog-BlogCommendFromMachineLearnPai2-3.add_param_isCf)

### LinkedList集合

`java.util.LinkedList`集合的底层是双向链表，增删快，查询慢。


实际开发中对一个集合元素的添加与删除经常涉及到首尾操作，而LinkedList提供了大量首尾操作的方法。

* `public void addFirst(E e)`:将指定元素插入此列表的开头。
* `public void addLast(E e)`:将指定元素添加到此列表的结尾。此方法等效于add。
* `public E getFirst()`:返回此列表的第一个元素。
* `public E getLast()`:返回此列表的最后一个元素。
* `public E removeFirst()`:移除并返回此列表的第一个元素。
* `public E removeLast()`:移除并返回此列表的最后一个元素。
* `public E pop()`:从此列表所表示的堆栈处弹出一个元素。此方法等效于removeFirst。
* `public void push(E e)`:将元素推入此列表所表示的堆栈。此方法等效于addFirst。
* `public boolean isEmpty()`：如果列表不包含元素，则返回true。

在开发时，LinkedList集合可以作为队列、双向队列和堆栈使用。

### Vector集合

和ArrayList原理一样，实现了变长的对象数组。但与ArrayList不同的是，Vector是同步的，效率低。
