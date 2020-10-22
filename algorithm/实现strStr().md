2020年10月22日

[力扣](https://leetcode-cn.com/problems/implement-strstr/)

- [思路一](#思路一)

- [思路二](#思路二)

**描述**

#### 思路一

双指针

只有子串的第一个字符跟 needle 字符串第一个字符相同的时候才需要比较，可以一个字符一个字符比较，一旦不匹配了就立刻终止。

这里并不需要遍历整个母字符串，而是遍历到剩下的长度和子字符串相等的位置即可，子串第一个字符的遍历范围为[0，N-L+1)。

时间复杂度：最坏时间复杂度为O((N−L)L)，最优时间复杂度为O(N)。

空间复杂度：O(1)。

```java
class Solution {
    public int strStr(String haystack, String needle) {
        if(needle.length()==0)  return 0;
        int i=0;
        while(i<haystack.length()-needle.length()+1){
            //找到haystack字符串与needle字符串第一个字符相等的位置。
            while(i<haystack.length()-needle.length()+1 && haystack.charAt(i)!=needle.charAt(0)){
                i++;
            }
            int temp=i, j=0;
            //一个字符一个字符比较，一旦不匹配了就立刻终止。
            while(i<haystack.length() && j<needle.length()){
                if(haystack.charAt(i)==needle.charAt(j)){
                    i++;
                    j++;
                }else{
                    i=temp+1;
                    break;
                }
            }
            //j==needle.length(),说明完全匹配
            if(j==needle.length())   return temp;
        }
        return -1;
    }
}
```

#### 思路二

```java
```
