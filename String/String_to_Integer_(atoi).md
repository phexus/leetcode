# String to Integer (atoi)

Tags: String, Hard

## Problem

Implement atoi to convert a string to an integer.

**Hint:** Carefully consider all possible input cases. If you want a challenge, please do not see below and ask yourself what are the possible input cases.

**Notes:** It is intended for this problem to be specified vaguely (ie, no given input specs). You are responsible to gather all the input requirements up front.

**Update (2015-02-10):**
The signature of the `C++` function had been updated. If you still see your function signature accepts a `const char *` argument, please click the reload button  to reset your code definition.

[spoilers alert... click to show requirements for atoi.](https://leetcode.com/problems/string-to-integer-atoi/description/#)

Requirements for atoi:

The function first discards as many whitespace characters as necessary until the first non-whitespace character is found. Then, starting from this character, takes an optional initial plus or minus sign followed by as many numerical digits as possible, and interprets them as a numerical value.

The string can contain additional characters after those that form the integral number, which are ignored and have no effect on the behavior of this function.

If the first sequence of non-whitespace characters in str is not a valid integral number, or if no such sequence exists because either str is empty or it contains only whitespace characters, no conversion is performed.

If no valid conversion could be performed, a zero value is returned. If the correct value is out of the range of representable values, INT_MAX (2147483647) or INT_MIN (-2147483648) is returned.

## Solution

这道题无非就是考察对异常输入的捕捉，要尽量做到覆盖各种 corner case，然而很多 corner case 不看 hint 又不知道到底该输出啥，真是坑。。总结如下：

1. 忽略前面所有的 空白符 ' '
2. 考虑正负性，处理数字前的 '+' 和 '-'
3. 溢出问题，int 最大表示范围为 INT_MIN( -2147483648 ) - INT_MAX( 2147483647 )
4. 非法字符，然而 leetcode 处理逻辑是这样的 "a123" -> "0"    "1a23" -> "1"    "123a" -> "123"  "  1  23" -> "1"

如上所示，说明是要从字符串高位往地位处理的，而我自己的实现是从字符串尾部开始比较的，所以导致下面的例子过不去： 

```
Input:
"  -0012a42"
Output:
0
Expected:
-12
```

我的不符合测试用例的解法，从后往前遍历

```cpp
class Solution {
public:
    int myAtoi(string str) {
        if (str.empty()) return 0;
        
        int ans = 0, times = 1;
        int i = 0;
        while (str[i] == ' ') ++i;
        str = str.substr(i);
        int len = str.size();        
        
        for (int i = len - 1; i > -1; --i) {
            if (str[i] >= '0' && str[i] <= '9') {
                ans += times * (str[i] - '0');
                if (ans < 0) {ans = INT_MAX; break;}
                times *= 10;
            } else {
                if (i == 0 && (str[0] == '+' || str[0] == '-')) {}
                else ans = 0;
                break;
            }
        }
        
        if (str[0] == '-') ans = -ans;
        return ans;             
             
    }
};
```

### 改为从前往后匹配：

关于溢出的处理要小心，不能简单像下面这样处理，因为 INT_MIN =  -2147483648, 它的数字部分是不能够用正数表示的，该结果会错`Input:"-2147483648" Output:-2147483647 Expected:-2147483648`

```cpp
        while (str[i] >= '0' && str[i] <= '9') {
            ans = ans * 10 + (str[i] - '0');
            if (ans < 0) return indicator * INT_MAX;
            ++i;
        }
```

每次在累加前先判断和 `INT_MAX / 10` 的关系，再单独处理 `ans == INT_MAX / 10 && str[i] - '0' > 7` 这样的情况。

```cpp
class Solution {
public:
    int myAtoi(string str) {
        int ans = 0;
        int len = str.size();
        int indicator = 1;
        
        int i = 0;
        while (str[i] == ' ') ++i;
        //if (i >= len) return 0; 因为有字符串末尾的 '\0' 所以可以忽略
        
        if (str[i] == '+' || str[i] == '-') indicator = (str[i++] == '-') ? -1 : 1;
        //if (i >= len) return 0;
        
        while (str[i] >= '0' && str[i] <= '9') {
            if (ans >  INT_MAX / 10 || (ans == INT_MAX / 10 && str[i] > '7')) {
                if (indicator == 1) return INT_MAX;
                else return INT_MIN;
            }
            
            ans = ans * 10 + (str[i++] - '0');
        }
        
        return indicator * ans;
    }
};
```

