## Word Search

Tags: Array, Backtracking

## Problem

Given a 2D board and a word, find if the word exists in the grid.

The word can be constructed from letters of sequentially adjacent cell, where "adjacent" cells are those horizontally or vertically neighboring. The same letter cell may not be used more than once.

For example,
Given **board** =

```
[
  ['A','B','C','E'],
  ['S','F','C','S'],
  ['A','D','E','E']
]
```

**word** = `"ABCCED"`, -> returns `true`,

**word** = `"SEE"`, -> returns `true`,

**word** = `"ABCB"`, -> returns `false`.

## Solution 1 - backtracking - isVisited

典型的 DFS 问题，也就是 backtracking，不过由于可选路径有 4 个方向，即 上 下 左 右，而且如果在这次寻找过程中已经用过的点不应该继续去用，所以定义了一个和 board 维度一样的矩阵，用来标识。

原始函数去找第一个字符时，还是需要 两个 for loop 嵌套去遍历着找，很难融合到 backtracking 辅助函数里。

另外就是本题比较坑的地方了，一开始我的实现如下，直接导致下面这个极端 case 超时

```
[["a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a"],["a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a"],["a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a"],["a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a"],["a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a"],["a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a"],["a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a"],["a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a"],["a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a"],["a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a"],["a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a"],["a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a"],["a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a"],["a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a"],["a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a"],["a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a"],["a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a"],["a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a"],["a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a"],["a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a"],["a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a"],["a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a"],["a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a"],["a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a"],["a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a"],["a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a"],["a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a"],["a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a"],["a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a"],["a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","a","b"]]
"baaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaa"
```

### 超时解法

```cpp
class Solution {
public:
    bool exist(vector<vector<char>>& board, string word) {
        if (board.empty() || board[0].empty() || word.empty()) return false;
        
        vector<vector<bool>> isVisited(board.size(), vector<bool>(board[0].size(), false));
        
        for (int i = 0; i < board.size(); ++i) {
            for (int j = 0; j < board[0].size(); ++j) {
                if (board[i][j] == word[0]) {
                    if (dfs(board, word, isVisited, i, j, 0) == true) return true;
                }
            }
        }
        return false;
    }
    
    bool dfs(vector<vector<char>>& board, string& word, vector<vector<bool>>& isVisited, int row, int col, int id) {
        if (row < 0 || row >= board.size() || col < 0 || col >= board[0].size()) return false;
        if (id == word.size() - 1 && !isVisited[row][col] && board[row][col] == word[id]) return true;
        
        if (!isVisited[row][col] && board[row][col] == word[id]) {
            isVisited[row][col] = true;
            
            bool right = dfs(board, word, isVisited, row, col + 1, id + 1);
            bool down = dfs(board, word, isVisited, row + 1, col, id + 1);
            bool left = dfs(board, word, isVisited, row, col - 1, id + 1);
            bool up = dfs(board, word, isVisited, row - 1, col, id + 1);
            
            isVisited[row][col] = false;
            return right || down || left || up;
        }
        return false;
    }
};
```

经过和网上其他解法比对，有两种改法：

1. 将 isVisited 不要同意置为 false，而是只有全部不存在时才置位

```cpp
            bool right = dfs(board, word, isVisited, row, col + 1, id + 1);
            bool down = dfs(board, word, isVisited, row + 1, col, id + 1);
            bool left = dfs(board, word, isVisited, row, col - 1, id + 1);
            bool up = dfs(board, word, isVisited, row - 1, col, id + 1);
            
            isVisited[row][col] = right || down || left || up;
```

这样一来，在已经找到一条路径层层返回的过程中由于选中的那条路径的点都为 true，遍历其他方向时就能够早早退出，大量节省运算量，能降到 33 ms

2. 不要依次枚举 四个方向 bool 量，而是直接参与 或运算，由于或运算的性质，只要前面为 true，后面就不用再算了。能降到 24 ms

```cpp
            bool result = dfs(board, word, isVisited, row, col + 1, id + 1) ||
                dfs(board, word, isVisited, row + 1, col, id + 1) ||
                dfs(board, word, isVisited, row, col - 1, id + 1) ||
                dfs(board, word, isVisited, row - 1, col, id + 1);
            
            isVisited[row][col] = false;
            return result;
```

按理说上面的两个方法结合，应该能收敛得更快，然而事与愿违，两者结合起来为 27 ms

### 优化后的解法

```cpp
class Solution {
public:
    bool exist(vector<vector<char>>& board, string word) {
        if (board.empty() || board[0].empty() || word.empty()) return false;
        
        vector<vector<bool>> isVisited(board.size(), vector<bool>(board[0].size(), false));
        
        for (int i = 0; i < board.size(); ++i) {
            for (int j = 0; j < board[0].size(); ++j) {
                if (board[i][j] == word[0]) {
                    if (dfs(board, word, isVisited, i, j, 0) == true) return true;
                }
            }
        }
        return false;
    }
    
    bool dfs(vector<vector<char>>& board, string& word, vector<vector<bool>>& isVisited, int row, int col, int id) {
        if (row < 0 || row >= board.size() || col < 0 || col >= board[0].size()) return false;
        if (id == word.size() - 1 && !isVisited[row][col] && board[row][col] == word[id]) return true;
        
        if (!isVisited[row][col] && board[row][col] == word[id]) {
            isVisited[row][col] = true;
            
            bool result = dfs(board, word, isVisited, row, col + 1, id + 1) ||
                dfs(board, word, isVisited, row + 1, col, id + 1) ||
                dfs(board, word, isVisited, row, col - 1, id + 1) ||
                dfs(board, word, isVisited, row - 1, col, id + 1);
            
            isVisited[row][col] = false;  // 也可以置为 result
            return result;
        }
        return false;  // 其他情况返回 false
    }
};
```

