# Unique Paths II

Tags: Array, Dynamic Programming, Medium

## Problem

Follow up for "Unique Paths":

Now consider if some obstacles are added to the grids. How many unique paths would there be?

An obstacle and empty space is marked as `1` and `0` respectively in the grid.

For example,

There is one obstacle in the middle of a 3x3 grid as illustrated below.

```
[
  [0,0,0],
  [0,1,0],
  [0,0,0]
]
```

The total number of unique paths is `2`.

**Note:** *m* and *n* will be at most 100.

## Solution 1 - 预先处理边界

在上一题的基础上加了障碍，也就有更多的边界条件需要考虑，有以下两点需要注意：

1. dp 默认初始化为 0，单独处理第一行和第一列，为 0 时，dp 置1，一旦发现出现障碍 1，则立刻跳出循环，即当有障碍时，该行、列后面的位置都将变得不可达。（如果还是把 dp 初始化为 1，再处理就比较麻烦了不仅第一行第一列比较麻烦，每次生成 dp 时也得判断之前的状态）
2. 生成 dp 时，要先检查该点是否有障碍，如果有障碍则 dp 立即置 0， 代表不可达。（这里的 else 其实也可以不写，因为dp 矩阵初始化的值就是 0）

```cpp
class Solution {
public:
    int uniquePathsWithObstacles(vector<vector<int>>& obstacleGrid) {
        if (obstacleGrid.empty() || obstacleGrid[0].empty()) return 0;
        int m = obstacleGrid.size();
        int n = obstacleGrid[0].size();
        
        vector<vector<int>> dp(m, vector<int>(n, 0));
        for (int i = 0; i < m; ++i) {
            if (obstacleGrid[i][0] != 1) {
                dp[i][0] = 1;
            } else {
                break;
            }
        }
        
        for (int i = 0; i < n; ++i) {
            if (obstacleGrid[0][i] != 1) {
                dp[0][i] = 1;
            } else {
                break;
            }
        }
        
        for (int i = 1; i < m; ++i) {
            for (int j = 1; j < n; ++j) {
                if (obstacleGrid[i][j] == 0) {
                    dp[i][j] = dp[i - 1][j] + dp[i][j - 1];
                } else dp[i][j] = 0;
            }
        }
        
        return dp[m - 1][n - 1];
    }
};
```

### 复杂度

时间复杂度 $$O(m * n)$$

空间复杂度 $$O(m * n)$$

## Solution 2 - 矩阵加一行一列

观察上面的解法，发现其实第一行和第一列是可以放在后面的循环里一起处理的。这里用到了一个技巧就是给 dp 矩阵 多加一行一列，构成 m + 1 ， n + 1的矩阵，全部初始化为 0，除了 `dp[0][1]`， 可以把它理解成启动点，没有这个点的话，我们就无从判断 1,1 的状态，而 1,1 又决定了 第一行和第一列。所以这里的这个处理非常巧妙。

> 另注：这里也只能是 `dp[0][1]` ,`dp[1][0]` 都会错，会影响第一列第二行的元素。

需要注意的一点就是此时 i，j 序号往后挪了一格，所以在 判断 `!obstacleGrid[i - 1][j - 1]` 的时候均要 -1

```cpp
class Solution {
public:
    int uniquePathsWithObstacles(vector<vector<int>>& obstacleGrid) {
        if (obstacleGrid.empty() || obstacleGrid[0].empty()) return 0;
        int m = obstacleGrid.size();
        int n = obstacleGrid[0].size();
        vector<vector<int>> dp(m + 1, vector<int>(n + 1, 0));
        
        dp[0][1] = 1; // nice！！！
        for (int i = 1; i <= m; ++i) {
            for (int j = 1; j <= n; ++j) {
                if (!obstacleGrid[i - 1][j - 1]) { // 这里要减 1
                    dp[i][j] = dp[i - 1][j] + dp[i][j - 1];
                }
            }
        }
        
        return dp[m][n];
    }
};
```

### 优化，改为一行

在上面的基础，把空间改为一行。

注意，和上面一行，只能是由 dp[1] 来启动，dp[0] 会影响下面的状态

```cpp
class Solution {
public:
    int uniquePathsWithObstacles(vector<vector<int>>& obstacleGrid) {
        if (obstacleGrid.empty() || obstacleGrid[0].empty()) return 0;
        int m = obstacleGrid.size();
        int n = obstacleGrid[0].size();
        vector<int> dp(n + 1, 0);
        
        dp[1] = 1;
        for (int i = 1; i <= m; ++i) {
            for (int j = 1; j <= n; ++j) {
                if (!obstacleGrid[i - 1][j - 1]) {
                    dp[j] += dp[j - 1];
                } else dp[j] = 0;
            }
        }
        
        return dp[n];
    }
};
```

