2020年9月7日

[力扣]()

[思路一](#思路一)

**描述**

给你一个链表数组，每个链表都已经按升序排列。

请你将所有链表合并到一个升序链表中，返回合并后的链表。

示例 1：
```
输入：lists = [[1,4,5],[1,3,4],[2,6]]
输出：[1,1,2,3,4,4,5,6]
解释：链表数组如下：
[
  1->4->5,
  1->3->4,
  2->6
]
将它们合并到一个有序链表中得到。
1->1->2->3->4->4->5->6
```
示例 2：
```
输入：lists = []
输出：[]
```
示例 3：
```
输入：lists = [[]]
输出：[]
```

提示：
```
k == lists.length
0 <= k <= 10^4
0 <= lists[i].length <= 500
-10^4 <= lists[i][j] <= 10^4
lists[i] 按 升序 排列
lists[i].length 的总和不超过 10^4
```

```
/**
 * Definition for singly-linked list.
 * public class ListNode {
 *     int val;
 *     ListNode next;
 *     ListNode() {}
 *     ListNode(int val) { this.val = val; }
 *     ListNode(int val, ListNode next) { this.val = val; this.next = next; }
 * }
 */
```

#### 思路一

K个指针分别指向K条链表，每次比较K个指针求min，时间复杂度：O(NK)。

dummy用来保存排序完成后的节点的起始节点。

```java
class Solution {
    public ListNode mergeKLists(ListNode[] lists) {

        int k=lists.length;
        ListNode dummy = new ListNode(0);
        ListNode pre = dummy;

        while(true)
        {
            ListNode minNode = null;
            int minPointer = -1;

            for(int i=0; i<k; i++)
            {
                if(lists[i]==null)
                {
                    continue;
                }

                if(minNode==null || lists[i].val<minNode.val)
                {
                    minNode=lists[i];
                    minPointer=i;
                }
            }
            if(minPointer==-1)
            {
                break;
            }

            pre.next=minNode;
            pre=pre.next;
            lists[minPointer]=lists[minPointer].next;
        }
        return dummy.next;
    }
}
```
#### 思路二

逐一合并两条链表, 时间复杂度：O(NK)

```java
class Solution {
    public ListNode mergeKLists(ListNode[] lists) {
        ListNode res = null;
        for(ListNode list: lists)
        {
            res=merge2Lists(res, list);
        } 
        return res;
    }

    private ListNode merge2Lists(ListNode l1, ListNode l2)
    {
        ListNode dummy =new ListNode(0);
        ListNode pre = dummy;
        while(l1!=null && l2!=null)
        {
            
            if(l1.val<=l2.val)
            {
                pre.next=l1;
                l1=l1.next;
            }
            else
            {
                pre.next=l2;
                l2=l2.next;
            }
            pre=pre.next;
        }
        pre.next=l1==null?l2:l1;

        return dummy.next;
    }
}
```
