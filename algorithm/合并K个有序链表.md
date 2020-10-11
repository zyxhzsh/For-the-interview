2020年9月7日

[力扣](https://leetcode-cn.com/problems/merge-k-sorted-lists/submissions/)

[思路一](#思路一)

[思路二](#思路二)

[思路三](#思路三)

[思路四](#思路四)

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

k个指针分别指向k条链表，每次比较k个指针求min。

N为元素总数，n为每个链表的最长长度。

时间复杂度：O(kN)。每有一个元素，就要比较k个指针，每次比较完选出一个元素，因此时间复杂度为O(kN)=O(k^2n)

空间复杂度：O(1)。

head用来保存排序完成后的节点的起始节点。

```java
class Solution {
    public ListNode mergeKLists(ListNode[] lists) {
        ListNode head = new ListNode();
        ListNode cur = head;
        int k = lists.length;
        
        while(true){
            ListNode minNode = new ListNode(Integer.MAX_VALUE);
            int minIndex=-1;
            for(int i=0;i<k;i++){
                if(lists[i]==null){
                    continue;
                }
                if(lists[i].val<minNode.val){
                    minNode=lists[i];
                    minIndex=i;
                }
            }
            if(minIndex==-1)    break;//minIndex没被更新，说明k个指针均为null，链表数组中没有元素了。
            cur.next=lists[minIndex];//或者cur.next=minNode
            lists[minIndex]=lists[minIndex].next;
            cur=cur.next;
        }
        return head.next;
    }
}
```
#### 思路二

逐一合并两条链表, 时间复杂度：O(k^2n)。假设每个链表的最长长度是n，在第一次合并后，ans的长度为n，第i次合并后ans的长度为in，一共合并k次。时间复杂度=O(∑(i\*n))(i=1,...k)=O(k^2n)。
设总节点数位N，则时间复杂度：O(kN)，因为N=kn。

空间复杂度：O(1)。

```java
class Solution {
    public ListNode mergeKLists(ListNode[] lists) {
        ListNode ans = null;
        for(int i=0;i<lists.length;i++){
            ans=merge2Lists(ans, lists[i]);
        }
        return ans;
    }

    private ListNode merge2Lists(ListNode A, ListNode B){
        ListNode head = new ListNode();
        ListNode cur=head;
        while(A!=null && B!=null){
            if(A.val<=B.val){
                cur.next=A;
                A=A.next;
            }
            else{
                cur.next=B;
                B=B.next;
            }
            cur=cur.next;
        }
        cur.next=A==null?B:A;
        return head.next;
    }
}
```

#### 思路三

两两合并链表递归实现。

用分治的方法进行合并,将k个链表配对并将同一对中的链表合并；第一轮合并以后， k个链表被合并成了k/2个链表。第二轮是k/4...重复这一过程，直到我们得到了最终的有序链表。

N为元素总数，n为每个链表的最长长度

时间复杂度：O(Nlogk)。考虑递归「向上回升」的过程——第一轮合并k/2组链表，链表的平均长度为n，每一组的时间代价是O(2n)，第一轮的时间代价为O(k/2\*2n)=O(kn)。第二轮合并k/4组链表，每一组的时间代价是O(4n),第二轮的时间代价为O(k/4\*4n)=O(kn)。所以每一轮的时间代价均为O(kn)。一共有logk轮。

所以总的时间代价是O(knlogk)=O(Nlogk)。

空间复杂度：递归会使用到O(logk)空间代价的栈空间。

```java
class Solution {
    public ListNode mergeKLists(ListNode[] lists) {
        if(lists.length==0) return null;
        return recursion_merge(lists, 0, lists.length-1);
    }

    private ListNode recursion_merge(ListNode[] lists, int low, int high){
        if(low==high)   return lists[low];
        int mid=(low+high)/2;
        return merge2Lists(recursion_merge(lists, low, mid), recursion_merge(lists, mid+1, high));
        /*与return语句等价
        ListNode A = recursion_merge(lists, low, mid);
        ListNode B = recursion_merge(lists, mid+1, high);
        return merge2Lists(A, B);
        */
    }

    private ListNode merge2Lists(ListNode A, ListNode B){
        ListNode head = new ListNode();
        ListNode cur=head;
        while(A!=null && B!=null){
            if(A.val<=B.val){
                cur.next=A;
                A=A.next;
            }
            else{
                cur.next=B;
                B=B.next;
            }
            cur=cur.next;
        }
        cur.next=A==null?B:A;
        return head.next;
    }
}
```

#### 思路四

两两合并链表迭代实现。

用k保存每一轮合并之前的链表数量，每轮合并结束后再更新k为下一轮合并前的链表数量。注意链表数量为奇数时，最后一组只有一个链表，无需合并。

时间复杂度：O(Nlogk)。K条链表的总结点数是N，平均每条链表有N/K个节点，因此合并两条链表的时间复杂度是O(2N/K)=O(N/K)。从K条链表开始两两合并成1条链表，因此每条链表都会被合并logK 次，因此K条链表会被合并K\*logK次，因此总共的时间复杂度是K\*logK*(N/K) 即O(NlogK)。

空间复杂度：O(1)

```java
class Solution {
    public ListNode mergeKLists(ListNode[] lists) {
        if(lists.length==0) return null;
        int k = lists.length;//用k保存每一轮合并之前的链表数量，每轮合并结束后再更新k为下一轮合并前的链表数量
        while(k>1){
            int count=0;//lists[count]存当前组两两合并后的链表。
            for(int i=0;i<k;i+=2){
                if(i==k-1){//k为奇数时，最后一组只有一个链表。
                    lists[count++]=lists[i];
                }
                else{
                    lists[count++]=merge2Lists(lists[i],lists[i+1]);
                }
            }
            k=count;
        }
        return lists[0];
    }

    private ListNode merge2Lists(ListNode A, ListNode B){
        ListNode head = new ListNode();
        ListNode cur=head;
        while(A!=null && B!=null){
            if(A.val<=B.val){
                cur.next=A;
                A=A.next;
            }
            else{
                cur.next=B;
                B=B.next;
            }
            cur=cur.next;
        }
        cur.next=A==null?B:A;
        return head.next;
    }
}
```
