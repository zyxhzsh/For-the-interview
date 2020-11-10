2020年11月10日

[力扣](https://leetcode-cn.com/problems/jian-sheng-zi-ii-lcof/)

- [思路一](#思路一)
- [思路二](#思路二)

**描述**

给你一根长度为 n 的绳子，请把绳子剪成整数长度的 m 段（m、n都是整数，n>1并且m>1），每段绳子的长度记为 k[0],k[1]...k[m - 1] 。请问 k[0]*k[1]*...*k[m - 1] 可能的最大乘积是多少？
例如，当绳子的长度是8时，我们把它剪成长度分别为2、3、3的三段，此时得到的最大乘积是18。

答案需要取模 1e9+7（1000000007），如计算初始结果为：1000000008，请返回 1。

#### 思路一

取余之后max函数就不能用来比大小了，所以这题如果要用动态规划，就需要用java.math.BigInteger。

效率极低。

时间:o(n*n)

空间：o(n)
```java
import java.math.BigInteger;
class Solution {
    public int cuttingRope(int n) {
        BigInteger dp[] = new BigInteger[n + 1];
        Arrays.fill(dp, BigInteger.valueOf(1));
        for (int i = 3; i <= n; i++) {
            for (int j = 1; j < i; j++) {
                dp[i] = dp[i].max(BigInteger.valueOf(j * (i - j))).max(dp[i - j].multiply(BigInteger.valueOf(j)));
            }
        }
        return  dp[n].mod(BigInteger.valueOf(1000000007)).intValue();
    }
}
```

#### 思路二

[数学推导](https://leetcode-cn.com/problems/jian-sheng-zi-ii-lcof/solution/mian-shi-ti-14-ii-jian-sheng-zi-iitan-xin-er-fen-f/)

大数求余会越界，解决方案：
```
n<=3时，返回n-1;
n==4时，返回n;
n>4时，循环累乘，乘积res初始为1，每次乘以3后取模，然后n减3。循环结束后n一定小于等于4，最后res乘以n取模并转换成int即可。

乘积可能会超过int，要保证最大能存储1000000007^2，所以res应取long类型。
```

时间复杂度：O(n)。

空间复杂度 O(1)。
```java
class Solution {
    public int cuttingRope(int n) {
        if(n<4) return n-1;
        if(n==4) return n;
        long res=1;
        while(n>4){
            res=res*3%1000000007;
            n-=3;
        }
        return  (int)(n*res%1000000007);
    }
}
```
