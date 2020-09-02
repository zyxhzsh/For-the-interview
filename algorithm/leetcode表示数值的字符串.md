来源：力扣（LeetCode）
链接：https://leetcode-cn.com/problems/biao-shi-shu-zhi-de-zi-fu-chuan-lcof

**描述**

请实现一个函数用来判断字符串是否表示数值（包括整数和小数）。例如，字符串"+100"、"5e2"、"-123"、"3.1416"、"-1E-16"、"0123"都表示数值，但"12e"、"1a3.14"、"1.2.3"、"+-5"及"12e+5.4"都不是。

**读题**

在 C++ 文档 中，描述了一个合法的数值字符串应当具有的格式。具体而言，它包含以下部分：
```
符号位，即 ++、-− 两种符号
整数部分，即由若干字符 0-90−9 组成的字符串
小数点
小数部分，其构成与整数部分相同
指数部分，其中包含开头的字符 e（大写小写均可）、可选的符号位，和整数部分
```
相比于 C++ 文档而言，本题还有一点额外的不同，即允许字符串首末两端有一些额外的空格。

在上面描述的五个部分中，每个部分都不是必需的，但也受一些额外规则的制约，如：

如果符号位存在，其后面必须跟着数字或小数点。

小数点的前后两侧，至少有一侧是数字。

**预备知识：有限状态自动机**

确定有限状态自动机（以下简称「自动机」）是一类计算模型。它包含一系列状态，这些状态中：

有一个特殊的状态，被称作「初始状态」，还有一系列状态被称为「接受状态」，它们组成了一个特殊的集合。其中，一个状态可能既是「初始状态」，也是「接受状态」。
起初，这个自动机处于「初始状态」。随后，它顺序地读取字符串中的每一个字符，并根据当前状态和读入的字符，按照某个事先约定好的「转移规则」，从当前状态转移到下一个状态；当状态转移完成后，它就读取下一个字符。当字符串全部读取完毕后，如果自动机处于某个「接受状态」，则判定该字符串「被接受」；否则，判定该字符串「被拒绝」。

注意：如果输入的过程中某一步转移失败了，即不存在对应的「转移规则」，此时计算将提前中止。在这种情况下我们也判定该字符串「被拒绝」。

**一个自动机，总能够回答某种形式的「对于给定的输入字符串 S，判断其是否满足条件 P」的问题。在本题中，条件 P 即为「构成合法的表示数值的字符串」。**

自动机驱动的编程，可以被看做一种暴力枚举方法的延伸：它穷尽了在任何一种情况下，对应任何的输入，需要做的事情。

在工程领域，自动机是实现「正则表达式」的基础。

**思路**


1.定义自动机的状态集合

一个常用的技巧是，用「当前处理到字符串的哪个部分」当作状态的表述。

```
起始的空格
符号位
整数部分
左侧有整数的小数点
左侧无整数的小数点（根据前面的第二条额外规则，需要对左侧有无整数的两种小数点做区分）
小数部分
字符e
指数部分的符号位
指数部分的整数部分
末尾的空格
```
2.找出「初始状态」和「接受状态」的集合。

初始状态为起始的空格。接受状态有整数，前面有整数的小数点，小数，指数数字，末尾空格。

3.定义转移规则
结合数值字符串应当具备的格式，将自动机转移的过程以图解的方式表示出来：


<div align="center"><img width="60%" src="Finite-state_machine.png"></img></div>

没有单独地考虑每种字符，而是划分为若干类。由于全部 1010 个数字字符彼此之间都等价，因此只需定义一种统一的「数字」类型即可。对于正负号也是同理。

在实际代码中，我们需要处理转移失败的情况。例如当位于状态 1（起始空格）时，没有对应字符 \text{e}e 的状态。为了处理这种情况，我们可以创建一个特殊的拒绝状态。如果当前状态下没有对应读入字符的「转移规则」，我们就转移到这个特殊的拒绝状态。一旦自动机转移到这个特殊状态，我们就可以立即判定该字符串不「被接受」。

