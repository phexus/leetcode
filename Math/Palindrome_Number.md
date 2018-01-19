# Palindrome Number

Tags: Math, Easy

## Problem

Determine whether an integer is a palindrome. Do this without extra space.

Some hints:
Could negative integers be palindromes? (ie, -1)

If you are thinking of converting the integer to string, note the restriction of using extra space.

You could also try reversing an integer. However, if you have solved the problem "Reverse Integer", you know that the reversed integer might overflow. How would you handle such case?

There is a more generic way of solving this problem.

## 题解

要求不能使用额外的空间。
迭代的对最高位和最低位，进行比较，一旦发现一个不相等就返回false
取一个整数的最低位可以使用x%10
取一个整数的最高位需要知道最高是哪一位

```cpp
class Solution {
public:
    bool isPalindrome(int x) {
        if (x < 0) return false;
        
        // 判断最高位是哪一位
        int high = 1;
        while (x / high >= 10) high *= 10;
        
        while (x > 0) {
            int front = x / high;
            int back = x % 10;
            if (front != back) return false;
            x = (x % high) / 10;
            // x 少了 2 位，所以要除以 100
            high /= 100;
        }
        return true;
    }
};
```

### 源码分析

注意判断最高位的方法以及边界条件
更新 x 和 high，需要把最高位、最低位都去掉，所以 high 要除以 100

### 复杂度分析


https://www.nowcoder.com/questionTerminal/35b8166c135448c5a5ba2cff8d430c32
https://leetcode.com/problems/palindrome-number/solution/