dfs 的辅助函数的边界情况的判断可以重构下，但是我觉得没有必要，上面这样的写法虽然啰嗦但是胜在清晰。

别忘了 最后的其他情况要返回 fasle，否则进到了这个分支就会没有返回值而报错。

重构后的写法：

```cpp
class Solution {
public:
    bool exist(vector<vector<char>>& board, string word) {
        if (board.empty() || board[0].empty() || word.empty()) return false;
        
        vector<vector<bool>> isVisited(board.size(), vector<bool>(board[0].size(), false));
        
        for (int i = 0; i < board.size(); ++i) {
            for (int j = 0; j < board[0].size(); ++j) {
                if (board[i][j] == word[0]) {
                    if (dfs(board, word, isVisited, i, j, 0) == true) return true;
                }
            }
        }
        return false;
    }
    
    bool dfs(vector<vector<char>>& board, string& word, vector<vector<bool>>& isVisited, int row, int col, int id) {
        if (id == word.size()) return true; // 多判断一次，简化逻辑
        if (row < 0 || row >= board.size() || col < 0 || col >= board[0].size() || isVisited[row][col] || board[row][col] != word[id]) return false;
        
        isVisited[row][col] = true;
            
        bool result = dfs(board, word, isVisited, row, col + 1, id + 1) ||
                      dfs(board, word, isVisited, row + 1, col, id + 1) ||
                      dfs(board, word, isVisited, row, col - 1, id + 1) ||
                      dfs(board, word, isVisited, row - 1, col, id + 1);
            
        isVisited[row][col] = result;
        return result;
    }
};
```

### 复杂度

[DFS](https://algorithm.yuanbin.me/zh-hans/GLOSSARY.html#dfs) 最坏情况下遍历所有坐标点，二重 for 循环最坏情况下也全部执行完，故时间复杂度最差情况下为 $$O(m^2n^2)$$, 使用了`isVisited`矩阵，空间复杂度为 $$O(mn)$$, 当然这个可以优化到 $$O(1)$$.(原地更改原 board 数组字符内容)。



## Solution 2 - backtracking - inplace

上面的解法定义了 isVisited 二维矩阵，也可以 inplace 的替换，自己定义一个特殊字符，下面的例子用了 '*'

```cpp
class Solution {
public:
    bool exist(vector<vector<char>>& board, string word) {
        if (board.empty() || board[0].empty() || word.empty()) return false;
        
        for (int i = 0; i < board.size(); ++i) {
            for (int j = 0; j < board[0].size(); ++j) {
                if (board[i][j] == word[0]) {
                    if (dfs(board, word, i, j, 0) == true) return true;
                }
            }
        }
        return false;
    }
    
    bool dfs(vector<vector<char>>& board, string& word, int row, int col, int id) {
        if (id == word.size()) return true;
        if (row < 0 || row >= board.size() || col < 0 || col >= board[0].size() || board[row][col] == '*' || board[row][col] != word[id]) return false;
        
        char tmp = board[row][col];
        board[row][col] = '*'; // 用 * 来填充
            
        bool result = dfs(board, word, row, col + 1, id + 1) ||
                      dfs(board, word, row + 1, col, id + 1) ||
                      dfs(board, word, row, col - 1, id + 1) ||
                      dfs(board, word, row - 1, col, id + 1);
            
        board[row][col] = tmp; // 换回来
        return result;
    }
};
```

还有一种[解法](https://leetcode.com/problems/word-search/discuss/27658/Accepted-very-short-Java-solution.-No-additional-space.) 在 java 下是可以的，但是 C ++ 下不能用 256,512，得要用 255 511 才行，有点奇技淫巧的感觉

目的就是把 ascll 字符经过变换变成非 ascll 字符，两次异或又可以恢复自身。

```cpp
class Solution {
public:
    bool exist(vector<vector<char>>& board, string word) {
        if (board.empty() || board[0].empty() || word.empty()) return false;
        
        for (int i = 0; i < board.size(); ++i) {
            for (int j = 0; j < board[0].size(); ++j) {
                if (board[i][j] == word[0]) {
                    if (dfs(board, word, i, j, 0) == true) return true;
                }
            }
        }
        return false;
    }
    
    bool dfs(vector<vector<char>>& board, string& word, int row, int col, int id) {
        if (id == word.size()) return true;
        if (row < 0 || row >= board.size() || col < 0 || col >= board[0].size() || board[row][col] != word[id]) return false;
        
        board[row][col] ^= 255;
            
        bool result = dfs(board, word, row, col + 1, id + 1) ||
                      dfs(board, word, row + 1, col, id + 1) ||
                      dfs(board, word, row, col - 1, id + 1) ||
                      dfs(board, word, row - 1, col, id + 1);
            
        board[row][col] ^= 255;
        return result;
    }
};
```



 