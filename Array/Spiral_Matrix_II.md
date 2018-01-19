# Spiral Matrix II

Tags: Array, Medium

## Question

Given an integer n, generate a square matrix filled with elements from 1 to n2 in spiral order.

For example,
Given n = 3,

You should return the following matrix:
[
 [ 1, 2, 3 ],
 [ 8, 9, 4 ],
 [ 7, 6, 5 ]
]

## 题解1 - 一圈圈从外向内生成 - 计数器

```cpp
class Solution {
public:
    vector<vector<int> > generateMatrix(int n) {
        vector<vector<int>> result(n, vector<int>(n));
        
        int endVal = n * n;
        int rowStart = 0;
        int rowEnd = n - 1;
        int colStart = 0;
        int colEnd = n - 1;
        
        int count = 1;
        while (count <= endVal) {
            for (int i = colStart; i <= colEnd; ++ i) {
                result[rowStart][i] = count ++;
            }
            ++ rowStart;
            for (int i = rowStart; i <= rowEnd; ++ i) {
                result[i][colEnd] = count ++;
            }
            -- colEnd;
            for (int i = colEnd; i >= colStart; -- i) {
                result[rowEnd][i] = count ++;
            }
            -- rowEnd;
            for (int i = rowEnd; i >= rowStart; -- i) {
                result[i][colStart] = count ++;
            }
            ++ colStart;
        }
        
        return result;
    }
};
```

### 更简洁的写法

通过计算多少轮来控制循环次数，这里的 `round < n / 2 + n % 2` 非常巧妙！

```cpp
class Solution {
public:
    vector<vector<int>> generateMatrix(int n) {
        vector<vector<int>> result(n, vector<int>(n));
        
        int val = 1;
        for (int round = 0; round < n / 2 + n % 2; ++ round) {
            for (int i = round; i < n - round; ++ i) result[round][i] = val ++;
            for (int i = round + 1; i < n - round; ++ i) result[i][n - round - 1] = val ++;
            for (int i = n - round - 2; i >= round; -- i) result[n - round - 1][i] = val ++;
            for (int i = n - round - 2; i >= round + 1; -- i) result[i][round] = val ++;
        }

        return result;
    }
};
```