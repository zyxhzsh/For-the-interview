2020年9月14日

[力扣](https://leetcode-cn.com/problems/lru-cache/submissions/)

[牛客](https://www.nowcoder.com/questionTerminal/undefined)

[力扣](#力扣)

[牛客](#牛客)

### 力扣

**描述**

运用你所掌握的数据结构，设计和实现一个LRU (最近最少使用) 缓存机制。它应该支持以下操作：获取数据get和写入数据put。

获取数据 get(key) - 如果关键字 (key) 存在于缓存中，则获取关键字的值（总是正数），否则返回 -1。

写入数据 put(key, value) - 如果关键字已经存在，则变更其数据值；如果关键字不存在，则插入该组「关键字/值」。
当缓存容量达到上限时，它应该在写入新数据之前删除最久未使用的数据值，从而为新的数据值留出空间。

进阶:

你是否可以在 O(1) 时间复杂度内完成这两种操作？

#### 思路

哈希表+双向链表。

出现了键值对，就要想到哈希表，因为他可以在O(1）时间内通过键找到值。

有出入顺序，首先想到栈，队列或者链表。而题目要求每次get或put数据后，该数据要被设置为最新访问的数据。这就要求能够随机访问，还要把最新的数据插入到头部和尾部。

链表可以快速移动节点的位置，而哈希表可以实现时间复杂度为O(1)的随机访问，如果哈希表的值包含链表的位置信息，那么就可以在O(1)时间访问链表。

由于链表记录了访问的时间顺序，所以链表中的元素必须储存键，这样就能通过键找到哈希表的项目，进而实现删除要求。
```
双向链表按照被使用的顺序存储了这些键值对，靠近头部的键值对是最近使用的，而靠近尾部的键值对是最久未使用的。

哈希表即为普通的哈希映射（HashMap），通过缓存数据的键映射到其在双向链表中的位置。
```
我们首先使用哈希表进行定位，找出缓存项在双向链表中的位置，随后将其移动到双向链表的头部，即可在O(1)的时间内完成get或者put操作。具体的方法如下：
```
对于 get 操作，首先判断 key 是否存在：

如果 key 不存在，则返回-1；

如果 key 存在，则 key 对应的节点是最近被使用的节点。通过哈希表定位到该节点在双向链表中的位置，并将其移动到双向链表的头部，最后返回该节点的值。

对于 put 操作，首先判断 key 是否存在：

如果 key 不存在，使用 key 和 value 创建一个新的节点，在双向链表的头部添加该节点，并将 key 和该节点添加进哈希表中。
然后判断双向链表的节点数是否超出容量，如果超出容量，则删除双向链表的尾部节点，并删除哈希表中对应的项；

如果 key 存在，则与 get 操作类似，先通过哈希表定位，再将对应的节点的值更新为value，并将该节点移到双向链表的头部。
```
时间复杂度：访问哈希表的时间复杂度为 O(1)O(1)，在双向链表的头部添加节点、在双向链表的尾部删除节点的复杂度也为 O(1)。

而将一个节点移到双向链表的头部，可以分成「删除该节点」和「在双向链表的头部添加节点」两步操作，都可以在O(1)时间内完成。

空间复杂度：O(capacity)，因为哈希表和双向链表最多存储capacity+1个元素。

```java
class LRUCache {

    class Node{
        int key, value;
        Node prev, next;
        public Node(){}
        public Node(int _key, int _value){
            this.key = _key;
            this.value = _value;
        }
    }

    private Map<Integer, Node> map;
    private Node head, tail;
    private int capacity;

    public LRUCache(int capacity) {

        this.capacity = capacity;
        head = new Node(-1,-1);
        tail = new Node(-1,-1);
        head.next = tail;
        tail.prev = head;
        map = new HashMap<>();
    }
    
    public int get(int key) {

        Node node = map.get(key);
        if(node == null){
            return -1;//key不存在返回-1
        }
        //key存在，把节点移动到双向链表的表头，然后返回该节点的值。
        removeNode(node);
        addToHead(node);
        return node.value;

    }
    
    public void put(int key, int value) {

        //key存在,将对应的节点移动到双向链表表头，并更新节点的值为value。

        /*key不存在，先判断双向链表的节点数是否超出容量，如果超出容量，则删除双向链表的尾部节点，并删除哈希表中对应的项；
        然后创建一个新的节点，在双向链表的头部添加该节点，并将key和该节点添加进哈希表中。*/
        Node node = map.get(key);
        if(node != null){
            node.value = value;
            removeNode(node);
        }else{
            if(map.size() == capacity){
               Node temp = tail.prev;
               removeNode(temp);
               map.remove(temp.key);
            }
            node = new Node(key, value);
            map.put(key, node);
        }
        addToHead(node);
    }

    //删除节点
    public void removeNode(Node node){

        node.prev.next = node.next;
        node.next.prev = node.prev;
    }

    //把节点加到表头。
    public void addToHead(Node node){

        node.next = head.next;
        head.next.prev = node;
        node.prev = head;
        head.next = node;
    }
}

/**
 * Your LRUCache object will be instantiated and called as such:
 * LRUCache obj = new LRUCache(capacity);
 * int param_1 = obj.get(key);
 * obj.put(key,value);
 */
```

### 牛客

### 描述

设计LRU缓存结构，该结构在构造时确定大小，假设大小为K，并有如下两个功能

set(key, value)：将记录(key, value)插入该结构

get(key)：返回key对应的value值

[要求]

set和get方法的时间复杂度为O(1)

某个key的set或get操作一旦发生，认为这个key的记录成了最常使用的。

当缓存的大小超过K时，移除最不经常使用的记录，即set或get最久远的。

若opt=1，接下来两个整数x, y，表示set(x, y)

若opt=2，接下来一个整数x，表示get(x)，若x未出现过或已被移除，则返回-1

对于每个操作2，输出一个答案

示例1
```
[[1,1,1],[1,2,2],[1,3,2],[2,1],[1,4,4],[2,2]],3
[1,-1]
说明：
第一次操作后：最常使用的记录为("1", 1)
第二次操作后：最常使用的记录为("2", 2)，("1", 1)变为最不常用的
第三次操作后：最常使用的记录为("3", 2)，("1", 1)还是最不常用的
第四次操作后：最常用的记录为("1", 1)，("2", 2)变为最不常用的
第五次操作后：大小超过了3，所以移除此时最不常使用的记录("2", 2)，加入记录("4", 4)，并且为最常使用的记录，然后("3", 2)变为最不常使用的记录
```
备注：
```
1≤K≤N≤10^5
−2×10^9≤x,y≤2×10^9
```
代码
```java
import java.util.*;

public class Solution {
    /**
     * lru design
     * @param operators int整型二维数组 the ops
     * @param k int整型 the k
     * @return int整型一维数组
     */
      class DNode{
        int key, value;
        DNode prev, next;
        public DNode(){}
        public DNode(int _key, int _value){
            this.key = _key;
            this.value = _value;
        }
    }
    
    private Map<Integer, DNode> map = new HashMap<>();
    private DNode head = new DNode(-1, -1);
    private DNode tail = new DNode(-1, -1);
    private int k;
    
    public int[] LRU (int[][] operators, int k) {
        
        head.next = tail;
        tail.prev = head;
        this.k = k;
        List<Integer> data = new ArrayList();
        for(int i=0;i<operators.length;i++){
            if(operators[i][0] == 1){
                set(operators[i][1], operators[i][2]);
            }else{
                data.add(get(operators[i][1]));
            }
        }
        int[] res = new int[data.size()];
        for(int i=0;i<res.length;i++){
            res[i] = data.get(i);
        }
        return res;
    }
    
    private void set(int key, int value){
        
        DNode node = map.get(key);
        if(node != null){
            node.value = value;
            removeNode(node);
            addToHead(node);
        }else{
            if(map.size()==k){
                DNode temp = tail.prev;
                map.remove(temp.key);
                removeNode(temp);
            }
            node = new DNode(key, value);
            map.put(key, node);
            addToHead(node);
        }
    }
    
    private int get(int key){
        
        DNode node = map.get(key);
        if(node != null){
            removeNode(node);
            addToHead(node);
            return node.value;
        }else{
            return -1;
        }
    }
    
    private void removeNode(DNode node){
        node.prev.next = node.next;
        node.next.prev = node.prev;
    }
    
    private void addToHead(DNode node){
        head.next.prev = node;
        node.next = head.next;
        head.next = node;
        node.prev = head;
    }
    
}
```
