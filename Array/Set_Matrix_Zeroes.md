# Set Matrix Zeroes

Tags: Array, Medium

## Problem

Given a *m* x *n* matrix, if an element is 0, set its entire row and column to 0. Do it in place.

[click to show follow up.](https://leetcode.com/problems/set-matrix-zeroes/description/#)

Follow up:

Did you use extra space?
A straight forward solution using O(*m**n*) space is probably a bad idea.
A simple improvement uses O(*m* + *n*) space, but still not the best solution.
Could you devise a constant space solution?

## Solution

由于题目要求用常数级别的空间，但是又得记录所有 0 所在的行和列的信息，所以得尝试用已有空间作记录。

下面的解法用了第一行和第一列的空间做记录。无论这一行有多少个 0 ，只用记录一次即可，这也是能用一行一列来记录的前提。另外，如果第一行第一列本身就已经有 0 了的话，它们本身也是要被清零的，所以需要设置两个 bool 量记录。

```cpp
class Solution {
public:
    void setZeroes(vector<vector<int>>& matrix) {
        int rows = matrix.size();
        int cols = matrix[0].size();
        bool row0 = false, col0 = false;
      
        //判断第一行和第一列是否有零，防止被覆盖
        for (int i = 0; i < cols; ++i) {
            if (matrix[0][i] == 0) {
                row0 = true;
                break;
            }
        }
        for (int i = 0; i < rows; ++i) {
            if (matrix[i][0] == 0) {
                col0 = true;
                break;
            }
        }
        
      //遍历矩阵，用第一行和第一列记录0的位置
        for (int i = 1; i < rows; ++i) {
            for (int j = 1; j < cols; ++j) {
                if (matrix[i][j] == 0) {
                    matrix[i][0] = 0;
                    matrix[0][j] = 0;
                }
            }
        }
        
      // 根据记录清零
        for (int i = 1; i < rows; ++i) {
            for (int j = 1; j < cols; ++j) {
                if (matrix[i][0] == 0 || matrix[0][j] == 0) matrix[i][j] = 0;
            }
        }
        
      // 单独处理第一行和第一列
        if (row0) {
            for (int i = 0; i < cols; ++i) matrix[0][i] = 0;
        }
        if (col0) {
            for (int i = 0; i < rows; ++i) matrix[i][0] = 0;
        }
    }
};
```

### 精简写法

上面的解法有些长，可以通过合并一些循环，做一些代码结构的调整，不过上面的写法长归长，但是跪在清晰呀，还是推荐的。

这种解法参考自 [leetcode discuss](https://leetcode.com/problems/set-matrix-zeroes/discuss/26014/Any-shorter-O(1)-space-solution) 。

首先，节省掉了一个 bool 量，因为 第一行的情况是可以通过设置第一列的第一个元素得知的，一旦第一行已经有 0，matrix[0][0] 就会被设置为 0

另外补零时，从下往上做，确保最后处理第一行，同时在外层 loop 把 第一列补零的情况一起做了，非常优雅

```cpp
class Solution {
public:
    void setZeroes(vector<vector<int> > &matrix) {
        int rows = matrix.size();
        int cols = matrix[0].size();
        bool col0 = false;
        
      // 要从 第一行 开始遍历
        for (int i = 0; i < rows; ++i) {
            if (matrix[i][0] == 0) col0 = true;
            for (int j = 1; j < cols; ++j) {
                if (matrix[i][j] == 0) {
                    matrix[0][j] = matrix[i][0] = 0;
                }
            }
        }
        
      // 填零， bottom-up 是为了最后处理 第一行
        for (int i = rows - 1; i >= 0; --i) {
            for (int j = cols - 1; j >= 1; --j) {
                if (matrix[0][j] == 0 || matrix[i][0] == 0) {
                    matrix[i][j] = 0;
                }
            }
            if (col0) matrix[i][0] = 0;
        }
    }
};
```

### 复杂度

时间复杂度 $$O(mn)$$

空间复杂度 $$O(1)$$