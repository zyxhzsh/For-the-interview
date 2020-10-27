2020年9月12日

[力扣](https://leetcode-cn.com/problems/subarray-sum-equals-k/submissions/)

[思路一](#思路一)

[思路二](#思路二)

**描述**

给定一个整数数组和一个整数 k，你需要找到该数组中和为 k 的连续的子数组的个数。

示例 1 :
```
输入:nums = [1,1,1], k = 2
输出: 2 , [1,1] 与 [1,1] 为两种不同的情况。
说明 :
数组的长度为 [1, 20,000]。
数组中元素的范围是 [-1000, 1000] ，且整数 k 的范围是 [-1e7, 1e7]。
```

#### 思路一

枚举

两层循环，i为起点，j为终点。统计和为K的个数。

时间复杂度：O(n^2)。

空间复杂度：O(1)。
```java
class Solution {
    public int subarraySum(int[] nums, int k) {
        int count=0;
        for(int i=0;i<nums.length;i++)
        {
            int sum=0;
            for(int j=i;j<nums.length;j++)
            {
                sum+=nums[j];
                if(sum==k)
                    count++;
            }
        }
        return count;
    }
}
```

#### 思路二

前缀和 + 哈希表优化

定义pre[i]为[0..i]里所有数的和，则pre[i]可由pre[i-1]递推得出。

[i,j]子数组和为K可以转化为pre[j]-pre[i-1]=K，即pre[i-1]=pre[j]-K。

因为pre[i]是个数值，且数组中有负数，可能对于不同的i,pre[i]是相等的。所以以j结尾，和为k。开头i可能不止一个。哈希表中键为pre[i]，值为满足pre[i]的不同的i的个数。

所以遍历到j时，先求出pre[j]，查询哈希表中有没有键为pre[j]-K，若有则count增加的次数为键pre[j]-K对应的值。然后将pre[j]作为键存入哈希表，若已有该键则值加1，若没有则值设为1。

因为子数组可能从0开始,所以需要在哈希表里存入(0,1)键值对，表示初始时使pre[i]为0的i有一个取值。

例：
```
比如和为5。 元素是[0,1,-1,2,3]
sum [i]表示下标0-i的和。
sum[4]=0+1-1+2+3=5。
sum [0]=sum [4]-5.说明[1,4]和为5.
sum[2]=sum[4]-5.说明[3,4]和为5

sum0和sum2都为0，所以要记和为0的元素数为2，把和为0的次数存到value里。
遍及到下标为4的时候，就用哈希表查满足前i个数的和为sum[4]-5,这样的i有几个。
```

时间复杂度：O(n)，其中n为数组的长度。我们遍历数组的时间复杂度为O(n)，中间利用哈希表查询删除的复杂度均为O(1)，因此总时间复杂度为O(n)。

空间复杂度：O(n)，其中n为数组的长度。哈希表在最坏情况下可能有n个不同的键值，因此需要O(n)的空间复杂度。

```java
class Solution {
    public int subarraySum(int[] nums, int k) {
        int count=0, pre=0;
        HashMap<Integer, Integer> map = new HashMap<>();
        map.put(0,1);
        for(int i=0;i<nums.length;i++)
        {
            pre += nums[i];
            if(map.containsKey(pre-k))
                count+=map.get(pre-k);
            map.put(pre, map.getOrDefault(pre,0)+1);
        }
        return count;
    }
}
```
