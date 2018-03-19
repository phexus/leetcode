# Longest Palindromic Substring

Tags: Medium, Easy

## Problem

Given a string s, find the longest palindromic substring in s. You may assume that the maximum length of s is 1000.

Example:

Input: "babad"

Output: "bab"

Note: "aba" is also a valid answer.

Example:

Input: "cbbd"

Output: "bb"

## Solution 1 - expand around center

中心扩展：
回文序列可以看成是从中心字符两边扩展得到的。因此把给定的字符串的每个字符当做回文序列的中心，依次像两边扩展到极限。但是要考虑到奇偶性：即中心有可能是两个相同字符。

下面具体实现：

这里原本可以写成 `start = i - (tmpLen - 1) / 2` 的，但是为了换成移位运算符就改写成了下面的样子，然而 bug 就来了。。因为 运算符的优先级为 乘除取余 > 加减 > 移位， 所以要加括号！！！
错误写法：
```cpp
            if (tmpLen > maxLen) {
                start = i - (tmpLen - 1) >> 1;
                maxLen = tmpLen;
            }
```

```cpp
class Solution {
public:
    string longestPalindrome(string s) {
        if (s.empty()) return "";
        
        int len = s.size();
        int start = 0, maxLen = 0;
        
        for (int i = 0; i < len; ++i) {
            int lenOdd = palindromeExpand(s, i, i);
            int lenEven = palindromeExpand(s, i, i + 1);
            
            int tmpLen = max(lenOdd, lenEven);
            if (tmpLen > maxLen) {
                start = i - ((tmpLen - 1) >> 1);
                maxLen = tmpLen;
            }
        }
        
        return s.substr(start, maxLen);
        
    }
    
private:
    int palindromeExpand(string& s, int left, int right) {
        while (left >= 0 && right < s.size() && s[left] == s[right]) {
            --left;
            ++ right;
        }
        
        return right - left - 1; // 此时 right 和 left 都已经超出范围了，所以要 -1
    }
};
```

### 复杂度

时间复杂度 $$O(n^2)$$，每次扩展都需要花费 $$O(n)$$，所以总的是 $$O(n^2)$$
空间复杂度 $$O(1)$$

## Solution2 - DP

链接：https://www.nowcoder.net/questionTerminal/c15cd9e18e4845758d4c1086963731e2
来源：牛客网

DP 思路：

用 `dp[i][j]` 表示 s[i ~ j] 的子串是否为回文字符串，那么就有 `dp[i][j] = dp[i + 1][j - 1] && s[i] == s[j]` 成立。

边界条件：

如果用矩阵表示的话，会发现左下部分的 i > j，所以应该将左下部分全部置为 false；

对角线元素代表单个元素，全部置为 true

另外还有一个很容易忽略，画出矩阵就比较好理解了，生成 dp 矩阵的时候是沿着对角线方向一层一层往右上角生成的，所以会有部分元素是对角线对不上的，需要对 `dp[i][i + 1] = s[i] == s[i + 1]` 也初始化，有了这初始的两层，后面的元素就都能生成出来了。

> 特别要注意 dp 矩阵生成的方向！！！

```cpp
class Solution {
public:
    string longestPalindrome(string s) {
        if (s.empty()) return "";
        int n = s.size();
        vector<vector<bool>> dp(n, vector<bool> (n, false));
        
        for (int i = 0; i < n; ++i) dp[i][i] = true;
        for (int i = 0; i < n - 1; ++i) dp[i][i + 1] = s[i] == s[i + 1];
        
        for (int k = 2; k < n; ++k) {
            int j = k;
            for (int i = 0; i < n && j < n; ++i, ++j) {
                dp[i][j] = dp[i + 1][j - 1] && s[i] == s[j];
            }
        }
        
        int startIdx = 0, maxLen = 0;
        for (int i = 0; i < n; ++i) {
            for (int j = i; j < n; ++j) {
                if (dp[i][j] && j - i + 1 > maxLen) {
                    maxLen = j - i + 1;
                    startIdx = i;
                }
            }
        }
        
        return s.substr(startIdx, maxLen);
    }
};
```

### 复杂度

由于辅助矩阵的存在，导致了该方法在 leetcode 上跑到了 236 ms，而方法 1 能跑到 16 ms

时间复杂度 $$O(n^2)$$

空间复杂度 $$O(n^2)$$ 

## Solution - Manacher's Algorithm - 比较难 - O(n)

[参考链接](http://articles.leetcode.com/longest-palindromic-substring-part-ii/)