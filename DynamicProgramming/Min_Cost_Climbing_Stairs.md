# Min Cost Climbing Stairs

Tags: Array, Dynamic Programming, Easy

## Problem

On a staircase, the `i`-th step has some non-negative cost `cost[i]` assigned (0 indexed).

Once you pay the cost, you can either climb one or two steps. You need to find minimum cost to reach the top of the floor, and you can either start from the step with index 0, or the step with index 1.

**Example 1:**

```
Input: cost = [10, 15, 20]
Output: 15
Explanation: Cheapest is start on cost[1], pay that cost and go to the top.

```

**Example 2:**

```
Input: cost = [1, 100, 1, 1, 1, 100, 1, 1, 100, 1]
Output: 6
Explanation: Cheapest is start on cost[0], and only step on 1s, skipping cost[3].

```

**Note:**

1. `cost` will have a length in the range `[2, 1000]`.
2. Every `cost[i]` will be an integer in the range `[0, 999]`.

## Solution 1 - Bottom-Up

这种类型的问题是典型的递归问题。

dp[i] 为到达第 i 个台阶继续往上爬所需要的最小花费。

那么， dp[i] = cost[i] + min(dp[i - 1], dp[i - 2])

注意，题目里的 cost[i] 的含义是在第 i 个台阶花费后才能继续往上爬。

dp[0] = cost[0], dp[1] = cost[1]

有 0 - n - 1 个台阶，要爬到顶峰，可以从第 n - 1 个台阶爬 1 步，也可以从第 n - 2 个台阶爬 2 步，

所以最终返回 min(dp[n - 1], dp[n - 2]) 

切记，不要写成了 cost[n - 1] + dp[n - 1] 或者 cost[n - 2] + dp[n - 2]，因为我们在定义 dp 的时候就已经把继续往上爬的花费加进去了，dp[n - 1] 已经就表示了到了 n -1 还可以往上爬 1 - 2 步，所以 dp 问题一定要想清楚 dp 数组的含义。

```cpp
class Solution {
public:
    int minCostClimbingStairs(vector<int>& cost) {
        int n = cost.size();
        vector<int> dp(n);
        
        dp[0] = cost[0];
        dp[1] = cost[1];
        
        for (int i = 2; i < n; ++i) {
            dp[i] = cost[i] + min(dp[i - 1], dp[i - 2]);
        }
        
        return min(dp[n - 1], dp[n - 2]);
    }
};
```

### 另一种形式的 DP

这里换上面跳到的另外一种定义方式，dp[i] 代表达到 i 所需的最小话费，所以最终返回的是 dp[n]

递推公式也就变成了 dp[i] = min(cost[i - 1] + dp[i - 1], cost[i - 2] + dp[i - 2])

要小心处理边界。题目提到了可以从 0 开始，也可以从 1 开始，也就说明了 dp[0] dp[1] 均为 0，不需要任何花费就能到达。

```cpp
class Solution {
public:
    int minCostClimbingStairs(vector<int>& cost) {
        int n = cost.size();
        vector<int> dp(n + 1);
        
        dp[0] = 0;
        dp[1] = 0;
        
        for (int i = 2; i <= n; ++i) {
            dp[i] = min(cost[i - 1] + dp[i - 1], cost[i - 2] + dp[i - 2]);
        }
        
        return dp[n];
    }
};
```

### 复杂度

时间复杂度 $$O(n)$$

空间复杂度 $$O(n)$$

由于当前的状态只跟前两个状态相关，所以其实可以定义两个变量不断刷新，把空间复杂度控制在 $$O(1)$$

```cpp
class Solution {
public:
    int minCostClimbingStairs(vector<int>& cost) {
        int n = cost.size();
        
        int first = 0;
        int second = 0;
        
        for (int i = 2; i <= n; ++i) {
            int tmp = min(cost[i - 1] + second, cost[i - 2] + first);
            first = second;
            second = tmp;
        }
        
        return second;
    }
};
```



## Solution 2 - 递归 - Top-Down

[参考](https://leetcode.com/problems/min-cost-climbing-stairs/discuss/110111/Easy-to-understand-C++-using-DP-with-detailed-explanation)

为了不超时，必须要用到 memo-ization 来存储中间量。注意两个 base case：

memo[0]=c[0]
memo[1]=c[1]

最后 return 的时候要对 c.size() 做判断，因为最后一步就不需要 cost 了。

```cpp
class Solution {
public:
    int minCostClimbingStairs(vector<int>& cost) {
        return helper(cost, cost.size());
    }
private:
    unordered_map<int,int> memo;
    int helper(vector<int>& c, int n){
        if (memo.count(n)) return memo[n]; // 如果有就直接返回，大大减少计算量
        if (n<=1) return memo[n]=c[n];
        return memo[n]=(n==c.size() ? 0 : c[n])+min(helper(c,n-2),helper(c,n-1));
    }
};
```

