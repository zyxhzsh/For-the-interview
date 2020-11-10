2020年11月10日

[力扣](https://leetcode-cn.com/problems/shu-zu-zhong-shu-zi-chu-xian-de-ci-shu-ii-lcof/)

- [思路一](#思路一)
- [思路二](#思路二)

**描述**

在一个数组 nums 中除一个数字只出现一次之外，其他数字都出现了三次。请找出那个只出现一次的数字。

限制：

1 <= nums.length <= 10000

1 <= nums[i] < 2^31

#### 思路一

考虑数字的二进制形式，对于出现三次的数字，各二进制位出现的次数都是3的倍数。

因此，统计所有数字的各二进制位中1的出现次数，并对3求余，结果则为只出现一次的数字。

（1）建立一个长度为32的数组 counts,记录所有数字的各二进制位的1的出现次数。使用与运算，配合无符号右移操作，可获取元素所有位的值。

（2）将counts各元素对3求余，则结果为 “只出现一次的数字” 的各二进制位。

（3）利用左移操作和或运算 ，可将counts数组中各二进位的值恢复到数字res上

时间复杂度：O(N)。

空间复杂度：O(1)。

```java
class Solution {
    public int singleNumber(int[] nums) {
        int[] count = new int[32];
        for(int num:nums){
            for(int j=0;j<32;j++){
                count[j]+=num&1;
                num>>>=1;
            }
        }

        int res=0;
        for(int i=0;i<32;i++){
            res<<=1;
            res |= count[31-i]%3;
        }
        return res;
    }
}
```


