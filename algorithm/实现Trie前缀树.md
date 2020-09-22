2020年9月22日

[力扣](https://leetcode-cn.com/problems/implement-trie-prefix-tree/)

**描述**

实现一个 Trie (前缀树)，包含 insert, search, 和 startsWith 这三个操作。

示例:
```
Trie trie = new Trie();

trie.insert("apple");
trie.search("apple");   // 返回 true
trie.search("app");     // 返回 false
trie.startsWith("app"); // 返回 true
trie.insert("app");   
trie.search("app");     // 返回 true
```
说明:

你可以假设所有的输入都是由小写字母 a-z 构成的。
保证所有输入均为非空字符串。
```
/**
 * Your Trie object will be instantiated and called as such:
 * Trie obj = new Trie();
 * obj.insert(word);
 * boolean param_2 = obj.search(word);
 * boolean param_3 = obj.startsWith(prefix);
 */
```

#### 思路

尽管哈希表可以在O(1)时间内寻找键值，却无法高效的完成以下操作：

找到具有同一前缀的全部键值。按词典序枚举字符串的数据集。

Trie树优于哈希表的另一个理由是，随着哈希表大小增加，会出现大量的冲突，时间复杂度可能增加到O(n)，其中n是插入的键的数量。

与哈希表相比，Trie树在存储多个具有相同前缀的键时可以使用较少的空间。此时Trie树只需要O(m)的时间复杂度，其中m为键长。而在平衡树中查找键值需要O(mlogn) 时间复杂度。

插入
```
时间复杂度：O(m)，其中m为键长。在算法的每次迭代中，我们要么检查要么创建一个节点，直到到达键尾。只需要m次操作。
空间复杂度：O(m)。最坏的情况下，新插入的键和 Trie 树中已有的键没有公共前缀。此时需要添加m个结点，使用O(m)空间。
```
查找单词
```
时间复杂度 : O(m)。算法的每一步均搜索下一个键字符。最坏的情况下需要m次操作。
空间复杂度 : O(1)。
```
查找前缀。和search操作类似，只是不需要判断最后一个字符结点的isEnd。
```
时间复杂度 : O(m)。
空间复杂度 : O(1)。
```
```java
class Trie {

    private boolean isEnd = false;//该结点是否是一个串的结束
    private Trie[] next = new Trie[26];//字母映射表，保存了对当前结点而言下一个可能出现的所有字符的链接。

    /** Initialize your data structure here. */
    public Trie() {
        
    }
    
    /** Inserts a word into the trie. */
    public void insert(String word) {
        Trie cur = this;//获取当前的字母数组next
        for(char c:word.toCharArray()){
            if(cur.next[c-'a']==null){
                //如果当前字符不在next中，生成一个新的节点
                cur.next[c-'a']=new Trie();
            }
            cur=cur.next[c-'a'];
        }
        cur.isEnd = true;
    }
    
    /** Returns if the word is in the trie. */
    public boolean search(String word) {
        Trie cur = this;//获取当前的字母数组next
        for(char c:word.toCharArray()){
            if(cur.next[c-'a']==null){
                //出现结点值为空就返回false
                return false;
            }
            cur=cur.next[c-'a'];
        }
        return cur.isEnd;//如果匹配到了最后一个字符，只需判断 node->isEnd即可。
    }
    
    /** Returns if there is any word in the trie that starts with the given prefix. */
    public boolean startsWith(String prefix) {
        /*
        和 search 操作类似，只是不需要判断最后一个字符结点的isEnd，
        因为既然能匹配到最后一个字符，那后面一定有单词是以它为前缀的。
        */
        Trie cur = this;//获取当前的字母数组next
        for(char c:prefix.toCharArray()){
            if(cur.next[c-'a']==null){
                return false;
            }
            cur=cur.next[c-'a'];
        }
        return true;
    }
}
```
