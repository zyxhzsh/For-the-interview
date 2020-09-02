来源：力扣（LeetCode）
链接：https://leetcode-cn.com/problems/biao-shi-shu-zhi-de-zi-fu-chuan-lcof

描述：请实现一个函数用来判断字符串是否表示数值（包括整数和小数）。例如，字符串"+100"、"5e2"、"-123"、"3.1416"、"-1E-16"、"0123"都表示数值，但"12e"、"1a3.14"、"1.2.3"、"+-5"及"12e+5.4"都不是。

读题：

在 C++ 文档 中，描述了一个合法的数值字符串应当具有的格式。具体而言，它包含以下部分：

符号位，即 ++、-− 两种符号
整数部分，即由若干字符 0-90−9 组成的字符串
小数点
小数部分，其构成与整数部分相同
指数部分，其中包含开头的字符 \text{e}e（大写小写均可）、可选的符号位，和整数部分

作者：LeetCode-Solution
链接：https://leetcode-cn.com/problems/biao-shi-shu-zhi-de-zi-fu-chuan-lcof/solution/biao-shi-shu-zhi-de-zi-fu-chuan-by-leetcode-soluti/
来源：力扣（LeetCode）
