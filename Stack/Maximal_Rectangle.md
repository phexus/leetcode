# Maximal Rectangle

Tags: Array, Hash Table, Dynamic Programming, Stack

## Problem

Given a 2D binary matrix filled with 0's and 1's, find the largest rectangle containing only 1's and return its area.

For example, given the following matrix:

```
1 0 1 0 0
1 0 1 1 1
1 1 1 1 1
1 0 0 1 0
```
Return 6.

## Solution 1 - 逐行叠加 - largest rectangle in histogram 

在 Largest Rectangle in Histogram 问题中，利用单调栈，时间复杂度为 $$O(n)$$ , 本题可以转换为多行的 柱状图问题。

以题目描述中的矩阵为例，在生成每一行的柱状图时，叠加之前的结果，但是如果该行为 0 ，则丢弃原来的结果。

第一行  1 0 1 0 0

第二行  2 0 2 1 1

第三行  3 1 3 2 2

第四行  4 0 0 3 0

分别计算每行的 柱状图子问题即可。

至于这背后的原理，可以这样理解，当遇到 0 的时候，证明原先的 矩形肯定会被破坏，所以只能归零，而不为 0 的时候表明矩形还在延续，所以累加。每行都得算也是因为最大结果有可能出现在中间。 

```cpp
class Solution {
public:
    int maximalRectangle(vector<vector<char>>& matrix) {
        if (matrix.empty() || matrix[0].empty()) return 0;
        
        int row = matrix.size();
        int col = matrix[0].size();
        
        vector<int> heights(col, 0);
        int ans = 0;
        for (int i = 0; i < row; ++i) {
            for (int j = 0; j < col; ++j) {
                if (matrix[i][j] == '0') heights[j] = 0;
                else heights[j] += 1;
            }
            
            ans = max(ans, largestRectangleArea(heights));
        }
        
        return ans;
    }
    
    int largestRectangleArea(vector<int>& heights) {
        int len = heights.size();
        int ans = 0;
        stack<int> st;
        
        for (int i = 0; i < len; ++i) {
            while (!st.empty() && heights[i] <= heights[st.top()]) {
                int h = heights[st.top()];
                st.pop();
                ans = max(ans, (i - 1 - (st.empty() ? -1 : st.top())) * h);
            }
            st.push(i);
        }
        
        while (!st.empty()) {
            int h = heights[st.top()];
            st.pop();
            ans = max(ans, (len - 1 - (st.empty() ? -1 : st.top())) * h);
        }
        
        return ans;
    }
};
```

## Solution 2 - DP

[DP](https://leetcode.com/problems/maximal-rectangle/discuss/29054/Share-my-DP-solution) 的解法感觉比较难理解，之后再补充。