# Spiral Matrix

Tags: Array, Medium

## Question

Given a matrix of m x n elements (m rows, n columns), return all elements of the matrix in spiral order.

For example,
Given the following matrix:

[
 [ 1, 2, 3 ],
 [ 4, 5, 6 ],
 [ 7, 8, 9 ]
]
You should return [1,2,3,6,9,8,7,4,5].

## 题解1 - 一圈圈从外向内生成

本题与《剑指 offer》一书中的面试题 20：顺时针打印矩阵相同。注意好边界问题。

```cpp
class Solution {
public:
    vector<int> spiralOrder(vector<vector<int> > &matrix) {
        int row = matrix.size();
        // 异常处理很关键！！！
        if (row == 0) return {};
        int col = matrix[0].size();
        
        vector<int> result;
        int start = 0;
        while (row > start * 2 && col > start * 2) {
            vector<int> tmp = getSpiralCircle(matrix, start);
            result.insert(result.end(), tmp.begin(), tmp.end());
            ++ start;
        }
        
        return result;
    }
    
    vector<int> getSpiralCircle(vector<vector<int>>& matrix, int start) {
        int endY = matrix.size() - 1 - start;
        int endX = matrix[0].size() - 1 - start;
        
        vector<int> circle;
        for (int i = start; i <= endX; ++ i) {
            circle.push_back(matrix[start][i]);
        }
        
        if (endY > start) {
            for (int i = start + 1; i <= endY; ++ i) {
                circle.push_back(matrix[i][endX]);
            }
        }
        
        if (endY > start && endX > start) {
            for (int i = endX - 1; i >= start; -- i) {
                circle.push_back(matrix[endY][i]);
            }
        }
        
        if (endX > start && endY - 1 > start) {
            for (int i = endY - 1; i > start; -- i) {
                circle.push_back(matrix[i][start]);
            }
        }
        return circle;
    }
};
```

[参考解法](https://leetcode.com/problems/spiral-matrix/solution/)
[参考2](https://www.nowcoder.com/questionTerminal/7edf70f2d29c4b599693dc3aaeea1d31)

### 优化

上面的判断显得太过于累赘

while 里的 `if (r1 < r2 && c1 < c2)` 很关键。

```cpp
class Solution {
public:
    vector<int> printMatrix(vector<vector<int> > matrix) {
        if (matrix.empty()) return vector<int>();
        int r1 = 0, r2 = matrix.size() - 1;
        int c1 = 0, c2 = matrix[0].size() - 1;
        
        vector<int> ans;
        while (r1 <= r2 && c1 <= c2) {
            for (int c = c1; c <= c2; ++c) ans.push_back(matrix[r1][c]);
            for (int r = r1 + 1; r <= r2; ++r) ans.push_back(matrix[r][c2]);
            
            if (r1 < r2 && c1 < c2) {
                for (int c = c2 - 1; c >= c1; --c) ans.push_back(matrix[r2][c]);
                for (int r = r2 - 1; r >= r1 + 1; --r) ans.push_back(matrix[r][c1]);
            }
            ++r1, --r2, ++c1, --c2;
        }
        return ans;
    }
};
```

