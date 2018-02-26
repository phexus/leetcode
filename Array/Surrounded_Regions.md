# Surrounded Regions

Tags: Depth-first Search, Breadth-first Search, Union Find

## Problem

Given a 2D board containing `'X'` and `'O'` (the **letter** O), capture all regions surrounded by `'X'`.

A region is captured by flipping all `'O'`s into `'X'`s in that surrounded region.

For example,

```
X X X X
X O O X
X X O X
X O X X
```

After running your function, the board should be:

```
X X X X
X X X X
X X X X
X O X X
```

## Solution 1 - DFS

[参考](https://leetcode.com/problems/surrounded-regions/discuss/41612/A-really-simple-and-readable-C++-solutiononly-cost-12ms) 本道题有点像围棋，做法就是遍历四条边上的 O，并深度遍历与其相连的 O，现将这些 O 变为特殊字符 *。

之后在遍历这个矩阵，遇见 O 就变为 ×，遇见 * 就变为 O

但是貌似这种做法有点问题？而且这部分 i 的判断条件为 1   `if (i > 1) dfs(board, i - 1, j);` ，虽然我改成 0 也能过，但是看 leetcode 下面的评论说是 test case 太弱了，DFS 遍历的太深，导致 stackOverflow，应该用 BFS 或 Union Find ？ 而且为何这种 DFS 不会死循环？之后再详细研究。 

```cpp
class Solution {
public:
    void solve(vector<vector<char>>& board) {
        if (board.empty() || board[0].empty()) return;
        
        int m = board.size(), n = board[0].size();
        
        for (int j = 0; j < n; ++j) {
            dfs(board, 0, j);
            dfs(board, m - 1, j);
        }
        for (int i = 0; i < m; ++i) {
            dfs(board, i, 0);
            dfs(board, i, n - 1);
        }
        
        for (int i = 0; i < m; ++i) {
            for (int j = 0; j < n; ++j) {
                if (board[i][j] == 'O') board[i][j] = 'X';
                else if (board[i][j] == '*') board[i][j] = 'O';
            }
        }
    }
    
    void dfs(vector<vector<char>>& board, int i, int j) {
        if (board[i][j] == 'O') {
            board[i][j] = '*';
            
            if (i > 1) dfs(board, i - 1, j); // 这里比较奇怪？ 写成 0 也可以
            if (j > 1) dfs(board, i, j - 1);
            if (i < board.size() - 1) dfs(board, i + 1, j);
            if (j < board[0].size() - 1) dfs(board, i, j + 1);
        }
    }
};
```

## Solution 2 - BFS 



## Solution 3 - Union Find

