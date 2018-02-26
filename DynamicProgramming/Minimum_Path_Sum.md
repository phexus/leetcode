# Minimum Path Sum

Tags: Array, Dynamic Programming, Medium

## Problem

Given a *m* x *n* grid filled with non-negative numbers, find a path from top left to bottom right which *minimizes* the sum of all numbers along its path.

**Note:** You can only move either down or right at any point in time.

**Example 1:**

```
[[1,3,1],
 [1,5,1],
 [4,2,1]]
```

Given the above grid map, return 

```
7
```

. Because the path 1→3→1→1→1 minimizes the sum.

## Solution

典型的 DP 问题。不过第一行和第一列要单独处理，一直累加到尾

递推公式 `dp[i][j] = grid[i][j] + min(dp[i - 1][j], dp[i][j - 1])`

```cpp
class Solution {
public:
    int minPathSum(vector<vector<int>>& grid) {
        if (grid.empty() || grid[0].empty()) return 0;
        int m = grid.size();
        int n = grid[0].size();
        
        vector<vector<int>> dp(m, vector<int>(n));
        dp[0][0] = grid[0][0];
        for (int i = 1; i < m; ++i) {
            dp[i][0] = dp[i - 1][0] + grid[i][0];
        }
        for (int i = 1; i < n; ++i) {
            dp[0][i] = dp[0][i - 1] + grid[0][i];
        }
        
        for (int i = 1; i < m; ++i) {
            for (int j = 1; j < n; ++j) {
                dp[i][j] = grid[i][j] + min(dp[i - 1][j], dp[i][j - 1]);
            }
        }
        
        return dp[m - 1][n - 1];
    }
};
```

### 复杂度

时间复杂度 $$O(m * n)$$

空间复杂度 $$O(m * n)$$

### 改为一行一行覆盖

和 unique _path 系列一样，空间复杂度可以继续优化为一行。

下面的解法，为了在循环里把 i = 0 和 j = 0 的情况也囊括进去，实现的比较 ugly，用了两个三元表达式

```cpp
class Solution {
public:
    int minPathSum(vector<vector<int>>& grid) {
        if (grid.empty() || grid[0].empty()) return 0;
        int m = grid.size();
        int n = grid[0].size();
        
        vector<int> dp(n, 0);
        dp[0] = grid[0][0];
        
        for (int i = 0; i < m ; ++i) {
            for (int j = 0; j < n; ++j) {
                if (i == 0) dp[j] = grid[0][j] + (j == 0 ? 0 : dp[j - 1]);
                else {
                    dp[j] = grid[i][j] + min(dp[j], (j == 0 ? dp[j] : dp[j - 1]));
                }
            }
        }
        return dp[n - 1];
    }
};
```

更加简洁的写法，注意把 dp 的初始值设为了最大值，这么处理是为了处理第一行时有个参照对比，就不用像上面那样单独处理第一行了。

dp[0] 设为 0，是为了处理第一个元素不会出错。

```cpp
class Solution {
public:
    int minPathSum(vector<vector<int>>& grid) {
        if (grid.empty() || grid[0].empty()) return 0;
        int m = grid.size();
        int n = grid[0].size();
        
        vector<int> dp(n, 0x7fffffff); // 设为最大值
        dp[0] = 0; // 第一个值设为 0
        
        for (int i = 0; i < m ; ++i) {
            dp[0] += grid[i][0];
            for (int j = 1; j < n; ++j) {
                dp[j] = grid[i][j] + min(dp[j], dp[j - 1]);
            }
        }
        return dp[n - 1];
    }
};
```

