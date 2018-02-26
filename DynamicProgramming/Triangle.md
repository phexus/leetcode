# Triangle

Tags: Array, Dynamic Programming, Medium

## Problem

Given a triangle, find the minimum path sum from top to bottom. Each step you may move to adjacent numbers on the row below.

For example, given the following triangle

```
[
     [2],
    [3,4],
   [6,5,7],
  [4,1,8,3]
]
```

The minimum path sum from top to bottom is `11` (i.e., 2 + 3 + 5 + 1 = 11).

**Note:**
Bonus point if you are able to do this using only *O*(*n*) extra space, where *n* is the total number of rows in the triangle.

## Solution - 自上而下，自右及左

典型的 DP 问题，但是由于是金字塔结构，在构建 dp 矩阵的时候一定要以最长行和最长列来构建，下面的实现就犯了这个错误，leetcode 一般会报这种 run time error，也就是数组访问越界了。

把 `triangle[0].size()` 最短的列作为列的数目与显然是错的，始终为 1。

### 错误实现

```
Run Code Status: Runtime Error
free(): invalid next size (fast): 0x0000000001b85880 ***
```



```cpp
class Solution {
public:
    int minimumTotal(vector<vector<int>>& triangle) {
        if (triangle.empty() || triangle[0].empty()) return 0;
        
        int m = triangle.size();
        int n = triangle[0].size(); // 这里出错了！！！
        
        vector<vector<int>> dp(m, vector<int>(n));
        dp[0][0] = triangle[0][0];
        for (int i = 1; i < m; ++i) {
            dp[i][0] = dp[i - 1][0] + triangle[i][0];
            dp[i][i] = dp[i - 1][i - 1] + triangle[i][i];
        }
        
        for (int i = 2; i < m; ++i) {
            for (int j = 1; j < i; ++j) {
                dp[i][j] = triangle[i][j] + min(dp[i - 1][j], dp[i - 1][j - 1]);
            }
        }
        
        int ans = 0x7fffffff;
        for (int i = 0; i < n; ++i) {
            if (dp[m - 1][i] < ans) ans = dp[m - 1][i];
        }
        
        return ans;
    }
};
```

做这种非正常矩阵问题的时候，还是需要按照正常矩阵的位置来排列元素，以寻找状态转移方程。

`dp[i][j] = triangle[i][j] + min(dp[i - 1][j], dp[i - 1][j - 1]);`

两边的元素由于上层缺少对应元素，所以要单独处理。

其实发现这种金字塔结构的矩阵的行和最大列数是相同的，所以下面的正确实现里只求了行数。

### 正确实现

```cpp
class Solution {
public:
    int minimumTotal(vector<vector<int>>& triangle) {
        if (triangle.empty() || triangle[0].empty()) return 0;
        
        int n = triangle.size();
        
        vector<vector<int>> dp(n, vector<int>(n));
        dp[0][0] = triangle[0][0];
        for (int i = 1; i < n; ++i) {
            dp[i][0] = dp[i - 1][0] + triangle[i][0];
            dp[i][i] = dp[i - 1][i - 1] + triangle[i][i];
        }
        
        for (int i = 2; i < n; ++i) {
            for (int j = 1; j < i; ++j) {
                dp[i][j] = triangle[i][j] + min(dp[i - 1][j], dp[i - 1][j - 1]);
            }
        }
        
        int ans = 0x7fffffff;
        for (int i = 0; i < n; ++i) {
            if (dp[n - 1][i] < ans) ans = dp[n - 1][i];
        }
        
        return ans;
    }
};
```

### 优化 

题目要求做到 $$O(n)$$ 的复杂度，跟 unique_path 一样的套路，然而看似简单，实则暗藏杀机。由于这次的状态会受到上一轮的同一列和前一列状态的影响，所以如果跟 unique_path 一样从左往右更新的话就会大大的不妥，会把上一轮的前一列状态给刷新掉。所以要按照每一行从右往左更新，对于第 0 列 也要单独处理，一定要放在内层 loop 后面更新。

```cpp
class Solution {
public:
    int minimumTotal(vector<vector<int>>& triangle) {
        if (triangle.empty() || triangle[0].empty()) return 0;
        
        int n = triangle.size();
        
        vector<int> dp(n, 0x7fffffff);
        dp[0] = triangle[0][0];
        
        for (int i = 1; i < n; ++i) {
            for (int j = i; j > 0; --j) {
                dp[j] = triangle[i][j] + min(dp[j], dp[j - 1]);
            }
            dp[0] += triangle[i][0];
        }
        
        int ans = 0x7fffffff;
        for (int i = 0; i < n; ++i) {
            if (dp[i] < ans) ans = dp[i];
        }
        
        return ans;
    }
};
```

## Solution 2 - 自下而上，自左及右

看了 leetcode 的[解答](https://leetcode.com/problems/triangle/discuss/38730/DP-Solution-for-Triangle) ，才发现自己的思维还是太僵化了，上面的解法自上而下有一个弊端，就是最后要遍历一遍，虽然并没有增加时间复杂度，但是还是太繁琐了。

虽然状态转移方程为 `dp[i][j] = triangle[i][j] + min(dp[i - 1][j], dp[i - 1][j - 1]);` 

但是我们可以倒着往前走，也不会改变最终的结果，只要路径选择的规则和正着走一样就行，这样一来，状态转移方程就变成了：

`dp[i][j] = triangle[i][j] + min(dp[i + 1][j], dp[i + 1][j + 1]);`

更加精妙的是，每走完一层，存结果时就会少一位。所以最终返回 dp[0] 即可。第 0 列也不用单独处理了。

不过初始化 dp 的时候，要用最后一行来初始化，而不是 0x7fffffff

此时的状态转移后一个会影响前一个，所以要从左往右走。

代码不可思议的简洁。

```cpp
class Solution {
public:
    int minimumTotal(vector<vector<int>>& triangle) {
        if (triangle.empty() || triangle[0].empty()) return 0;
        
        int n = triangle.size();
        
        vector<int> dp(triangle.back());
        
        for (int i = n - 2; i >= 0; --i) {
            for (int j = 0; j <= i; ++j) {
                dp[j] = triangle[i][j] + min(dp[j], dp[j + 1]);
            }
        }
        
        return dp[0];
    }
};
```



