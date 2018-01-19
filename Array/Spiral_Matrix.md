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