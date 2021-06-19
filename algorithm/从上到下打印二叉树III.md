2020年10月19日

[力扣](https://leetcode-cn.com/problems/cong-shang-dao-xia-da-yin-er-cha-shu-iii-lcof/submissions/)

**描述**

请实现一个函数按照之字形顺序打印二叉树，即第一行按照从左到右的顺序打印，第二层按照从右到左的顺序打印，第三行再按照从左到右的顺序打印，其他行以此类推。



#### 思路

层序遍历 + 双端队列。

当deque 为空时跳出循环。

若是奇数层： 从左向右打印，先左后右将下层节点加入队尾；

若是偶数层： 从右向左打印，先右后左将下层节点加入队首；
```
时间复杂度：O(N)。N为二叉树的节点数量，遍历N个节点需要O(n)；双端队列的队首和队尾的添加和删除操作的时间复杂度均为O(1) 。

空间复杂度：O(N)。最差情况下，即当树为满二叉树时，最多有N/2(向上取整）个树节点同时在deque中，使用O(N)大小的额外空间。

```java
class Solution {
    public List<List<Integer>> levelOrder(TreeNode root) {
        List<List<Integer>> res = new LinkedList<>();
        Deque<TreeNode> dequeue = new LinkedList<>();
        if(root!=null)  dequeue.offer(root);
        int level=0;
        while(!dequeue.isEmpty()){
            level++;
            int size = dequeue.size();
            LinkedList<Integer> temp = new LinkedList<>();
            if(level%2==1){
                while(size>0){
                    TreeNode cur = dequeue.poll();
                    temp.add(cur.val);
                    if(cur.left!=null) dequeue.add(cur.left);
                    if(cur.right!=null) dequeue.add(cur.right);
                    size--;
                }
            }else{
                while(size>0){
                    TreeNode cur = dequeue.pollLast();
                    temp.add(cur.val);
                    if(cur.right!=null) dequeue.addFirst(cur.right);
                    if(cur.left!=null) dequeue.addFirst(cur.left);
                    size--;
                }
            }
            res.add(new LinkedList(temp));
        }
        return res;
    }
}
```

