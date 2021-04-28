2020年9月14日

[力扣](https://leetcode-cn.com/problems/lru-cache/submissions/)

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

    class DLinkedNode{
        int key;
        int value;
        DLinkedNode prev;
        DLinkedNode next;
        DLinkedNode(){}
        DLinkedNode(int _key, int _vaule){
            key = _key;
            value = _vaule;
        }
    }

    private Map<Integer, DLinkedNode> map = new HashMap<>();
    private int size;
    private int capacity;
    private DLinkedNode head,tail;

    public LRUCache(int capacity) {
        this.size=0;
        this.capacity=capacity;
        head = new DLinkedNode();
        tail = new DLinkedNode();
        head.next=tail;
        tail.prev=head;
    }
    
    public int get(int key) {
        DLinkedNode node = map.get(key);
        if(node==null){
            return -1;//key不存在返回-1
        }
        //key存在，把节点移动到双向链表的表头，然后返回该节点的值。
        moveToHead(node);
        return node.value;
    }
    
    public void put(int key, int value) {
        DLinkedNode node = map.get(key);
        if(node==null){
            /*key不存在，创建一个新的节点，在双向链表的头部添加该节点，并将key和该节点添加进哈希表中。
            然后判断双向链表的节点数是否超出容量，如果超出容量，则删除双向链表的尾部节点，并删除哈希表中对应的项；*/
            DLinkedNode newnode = new DLinkedNode(key,value);
            addToHead(newnode);
            map.put(key,newnode);
            size++;
            if(size>capacity){
                DLinkedNode temp=removeTail();
                map.remove(temp.key);
                size--;
            }
        }
        else {
            //key存在,将对应的节点移动到双向链表表头，并更新节点的值为value。
            moveToHead(node);
            node.value=value;
        }
    }
    
    //把节点加到表头。
    private void addToHead(DLinkedNode node){
        node.prev=head;
        node.next=head.next;
        head.next=node;
        node.next.prev=node;
    }
    
    //删除节点
    private void removeNode(DLinkedNode node){
        node.next.prev=node.prev;
        node.prev.next=node.next;
    }
    
    //删除尾结点，尾结点就是最近最久未使用的节点。
    private DLinkedNode removeTail(){
        DLinkedNode temp=tail.prev;
        removeNode(temp);
        return temp;
    }
    
    //移动节点到表头。
    private void moveToHead(DLinkedNode node){
        removeNode(node);
        addToHead(node);
    }
}

/**
 * Your LRUCache object will be instantiated and called as such:
 * LRUCache obj = new LRUCache(capacity);
 * int param_1 = obj.get(key);
 * obj.put(key,value);
 */
```

