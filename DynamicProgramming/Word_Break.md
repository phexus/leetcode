# Word Break

Tags: String, Dynamic Programming, Medium

## Problem

Given a **non-empty** string *s* and a dictionary *wordDict* containing a list of **non-empty** words, determine if *s* can be segmented into a space-separated sequence of one or more dictionary words. You may assume the dictionary does not contain duplicate words.

For example, given
*s* = `"leetcode"`,
*dict* = `["leet", "code"]`.

Return true because `"leetcode"` can be segmented as `"leet code"`.

## Solution

这道题相对来说比较简单，就是标准的一维 DP。要注意的一点是，leetcode 把输入给改成了 vector，但是我们在做的时候还是得把 vector 重新写成 set，利用 hash map 查询 $$O(1)$$ 的特性。dp[0] 得初始化为 true，否则第一个单词会有问题。

```cpp
class Solution {
public:
    bool wordBreak(string s, vector<string>& wordDict) {
        unordered_set<string> wordSet;
        for (string& str : wordDict) wordSet.insert(str);
        
        int n = s.size();
        vector<int> dp(n + 1, false);
        dp[0] = true;
        
        for (int i = 1; i <= n; ++i) {
            for (int j = i - 1; j >= 0; --j) {
                if (dp[j] && wordSet.count(s.substr(j, i - j))) {
                    dp[i] = true;
                    break;
                }
            }
        }
        return dp[n];
    }
};
```

### 复杂度

时间复杂度 $$O(n^2)$$

空间复杂度 $$O(n)$$ 包含了 set 和 dp