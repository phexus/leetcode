# Multiply Strings

Tags: Medium, Medium

## Problem

Given two non-negative integers num1 and num2 represented as strings, return the product of num1 and num2.

Note:

The length of both num1 and num2 is < 110.
Both num1 and num2 contains only digits 0-9.
Both num1 and num2 does not contain any leading zero.
You must not use any built-in BigInteger library or convert the inputs to integer directly.

## Solution

其实就是在模拟乘法的手工竖式运算，[W.sl 的解答](https://www.nowcoder.net/questionTerminal/76a5d7a3173446c2ab34b8c5fe836f1d) 详细配了插图，感觉比较好理解。

因为这是个大数问题，不能直接转成 int 算，会溢出，所以只能一位一位算，再一次加起来，处理好进位。

下面的做法是把中间的临时结果存在了一个 int 数组里，2个数相乘，位数最多为 `len1 + len2` 。累加中间结果的时候，注意好下标，其实举个例子就能推算出来。

之后要把结果摊平，处理进位，数组的每个元素只存放一个数。

最后，要找到第一个不是 0 的位，依次追加到 ans 字符串中，这里要注意的是找不到第一个非 0 位的话，要单独输出 “0”。不过，如果找第一个 0 的时候，少处理一位，就不用单独处理 “0” 啦

```cpp
        int i = 0;
        while (i < len1 + len2 - 1 && tmp[i] == 0) ++i; // len1 + len2 - 1 留最后一位
        //if (i == len1 + len2) return "0";
        
        for ( ; i < len1 + len2; ++i) {
            ans.push_back(tmp[i] + '0');
        }
        
        return ans;
    }
```





```cpp
class Solution {
public:
    string multiply(string num1, string num2) {
        if (num1.empty() || num2.empty()) return "";
        
        string ans;
        int len1 = num1.size();
        int len2 = num2.size();
        int tmp[len1 + len2] = {0};
        
        for (int i = len1 - 1; i >= 0; --i) {            
            for (int j = len2 - 1; j >= 0; --j) {
                tmp[i + j + 1] += (num1[i] - '0') * (num2[j] - '0'); // 存储中间结果
            }
        }
        
        int carry = 0;
        for (int i = len1 + len2 - 1; i >= 0; --i) {
            carry += tmp[i];
            tmp[i] = carry % 10;
            carry /= 10;
        }
        
        int i = 0;
        while (i < len1 + len2 && tmp[i] == 0) ++i;
        if (i == len1 + len2) return "0"; // 结果为 0 的 corner case
        
        for ( ; i < len1 + len2; ++i) {
            ans.push_back(tmp[i] + '0');
        }
        
        return ans;
    }
};
```
### 优化

下面的[解法](https://leetcode.com/problems/multiply-strings/discuss/17646/Brief-C++-solution-using-only-strings-and-without-reversal)，直接把中间结果直接存成了 string，把我上面的 前两个 for loop 合并到一起了，非常优雅～

`sum[i] += carry;` 这一步是因为，上面的 for loop 已经处理到了 j = 0 的情况，即 sum[i + 1]，所以最高位的进位自然要叠加到 sum[i] 上

```cpp
string multiply(string num1, string num2) {
    string sum(num1.size() + num2.size(), '0');
    
    for (int i = num1.size() - 1; 0 <= i; --i) {
        int carry = 0;
        for (int j = num2.size() - 1; 0 <= j; --j) {
            int tmp = (sum[i + j + 1] - '0') + (num1[i] - '0') * (num2[j] - '0') + carry;
            sum[i + j + 1] = tmp % 10 + '0';
            carry = tmp / 10;
        }
        sum[i] += carry; // 这一步也比较关键，因为 for loop 已经处理到 sum[i + 1] 了
    }
    
    size_t startpos = sum.find_first_not_of("0");
    if (string::npos != startpos) {
        return sum.substr(startpos);
    }
    return "0";
}
```

