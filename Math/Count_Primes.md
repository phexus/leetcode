# Count Primes

Tags: Math, Hash Table, Easy

## Problem

**Description:**

Count the number of prime numbers less than a non-negative number, **n**.

## Solution 1 - 暴力

直接暴力匹配，看从 2 到 $\sqrt{num}$ 是否能被整除

时间复杂度高达 $$O(n^1.5)$$

```cpp
class Solution {
public:
    int countPrimes(int n) {
        if (n < 2) return 0;
        int ans = 0;
        for (int i = 2; i < n; ++i) {
            if (isPrime(i)) ++ans;
        }
        return ans;
    }
    
    bool isPrime(int num) {
        if (num < 1) return false;
        int max = sqrt(num);
        
        for (int i = 2; i <= max; ++i) {
            if ((num % i) == 0) return false;
        }
        
        return true;
    }
};
```

## Solution 2 - 埃氏筛选法

参考了 [hint](https://leetcode.com/problems/count-primes/hints/) 

![img](https://leetcode.com/static/images/solutions/Sieve_of_Eratosthenes_animation.gif)

注意好边界，外层循环 `for (int i = 2; i * i < n; ++i) ` 只用判断到 $\sqrt{n}$ 即可，因为在处理前面的时候，后面的肯定已经被处理过了。内层循环 `for (int j = i * i; j < n; j += i)` 也是一样的道理，尽可能把已经做过的跳过。

```cpp
class Solution {
public:
    int countPrimes(int n) {
        if (n < 2) return 0;
        vector<bool> isPrime(n, true);
        
        isPrime[0] = false;
        isPrime[1] = false;
        for (int i = 2; i * i < n; ++i) {
            if (isPrime[i]) {
                for (int j = i * i; j < n; j += i) {
                    isPrime[j] = false;
                }
            }
        }
        
        int ans = 0;
        for (int i = 2; i < n; ++i) {
            if (isPrime[i]) ++ans;
        }
        
        return ans;
    }
};
```

