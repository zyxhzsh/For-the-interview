2020年11月19日

[力扣](https://leetcode-cn.com/problems/powx-n/)

- [思路一](#思路一)
- [思路二](#思路二)

**描述**

实现 pow(x, n) ，即计算 x 的 n 次幂函数。

说明:

-100.0 < x < 100.0

n 是 32 位有符号整数，其数值范围是 [−231, 231 − 1] 。

#### 思路一

快速幂迭代实现

从x开始，每次直接把上一次的结果进行平方，把幂从n降至n/2 ，直至将幂降为0；

设res=1 ，则初始状态 x^n = x^n * res 

在循环二分时，每当n为奇数时，将多出的一项x乘入res ，则最终可化至x^n = x^0 * res，返回res即可。

最后一次循环时n一定为1，因为除以2相当于右移，最后一定只有一个1且1在最低位，然后再移一次n就变为0了。

当n<0时：把问题转化至n≥0的范围内。Integer.MIN_VALUE取绝对值会溢出，因为用long变量保存n。 

n为负数时x可以取导数，然后计算pow(x,|n|)。也可以先计算pow(x,|n|)，得到的结果res再取倒数。

```java
class Solution {
    public double myPow(double x, int n) {

        if(n==0)    return 1;
        double res=1;
        boolean sign = n>0?true:false;

        long b = n;//Integer.MIN_VALUE取绝对值会溢出，因为用long变量保存n。 
        b = Math.abs(b);
        while(b>0){
            if(b%2==1)  res*=x;
            x*=x;
            b>>=1;
        }

        return sign?res:(1.0/res);
    }
}
```

时间复杂度：O(logn)。n每次减小一半，二分的时间复杂度为对数级别。

空间复杂度 O(1)。

#### 思路二

快速幂递归实现

当我们要计算x^n时，我们可以先递归地计算出y=x^⌊n/2⌋。

根据递归计算的结果，如果n为偶数，那么x^n=y*y;如果n为奇数，那么x^n=y*y*x。

递归的边界为n=0，任意数的0次方均为1。

时间复杂度：O(logn)，即为递归的层数。

时间复杂度：O(logn)，递归使用的栈空间为O(logn)。

```java
class Solution {
    public double myPow(double x, int n) {

        if(n==0)    return 1.0;
        long b = n;
        return b > 0 ? qucikMul(x, b) : 1.0/qucikMul(x, -b);
    }

    private double qucikMul(double x, long b){

        if(b==0)    return 1.0;
        double y = qucikMul(x, b/2);
        return b%2 == 0 ? y*y : y*y*x;
    }
}
```
