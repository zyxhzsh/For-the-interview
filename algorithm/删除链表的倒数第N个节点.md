2020年9月10日

[力扣](https://leetcode-cn.com/problems/remove-nth-node-from-end-of-list/)

[思路一](#思路一)

[思路二](#思路二)

**描述**

```
/**
 * Definition for singly-linked list.
 * public class ListNode {
 *     int val;
 *     ListNode next;
 *     ListNode(int x) { val = x; }
 * }
 */
```

#### 思路一

双指针

设立一个哑结点，下一个节点为head.两个指针均等于哑结点，让前面的指针先移动n步，之后前后指针共同移动直到前面的指针到尾部为止。

时间复杂度：O(n)。

空间复杂度：O(1)。

```java
class Solution {
    public ListNode removeNthFromEnd(ListNode head, int n) {
        ListNode pre = new ListNode(0);
        pre.next=head;
        ListNode first=pre,second=pre;
        for(int i=0;i<n;i++)
        {
            first=first.next;
        }
        while(first.next!=null)
        {
            first=first.next;
            second=second.next;
        }
        second.next=second.next.next;
        return pre.next;
    }
}
```

#### 思路二

二次遍历

第一次遍历得到链表长度，第二次遍历找到倒数第N个节点的前一个节点。

时间复杂度：O(n)。

空间复杂度：O(1)。

```java
class Solution {
    public ListNode removeNthFromEnd(ListNode head, int n) {
        ListNode dummy = new ListNode(0);
        dummy.next=head;
        ListNode point = dummy;

        int len=0;
        while(point.next!=null)
        {
            point=point.next;
            len++;
        }
        point=dummy;
        for(int i=0; i<len-n;i++)
        {
            point=point.next;
        }
        point.next=point.next.next;
        return  dummy.next;
    }
}
```
