# Valid Sudoku

Tags: Hash Table, Medium

## Problem

Determine if a Sudoku is valid, according to: [Sudoku Puzzles - The Rules](http://sudoku.com.au/TheRules.aspx).

The Sudoku board could be partially filled, where empty cells are filled with the character `'.'`.

![img](http://upload.wikimedia.org/wikipedia/commons/thumb/f/ff/Sudoku-by-L2G-20050714.svg/250px-Sudoku-by-L2G-20050714.svg.png)

A partially filled sudoku which is valid.

**Note:**
A valid Sudoku board (partially filled) is not necessarily solvable. Only the filled cells need to be validated.

## Solution

比较基本的判断是否有重复数字的问题。有三点要考虑：

1. 每行不得有重复数字
2. 每列不得有重复数字
3. 每个九宫格不得有重复数字

这里可以每个单独去遍历，如 [参考解法](https://algorithm.yuanbin.me/zh-hans/problem_misc/valid_sudoku.html) 。但其实也是可以共用一个遍历的，

每个最外层 loop 确定一行，一列，一个九宫格。

确定九宫格的做法比较特别，`cubeRow = i / 3 * 3 + j / 3` `cubeCol = i % 3 * 3 + j % 3` 把模运算与数独矩阵的结构联系起来就很好理解了。

判断是否重复，这里用了 unordered_set 的 insert 的返回结果 `pair<iterator, bool>` 的 bool 结果，比较巧妙。

```cpp
class Solution {
public:
    bool isValidSudoku(vector<vector<char>>& board) {
        for (int i = 0; i < 9; ++i) {
            unordered_set<int> rowSet;
            unordered_set<int> colSet;
            unordered_set<int> cubeSet;
            
            for (int j = 0; j < 9; ++j) {
                if (board[i][j] != '.' && !rowSet.insert(board[i][j]).second) return false;
                if (board[j][i] != '.' && !colSet.insert(board[j][i]).second) return false;
                
                int cubeRow = i / 3 * 3 + j / 3, cubeCol = i % 3 * 3 + j % 3; 
                if (board[cubeRow][cubeCol] != '.' && !cubeSet.insert(board[cubeRow][cubeCol]).second) return false;
            }
        }
        return true;
    }
};
```

### 其他解法

下面的两个解法都带有 trick 的性质，不如上面的直观

[解法](https://leetcode.com/problems/valid-sudoku/discuss/15464/My-short-solution-by-C++.-O(n2)) 设置了个 3 个 int 数组，用来存放出现的结果，若为 1 则表示该位置已经出现，则返回 false

```cpp
class Solution
{
public:
    bool isValidSudoku(vector<vector<char> > &board)
    {
        int used1[9][9] = {0}, used2[9][9] = {0}, used3[9][9] = {0};
        
        for(int i = 0; i < board.size(); ++ i)
            for(int j = 0; j < board[i].size(); ++ j)
                if(board[i][j] != '.')
                {
                    int num = board[i][j] - '0' - 1, k = i / 3 * 3 + j / 3;
                    if(used1[i][num] || used2[j][num] || used3[k][num])
                        return false;
                    used1[i][num] = used2[j][num] = used3[k][num] = 1;
                }
        
        return true;
    }
};
```

下面的这个 [解法](https://leetcode.com/problems/valid-sudoku/discuss/15452/C++-very-simple-and-easy-understand.-using-bit-operation) 与上面的思路一样，只不过换成了位运算

```cpp
   bool isValidSudoku(vector<vector<char>>& board) {
    vector<short> col(9, 0);
    vector<short> block(9, 0);
    vector<short> row(9, 0);
    for (int i = 0; i < 9; i++)
     for (int j = 0; j < 9; j++) {
         if (board[i][j] != '.') {
             int idx = 1 << (board[i][j] - '0');
             if (row[i] & idx || col[j] & idx || block[i/3 * 3 + j / 3] & idx)
                return false;
            row[i] |= idx;
            col[j] |= idx;
            block[i/3 * 3 + j/3] |= idx;
         }
     }
     return true;
  }
```

