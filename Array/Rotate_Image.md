# Rotate Image

Tags: Array, Medium

## Question

- leetcode: [Rotate Image](https://leetcode.com/problems/rotate-image/description/)
- nowcoder: [Rotate Image](https://www.nowcoder.com/practice/4018c0c6d15d473e804656afcbc2c501?tpId=46&tqId=29131&tPage=5&rp=5&ru=/ta/leetcode&qru=/ta/leetcode/question-ranking)

### Problem Statement

You are given an n x n 2D matrix representing an image.

Rotate the image by 90 degrees (clockwise).

Note:
You have to rotate the image in-place, which means you have to modify the input 2D matrix directly. DO NOT allocate another 2D matrix and do the rotation.

## 题解 - 转置 + 对称

```cpp
class Solution {
public:
    void rotate(vector<vector<int> > &matrix) {
        int row = matrix.size();
        int col = matrix[0].size();
        
        for (int i = 0; i < row; ++ i) {
            for (int j = i + 1; j < col; ++ j) {
                swap(matrix[i][j], matrix[j][i]);
            }
        }
        
        for (int i = 0; i < row; ++ i) {
            for (int j = 0; j < (col >> 1); ++ j) {
                swap(matrix[i][j], matrix[i][col - 1 - j]);
            }
        }
    }
};

// 调用 reverse 方法更加简洁
class Solution {
public:
    void rotate(vector<vector<int>>& matrix) {
        int row = matrix.size();
        if (row == 0) return;
        int col = matrix[0].size();
        
        reverse(matrix.begin(), matrix.end());
        
        for (int i = 0; i < row; ++ i) {
            for (int j = i + 1; j < col; ++ j) {
                swap(matrix[i][j], matrix[j][i]);
            }
        }
    }
};
```

### 源码分析

问题可以迁移到逆时针旋转。不管怎样旋转，都离不开两个操作，转置 + 对称，先后顺序以及对称轴的选择决定了是顺时针还是逆时针。

注意 `col / 2` 可以写作 `col >> 1`

### 复杂度分析

时间复杂度是 $$O(n^2)$$, 空间复杂度为 $$O(1)$$.