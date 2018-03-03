# Number of 1 Bits

Tags: Bit Manipulation, Easy

## Problem

Write a function that takes an unsigned integer and returns the number of ’1' bits it has (also known as the [Hamming weight](http://en.wikipedia.org/wiki/Hamming_weight)).

For example, the 32-bit integer ’11' has binary representation `00000000000000000000000000001011`, so the function should return 3.

## Solution 1

本题有点像 `剑指 offer` 的面试题 10，简化版，不用考虑负数的情况。

下面的解法，32 位的整数需要循环 32 次

```cpp
class Solution {
public:
    int hammingWeight(uint32_t n) {
        unsigned int flag = 1;
        int count = 0;
        while (flag) {
            if (n & flag) ++count;
            flag <<= 1;
        }
        return count;
    }
};
```

## Solution 2 

下面的解法，整数有几个 1 就循环几次

```cpp
class Solution {
public:
    int hammingWeight(uint32_t n) {
        int count = 0;
        while (n) {
            ++count;
            n &= (n - 1);
        }
        return count;
    }
};
```

