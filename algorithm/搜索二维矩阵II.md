2020年9月23日

[力扣](https://leetcode-cn.com/problems/search-a-2d-matrix-ii/)

[剑指](https://leetcode-cn.com/problems/er-wei-shu-zu-zhong-de-cha-zhao-lcof/)

**描述**

编写一个高效的算法来搜索 m x n 矩阵 matrix 中的一个目标值 target。该矩阵具有以下特性：

每行的元素从左到右升序排列。
每列的元素从上到下升序排列。
示例:

现有矩阵 matrix 如下：

[
  [1,   4,  7, 11, 15],
  [2,   5,  8, 12, 19],
  [3,   6,  9, 16, 22],
  [10, 13, 14, 17, 24],
  [18, 21, 23, 26, 30]
]
给定 target = 5，返回 true。

给定 target = 20，返回 false。

#### 思路

看作排序二叉树

选左上角，往右走和往下走都增大，不能选

选右下角，往上走和往左走都减小，不能选

选左下角，往右走增大，往上走减小，可选

选右上角，往下走增大，往左走减小，可选

以右上角为例，向左数字变小，向右数字变大，所以可以把target和当前值比较。
```
如果 target 的值大于当前值，那么就向下走。
如果 target 的值小于当前值，那么就向左走。
如果相等的话，直接返回 true 。
```
时间复杂度： O(m+n)。每次迭代（我们不返回 true）时，行或列都会精确地递减/递增一次。行最多减少m次，而列最多增加n次。所以循环不会超过n+m次。
空间复杂度：O(1)，因为这种方法只处理几个指针，所以它的内存占用是恒定的。

```java
class Solution {
    public boolean searchMatrix(int[][] matrix, int target) {
        if(matrix.length==0 || matrix[0].length==0 )
            return false;
        int rows = matrix.length;
        int columns = matrix[0].length;
        int row=0;
        int column=columns-1;
        
        //从右上角开始遍历
        while(row<rows && column>=0){
            if(matrix[row][column]==target)
                return true;
            else if(matrix[row][column]<target)
                row++;//当前值小于目标值，就向下走
            else
                column--;//当前值大于目标值，就向左走
        }
        return false;
    }
}
```
