2020年10月8日

[力扣](https://leetcode-cn.com/problems/top-k-frequent-elements/submissions/)

- [思路一](#思路一)
- [思路二](#思路二)

**描述**

给定一个非空的整数数组，返回其中出现频率前 k 高的元素。

示例 1:
```
输入: nums = [1,1,1,2,2,3], k = 2
输出: [1,2]
```
示例 2:
```
输入: nums = [1], k = 1
输出: [1]
```

提示：

你可以假设给定的 k 总是合理的，且 1 ≤ k ≤ 数组中不相同的元素的个数。

你的算法的时间复杂度必须优于 O(n log n) , n 是数组的大小。

题目数据保证答案唯一，换句话说，数组中前 k 个高频元素的集合是唯一的。

你可以按任意顺序返回答案。

#### 思路一

最小堆

首先遍历整个数组，并使用哈希表记录每个数字出现的次数。

维护一个元素数目为k的最小堆，遍历哈希表，当堆的容量等于k时，将每次将新的元素与堆顶元素（堆中频率最小的元素）进行比较：如果新的元素的频率比堆顶端的元素大，则弹出堆顶端的元素，将新的元素添加进堆中。

当堆的容量小于k时，直接将元素加入堆中。

最终，堆中的k个元素即为前k个高频元素。

时间复杂度：O(Nlogk)，其中N为数组的长度。我们首先遍历原数组，并使用哈希表记录出现次数，共需 O(N)O(N) 的时间。

随后，我们遍历哈希表，由于堆的大小至多为k，如果元素的频率大于最小堆中顶部的元素，则将顶部的元素删除并将该元素加入堆中，这些操作需要O(logk) 的时间，所以遍历完哈希表需要O(n*logk)的时间复杂度。

空间复杂度：空间复杂度：O(N)。最坏情况下（每个元素都不同），map 需要存储n个键值对，哈希表的大小为O(N)。而堆的大小为O(k)，共计为O(N)。

```java
class Solution {
    public int[] topKFrequent(int[] nums, int k) {
        Map<Integer, Integer> map = new HashMap<>();
        for(int num:nums){
            if(map.containsKey(num)){
                map.put(num, map.get(num)+1);
            }
            else{
                map.put(num,1);
            }
        }

        PriorityQueue<Integer> min_heap = new PriorityQueue<>(new Comparator<Integer>(){
            @Override
            public int compare(Integer a, Integer b){
                return map.get(a)-map.get(b);
            }
        });//堆中的元素根据构造队列时提供的Comparator进行排序，这里是根据映射值正序排序，也就是频率。
        
        /*最小堆中只保留k个元素，如果新的元素的频率比堆顶端的元素大，则弹出堆顶端的元素，将新的元素添加进堆中。
        最终，堆中的 kk 个元素即为前 kk 个高频元素*/
        for(Integer key:map.keySet()){
            if(min_heap.size()<k){
                min_heap.add(key);
            }
            else if(map.get(key)>=map.get(min_heap.peek())){
                min_heap.remove();
                min_heap.add(key);
            }
        }
        int[] res = new int[k];
        for(int i=0;i<k;i++){
            res[i]=min_heap.remove();
        }
        return res;
    }
}
```

#### 思路二

使用桶排序，在建立好数字和其出现次数的映射后，创建一个数组，将频率作为数组下标。对于出现频率不同的数字集合，存入对应的数组下标。我们按照其出现次数将数字放到对应的位置中去，这样我们从桶的后面向前面遍历，最先得到的就是出现次数最多的数字，我们找到k个后返回即可。

注意不同的元素的频数可能会相同，所以使用list数组。

时间复杂度：O(n)，n表示数组的长度。首先，遍历一遍数组统计元素的频率，这一系列操作的时间复杂度是O(n)；桶的数量为n+1，所以遍历桶的时间复杂度为O(n)；因此，总的时间复杂度是O(n)。

空间复杂度：O(n)。

```java
class Solution {
    public int[] topKFrequent(int[] nums, int k) {
        Map<Integer, Integer> map = new HashMap<>();
        for(int num:nums){
            if(map.containsKey(num)){
                map.put(num, map.get(num)+1);
            }
            else{
                map.put(num,1);
            }
        }

        List<Integer>[] list = new List[nums.length+1];//频数可能相同，所以用list数组。
        int[] ans = new int[k];

        for(int key:map.keySet()){
            int i = map.get(key);
            if(list[i] == null){
               list[i] = new ArrayList();
            } 
            list[i].add(key);
        }
        int j=0;
        for(int i=nums.length;i>=1 && j<k;i--){
            if(list[i]==null)   continue;
            int iLen=list[i].size();
            int temp=0;
            for(;j<k && temp<iLen;j++){
                ans[j]=list[i].get(temp);
                temp++;
            }
        }
        return ans;
    }
}
```
