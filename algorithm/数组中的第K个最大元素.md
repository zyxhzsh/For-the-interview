2020年9月10日

[力扣](https://leetcode-cn.com/problems/kth-largest-element-in-an-array/)

[思路一](#思路一)

[思路二](#思路二)

**描述**

在未排序的数组中找到第k个最大的元素。请注意，你需要找的是数组排序后的第k个最大的元素，而不是第k个不同的元素。

你可以假设k总是有效的，且 1 ≤ k ≤ 数组的长度。

#### 思路一

利用partition减治

partition（切分）操作总能排定一个元素，还能知道这个元素最终所在的位置，这样每经过一次 partition（切分）操作就能缩小搜索的范围，这样的思想叫做 “减而治之”

如果pivot点刚好是第K大元素，它的下标应该是len-k。
```
当partition函数返回的下标i=len-k，则 arr[i] 就是我们要求的第K大元素。
当partition函数返回的下标 i<len-k，那么说明第K大元素在下标 i 的右边，我们继续分区在 arr[i+1, len-1] 区间内查找：partition(arr, i+1, len-1)
当partition函数返回的下标 i>len-k，那么说明第K大元素在下标 i 的左边，我们继续分区在 arr[0, i-1] 区间内查找：partition(arr, 0, i-1)
```
可以通过随机选择pivot分区点来提高分区效率。

时间复杂度：O(n)
```
第一次分区，需要对大小为n的数组执行分区操作，遍历n个元素；
第二次分区，只需要对大小为n/2的数组执行分区操作，遍历n/2个元素；
第三次分区，遍历n/4；
第四次分区，遍历n/8；
...
n + n/2 + n/4 + n/8 + ... + 1=2n-1
所以总体时间复杂度为O(n)

空间复杂度：O(1)。
```

```java
class Solution {
    public int findKthLargest(int[] nums, int k) {
        int n = nums.length;
        int target=n-k;
        int low=0, high=n-1;
        int cur;
        while(true)
        {
            cur = partition(nums,low,high);
            if(cur==target)
            {
                return nums[cur];
            }
            else if(cur<target)
            {
                low=cur+1;
            }
            else
            {
                high=cur-1;
            }
        }
    }

 
    private int partition(int[] nums, int low, int high)
    {   
        int pivot=nums[low];
        if (high > low) {
            //在下标 low 和 high 之间随机选择，然后和下标low元素进行交换
            int random = low + new Random().nextInt(high - low);
            pivot=nums[random];
            nums[random]=nums[low];
        }
        
        while(low<high)
        {
            while(low<high && nums[high]>=pivot) high--;
            nums[low]=nums[high];
            while(low<high && nums[low]<=pivot) low++;
            nums[high]=nums[low];
        }
        nums[low]=pivot;
        return low;
    }
}
```

#### 思路二

堆排序

建立一个大根堆，做k−1次删除操作后堆顶元素就是我们要找的答案。

时间复杂度：O(nlogn)，建堆的时间代价是O(n)，删除的总代价是O(k\*logn)，因为k<n，故渐进时间复杂度为O(n+k\*logn)=O(n\*logn)=O(n\*logn)。

空间复杂度：O(logn)，即递归使用栈空间的空间代价。

```java
class Solution {
    public int findKthLargest(int[] nums, int k) {
        int heapSize=nums.length;
        buildMaxHeap(nums, heapSize);
        for(int i=nums.length-1;i>nums.length-k;i--)
        {
            swap(nums,0,i);
            --heapSize;//k-1次去掉对顶元素，最后堆顶元素就是第K个最大元素。
            maxHeapify(nums, 0, heapSize);
        }
        return nums[0];
    }

    public void buildMaxHeap(int[] a, int heapSize) {
        for (int i = heapSize/2-1; i >= 0; --i) {
            maxHeapify(a, i, heapSize);
        } 
    }

    public void maxHeapify(int[] a, int i, int heapSize) {
        int l = i * 2 + 1, r = i * 2 + 2, largest = i;
        if (l < heapSize && a[l] > a[largest]) {
            largest = l;
        } 
        if (r < heapSize && a[r] > a[largest]) {
            largest = r;
        }
        if (largest != i) {
            swap(a, i, largest);
            maxHeapify(a, largest, heapSize);
        }
    }

    public void swap(int[] nums, int i, int j) {
        int temp = nums[i];
        nums[i] = nums[j];
        nums[j] = temp;
    }
}
```
