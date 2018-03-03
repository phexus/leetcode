# Search a 2D Matrix II

Tags: Binary Search, Divide  and Conquer, Medium

## Problem

Write an efficient algorithm that searches for a value in an *m* x *n* matrix. This matrix has the following properties:

- Integers in each row are sorted in ascending from left to right.
- Integers in each column are sorted in ascending from top to bottom.

For example,

Consider the following matrix:

```
[
  [1,   4,  7, 11, 15],
  [2,   5,  8, 12, 19],
  [3,   6,  9, 16, 22],
  [10, 13, 14, 17, 24],
  [18, 21, 23, 26, 30]
]
```

Given **target** = `5`, return `true`.

Given **target** = `20`, return `false`.

## Solution - 固定右上角元素查找(或左下)

跟 `剑指 offer ` 的面试题 3 相同，这里有可能是有重复元素的，所以用二分法做并不是一个特别好的选择。采用 Search a 2D Matrix 一题的解法 3 ，固定右上或者左下元素来查找，每次删除一行或一列。

```cpp
class Solution {
public:
    bool searchMatrix(vector<vector<int>>& matrix, int target) {
        if (matrix.empty() || matrix[0].empty()) return false;
        int m = matrix.size(), n = matrix[0].size();
        int i = 0, j = n - 1;
        
        while (i < m && j >= 0) {
            if (matrix[i][j] == target) return true;
            else if (matrix[i][j] > target) --j;
            else ++i;
        }
        return false;
    }
};
```

### 时间复杂度

最差情况，要删除 m 行，n 列，所以时间复杂度为 $$O(m + n)$$