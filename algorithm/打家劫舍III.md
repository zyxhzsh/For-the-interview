2020年9月17日

[力扣](https://leetcode-cn.com/problems/house-robber-iii/)

[思路一](#思路一)

[思路二](#思路二)

**描述**

在上次打劫完一条街道之后和一圈房屋后，小偷又发现了一个新的可行窃的地区。这个地区只有一个入口，我们称之为“根”。 除了“根”之外，每栋房子有且只有一个“父“房子与之相连。
一番侦察之后，聪明的小偷意识到“这个地方的所有房屋的排列类似于一棵二叉树”。 如果两个直接相连的房子在同一天晚上被打劫，房屋将自动报警。

计算在不触动警报的情况下，小偷一晚能够盗取的最高金额。

示例 1:
```
输入: [3,2,3,null,3,null,1]

     3
    / \
   2   3
    \   \ 
     3   1

输出: 7 
解释: 小偷一晚能够盗取的最高金额 = 3 + 3 + 1 = 7.
```
```
/**
 * Definition for a binary tree node.
 * public class TreeNode {
 *     int val;
 *     TreeNode left;
 *     TreeNode right;
 *     TreeNode(int x) { val = x; }
 * }
 */
```
#### 思路一

动态规划

简化一下这个问题：一棵二叉树，树上的每个点都有对应的权值，每个点有两种状态（选中和不选中），问在不能同时选中有父子关系的点的情况下，能选中的点的最大权值和是多少。

f(i)表示选择i节点的情况下，i节点的权值和其子树上被选择的节点的最大权值和。g(i)表示不选择i节点的情况下，i节点的子树上被选择的节点的最大权值和。
```
f(i)=i.val+g(l)+g(r)
g(i)=max(g(l),f(l))+max(g(r)+f(r))
```
我们可以用哈希映射来存f和g的函数值，用深度优先搜索的办法后序遍历这棵二叉树，我们就可以得到每一个节点的f和g。根节点的f和g的最大值就是我们要找的答案。

时间复杂度：对二叉树做了一次后序遍历，时间复杂度是O(n)。

空间复杂度：由于递归会使用到栈空间，空间代价是O(n)，哈希映射的空间代价也是O(n)，故空间复杂度也是O(n)。

```java
class Solution {
    //f(i)表示选择i节点的情况下，i节点的权值和其子树上被选择的节点的最大权值和。
    Map<TreeNode, Integer> f = new HashMap<TreeNode, Integer>();
    //g(i)表示不选择i节点的情况下，i节点的子树上被选择的节点的最大权值和。
    Map<TreeNode, Integer> g = new HashMap<TreeNode, Integer>();

    public int rob(TreeNode root) {
        dfs(root);
        return Math.max(f.getOrDefault(root, 0), g.getOrDefault(root, 0));
    }

    private void dfs(TreeNode node){
        if(node==null)  return;
        //用深度优先搜索的办法后序遍历这棵二叉树，我们就可以得到每一个节点的f和g。
        dfs(node.left);
        dfs(node.right);
        f.put(node, node.val+g.getOrDefault(node.left,0)+g.getOrDefault(node.right,0));
        g.put(node,Math.max(g.getOrDefault(node.left,0),f.getOrDefault(node.left,0))+
        Math.max(g.getOrDefault(node.right,0),f.getOrDefault(node.right,0)));
    }
}
```

#### 思路二

无论是 f(o)还是g(o)，他们最终的值只和f(l)、g(l)、f(r)、g(r) 有关，所以对于每个节点，我们只关心它的孩子节点们的f和g是多少。

我们可以设计一个结构，表示某个节点的f和g值，在每次递归返回的时候，都把这个点对应的f和g返回给上一级调用，这样可以省去哈希映射的空间。

时间复杂度：O(n)，和方法一一样。

空间复杂度：O(n)。虽然优化过的版本省去了哈希映射的空间，但是栈空间的使用代价依旧是O(n)，故空间复杂度不变。
```java
class Solution {

    public int rob(TreeNode root) {
        int[] res = dfs(root);
        return Math.max(res[0], res[1]);
    }

    private int[] dfs(TreeNode node){
        if(node==null)  
            return new int[]{0, 0};
        //用深度优先搜索的办法后序遍历这棵二叉树，我们就可以得到每一个节点的f和g。
        int[] left = dfs(node.left);
        int[] right = dfs(node.right);
        int used = node.val+left[1]+right[1];
        int unused = Math.max(left[0],left[1]) + Math.max(right[0],right[1]);
        return new int[]{used, unused};//第一个元素是使用时的最大权值和，第二个元素是不使用时的最大权值和。
    }
}
```
