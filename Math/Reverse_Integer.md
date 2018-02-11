# Reverse Integer

Tags: Math, Easy

## Problem

Given a 32-bit signed integer, reverse digits of an integer.

**Example 1:**

```
Input: 123
Output:  321

```

**Example 2:**

```
Input: -123
Output: -321

```

**Example 3:**

```
Input: 120
Output: 21

```

**Note:**
Assume we are dealing with an environment which could only hold integers within the 32-bit signed integer range. For the purpose of this problem, assume that your function returns 0 when the reversed integer overflows.

## Solution

因为题目说明了只会处理 32 位整数，也就证明输入结果肯定是不溢出的，我这里做的溢出判断比较偷懒，直接判断溢出后是否反号，然而。。 1999999999 的逆序按理说溢出，且不会反号，感觉得补补计算机补码的知识了。。

我的错误解法：

```cpp
class Solution {
public:
    int reverse(int x) {
        if (x == INT_MIN) return 0;
        
        bool sign = x >= 0 ? true : false;
        x = x > 0 ? x : -x;
        
        int ans = 0;
        
        while (x != 0) {
            int digit = x % 10;
            x /= 10;
            ans = ans * 10 + digit;
            if (ans < 0) return 0;
        }
        return sign ? ans : -ans;
    }
};
```

正确解法

不用单独处理正负数，负数也是满足的。

检测 `ans / 10 != tmp` 即可判断

```cpp
class Solution {
public:
    int reverse(int x) {
        int ans = 0;
        
        while (x != 0) {
            int tmp = ans;
            ans = ans * 10 + x % 10;
            if (ans / 10 != tmp) return 0;
            x /= 10;
        }
        return ans;
    }
};
```

也可以用 long  long 来存，这样就可以直接比较溢出值了

```cpp
class Solution {
public:
    int reverse(int x) {
        long long res = 0;
        while(x) {
            res = res*10 + x%10;
            x /= 10;
        }
        return (res<INT_MIN || res>INT_MAX) ? 0 : res;
    }
};
```

