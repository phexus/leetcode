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

DP 思路：P[i][j]是记录i到j子串是不是回文串。P(i,j)={true,if the substring Si…Sj is a palindrome;false,if the substring Si…Sj is not a palindrome}。那么可以得到：
P(i,j)=(P(i+1,j−1) && Si==Sj)

## Solution - Manacher's Algorithm - 比较难 - O(n)

[参考链接](http://articles.leetcode.com/longest-palindromic-substring-part-ii/)