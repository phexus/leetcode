# First Unique Character in a String

Tags: String, Hash Table, Easy

## Problem

Given a string, find the first non-repeating character in it and return it's index. If it doesn't exist, return -1.

**Examples:**

```
s = "leetcode"
return 0.

s = "loveleetcode",
return 2.
```

**Note:** You may assume the string contain only lowercase letters.

## Solution

本题与 `剑指 offer` 面试题 55 有点类似，只是把字符流换成了字符串。

核心思想是用一个 vector 来模拟 map，key 为 Ascll 码，val 为该字符在字符串中的下标。要注意的是初始值都设为了 -1，因为 0 也是有意义的，如果一旦重复，则置为 -2，最后统计下标最小的非负数即可。

但是还是要处理边界问题，如果没找到，则要返回 -1

```cpp
class Solution {ans
public:
    int firstUniqChar(string s) {
        if (s.empty()) return -1;
        
        vector<int> map(256, -1);
        
        for (int i = 0; i < s.size(); ++i) {
            if (map[s[i]] == -1) map[s[i]] = i;
            else if (map[s[i]] >= 0) map[s[i]] = -2;
        }
        
        int ans = 0x7fffffff;
        for (int i = 0; i < 256; ++i) {
            if (map[i] >= 0) ans = min(ans, map[i]);
        }
        
        return ans == 0x7fffffff ? -1 : ans;
    }
};
```

