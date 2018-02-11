# Regular Expression Matching

Tags: Medium, Hard

## Problem

Implement regular expression matching with support for '.' and '*'.

'.' Matches any single character.
'*' Matches zero or more of the preceding element.

The matching should cover the entire input string (not partial).

The function prototype should be:
bool isMatch(const char *s, const char *p)

Some examples:
isMatch("aa","a") → false
isMatch("aa","aa") → true
isMatch("aaa","aa") → false
isMatch("aa", "a*") → true
isMatch("aa", ".*") → true
isMatch("ab", ".*") → true
isMatch("aab", "c*a*b") → true

## Solution 1 - recursive

```cpp
class Solution {
public:
    bool isMatch(string s, string p) {
        if (p.empty()) return s.empty();
        
        bool first_match = (!s.empty()) && (p[0] == s[0] || p[0] == '.');
        
        if (p.size() > 1 && p[1] == '*') {
            return isMatch(s, p.substr(2)) || (first_match && isMatch(s.substr(1), p));
        } else {
            return first_match && isMatch(s.substr(1), p.substr(1));
        }
    }
};
```

```c
class Solution {
public:
    bool isMatch(const char *s, const char *p) {
        if (*p == '\0') return *s == '\0';
        
        bool first_match = *s != '\0' && (*s == *p || *p == '.');
        
        if (*(p + 1) == '*') {
            return isMatch(s, p + 2) || (first_match && isMatch(s + 1, p));
        } else {
            return first_match && isMatch(s + 1, p + 1);
        }
    }
};
```

