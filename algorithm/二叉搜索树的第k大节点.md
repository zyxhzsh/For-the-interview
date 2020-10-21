2020年10月20日

[力扣](https://leetcode-cn.com/problems/er-cha-sou-suo-shu-de-di-kda-jie-dian-lcof/)

**类似的题** [二叉搜索树中第K小的元素](#二叉搜索树中第K小的元素)

[思路一](#思路一)

[思路二](#思路二)

**描述**

给定一棵二叉搜索树，请找出其中第k大的节点。

限制：

1 ≤ k ≤ 二叉搜索树元素个数

#### 思路一

二叉搜索树的中序遍历倒序为递减序列。因此，求 “二叉搜索树第k大的节点” 可转化为求 “此树的中序遍历倒序的第k个节点”。

迭代实现

时间复杂度：O(n)。 每个节点只入栈出栈一次。

空间复杂度：O(n)。最坏的的情况，树退化为链表（全部为右子节点），栈中最多同时有n个元素。
```java
class Solution {
    public int kthLargest(TreeNode root, int k) {
        int count=0;
        Deque<TreeNode> stack = new LinkedList<>();
        while(root!=null || !stack.isEmpty()){
            while(root!=null){
                stack.push(root);
                root=root.right;
            }
            root = stack.poll();
            if(++count==k)   break;
            /*注意这里，root节点已经访问过。不管root.left是否为null，root必须指向另一节点，否则答案会错误。
            因为root如果不变，外层循环时root不为null，且出栈时还是这个节点，而count会继续加1，会一直循环知道count==k，最终会返回这个节点。
            root.left为null时，也不需要判断栈是否为空以及出栈，因为下一次外层循环会判断*/
            root=root.left;
        }
        return root.val;
    }
}
```

#### 思路二

二叉搜索树的中序遍历倒序的递归实现。

时间复杂度：O(n)。

空间复杂度：O(n)。当树退化为链表时（全部为右子节点），系统使用O(N)大小的栈空间。

```java
class Solution {
    int k,res;
    public int kthLargest(TreeNode root, int k) {
        this.k=k;//如果把k当做dfs的形参，那么递归返回时，实参不会改变，就无法计算是不是第k个了。所以这里用全局变量来实现。
        dfs(root);
        return res;
    }

    private void dfs(TreeNode root){
        if(root==null)  return;
        dfs(root.right);
        if(k==0)    return;
        if(--k==0)  res=root.val;
        dfs(root.left);
    }
}
```

### 二叉搜索树中第K小的元素

[力扣](https://leetcode-cn.com/problems/kth-smallest-element-in-a-bst/)

求中序遍历的第k个节点。

递归实现
```java
class Solution {
    int k,res;
    public int kthSmallest(TreeNode root, int k) {
        this.k=k;
        inOrderRecur(root);
        return res;
    }

    private void inOrderRecur(TreeNode root){
        if(root==null)  return;
        inOrderRecur(root.left);
        if(k==0)    return;
        if(--k==0)  res=root.val;
        inOrderRecur(root.right);
    }
}
```
迭代实现
```java
class Solution {
    public int kthSmallest(TreeNode root, int k) {
        Deque<TreeNode> stack = new LinkedList<>();
        TreeNode cur=root;
        while(cur!=null || !stack.isEmpty()){
            while(cur!=null){
                stack.push(cur);
                cur=cur.left;
            }

            cur=stack.pop();
            if(--k==0)  break;
            cur=cur.right;
        } 
        return cur.val;
    }
}
```
