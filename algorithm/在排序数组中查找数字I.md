[力扣](https://leetcode-cn.com/problems/zai-pai-xu-shu-zu-zhong-cha-zhao-shu-zi-lcof/)

统计一个数字在排序数组中出现的次数。

示例 1:

输入: nums = [5,7,7,8,8,10], target = 8
输出: 2
示例 2:

输入: nums = [5,7,7,8,8,10], target = 6
输出: 0
 
限制：

0 <= 数组长度 <= 50000

本题与[主站34题](在排序数组中查找元素的第一个和最后一个位置.md)相同（仅返回值不同）

```java
class Solution {
    public int search(int[] nums, int target) {
        if(nums.length==0)  return 0;
        int left=findfirst(nums, target);
        if(left>=nums.length || nums[left]!=target)  return 0;
        int right=findfirst(nums, target+1)-1;
        return right-left+1;
    }

    private int findfirst(int[] nums, int target){
        int left=0;
        int right=nums.length;
        while(left<right){
            int mid=left+(right-left)/2;
            if(nums[mid]<target){
                left=mid+1;
            }else{
                right=mid;
            }
        }
        return left;
    }
}
```
