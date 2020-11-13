2020年11月13日

[力扣](https://leetcode-cn.com/problems/reverse-words-in-a-string-iii/)

**描述**

#### 思路

将字符串转为char数组。

用两个指针i,j初始值均为n-1。循环条件为i>=0;每次循环先让i左移找到空格，[i+1,j]确定了一个单词,char数组中对这个单词进行原地翻转，然后i减1,j=i(因为单词之前只有一个空格)

时间复杂度：O(n)。

空间复杂度：O(n)。

```java
class Solution {
    public String reverseWords(String s) {
        char[] ori=s.toCharArray();
        int n=s.length();
        int i=n-1,j=n-1;
        while(i>=0){
            while(i>=0 && ori[i]!=' ')  i--;
            int mid=i+1+(j-i)/2;
            for(int k=i+1;k<mid;k++){
                char temp=ori[k];
                ori[k]=ori[j+i+1-k];
                ori[j+i+1-k]=temp;
            }
            i--;
            j=i;
        }
        return String.valueOf(ori);
    }
}
```

