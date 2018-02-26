# Decode Ways

Tags: String, Dynamic Programming, Hard

## Problem

A message containing letters from `A-Z` is being encoded to numbers using the following mapping:

```
'A' -> 1
'B' -> 2
...
'Z' -> 26
```

Given an encoded message containing digits, determine the total number of ways to decode it.

For example,
Given encoded message `"12"`, it could be decoded as `"AB"` (1 2) or `"L"` (12).

The number of ways decoding `"12"` is 2.

## Solution

先看看我的错误实现，1122就跪了，返回了 4 ，实际上是 5

明显是把问题想得太简单了，新加的这个数字并不是智能选择要么和前一个凑成一个两位数，要么自己单独一个数，而是应该这两种累加才对！！

### 错误实现

```cpp
class Solution {
public:
    int numDecodings(string s) {
        int n = s.size(); 
        if (n < 2) return n;
        
        vector<int> dp(n + 1, 0);
        dp[1] = 1;
        
        for (int i = 2; i <= n; ++i) {
            if (s[i - 1] == '1' || s[i - 1] == '2') dp[i] = dp[i - 1] + 1;
            else dp[i] = dp[i - 1];
        }
        return dp[n];
    }
};
```

下面看看正确实现，first 代表自己单独做一个数，那么 `dp[i] = dp[i - 1]`

second 代表自己和前一个数组成一个两位数，那么 `dp[i] += dp[i - 2]`

但是要注意这里的初始值，dp[0] 并不是表面意义的空字符串，而且空字符串应该是 0 才对，这里完全是为了凑第一个两位数才这么处理的，例如 12 ，当 i = 2 的时候，`dp[i] += dp[i - 2];` dp[0] 必须为 1 才能得出正确结果。空字符串的处理早在 `if (s.empty()) return 0;` 开头就单独做了。

另外要小心不要把 0 算成是一个可以单独出现的数，0 只能和前一个数字配对才行。

```cpp
class Solution {
public:
    int numDecodings(string s) {
        if (s.empty()) return 0;
        int n = s.size(); 
        
        vector<int> dp(n + 1, 0);
        dp[0] = 1;
        dp[1] = s[0] == '0' ? 0 : 1;
        
        for (int i = 2; i <= n; ++i) {
            int first = s[i - 1] - '0';
            int second = stoi(s.substr(i - 2, 2));
            
            if (first >= 1 && first <= 9) dp[i] = dp[i - 1];
            if (second >= 10 && second <= 26) dp[i] += dp[i - 2];

        }
        return dp[n];
    }
};
```

可以把 dp 降到两个变量，这样空间复杂度能到 $$O(1)$$

```cpp
class Solution {
public:
    int numDecodings(string s) {
        if (s.empty()) return 0;
        int n = s.size(); 
        
        vector<int> dp(n + 1, 0);
        int pre2 = 1;
        int pre1 = s[0] == '0' ? 0 : 1;
        
        for (int i = 2; i <= n; ++i) {
            int curr = 0;
            int first = s[i - 1] - '0';
            int second = stoi(s.substr(i - 2, 2));
            
            if (first >= 1 && first <= 9) curr = pre1;
            if (second >= 10 && second <= 26) curr += pre2;
            
            pre2 = pre1;
            pre1 = curr;

        }
        return pre1;
    }
};
```

### 另一种写法

[参考](https://leetcode.com/problems/decode-ways/discuss/30357/DP-Solution-(Java)-for-reference)

感觉倒着处理有奇效，这里的 dp[i] 指的是 从下标 i 到 末尾组成的字符串所拥有的解码个数。

```cpp
class Solution {
public:
    int numDecodings(string s) {
        if (s.empty()) return 0;
        int n = s.size(); 
        
        vector<int> dp(n + 1, 0);
        dp[n] = 1;
        dp[n - 1] = s[n - 1] == '0' ? 0 : 1;
        
        for (int i = n - 2; i >= 0; --i) {
            if (s[i] == '0') continue;
            else dp[i] = (stoi(s.substr(i, 2)) <= 26) ? dp[i + 1] + dp[i + 2] : dp[i + 1];

        }
        return dp[0];
    }
};
```

