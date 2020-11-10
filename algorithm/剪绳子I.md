2020年11月10日

[力扣](https://leetcode-cn.com/problems/jian-sheng-zi-lcof/)

- [思路一](#思路一)
- [思路二](#思路二)

**描述**

给你一根长度为 n 的绳子，请把绳子剪成整数长度的 m 段（m、n都是整数，n>1并且m>1），每段绳子的长度记为 k[0],k[1]...k[m-1] 。请问 k[0]*k[1]*...*k[m-1] 可能的最大乘积是多少？例如，当绳子的长度是8时，我们把它剪成长度分别为2、3、3的三段，此时得到的最大乘积是18。

提示：

2 <= n <= 58

#### 思路一

动态规划

对于的正整数 n，当 n≥2 时，可以拆分成至少两个正整数的和。令 k 是拆分出的第一个正整数，则剩下的部分是 n−k，n−k可以不继续拆分，或者继续拆分成至少两个正整数的和。
由于每个正整数对应的最大乘积取决于比它小的正整数对应的最大乘积，因此可以使用动态规划求解。

dp[i] 表示将正整数 i 拆分成至少两个正整数的和之后，这些正整数的最大乘积。

边界条件： 0 不是正整数，1是最小的正整数，0和1都不能拆分，因此 dp[0]=dp[1]=0。

状态转移方程：
```
当 i≥2 时，假设对正整数 i 拆分出的第一个正整数是 j（1≤j<i），则有以下两种方案：

将 i 拆分成 j 和 i−j 的和，且 i−j 不再拆分成多个正整数，此时的乘积是 j×(i−j)；
将 i 拆分成 j 和 i−j 的和，且 i−j 继续拆分成多个正整数，此时的乘积是 j×dp[i−j]。
由于 j 的取值范围是 1 到 i−1，需要遍历所有的 j 得到 dp[i] 的最大值。
```
最终得到dp[n]的值即为将正整数 n 拆分成至少两个正整数的和之后，这些正整数的最大乘积。

时间:o(n*n) 

空间：o(n)
```java
class Solution {
    public int cuttingRope(int n) {
        int[] dp = new int[n+1];
        for(int i=2;i<=n;i++){
            for(int j=1;j<i;j++){
                int temp=Math.max(j*(i-j), j*dp[i-j]);
                dp[i]=Math.max(dp[i],temp);
            }
        }
        return dp[n];
    }
}
```

#### 思路二

数学：函数极值。

直觉上把数拆的越平均他们的积越大。拆分的整数越接近自然参数e，他们的乘积的越大。

如果 n≤3:f(1)=0,f(2)=1,f(3)=2。即当n≤3 时，最大乘积是n-1。

n≥4时，根据n除以3的余数进行分类讨论，m为商：
```
如果余数为 0，则将 n 拆分成 m 个 3；
如果余数为 1，因此将 n 拆分成 m-1 个 3 和 2 个 2；
如果余数为 2，则将 n 拆分成 m 个 3 和 1 个 2。
```
时间复杂度 O(1)

空间复杂度 O(1)
```java
class Solution {
    public int cuttingRope(int n) {
        if (n <= 3) {
            return n - 1;
        }
        int quotient = n / 3;//商
        int remainder = n % 3;//余数
        if (remainder == 0) {
            return (int) Math.pow(3, quotient);
        } else if (remainder == 1) {
            return (int) Math.pow(3, quotient - 1) * 4;
        } else {
            return (int) Math.pow(3, quotient) * 2;
        }
    }
}
```
