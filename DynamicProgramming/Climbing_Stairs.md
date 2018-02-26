# Climbing Stairs

Tags: Dynamic Programming, Easy

## Problem

You are climbing a stair case. It takes *n* steps to reach to the top.

Each time you can either climb 1 or 2 steps. In how many distinct ways can you climb to the top?

**Note:** Given *n* will be a positive integer.

**Example 1:**

```
Input: 2
Output:  2
Explanation:  There are two ways to climb to the top.

1. 1 step + 1 step
2. 2 steps
```

**Example 2:**

```
Input: 3
Output:  3
Explanation:  There are three ways to climb to the top.

1. 1 step + 1 step + 1 step
2. 1 step + 2 steps
3. 2 steps + 1 step
```

## Solution 1 - Fibonacci

本题与 `剑指 offer` 面试题 9 的题目 2 部分相同。其实就是一个 Fibonacci 数列。

f(1) = 1, f(2) = 2, ... , f(n) = f(n - 1) + f(n - 2)

一般这种问题虽然用递归实现起来很方便，但是效率会很差，消耗了太多的栈空间，所以还是用递推自下往上来算。

下面采用的解法非常朴素，argu1 argu2 分别代表参数 1 和参数 2

```cpp
class Solution {
public:
    int climbStairs(int n) {
        if (n < 4) return n;
        
        int argu1 = 2;
        int argu2 = 3;
        int ans;
        for (int i = 4; i <= n; ++i) {
            ans = argu1 + argu2;
            argu1 = argu2;
            argu2 = ans;
        }
        return ans;
    }
};
```

### 另一种写法

比较巧妙的处理了边界情况，减少了变量。

> 注意，f(2) = 2, 原版 Fibonacci 的 f(2) = 1，要小心处理。

```cpp
class Solution {
public:
    int climbStairs(int n) {
        
        int first = 0;
        int second = 1;
        while (n--) {
            second += first;
            first = second - first;
        }
        return second;
    }
};
```

## Solution 2 - DP

用 dp[i] 来代表到第 n 个台阶的步数，dp[i] = dp[i - 1] + dp[i - 2] 

```cpp
class Solution {
public:
    int climbStairs(int n) {
        if (n == 1) return 1;
        vector<int> dp(n + 1, 0);
        
        dp[1] = 1, dp[2] = 2;
        for (int i = 3; i <= n; ++i) {
            dp[i] = dp[i - 1] + dp[i - 2];
        }
        
        return dp[n];
    }
};
```

## 其他解法

还有时间复杂度更低的矩阵方法，跟数学公式比较紧密，之后再补充

[solution](https://leetcode.com/problems/climbing-stairs/solution/)