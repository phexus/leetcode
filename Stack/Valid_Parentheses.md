# Valid Parentheses

Tags: Stack, Easy

## Problem

Given a string containing just the characters '(', ')', '{', '}', '[' and ']', determine if the input string is valid.

The brackets must close in the correct order, "()" and "()[]{}" are all valid but "(]" and "([)]" are not.

## 解法

这题比较简单，典型的压栈出栈问题。不过自己的实现比较 ugly。要写的比较优雅还是要写之前尽可能想好各种情况。

要注意 st.empty() 的判定

贴出几个 leetcode discuss 上比较优雅的解答：

```cpp
class Solution {
public:
    bool isValid(string s) {
        stack<char> st;
        
        for (char& c : s) {
            if (c == '(' || c == '[' || c == '{') st.push(c);
            else {
                if (st.empty()) return false;
                if (c == ')' && st.top() != '(') return false;
                if (c == ']' && st.top() != '[') return false;
                if (c == '}' && st.top() != '{') return false;
                st.pop();
            }
        }
        
        return st.empty();
    }
};
```

switch case 的写法，[参考解法](https://leetcode.com/problems/valid-parentheses/discuss/9252/2ms-C++-sloution)，`This solution also accepts (and ignores) any characters other than parenthesis in the string. Hence, it can be used to check if the parenthesis matches in an equation for example.`

```cpp
class Solution {
public:
    bool isValid(string s) {
        stack<char> st;
        
        for (char& c : s) {
            switch (c) {
                case '(' :
                case '[' :
                case '{' : st.push(c); break;
                case ')' : if (st.empty() || st.top() != '(') return false; else st.pop(); break;
                case ']' : if (st.empty() || st.top() != '[') return false; else st.pop(); break;
                case '}' : if (st.empty() || st.top() != '{') return false; else st.pop(); break;
                default : ;
            }
        }
        
        return st.empty();
    }
};
```