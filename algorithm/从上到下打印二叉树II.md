2020年10月19日

[力扣](https://leetcode-cn.com/problems/cong-shang-dao-xia-da-yin-er-cha-shu-ii-lcof/)

**描述**

从上到下按层打印二叉树，同一层的节点按从左到右的顺序打印，每一层打印到一行。

例如:
```
给定二叉树: [3,9,20,null,null,15,7],

    3
   / \
  9  20
    /  \
   15   7
返回其层次遍历结果：

[
  [3],
  [9,20],
  [15,7]
]
```

提示：

节点总数 <= 1000

#### 思路

层次遍历，借助队列的先入先出特性来实现。

时间复杂度 O(N)： N为二叉树的节点数量，BFS遍历每个节点的时间复杂度为O(1)。

空间复杂度 O(N)： 最差情况下，即当树为完全二叉树时，最多有N/2+1（向下取整）个树节点同时在queue中，使用 O(N)O(N) 大小的额外空间。

```java
class Solution {
    public List<List<Integer>> levelOrder(TreeNode root) {
        List<List<Integer>> res = new LinkedList<>();
        Queue<TreeNode> queue = new LinkedList<>();
        if(root!=null)  queue.offer(root);
        
        while(!queue.isEmpty()){

            int size=queue.size();
            List<Integer> cur = new LinkedList<>();
            while(size>0){
                root=queue.poll();
                if(root.left!=null) queue.offer(root.left);
                if(root.right!=null) queue.offer(root.right);
                cur.add(root.val);
                size--;
            }
            res.add(new LinkedList(cur));
        }
        return res;
    }
}
```

