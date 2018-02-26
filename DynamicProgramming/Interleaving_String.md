# Interleaving String

Tags: String, Dynamic Programming, Hard

## Problem

Given *s1*, *s2*, *s3*, find whether *s3* is formed by the interleaving of *s1* and *s2*.

For example,
Given:
*s1* = `"aabcc"`,
*s2* = `"dbbca"`,

When *s3* = `"aadbbcbcac"`, return true.
When *s3* = `"aadbbbaccc"`, return false.

## Solution 1 - Recursion with memoization

本道题是带有 [solution](https://leetcode.com/problems/interleaving-string/solution/) 的，如果要用递归的思路来暴力比对的话会超时。但是如果加了记忆矩阵，性能就会好很多。

用 i j k 三个指针分别指向三个字符串当前处理的位置。每次都有两种选择，要么选 s1 的元素，要么选 s2 的元素，如果最终可解，那么其中必定有一个是正确的路径。我们用 `memo[i][j]` 来缓存递归过程中所处理过的情况，如果返回 false 则填 0，true 则填 1。即使递归到了错误的分支，也会把错误的路径结果存下来，下次遇到时就不用再次递归了。因为从本质上讲，这种递归就是 DFS。

`memo[i][j]` 还有一个第三状态 -1，代表还未处理，memo 矩阵初始化为 -1

另外，还有些剪枝处理，如果一个字符串到头了，剩下只需要对比另一个字符串和 s3 的关系就好。

```cpp
class Solution {
public:
    bool isInterleave(string s1, string s2, string s3) {
        vector<vector<int>> memo(s1.size(), vector<int> (s2.size(), -1));
        
        return help(s1, 0, s2, 0, s3, 0, memo);
    }
    
    bool help(string& s1, int i, string& s2, int j, string& s3, int k, vector<vector<int>>& memo) {
        if (i == s1.size()) return s2.substr(j) == s3.substr(k);
        if (j == s2.size()) return s1.substr(i) == s3.substr(k);
        
        if (memo[i][j] != -1) return memo[i][j] == 1 ? true : false;
        
        bool ans = false;
        if (s3[k] == s1[i] && help(s1, i + 1, s2, j, s3, k + 1, memo) ||
            s3[k] == s2[j] && help(s1, i, s2, j + 1, s3, k + 1, memo))
            ans = true;
        
        memo[i][j] = ans ? 1 : 0;
        return ans;
    }
};
```

## 时间复杂度

虽然做了 memo，也做了剪枝，但是最坏情况下这种递归还是会有   $$2^(m+n) $$ 个状态，所以时间复杂度依旧是 $$O(2^(m + n))$$ 比较高

空间复杂度 $$O(m * n)$$ 为 memo 矩阵，其实还有 $$O(m + n)$$ 的递归栈的消耗。

## Solution 2 - DP

还是 dp 的解法清晰明了。一样的套路，`dp[i][j]` 代表 s1 的前 i 个元素和 s2 的前 j 个元素是否能够组成 s3 的前 i + j 个元素。这里的难点感觉是不能把 s3 也囊括进来，我在初次做时一直在想是不是得搞三阶 dp 。其实不用，s1 和 s2 的长度确定了，那么 s3 的长度也就确定了。

注意好边界情况，单独处理。正常情况下，要分两种 即 dp(i, j) 有可能是由 dp(i - 1, j) 变来的，也有可能是由 dp(i, j - 1) 变来的，这两种只有有一种成立即可。

还需要小心 i 和 j 都代表长度，映射到下标得 -1

```cpp
class Solution {
public:
    bool isInterleave(string s1, string s2, string s3) {
        if (s1.size() + s2.size() != s3.size()) return false;
        
        int m = s1.size(), n = s2.size();
        vector<vector<bool>> dp(m + 1, vector<bool> (n + 1, false));
        
        dp[0][0] = true;
        for (int i = 1; i <= m; ++i) dp[i][0] = dp[i - 1][0] && s1[i - 1] == s3[i - 1];
        for (int j = 1; j <= n; ++j) dp[0][j] = dp[0][j - 1] && s2[j - 1] == s3[j - 1];
        
        for (int i = 1; i <= m; ++i) {
            for (int j = 1; j <= n; ++j) {
                dp[i][j] = (dp[i - 1][j] && s1[i - 1] == s3[i + j - 1]) ||
                           (dp[i][j - 1] && s2[j - 1] == s3[i + j - 1]);
            }
        }
        return dp[m][n];
    }
};
```

喜闻乐见的优化环节，dp可以降到 1 维，注意每轮 dp[0] 都要重新处理

```cpp
class Solution {
public:
    bool isInterleave(string s1, string s2, string s3) {
        if (s1.size() + s2.size() != s3.size()) return false;
        
        int m = s1.size(), n = s2.size();
        vector<bool> dp(n + 1, false);
        
        dp[0] = true;
        for (int j = 1; j <= n; ++j) dp[j] = dp[j - 1] && s2[j - 1] == s3[j - 1];
        
        for (int i = 1; i <= m; ++i) {
            dp[0] = dp[0] && s1[i - 1] == s3[i - 1];
            for (int j = 1; j <= n; ++j) {
                dp[j] = (dp[j] && s1[i - 1] == s3[i + j - 1]) ||
                        (dp[j - 1] && s2[j - 1] == s3[i + j - 1]);
            }
        }
        return dp[n];
    }
};
```

