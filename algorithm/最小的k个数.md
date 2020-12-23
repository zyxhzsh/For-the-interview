2020年10月10日

[力扣](https://leetcode-cn.com/problems/zui-xiao-de-kge-shu-lcof/solution/)

- [思路一](#思路一)
- [思路二](#思路二)
- [思路三](#思路三)

**描述**

输入整数数组 arr ，找出其中最小的 k 个数。例如，输入4、5、1、6、2、7、3、8这8个数字，则最小的4个数字是1、2、3、4。

示例 1：
```
输入：arr = [3,2,1], k = 2
输出：[1,2] 或者 [2,1]
```
示例 2：
```
输入：arr = [0,1,2,1], k = 1
输出：[0]
```
限制：

0 <= k <= arr.length <= 10000

0 <= arr[i] <= 10000

#### 思路一

先排序，然后取出前k个数。

时间复杂度：O(nlogn)，其中n是数组 arr 的长度。算法的时间复杂度即排序的时间复杂度。

空间复杂度：O(logn)，排序所需额外的空间复杂度为O(logn)。

```java
class Solution {
    public int[] getLeastNumbers(int[] arr, int k) {
        Arrays.sort(arr);
        int[] ans = new int[k];
        for(int i=0;i<k;i++){
            ans[i]=arr[i];
        }
        return ans;
    }
}
```

#### 思路二

堆排序

用一个大根堆实时维护数组的前k小值。首先将前k个数插入大根堆中，随后从第k+1个数开始遍历，如果当前遍历到的数比大根堆的堆顶的数要小，就把堆顶的数弹出，再插入当前遍历到的数。最后将大根堆里的数存入数组返回即可。

时间复杂度：O(nlogk)，其中n是数组 arr 的长度。由于大根堆实时维护前k小值，所以插入删除都是O(logk)的时间复杂度，最坏情况下数组里n个数都会插入，所以一共需要O(nlogk)的时间复杂度。

空间复杂度：O(k)，因为大根堆里最多k个数。

```java
class Solution {
    public int[] getLeastNumbers(int[] arr, int k) {
        if(k==0) return new int[0];
        PriorityQueue<Integer> max_heap = new PriorityQueue<>(new Comparator<Integer>(){
            @Override
            public int compare(Integer a, Integer b){
                return b-a;
            }
        });

        for(int i=0;i<arr.length;i++){
            if(max_heap.size()<k){
                max_heap.offer(arr[i]);
            }
            else if(max_heap.peek()>arr[i]){
                max_heap.poll();
                max_heap.offer(arr[i]);
            }
        }

        int[] res = new int[k];
        for(int i=0;i<k;i++){
            res[i]=max_heap.poll();
        }
        return res;
    }
}
```

### 思路三

快速排序,每次只处理第k大的元素所在的那段。当partition返回的值是k-1时，数组左边的前k个元素就是最小的k个元素，返回这k个元素即可。

时间复杂度：期望为 O(n)，每次调用partition遍历的元素数目都是上一次遍历的1/2：N + N/2 + N/4 + ... + N/N = 2N, 因此时间复杂度是 O(n)。

最坏为O(n^2)。情况最差时，每次的划分点都是最大值或最小值，最后才找到k-1，一共需要划分n−1次，而一次划分需要O(n)时间复杂度，所以最坏情况下时间复杂度为O(n^2)。

空间复杂度：O(k),需要长度为k的数组保存答案。

```java
class Solution {
    public int[] getLeastNumbers(int[] arr, int k) {
        if(k==0)    return new int[0];
        int low=0, high=arr.length-1;
        int cur;
        while(true){
            cur = Partition(low, high, arr);
            if(cur==k-1){
                break;
            }
            else if(cur<k){
                low=cur+1;
            }
            else{
                high=cur-1;
            }
        }
        int[] res = new int[k];
        for(int i=0;i<k;i++){
            res[i]=arr[i];
        }
        return res;
    }

    private int Partition(int low, int high, int[] arr){
        int pivot=arr[low];
        if(high>low){
            int random = low + new Random().nextInt(high-low);
            pivot=arr[random];
            arr[random]=arr[low];
        }
        while(low<high){
            while(low<high && arr[high]>=pivot) high--;
            arr[low]=arr[high];
            while(low<high && arr[low]<pivot) low++;
            arr[high]=arr[low];
        }
        arr[low]=pivot;
        return low;
    }
}
```
