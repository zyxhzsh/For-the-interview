2020年10月19日

[力扣](https://leetcode-cn.com/problems/cong-shang-dao-xia-da-yin-er-cha-shu-lcof/)

**描述**

#### 思路

层次遍历，通常借助队列的先入先出特性来实现。不知数组长度，先使用ArrayList，遍历结束后for循环转为数组。

时间复杂度 O(N)： N为二叉树的节点数量，BFS遍历每个节点的时间复杂度为O(1)。

空间复杂度 O(N)： 最差情况下，即当树为完全二叉树时，最多有（向下取整）N/2+1个树节点同时在queue中，使用 O(N)O(N) 大小的额外空间。

```java
class Solution {
    public int[] levelOrder(TreeNode root) {
        List<Integer> list = new ArrayList<>();
        Queue<TreeNode> queue = new LinkedList<>();
        if(root!=null)  queue.offer(root);

        while(!queue.isEmpty()){
            TreeNode cur=queue.poll();
            list.add(cur.val);
            if(cur.left!=null)  queue.offer(cur.left);
            if(cur.right!=null) queue.offer(cur.right);
        }

        int[] res=new int[list.size()];
        for(int i=0;i<list.size();i++){
            res[i]=list.get(i);
        }
        return res;
    }
}
```


