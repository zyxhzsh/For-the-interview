2020年11月7日

[力扣](https://leetcode-cn.com/problems/lian-biao-zhong-dao-shu-di-kge-jie-dian-lcof/)

**描述**

输入一个链表，输出该链表中倒数第k个节点。为了符合大多数人的习惯，本题从1开始计数，即链表的尾节点是倒数第1个节点。例如，一个链表有6个节点，从头节点开始，它们的值依次是1、2、3、4、5、6。这个链表的倒数第3个节点是值为4的节点。

示例：
```
给定一个链表: 1->2->3->4->5, 和 k = 2.
返回链表 4->5.
```
#### 思路1

双指针

两个指针p,q指向head。遍历链表让q指向第k个元素，然后p和q每次同时移动一次，直到q指向链表最后一个元素，此时p指向倒数第k个节点。

时间复杂度 O(N)：q走了N步， p走了(N−k)步。
空间复杂度 O(1)

作者：jyd
链接：https://leetcode-cn.com/problems/lian-biao-zhong-dao-shu-di-kge-jie-dian-lcof/solution/mian-shi-ti-22-lian-biao-zhong-dao-shu-di-kge-j-11/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。

```java
class Solution {
    public ListNode getKthFromEnd(ListNode head, int k) {
        ListNode p=head,q=head;
        for(int i=1;i<k;i++){
            q=q.next;
        }
        while(q.next!=null){
            p=p.next;
            q=q.next;
        }
        return p;
    }
}
```
### 思路2

另一种写法，快指针先走k步(移动前先判断快指针是否为null)，然后快慢指针一起移动直到快指针为null。
```java
class Solution {
    public ListNode getKthFromEnd(ListNode head, int k) {

        ListNode p=head,q=head;
        while(q != null && k>0){
            q = q.next;
            k--;
        }

        while(q != null){
            p = p.next;
            q = q.next;
        }
        return p;
    }
}
```
