2020年11月10日

[力扣](https://leetcode-cn.com/problems/he-wei-sde-lian-xu-zheng-shu-xu-lie-lcof/)

**描述**

输入一个正整数 target ，输出所有和为 target 的连续正整数序列（至少含有两个数）。

序列内的数字由小到大排列，不同序列按照首个数字从小到大排列。

示例：
```
输入：target = 15
输出：[[1,2,3,4,5],[4,5,6],[7,8]]
```
限制：

1 <= target <= 10^5

#### 思路

用small和big分别表示当前序列的最小值和最大值，small初始化为1，big初始化为2。这样初始化，small和big就只需要右移。

如果small到big的序列和大于s，则去掉序列中最小的值，即small++;

如果small到big的序列和小于s，则增大序列中最大的值，即big++;

因为序列至少有两个数，所以增加small到大于(target-1)/2为止。（small最大为(target-1)/2）

tip：可以用一个循环求连续序列的和，但考虑到每次操作之后的序列和操作之前的序列值相差一个元素，因此我们可以在前一个序列和的基础上求操作之后的序列和。用变量cursum记录当前序列和，初始化为3。

时间复杂度：O(target)。由于两个指针移动均单调不减，且small不会超过(target-1)/2，big不会超过(target+1)/2,所以时间复杂度为O(target)。

空间复杂度：O(1)。除了答案数组只需要常数的空间存放若干变量
```java
class Solution {
    public int[][] findContinuousSequence(int target) {
        int small=1,big=2;
        int cursum=small+big;
        int maxSmall=(target-1)/2;
        List<int[]> res = new ArrayList<int[]>();
        while(small<=maxSmall){
            if(cursum==target){
                int[] cur=new int[big-small+1];
                for(int i=small;i<=big;i++){
                    cur[i-small]=i;
                }
                res.add(cur);
            }
            while(cursum>target && small<=maxSmall){
                cursum-=small;
                small++;
                if(cursum==target){
                    int[] cur=new int[big-small+1];
                    for(int i=small;i<=big;i++){
                        cur[i-small]=i;
                    }   
                    res.add(cur);
                }
            }
            /*执行到此处时，要么small<maxSmall，要么cursum<=target。若small<maxSmall，本次循环结束就跳出循环了，
            若cursum==target，前面已经记录。所以不管cursum小于还是等于target,都增大big*/
            big++;
            cursum+=big;
        }
        return res.toArray(new int[res.size()][]);
    }
}
```
