# Pow(x, n)

Tags: Math, Medium

## Problem

Implement [pow(*x*, *n*)](http://www.cplusplus.com/reference/valarray/pow/).

**Example 1:**

```
Input: 2.00000, 10
Output: 1024.00000
```

**Example 2:**

```
Input: 2.10000, 3
Output: 9.26100
```

## Solution

本题与`剑指 offer` 面试题 11 相同，具体细节可以参见 `价值 offer` 上的解释。有几个需要注意的地方：

1. 底数为 0，指数是负数的情况需要单独处理，不能仅仅返回 0，这样就与正常的底数为 0 的时候返回 0 的情况冲突了。一般有 3 种做法， 返回值，全局代码， 异常。下面的做法定义可一个全局变量 g_InvalidInput，出错时设为 true。这种做法要求调用者去检查全局变量。
2. 由于计算机表示小数（flaot，double）都有误差，不能直接用 == 来判断两个小数是否相等，如果两个小数的差的绝对值很小，比如小于 0.0000001，就可以认为它们相等。下面单独定义了 equal 函数。
3. 通过判断 n 的奇偶性，递归地调用，可以将时间复杂度降到 $$O(log n)$$
4. 考虑到 INT_MIN 的 abs 结果不变，这里用了 `unsigned int absN = abs(n);` 扩大了一位就不会溢出了

```cpp
class Solution {
public:
    bool g_InvalidInput = false;
    
    double myPow(double x, int n) {
        g_InvalidInput = false;
        
        if (equal(x, 0.0) && n < 0) {
            g_InvalidInput = true;
            return 0.0;
        }
        
        unsigned int absN = abs(n);
        double ans = powerWithUnsignedN(x, absN);
        
        if (n < 0) ans = 1.0 / ans;
        return ans;
    }
    
    double powerWithUnsignedN(double x, unsigned int n) {
        if (n == 0) return 1.0;
        if (n == 1) return x;
        double ans = powerWithUnsignedN(x, n >> 1);
        ans *= ans;
        if (n & 0x1) ans *= x; // 用位运算判断奇偶性
        return ans;
    }
    
    bool equal(double num1, double num2) {
        if (num1 - num2 > -0.0000001 && num1 -num2 < 0.0000001) return true;
        else return false;
    }
};
```

### 复杂度

时间复杂度 $$O(log n)$$

空间复杂度 $$O(logn)$$ ，递归的调用过程中，每次都要存储中间结果，递归调用的次数是 $$O(log n)$$，递归调用消耗的栈空间也是如此规模。

## Solution 2 - Iterative

感觉迭代的做法不是很好理解，参见 [leetcode solution](https://leetcode.com/problems/powx-n/solution/)

```cpp
class Solution {
public:
    double myPow(double x, int n) {
        long long N = n;
        if (N < 0) {
            x = 1 / x;
            N = -N;
        }
        double ans = 1;
        double current_product = x;
        for (long long i = N; i ; i /= 2) {
            if ((i % 2) == 1) {
                ans = ans * current_product;
            }
            current_product = current_product * current_product;
        }
        return ans;
    }
};
```

### 复杂度

- Time complexity : $$O(log(n))$$. For each bit of `n` 's binary representation, we will at most multiply once. So the total time complexity is $$O(log(n))$$.
- Space complexity : $$O(1)$$. We only need two variables for the current product and the final result of `x`.

