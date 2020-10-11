2020年10月11日

[力扣](https://leetcode-cn.com/problems/bao-han-minhan-shu-de-zhan-lcof/)

- [思路一](#思路一)

- [思路二](#思路二)

- [思路三](#思路三)

**描述**

定义栈的数据结构，请在该类型中实现一个能够得到栈的最小元素的 min 函数在该栈中，调用 min、push 及 pop 的时间复杂度都是 O(1)。

```
/*
 * Your MinStack object will be instantiated and called as such:
 * MinStack obj = new MinStack();
 * obj.push(x);
 * obj.pop();
 * int param_3 = obj.top();
 * int param_4 = obj.min();
 */
```

#### 思路一

辅助栈

数据栈入栈一个元素，辅助栈也入栈一个当前栈中最小元素。数据出栈一个元素，辅助栈也出栈一个元素。当前数据栈中的最小元素就是辅助栈的栈顶元素。

时间复杂度：O(1) 

空间复杂度：O(n)。辅助栈最差情况下存储n个元素，使用O(n)额外空间。

辅助栈也就可以只入栈小于等于当前最小值的元素，对应地只在数据栈出栈最小元素时，辅助栈才出栈。这样节约了空间，但是每次push和pop都要判断。时间复杂度虽然没变，但是执行时间慢了很多。所以没有采用这种做法。

```java
class MinStack {

    Deque<Integer> stack, support;

    /** initialize your data structure here. */
    public MinStack() {
        stack = new LinkedList<>();
        support = new LinkedList<>();
    }
    
    public void push(int x) {
        stack.push(x);
        if(!support.isEmpty() && support.peek()<x){
            support.push(support.peek());
        }
        else{
            support.push(x);
        }
    }
    
    public void pop() {
        stack.pop();
        support.pop();
    }
    
    public int top() {
        return stack.peek();//栈为空时返回null
    }
    
    public int min() {
        return support.peek();//栈为空时返回null
    }
}
```

#### 思路二

用一个变量保存当前的最小值：

新来的元素小于或等于当前最小值时，把当前的最小值入栈并更新当前最小值，然后将新来的元素入栈：

元素出栈时，若出栈元素等于当前最小值，就让下一个元素出栈作为新的最小值。

时间复杂度：O(1)。

空间复杂度：O(n)。使用了一个变量，还需要额外存之间的最小值，最坏需要n个空间。

```java
class MinStack {

    Deque<Integer> stack;
    int curMin;

    /** initialize your data structure here. */
    public MinStack() {
        stack = new LinkedList<>();
        curMin=Integer.MAX_VALUE;
    }
    
    public void push(int x) {
        
        if(curMin>=x){
            stack.push(curMin);
            curMin=x;
        }
        stack.push(x);
    }
    
    public void pop() {
        if(stack.pop()==curMin)
            curMin=stack.pop();
    }
    
    public int top() {
        return stack.peek();//栈为空时返回null
    }
    
    public int min() {
        return curMin;
    }
}

```

### 思路三

用一个变量保存当前的最小值，栈里不存储原来的值，而是存储原来的值和最小值的差值。

入栈时若栈为空，令最小值为入栈元素的值，栈中存储的元素为当前值减去最小值，即为0。

出栈时，若出栈元素是非负数，说明元素原来的值大于或等于当前最小值，原来的元素=出栈元素+当前最小值。

出栈时，若出栈元素是负数，说明元素原来的值小于之前的最小值，等于当前的最小值。而之前的最小值=当前最小值-出栈元素。

相应地，获取栈顶元素时，要先判断栈顶元素的正负，再通过当前最小值得到原本的元素值。

由于我们保存的是差值，需要用数据范围更大的 long 类型。相对于解法二，最小值需要更新的时候，没有将之前的最小值存起来。每次都是通过当前最小值和栈顶元素推出了之前的最小值，所以会省一些空间。

时间复杂度:O(1)。

空间复杂度：O(n)，数据栈需要存储n个元素。
```java
class MinStack {
    //x-min可能会超出int的范围，所以栈和最小值用long
    Deque<Long> stack;
    long min;

    /** initialize your data structure here. */
    public MinStack() {
        stack = new LinkedList<>();
        min=Integer.MAX_VALUE;
    }
    
    public void push(int x) {
        if(!stack.isEmpty()){
            stack.push(x-min);
            if(x<min){
                min=x;
            }
        }
        else{
            min=x;
            stack.push(x-min);
        }
    }
    
    public void pop() {
        if(!stack.isEmpty()){
            long temp=stack.pop();
            if(temp>=0){
                return;
            }else{
                min=min-temp;
            }
        }
    }
    
    public int top() {
        if(stack.peek()>=0)
            return (int)(stack.peek()+min);
        else 
            return (int)min;
    }
    
    public int min() {
        return (int)min;
    }
}
```
