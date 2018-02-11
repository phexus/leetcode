# Length of Last Word

Tags: String, Easy

## Problem

Given a string s consists of upper/lower-case alphabets and empty space characters ' ', return the length of last word in the string.

If the last word does not exist, return 0.

Note: A word is defined as a character sequence consists of non-space characters only.

Example:

Input: "Hello World"
Output: 5

## Solution1 - 反向查找

这道题很简单，理解了 string 是怎么存的就很好做了，string 感觉就是 存 char 的数组，所以可以反向查找。另外比较坑的是，题意没说明白当最后是空格时到底算是否存在 last word，经过尝试才知道得把末尾空格也算是存在 last word 的情况。

```cpp
class Solution {
public:
    int lengthOfLastWord(string s) {
        if (s.empty()) return 0;
        
        int size = s.size();
        int ans = 0, i = size - 1;
        
        while (s[i] == ' ') --i;
        
        for (; i >= 0; --i) {
            if (s[i] != ' ') ++ans;
            else break;
        }
        
        return ans;
    }
};
```

```c
class Solution {
public:
    int lengthOfLastWord(const char *s) {
        int size = strlen(s);
        int ans = 0, i = size - 1;
        
        while (i >= 0 && *(s + i) == ' ') --i;
        while (i >= 0 && *(s + i) != ' ') {
            ++ans;
            --i;
        }
        
        return ans;
    }
};
```

后面的方法感觉在面试里做不太好，还可以正则、分割、正序扫描等等方法

## solution2 - C++的输入输出控制类 stringstream

```cpp
//链接：https://www.nowcoder.net/questionTerminal/dae459428862446daf49b75d26d7523d
//来源：牛客网

//利用C++的输入输出控制类 stringstream
class Solution {
public:
    int lengthOfLastWord(const char *s) {
        stringstream ss(s);
        string str;
        while(ss >> str);
        return str.length();
    }
};
```

## solution3 - STL string 的方法

```cpp
class Solution {
public:
    int lengthOfLastWord(string s) {
        if (s.empty()) return 0;

        int x = s.find_last_not_of(' ');
        return (x == std::string::npos) ? 0 : x - s.find_last_of(' ', x);
    }
};
```