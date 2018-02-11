# Longest Common Prefix

Tags: String, Easy

## Problem

Write a function to find the longest common prefix string amongst an array of strings.

## Solution

本题在 [leetcode Solution] 上有多种解法

我这里用了比较暴力的匹配方法，用 i 作游标，依次往右挪，相同计数器 +1，做完一轮后比较计数器与 vector 的长度，若相同说明所有字符相同，追加到答案 ans 后

```cpp
class Solution {
public:
    string longestCommonPrefix(vector<string>& strs) {
        if (strs.empty()) return "";

        int n = strs.size();
        string ans;
        int i = 0;

        while (1) {
            int count = 0;
            for (string& str : strs) {
                if (str[i] == '\0') return ans;
                if (str[i] == strs[0][i]) ++count;
            }
            if (count != n) return ans;
            ans.push_back(strs[0][i++]);
        }
    }
};
```

### 复杂度

时间复杂度 $$O(strs.size() * L)$$ 这里的 L 是最长子串的长度

空间复杂度 $$O(1)$$