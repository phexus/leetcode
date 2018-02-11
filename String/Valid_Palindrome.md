# Valid Palindrome

Tags: String, Two Pointers, Easy

## Problem

Given a string, determine if it is a palindrome, considering only alphanumeric characters and ignoring cases.

For example,
`"A man, a plan, a canal: Panama"` is a palindrome.
`"race a car"` is *not* a palindrome.

**Note:**
Have you consider that the string might be empty? This is a good question to ask during an interview.

For the purpose of this problem, we define empty string as valid palindrome.

## Solution - 两指针头尾遍历

本题比较标准，string 也可以看成是存储 char 的数组，用头尾指针往中间遍历。

由于只判断数组和字母（忽略大小写），下面的解法对字符的判断没有使用库函数，而是自己写的，出了不少 bug。。

忽略字母大小写来比较，`(a >= 'A' && b >= 'A' && abs(a - b) == 'a' - 'A')` 一定要限定 字符的范围，如果直接写 ` abs(a - b) == 'a' - 'A')` 会有问题的。虽然输入限定了一定是数字和字母，但是 对于 `0` `P` 就会误判，`'P' - '0' = 32` 。在 ASCII 表里 0 -9，A - Z, a - z 都是不连续的，一定要小心。

```cpp
class Solution {
public:
    bool isPalindrome(string s) {
        if (s.empty()) return true;
        int len = s.size();
        
        for (int i = 0, j = len - 1; i < j; ) {
            if (!isAlphanumeric(s[i])) ++i;
            if (!isAlphanumeric(s[j])) --j;
            
            if (isAlphanumeric(s[i]) && isAlphanumeric(s[j])) {
                if (!charEqual(s[i], s[j])) return false;
                ++i; --j;
            }
        }
        return true;
    }
    

private:
    bool isAlphanumeric(char c) {
        if ((c >= '0' && c<= '9') || (c >= 'A' && c<= 'Z') || (c >= 'a' && c <= 'z') ) return true;
        return false;
    }
    bool charEqual(char a, char b) {
        if (a == b || (a >= 'A' && b >= 'A' && abs(a - b) == 'a' - 'A')) return true;
        return false;
    }
};
```

调用了 `isalnum()` 和 `tolower()` 也可以用 `toupper()`

由于 for 里面嵌套的 while，破坏了统一的循环判断条件 `i < j` 所以为了不越界，一定要在每个 while 加 `i < j` 的判断条件。 

```cpp
class Solution {
public:
    bool isPalindrome(string s) {
        for (int i = 0, j = s.length() - 1; i < j; ++i, --j) {
            while (i < j && !isalnum(s[i])) ++i;
            while (i < j && !isalnum(s[j])) --j;
            if (tolower(s[i]) != tolower(s[j])) return false; // 一直转换为小写来比较
        }
        return true;
    }
};
```

### 复杂度

两根指针遍历一次，时间复杂度 $$O(n)$$

空间复杂度 $$O(1)$$

 