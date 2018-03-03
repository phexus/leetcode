# Number of Digit One

Tags: Math, Hard

## Problem

Given an integer n, count the total number of digit 1 appearing in all non-negative integers less than or equal to n.

For example:
Given n = 13,
Return 6, because digit 1 occurred in the following numbers: 1, 10, 11, 12, 13.

## Solution 1 - TLE

本题和 `剑指 offer` 一书的面试题 32 相同，下面采用最常规的做法会超时

```cpp
class Solution {
public:
    int countDigitOne(int n) {
        int ans = 0;
        for (int i = 1; i <= n; ++i) {
            ans += numberOf1(i);
        }
        return ans;
    }
    
    int numberOf1(int n) {
        int count = 0;
        while (n) {
            if (n % 10 == 1) ++count;
            n /= 10;
        }
        return count;
    }
};
```

或者

```cpp
int countDigitOne(int n)
{
    int countr = 0;
    for (int i = 1; i <= n; i++) {
        string str = to_string(i);
        countr += count(str.begin(), str.end(), '1');
    }
    return countr;
}
```

### 复杂度

时间复杂度 $$O(n*log_{10}(n))$$ 

## Solution 2 - 数学规律

参考 [Solution](https://leetcode.com/problems/number-of-digit-one/solution/)

```cpp
class Solution {
public:
    int countDigitOne(int n) {
        int count = 0;
        for (long long i = 1; i <= n; i *= 10) {
            long long divider = i * 10;
            count += (n / divider) * i + min(max(n % divider - i + 1, 0LL), i);
        }
        return count;
    }
};
```

### 复杂度

时间复杂度 $$O(log_{10}(n))$$ 