**代码**
```java
class Solution {
    public boolean isNumber(String s) {
        Map <State, Map<CharType, State>> transfer = new HashMap<State, Map<CharType, State>>();
        Map <CharType, State> initialMap = new HashMap<CharType, State> ()
        {
            {
            put(CharType.Char_Space, State.State_Initial);
            put(CharType.Char_Sign, State.State_Int_Sign);
            put(CharType.Char_Num, State.State_Int);
            put(CharType.Char_Point, State.State_Point_Without_Int);
            }
        };
        transfer.put(State.State_Initial, initialMap);
        Map <CharType, State> intSignMap = new HashMap<CharType, State> (){
            {
            put(CharType.Char_Num, State.State_Int);
            put(CharType.Char_Point, State.State_Point_Without_Int);
            }
        };
        transfer.put(State.State_Int_Sign, intSignMap);
        Map <CharType, State> intMap = new HashMap<CharType, State> (){

            {
            put(CharType.Char_Num, State.State_Int);
            put(CharType.Char_Point, State.State_Point_With_Int);
            put(CharType.Char_Exp, State.State_Exp);
            put(CharType.Char_Space, State.State_End);
            }
        };
        transfer.put(State.State_Int, intMap);
        Map <CharType, State> pointwithintMap = new HashMap<CharType, State> (){
            {
            put(CharType.Char_Num, State.State_Fraction);
            put(CharType.Char_Exp, State.State_Exp);
            put(CharType.Char_Space, State.State_End);
            }
        };
        transfer.put(State.State_Point_With_Int, pointwithintMap);
        Map <CharType, State> pointwithoutintMap = new HashMap<CharType, State> (){
            {
            put(CharType.Char_Num, State.State_Fraction);
            }
        };
        transfer.put(State.State_Point_Without_Int, pointwithoutintMap);
        Map <CharType, State> fractionMap = new HashMap<CharType, State> (){
            {
            put(CharType.Char_Num, State.State_Fraction);
            put(CharType.Char_Exp, State.State_Exp);
            put(CharType.Char_Space, State.State_End);
            }
        };
        transfer.put(State.State_Fraction, fractionMap);
        Map <CharType, State> expMap = new HashMap<CharType, State> (){
            {
            put(CharType.Char_Sign, State.State_Exp_Sign);
            put(CharType.Char_Num, State.State_Exp_Number);
            }
        };
        transfer.put(State.State_Exp, expMap);
        Map <CharType, State> expsignMap= new HashMap<CharType, State> (){
            {
            put(CharType.Char_Num, State.State_Exp_Number);
            }
        };
        transfer.put(State.State_Exp_Sign, expsignMap);
        Map <CharType, State> expnumMap= new HashMap<CharType, State> (){
            {
            put(CharType.Char_Num, State.State_Exp_Number);
            put(CharType.Char_Space,State.State_End);
            }
        };
        transfer.put(State.State_Exp_Number, expnumMap);
        Map <CharType, State> endMap= new HashMap<CharType, State> (){
            {
            put(CharType.Char_Space,State.State_End);
            }
        };
        transfer.put(State.State_End, endMap);

        int length = s.length();
        State state = State.State_Initial;

        for (int i=0; i<length; i++){
            CharType chartype = getCharType(s.charAt(i));
            if (!transfer.get(state).containsKey(chartype))
            {
                return false;
            }
            else
            {
                state=transfer.get(state).get(chartype);
            }

        }   

        return state == State.State_End || state==State.State_Exp_Number || state==State.State_Point_With_Int ||  state==State.State_Fraction ||                      state==State.State_Int;
    }

       public CharType getCharType(char ch) {
        if (ch >= '0' && ch <= '9') {
            return CharType.Char_Num;
        } else if (ch == 'e' || ch == 'E') {
            return CharType.Char_Exp;
        } else if (ch == '.') {
            return CharType.Char_Point;
        } else if (ch == '+' || ch == '-') {
            return CharType.Char_Sign;
        } else if (ch == ' ') {
            return CharType.Char_Space;
        } else {
            return CharType.Char_Illeage;
        }
    }

    enum State{

        State_Initial,
        State_Int_Sign,
        State_Int,
        State_Point_Without_Int,
        State_Point_With_Int,
        State_Fraction,
        State_Exp,
        State_Exp_Sign,
        State_Exp_Number,
        State_End
    }

    enum CharType{

        Char_Num,
        Char_Sign,
        Char_Point,
        Char_Exp,
        Char_Space,
        Char_Illeage
    }
}
```
