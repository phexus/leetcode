# Search a 2D Matrix

Tags: Array, Medium

## Question

- leetcode: [Search a 2D Matrix](https://leetcode.com/problems/search-a-2d-matrix/description/)
- nowcoder: [Search a 2D Matrix](https://www.nowcoder.com/practice/5145394607ea4c5f8b25755718bfddba?tpId=46&tqId=29104&tPage=5&rp=5&ru=/ta/leetcode&qru=/ta/leetcode/question-ranking)

### Problem Statement

Write an efficient algorithm that searches for a value in an m x n matrix. This matrix has the following properties:

Integers in each row are sorted from left to right.
The first integer of each row is greater than the last integer of the previous row.
For example,

Consider the following matrix:

[
  [1,   3,  5,  7],
  [10, 11, 16, 20],
  [23, 30, 34, 50]
]
Given target = 3, return true.

## 解法1

我的解法，先遍历第一列，找到这个元素所在的行，再遍历这一行，查找该元素。

```cpp
class Solution {
public:
    bool searchMatrix(vector<vector<int> > &matrix, int target) {
        int row = matrix.size();
        if (row == 0) return false;
        int col = matrix[0].size();
        if (col == 0) return false;
        
        if (matrix[0][0] > target) return false;
        
        int i = 0;
        for ( ; i < row; ++ i) {
            if (matrix[i][0] > target) break;
        }
        -- i;
        
        for (int j = 0; j < col; ++ j) {
            if (matrix[i][j] == target) return true;
        }
        
        return false;
    }
};


//每次的查找可优化为二分查找
class Solution {
public:
    bool searchMatrix(vector<vector<int> > &matrix, int target) {
        int row = matrix.size();
        if (row == 0) return false;
        int col = matrix[0].size();
        if (col == 0) return false;
        
        if (matrix[0][0] > target) return false;
        
        int i = 0, j = row - 1;
        while (i <= j) {
            int middle = (i + j) >> 1;
            if (matrix[middle][0] == target) return true;
            else if (matrix[middle][0] > target) j = middle - 1;
            else i = middle + 1;
        }
        
        if (i == 0) return false;
        
        int rowId = i - 1;
        
        i = 0; j < col - 1;
        while (i <= j) {
            int middle = (i + j) >> 1;
            if (matrix[rowId][middle] == target) return true;
            else if (matrix[rowId][middle] > target) j = middle - 1;
            else i = middle + 1;
        }
        
        return false;
    }
};
```

### 源码分析

注意异常处理的写法，要处理行向量、列向量为空的情况！！！

###　复杂度分析

时间复杂度是 $$O(row + col)$$, 空间复杂度为 $$O(1)$$.

## 解法2 - 把二维矩阵看成是一维数组 + 二分法

```cpp
class Solution {
public:
    bool searchMatrix(vector<vector<int> > &matrix, int target) {
        int n = matrix.size();
        int m = matrix[0].size();
        int l = 0, r = m * n - 1;
        while (l != r){
            int mid = (l + r - 1) >> 1;
            if (matrix[mid / m][mid % m] < target)
                l = mid + 1;
            else 
                r = mid;
        }
        return matrix[r / m][r % m] == target;
    }
};

// 另一种写法
class Solution {
public:
    bool searchMatrix(vector<vector<int> > &matrix, int target) {
        int row = matrix.size();
        if (row == 0) return false;
        int col = matrix[0].size();
        if (col == 0) return false;
        
        int start = 0;
        int end = row * col - 1;
        
        while (start <= end) {
            int middle = (start + end) >> 1;
            if (matrix[middle / col][middle % col] == target) return true;
            else if (matrix[middle / col][middle % col] < target) start = middle + 1;
            else end = middle - 1;
        }
        
        return false;
    }
};
```

## 解法3 - 固定右上角元素查找

首先选取右上角数字，如果该数字等于要查找的数字，查找过程结束；如果该数字大于要查找的数字，去掉此数字所在列；如果该数字小于要查找的数字，则去掉该数字所在行。重复上述过程直到找到要查找的数字，或者查找范围为空。

```cpp
    class Solution {
    public:
        bool searchMatrix(vector<vector<int> > &matrix, int target) {
            int row = matrix.size();
            if (row == 0) return false;
            int col = matrix[0].size();
            if (col == 0) return false;
            
            int i = 0, j = col - 1;
            while (i < row && j >= 0) {
                if (matrix[i][j] == target) return true;
                else if (matrix[i][j] < target) ++ i;
                else -- j;
            }

            return false;
        }
    };
```

