# Ugly Number II

Tags: Math, Dynamic Programming, Heap, Medium

## Problem

Write a program to find the `n`-th ugly number.

Ugly numbers are positive numbers whose prime factors only include `2, 3, 5`. For example, `1, 2, 3, 4, 5, 6, 8, 9, 10, 12` is the sequence of the first `10` ugly numbers.

Note that `1` is typically treated as an ugly number, and *n* **does not exceed 1690**.

## Solution

本题与 `剑指 offer` 面试题 34 相同

定义三个指针分别指向已经遍历到的作为乘数的下一个元素

由于要找三个数中最小的那个，所以自己定义了 mymin，不能直接写成 min，否则调用库函数 min 的时候会报错（按理说不应该）

```cpp
class Solution {
public:
    int nthUglyNumber(int n) {
        if (n <= 0) return 0;
        
        vector<int> ans(n);
        ans[0] = 1;
        int last2_id = 0, last3_id = 0, last5_id = 0;
        
        for (int i = 1; i < n; ++i) {
            ans[i] = mymin(ans[last2_id] * 2, ans[last3_id] * 3, ans[last5_id] * 5);
            
            while (ans[last2_id] * 2 <= ans[i]) ++last2_id;
            while (ans[last3_id] * 3 <= ans[i]) ++last3_id;
            while (ans[last5_id] * 5 <= ans[i]) ++last5_id;
        }
        
        return ans[n - 1];
    }
    
    int mymin(int i, int j, int k) {
        return min(min(i, j), k);
    }
};
```

