2020年11月16日

[力扣](https://leetcode-cn.com/problems/best-time-to-buy-and-sell-stock-ii/)

- [思路一](#思路一)
- [思路二](#思路二)

**描述**

#### 思路一

贪心

问题可以简化为找x个长度为1的区间 (l_i,l_i+1]使得∑a[l_i+1]-a[l_i](i=1,2,...x)价值最大化。

贪心的角度考虑我们每次选择贡献大于0的区间即能使得答案最大化，因此最后答案为

ans=∑max(0,a[i]-a[i-1])(i=1,2,...n-1).其中n为数组的长度。

时间复杂度：O(n)。

空间复杂度：O(1)。

```java
class Solution {
    public int maxProfit(int[] prices) {
        int max=0;
        for(int i=1;i<prices.length;i++){
            max+=Math.max(0,prices[i]-prices[i-1]);
        }
        return max;
    }
}
```

#### 思路二

动态规划

不能同时参与多笔交易，因此每天交易结束后只可能存在手里有一支股票或者没有股票的状态。

定义状态dp[i][0] 表示第i天交易完后手里没有股票的最大利润，dp[i][1] 表示第i天交易完后手里持有一支股票的最大利润（i从0开始）。

dp[i][0]=max{dp[i−1][0],dp[i−1][1]+prices[i]}（i-1天就不持有，或者i-1天持有且第i天卖了）

dp[i][1]=max{dp[i−1][1],dp[i−1][0]−prices[i]}（i-1天就持有，或者i-1天不持有但第i天买入）

每一天的状态只与前一天的状态有关，而与更早的状态都无关，因此我们不必存储这些无关的状态，只需要四个变量即可。

时间复杂度：O(n)。一共有2n个状态。

空间复杂度：O(1)

```java
class Solution {
    public int maxProfit(int[] prices) {
        int max=0;
        int dp0=0;
        int dp1=-prices[0];
        for(int i=1;i<prices.length;i++){
            int temp1 = Math.max(dp0, dp1+prices[i]);
            int temp2 = Math.max(dp1, dp0-prices[i]);
            dp0=temp1;
            dp1=temp2;
        }
        return dp0;
    }
}
```
