2020年10月2日

[力扣](https://leetcode-cn.com/problems/path-sum-iii/)

- [思路一](#思路一)
- [思路二](#思路二)

**描述**

给定一个二叉树，它的每个结点都存放着一个整数值。

找出路径和等于给定数值的路径总数。

路径不需要从根节点开始，也不需要在叶子节点结束，但是路径方向必须是向下的（只能从父节点到子节点）。

二叉树不超过1000个节点，且节点数值范围是 [-1000000,1000000] 的整数。

示例：
```
root = [10,5,-3,3,2,null,11,3,-2,null,1], sum = 8

      10
     /  \
    5   -3
   / \    \
  3   2   11
 / \   \
3  -2   1

返回 3。和等于 8 的路径有:

1.  5 -> 3
2.  5 -> 2 -> 1
3.  -3 -> 11
```
```
/**
 * Definition for a binary tree node.
 * public class TreeNode {
 *     int val;
 *     TreeNode left;
 *     TreeNode right;
 *     TreeNode() {}
 *     TreeNode(int val) { this.val = val; }
 *     TreeNode(int val, TreeNode left, TreeNode right) {
 *         this.val = val;
 *         this.left = left;
 *         this.right = right;
 *     }
 * }
 */
```
#### 思路一

递归，深度优先

计算以当前节点为路径终点的所有路径和，计算以当前节点的左孩子为路径终点的所有路径和，计算以当前节点的右孩子为路径终点的所有路径和。将这三部分之和作为最后结果即可。

关键点：用一个数组保存从根节点到当前节点路径。

时间复杂度：遍历n个节点，为每个节点计算以当前节点为路径终点的所有路径和，平均路径长度是logn，所以平均时间复杂度是O(nlogn)。最坏的情况下是单分支树，时间复杂度为O(n^2)。

空间复杂度：O(height)，height为树的高度，最坏为O(n)。

```java
class Solution {
    public int pathSum(TreeNode root, int sum) {
        return helper(root, sum, new int[1001], 1);
        //二叉树不超过1000个节点。因为用1表示根节点，没有用到0，所以最多需要的数组长度为1001。
    }

    //array数组存储某一次递归时所遍历结点的值,level表示当前节点的层数。
    public int helper(TreeNode endNode, int sum, int[] array, int level){
        if(endNode==null)   return 0;
        array[level] = endNode.val;
        int cur=0;
        int curPathSum = 0;

        //计算以当前节点为路径终点的所有路径和，统计符合条件的数量。
        for(int i=level;i>=1;i--){
            curPathSum+=array[i];//从当前节点开始，向上加父节点的值,一直加到根节点。
            if(curPathSum==sum)
                cur++;
        }

        //计算以当前节点的左孩子为路径终点的所有路径和，统计符合条件的数量。
        int left=helper(endNode.left, sum, array, level+1);
        //计算以当前节点的右孩子为路径终点的所有路径和，统计符合条件的数量。
        int right=helper(endNode.right, sum, array, level+1);

        return cur+left+right;
    }
}
```

#### 思路二

递归，深度优先。

计算以当前节点作为起始结点的路径数量，以当前节点的左孩子作为根结点的路径数量，以当前节点的右孩子作为根结点的路径数量，将这三部分之和作为最后结果即可。

需要双重DFS，有很多重复计算，效率比思路一低，不过思路简单。

```java
class Solution {
    public int pathSum(TreeNode root, int sum) {
        if(root==null)  return 0;
        int cur = CountPath(root,sum);//计算以当前节点作为头结点的路径数量
        int left = pathSum(root.left, sum);//计算以当前节点的左孩子作为根结点的路径数量
        int right = pathSum(root.right, sum);//计算以当前节点的右孩子作为根结点的路径数量

        return cur+left+right;
    }
    
    //求以当前节点作为头结点的路径的数量
    public int CountPath(TreeNode root, int sum){
        if(root==null)  return 0;
        int cur = root.val==sum?1:0;//当前节点的值是否等于目标数值
        sum-=root.val;
        return cur + CountPath(root.left,sum) + CountPath(root.right,sum);
    }
}
```
