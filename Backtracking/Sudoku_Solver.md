# Sudoku Solver

Tags: Hash Table, Backtracking

## Problem

Write a program to solve a Sudoku puzzle by filling the empty cells.

Empty cells are indicated by the character `'.'`.

You may assume that there will be only one unique solution.

![img](http://upload.wikimedia.org/wikipedia/commons/thumb/f/ff/Sudoku-by-L2G-20050714.svg/250px-Sudoku-by-L2G-20050714.svg.png)

A sudoku puzzle...

![img](http://upload.wikimedia.org/wikipedia/commons/thumb/3/31/Sudoku-by-L2G-20050714_solution.svg/250px-Sudoku-by-L2G-20050714_solution.svg.png)

...and its solution numbers marked in red.

## Solution 1

[参考解法](https://leetcode.com/problems/sudoku-solver/discuss/15752/Straight-Forward-Java-Solution-Using-Backtracking) 

注意这里的 cube 的 row 和 col 的取值，和 valid sudoku 一题不同，这里变量是 i，row 和 col 是用来确定到底在哪一个方块里的。

```cpp
            int cubeRow = row / 3 * 3 + i / 3;
            int cubeCol = col / 3 * 3 + i % 3;
```

几个 return 的地方一定要小心处理。

```cpp
class Solution {
public:
    void solveSudoku(vector<vector<char>>& board) {
        if (board.empty() || board[0].empty()) return;
        backtracking(board);
    }
    
    bool backtracking(vector<vector<char>>& board) {
        int row = board.size();
        int col = board[0].size();
        for (int i = 0; i < row; ++i) {
            for (int j = 0; j < col; ++j) {
                if (board[i][j] != '.') continue;
                
                for (char c = '1'; c <= '9'; ++c) {
                    if (isValid(board, i, j, c)) {
                        board[i][j] = c;
                        
                        if (backtracking(board)) {
                            return true;
                        } else {
                            board[i][j] = '.';
                        }
                    }
                }
                return false; // 这里是异常分支，正常逻辑不会走到这里的，所以要返回 fasle
            }
        }
        return true;
    }
    
    bool isValid(vector<vector<char>>& board, int row, int col, char c) {
        for (int i = 0; i < board.size(); ++i) {
            int cubeRow = row / 3 * 3 + i / 3;
            int cubeCol = col / 3 * 3 + i % 3;
            if (board[row][i] == c || board[i][col] == c || board[cubeRow][cubeCol] == c) return false;
        }
        return true;
    }
};
```

### 优化

[参考](http://blog.csdn.net/zyl26/article/details/26680001)

没有必要每次都从 0, 0 开始递归，只需从当前方格的右侧方格开始递归即可。若当前方格已经是该行的最后一个方格，则需从下一行的第一个方格开始递归。从之前的 18 ms 降到了 13 ms。

> 这里有个小 trick，内层 for loop 也改写成了从 c 开始，当改行遍历完后需要从下一行第 0 个开始，所以在 外层 for loop 做了 c 的reset操作，非常巧妙。

```cpp
class Solution {
public:
    void solveSudoku(vector<vector<char>>& board) {
        if (board.empty() || board[0].empty()) return;
        backtracking(board, 0, 0);
    }
    
    bool backtracking(vector<vector<char>>& board, int r, int c) {
        int row = board.size();
        int col = board[0].size();
        for (int i = r; i < row; ++i, c = 0) { // must reset c！！！
            for (int j = c; j < col; ++j) {
                if (board[i][j] != '.') continue;
                
                for (char c = '1'; c <= '9'; ++c) {
                    if (isValid(board, i, j, c)) {
                        board[i][j] = c;
                        
                        if (backtracking(board, i, j + 1)) {
                            return true;
                        } else {
                            board[i][j] = '.';
                        }
                    }
                }
                return false;
            }
        }
        return true;
    }
    
    bool isValid(vector<vector<char>>& board, int row, int col, char c) {
        for (int i = 0; i < board.size(); ++i) {
            int cubeRow = row / 3 * 3 + i / 3;
            int cubeCol = col / 3 * 3 + i % 3;
            if (board[row][i] == c || board[i][col] == c || board[cubeRow][cubeCol] == c) return false;
        }
        return true;
    }
};
```

### 进一步优化 - 7ms

这里创建了 3 个向量用来表示当前数字是否在该行、该列、该方形内有效，舍弃了 isValid 函数，节省了大量重复计算。cube 的编号 0 - 8 由 i / 3 * 3 + j / 3 取得。

```cpp
class Solution {
public:
    vector<vector<bool>> rowValid;
    vector<vector<bool>> colValid;
    vector<vector<bool>> cubeValid;
    
    void solveSudoku(vector<vector<char>>& board) {
        if (board.empty() || board[0].empty()) return;
        
        vector<bool> b(9, true);
        rowValid.assign(9, b);
        colValid.assign(9, b);
        cubeValid.assign(9, b);
        for (int i = 0; i < 9; ++i) {
            for (int j = 0; j < 9; ++j) {
                if (board[i][j] != '.') {
                    int num = board[i][j] - '1';
                    rowValid[i][num] = false;
                    colValid[j][num] = false;
                    
                    int cubeId = i / 3 * 3 + j / 3;
                    cubeValid[cubeId][num] = false;
                }
            }
        }
        
        backtracking(board, 0, 0);
    }
    
    bool backtracking(vector<vector<char>>& board, int r, int c) {
        int row = board.size();
        int col = board[0].size();
        for (int i = r; i < row; ++i, c = 0) {
            for (int j = c; j < col; ++j) {
                if (board[i][j] != '.') continue;
                
                for (int k = 0; k < 9; ++k) {
                    if (rowValid[i][k] && colValid[j][k] && cubeValid[i / 3 * 3 + j / 3][k]) {
                        board[i][j] = '1' + k;
                        rowValid[i][k] = false;
                        colValid[j][k] = false;
                        cubeValid[i / 3 * 3 + j / 3][k] = false;
                        
                        if (backtracking(board, i, j + 1)) {
                            return true;
                        } else {
                            board[i][j] = '.';
                            rowValid[i][k] = true;
                            colValid[j][k] = true;
                            cubeValid[i / 3 * 3 + j / 3][k] = true;
                        }
                    }
                }
                return false;
            }
        }
        return true;
    }
};
```

## 其他

其他解法参见 [1](https://leetcode.com/problems/sudoku-solver/discuss/15748/Sharing-my-2ms-C++-solution-with-comments-and-explanations.) [2](https://leetcode.com/problems/sudoku-solver/discuss/15806/yet-another-0ms-c-solution)