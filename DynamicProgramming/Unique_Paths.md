# Unique Paths

Tags: Array, Dynamic Programming, Medium

## Problem

A robot is located at the top-left corner of a *m* x *n* grid (marked 'Start' in the diagram below).

The robot can only move either down or right at any point in time. The robot is trying to reach the bottom-right corner of the grid (marked 'Finish' in the diagram below).

How many possible unique paths are there?

![img](https://leetcode.com/static/images/problemset/robot_maze.png)

Above is a 3 x 7 grid. How many possible unique paths are there?

**Note:** *m* and *n* will be at most 100.

## Solution

典型的入门 DP 问题，设置 `dp[i][j]` 的含义为到 (i , j) 所有的走法数量。

题目限制了只能往右走或往下走，所以第一行和第一列的值就只能是 1 ，这也是求后续状态的基础。

`dp[m][n] = dp[m-1][n] + dp[m][n-1] `分析终点与左边及右边节点的路径数，发现从左边或者右边到达终点的路径一定不会重合，相加即为唯一的路径总数

接下来算的顺序就按照二维矩阵的遍历即可，从 1,1 出发 到 m-1，n-1

```cpp
class Solution {
public:
    int uniquePaths(int m, int n) {
        if (m < 1 || n < 1) return 0;
        vector<vector<int>> dp(m, vector<int>(n, 1)); // 全部初始化为 1
        
        for (int i = 1; i < m; ++i) {
            for (int j = 1; j < n; ++j) {
                dp[i][j] = dp[i - 1][j] + dp[i][j - 1];
            }
        }
        
        return dp[m - 1][n - 1];
    }
};
```

### 复杂度

时间复杂度 $$O(m * n)$$

空间复杂度 $$O(m * n)$$

### 优化 1

[参考](https://leetcode.com/problems/unique-paths/discuss/22954/0ms-5-lines-DP-Solution-in-C++-with-Explanations)

在更新 `dp[i][j]` 的时候只需要 上面的数和左边的数，所以维护了两个向量，分别代表上一行和当前行。有个小细节就是让行 > 列， 空间复杂度低一点，所以一开始做了判断。

外层 loop 控制总的列数，内层 loop 控制每列的每个元素如何更新，每做完一列，把 curr 交换给 pre，最后返回的是 pre 的最后一个元素。这样循环看起来别扭，但是能使得效率高点

优化后的时间复杂度 $$O(min(m, n))$$

```cpp
class Solution {
    int uniquePaths(int m, int n) {
        if (m > n) return uniquePaths(n, m); //使 行 > 列
        vector<int> pre(m, 1);
        vector<int> cur(m, 1);
        for (int j = 1; j < n; j++) {
            for (int i = 1; i < m; i++)
                cur[i] = cur[i - 1] + pre[i];
            swap(pre, cur);
        }
        return pre[m - 1];
    }
};
```

### 优化 2

利用了递增，只维护了一个向量就足够了。

```cpp
class Solution {
    int uniquePaths(int m, int n) {
        if (m > n) return uniquePaths(n, m);
        vector<int> cur(m, 1);
        for (int j = 1; j < n; j++)
            for (int i = 1; i < m; i++)
                cur[i] += cur[i - 1]; 
        return cur[m - 1];
    }
}; 
```

不能再继续优化了，因为在更新下一列的时候需要知道前一列的所有情况，只用 2 个变量会被覆盖的。

``````
 Since the whole cur needs to be updated for n - 1 times, it means that all of its values need to be saved for next update and so two variables is not enough.
``````

