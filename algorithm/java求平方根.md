### java求平方根

1.如果是整数求平方根，且结果只保留整数，那么可以用二分查找。由于x平方根的整数部分**ans是满k^2≤x的最大k值**，因此我们可以对k进行二分查找，从而得到答案。

[力扣](https://leetcode-cn.com/problems/sqrtx/solution/x-de-ping-fang-gen-by-leetcode-solution/)

时间复杂度：O(logx)，即为二分查找需要的次数。

空间复杂度：O(1)。
```java
class Solution {
    public int mySqrt(int x) {
        int l = 0, r = x, ans = -1;
        while (l <= r) {
            int mid = l + (r - l) / 2;
            if ((long) mid * mid <= x) {
                ans = mid;
                l = mid + 1;
            } else {
                r = mid - 1;
            }
        }
        return ans;
    }
}
```
当然这题也可以用牛顿迭代法，最后转成int返回
```java
class Solution {
    public int mySqrt(int x) {
        if(x==0)    return 0;
        long res=x/2+1;
        while(res*res>x && res*res-x>=1){
            res=(res+x/res)/2;
        }
        return (int)res;
    }
}
```

2.如果是浮点数求平方根，可以用牛顿迭代法。

令f(x)=x^2-n，相当于求f(x)=0的解。首先取一个起始点x0,如果x0不是解，做一个经过(x0,f(x0))这个点的切线，与x轴的交点为x1。

显然这条线的函数为：y=(x-x0)+f(x0)。

令y=0，易得x1=x0-f(x0)/f'(x0)。将函数带入得x1=(x0+n/x0)/2;就得到了迭代公式，以这样的方式得到的xi会无限趋近于f(x)=0的解。

**注意**：不管f(x)是几次方程，迭代公式都是：x_(i+1)=xi-f(xi)/f'(xi)

判断xi是否是f(x)=0的解有三种方法：一是直接计算f(xi)的值判断是否y与0无限接近，二是判断前后两个解xi和xi-1是否无限接近，三是判断(xi)^2与n是否无限接近。任选一种均可。

由于迭代可能无限进行，还有可能一直在真实值的某一端。可以设一个精度范围，误差在此范围内就可以返回结果了。
但使用牛顿迭代，有的数可能达不到该精度，为了避免无限循环，可以设置一个最大迭代次数。这样即使始终达不到精度要求，也不会陷入死循环，最终会返回一个结果。

```java
import java.util.*;

public class Test {
    public static void main(String[] args){
        Scanner sc = new Scanner(System.in);
        while(sc.hasNext()){
            Double x = sc.nextDouble();
            System.out.print(x+"的平方根是：");
            Double temp = mySqrt(x);
            System.out.print(temp);
            System.out.println(" 误差为："+(x-temp*temp));
            System.out.print(x+"用系统函数："+Math.sqrt(x));
            System.out.println(" 误差为："+(x-Math.sqrt(x)*Math.sqrt(x)));
        }
    }
    public static Double mySqrt(Double x) {
        if(x==0)    return 0.0;
        int count=10000;//最大迭代次数
        Double res=x/2+1;//对于一个非负数n，它的平方根不会大于(n/2+1）
        while(res*res>x && res*res-x>1.0E-10 && count-->0){
            res=(res+x/res)/2;
        }
        return res;
    }
}
```
