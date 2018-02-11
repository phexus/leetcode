# Valid Anagram

Tags: Medium, Easy

## Problem

Given two strings s and t, write a function to determine if t is an anagram of s.

For example,
s = "anagram", t = "nagaram", return true.
s = "rat", t = "car", return false.

Note:
You may assume the string contains only lowercase alphabets.

Follow up:
What if the inputs contain unicode characters? How would you adapt your solution to such case?

## Solution 1 - 统计字频

若两个词为变位词，则这两个词所拥有的每个字符的数量也应该都是相同的。所以统计这两个词的字频，若不等则返回 false。

字频可以用 map 来存储，不过需要注意的是，题目假定了字符串只有小写字母组成，所以完全可以定义一个 128 维的 ASCII 码表。
为减少循环次数，一个字符串 ++，另一个字符串 --，只要最后看字频表是否均为 0 即可。

最后的比较是否为 0,可以选择遍历其中一个数组，但是当数组长度过长的话　（> 128）, 可以选择去遍历字母表，效率更高点。

```cpp
class Solution {
public:
    bool isAnagram(string s, string t) {
        if (s.empty() && t.empty()) return true;
        if (s.size() != t.size()) return false;
        
        int len = s.size();
        int letterCount[128] = {0};
        
        for (int i = 0; i < len; ++i) {
            ++letterCount[s[i]];
            --letterCount[t[i]];
        }
        
        for (int& i : letterCount) {
            if (i != 0) return false;
        }
        
        return true;
    }
};
```

若 输入字符串包含了 unicode，则用 unordered_map 即可。最后判断计数是否为 0 时，这里由于 unordered_map 不好遍历，所以就遍历了其中一个字符串。因为两个字符串大小肯定相等，其中一个所有字符计数为 0 的话，另一个也势必为 0。

```cpp
class Solution {
public:
    bool isAnagram(string s, string t) {
        if (s.empty() && t.empty()) return true;
        if (s.size() != t.size()) return false;
        
        int len = s.size();
        unordered_map<char, int> charCount;
        
        for (int i = 0; i < len; ++i) {
            if (charCount.count(s[i]) > 0) ++charCount[s[i]];
            else charCount[s[i]] = 1;
            
            if (charCount.count(t[i]) > 0) --charCount[t[i]];
            else charCount[t[i]] = -1;
        }
        
        for (char& c : s) {
            if (charCount[c] != 0) return false;
        }
        
        return true;
    }
};
```

### 复杂度

时间复杂度 $$O(n)$$
空间复杂度 $$O(1)$$，因为使用的额外空间是常数级的，不会随着 n 的增大而增大。

## Solution 2 - 排序比较

```cpp
class Solution {
public:
    bool isAnagram(string s, string t) {
        if (s.empty() && t.empty()) return true;
        if (s.size() != t.size()) return false;
        
        sort(s.begin(), s.end());
        sort(t.begin(), t.end());
        
        if (s == t) return true;
        
        return false;
    }
};
```

### 复杂度

C++ STL sort 平均时间复杂度为 $$O(nlong(n))$$, 比较两个字符串花费 $$O(n)$$，所以时间复杂度 $$O(nlong(n))$$
空间复杂度 $$O(1)$$
(不过对于 java 来讲，由于 String 默认不可变，所以空间复杂度其实是 $$O(n)$$ [参考](https://leetcode.com/problems/valid-anagram/solution/))
