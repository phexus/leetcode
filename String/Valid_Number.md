# Valid Number

Tags: String, Hard

## Problem

Validate if a given string is numeric.

Some examples:
`"0"` => `true`
`" 0.1 "` => `true`
`"abc"` => `false`
`"1 a"` => `false`
`"2e10"` => `true`

**Note:** It is intended for the problem statement to be ambiguous. You should gather all requirements up front before implementing one.

**Update (2015-02-10):**
The signature of the `C++` function had been updated. If you still see your function signature accepts a `const char *` argument, please click the reload button  to reset your code definition.

## Solution 1 - 遍历

本题与 `剑指 offer` 一书 面试题 54： 表示数值的字符串 相同。

比较标准的扫描字符串的问题，需要考虑表示数值的字符串遵循的模式：

`[sign]integral-digits[.[fraction-digits]][e|E[sign]exponential-digits]`

扫描数字部分，有 3 类，整数部分，小数部分，指数部分，可以归纳成一个 scanDigits( ) 函数



然而如果按照 `剑指 offer` 一书的解法来解的话，会有一些 leetcode 规定的 corner case 不能覆盖，如

"e9" 应该返回 false

"  1  " 应该返回 true

"3." 	应该返回 true

总之，这种题比较坑，很多 corner case 得一个一个试出来。。

[参考解法](https://leetcode.com/problems/valid-number/discuss/23762/A-simple-solution-in-cpp)

由于需要处理 e 前面必须要有数字或者只能有一个 '.'，可以记录数字 和 '.' 的数量来做判断，非常优雅

也就是说 当 pointCount <= 1 && numCount >= 1 才可以接着进行下面的环节。

```cpp
class Solution {
public:
    bool isNumber(string s) {
        int i = 0;
        
        // 跳过开头的空格
        for ( ; s[i] == ' '; ++i) {}
        // 处理符号位
        if (s[i] == '+' || s[i] == '-') ++i;
        
        int pointCount = 0, numCount = 0;
        for ( ; (s[i] >= '0' && s[i] <= '9') || s[i] == '.'; ++i)
            s[i] == '.' ? ++pointCount : ++numCount;
        if (pointCount > 1 || numCount < 1) return false; // 如果只有 . 没有数字也是可以的
        
        // 开始处理 指数部分
        if (s[i] == 'e') {
            ++i;
            
            if (s[i] == '+' || s[i] == '-') ++i;
            
            numCount = 0;
            for ( ; s[i] >= '0' && s[i] <= '9'; ++i, ++numCount) {}
            if (numCount < 1) return false;
        }
        
        // 跳过末尾的空格
        for ( ; s[i] == ' '; ++i) {}
        
        return s[i] == '\0';
    }
};
```

### 另一种写法 - flag

设置了 4 个标志位，感觉不如上面的解法更加清晰

```cpp
//链接：https://www.nowcoder.net/questionTerminal/608d810765a34df2a0d47645626dd2d3
//来源：牛客网

/*
     *
     * 发现解答很多都用了一些小技巧，比如正则，或者NumberFormatException
     * 实际上这些解法都调用库函数，个人觉得不是出题者的真正意图
     *
     *
     * Runtime: 3 ms.Your runtime beats 83.72 % of java submissions.
     * 思路：这道题关键是分析所有会出现的情况。以及设置关键的flag来记录各种情况
     * 首先使用trim()函数去除前后空格。
     * 设置4个标志，分别是
     * boolean pointSeen = false;//是否出现过小数点“.”
     * boolean eSeen =alse;//是否出现过指数标志“e”
     * boolean numberSeen = false;//是否出现过数字
     * booleannumberAfterE = true;//e后面是否有数字
     * 进行循环s.charAt(i)：
     * 1.如果属于[0,9]，把numberSeen设为true
     * 2.如果是‘.’，必须之前没有遇到'.'和'e'
     * 3.如果是'e'，必须之前没有遇到过'e'
     * 4.如果是'+'或者'-',当前i必须是0，或者i-1上的字符是e
     * 5.其他情况返回false
     */
    public boolean isNumber(String s) {
        s = s.trim();
        if (s == null || s.length() == 0)
            return false;
        // 记录是否出现过数字
        boolean numberSeen = false;
        // 是否出现过小数点
        boolean pointSeen = false;
        // 是否出现过指数标志e
        boolean eSeen = false;
        // e后是否出现过数字
        boolean numberAfterE = false;
 
        for (int i = 0; i < s.length(); i++) {
            char c = s.charAt(i);
            // 如果是数字
            if (c >= '0' && c <= '9') {
                numberSeen = true;
                numberAfterE = true;
                // 如果是小数点
            } else if (c == '.') {
                if (pointSeen || eSeen)
                    return false;
                pointSeen = true;
                // 如果是e，必须没有出现过且出现过数字
            } else if (c == 'e') {
                if (eSeen || !numberSeen)
                    return false;
                eSeen = true;
                numberAfterE = false;
                // 如果出现正负号，必须是首位，或者前一位是e
            } else if (c == '+' || c == '-') {
                if (i != 0 && s.charAt(i - 1) != 'e')
                    return false;
            } else
                return false;
        }
        return numberSeen && numberAfterE;
    }
```





## Solution 2 - 有限状态机

https://leetcode.com/problems/valid-number/discuss/23725/C++-My-thought-with-DFA

https://www.nowcoder.net/questionTerminal/608d810765a34df2a0d47645626dd2d3

https://www.nowcoder.net/questionTerminal/6f8c901d091949a5837e24bb82a731f2