2020年11月16日

[力扣](https://leetcode-cn.com/problems/spiral-matrix-ii/)

**描述**

给定一个正整数 n，生成一个包含 1 到 n2 所有元素，且元素按顺时针顺序螺旋排列的正方形矩阵。

示例:
```
输入: 3
输出:
[
 [ 1, 2, 3 ],
 [ 8, 9, 4 ],
 [ 7, 6, 5 ]
]
```

#### 思路

设置边界l,r,up,down，模拟螺旋的过程。

时间和空间复杂度均为O(n^2)。

```java
class Solution {
    public int[][] generateMatrix(int n) {
        int[][] res = new int[n][n];
        int l=0,r=n-1,up=0,down=n-1;
        int k=1;
        while(l<=r && up<=down){
            for(int i=l;i<=r;i++)  res[up][i]=k++;
            up++;
            for(int i=up;i<=down;i++)  res[i][r]=k++;
            r--;
            for(int i=r;i>=l;i--)    res[down][i]=k++;
            down--;
            for(int i=down;i>=up;i--)    res[i][l]=k++;
            l++;
        }
        return res;
    }
}
```


