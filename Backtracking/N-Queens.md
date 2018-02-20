# N-Queens

Tags: Backtracking, Hard

## Problem

The *n*-queens puzzle is the problem of placing *n* queens on an *n*×*n* chessboard such that no two queens attack each other.

![img](https://leetcode.com/static/images/problemset/8-queens.png)

Given an integer *n*, return all distinct solutions to the *n*-queens puzzle.

Each solution contains a distinct board configuration of the *n*-queens' placement, where `'Q'` and `'.'` both indicate a queen and an empty space respectively.

For example,
There exist two distinct solutions to the 4-queens puzzle:

```
[
 [".Q..",  // Solution 1
  "...Q",
  "Q...",
  "..Q."],

 ["..Q.",  // Solution 2
  "Q...",
  "...Q",
  ".Q.."]
]
```

## Solution

初次实现时，参考了 `剑指 offer` 的面试题 28 的 相关题目 部分的思路，即将问题转化为对数组的全排列，并进行剪枝处理，判断每一个排列对应的 8 个皇后是否是在同一条对角线上。（因为使用不同的数字初始化的数组，所以任意两个皇后肯定不同列，只用判断是否子啊同意对角线上即可）

下面是我的第一版实现，以 C++ 的效率都给算到了 160 ms，直接击败了 0.24% 的提交。。。

```cpp
class Solution {
public:
    vector<vector<string>> solveNQueens(int n) {
        if (n < 1) return vector<vector<string>>();
        vector<vector<string>> ans;
        
        vector<int> columnIndex(n);
        for (int i = 0; i < n; ++i) columnIndex[i] = i;
        
        vector<int> candidate;
        backtracking(columnIndex, candidate, ans);
        
        return ans;
    }
    
    void backtracking(vector<int>& columnIndex, vector<int>& candidate, vector<vector<string>>& ans) {
        if (candidate.size() > columnIndex.size()) return;
        if (candidate.size() == columnIndex.size()) {
            if (isValid(candidate)) {
                generateToBoard(candidate, ans);
            }
            return;
        } else {
            for (int i = 0; i < columnIndex.size(); ++i) {
                if (find(candidate.begin(), candidate.end(), columnIndex[i]) != candidate.end()) continue;
                candidate.push_back(columnIndex[i]);
                backtracking(columnIndex, candidate, ans);
                candidate.pop_back();
            }
        }
    }
    
    bool isValid(vector<int>& candidate) {
        int n = candidate.size();
        for (int i = 0; i < n; ++i) {
            for (int j = i + 1; j < n; ++j) {
                if (j - i == candidate[j] - candidate[i] || j - i == candidate[i] - candidate[j]) return false;
            }
        }
        return true;
    }
    
    void generateToBoard(vector<int>& candidate, vector<vector<string>>& ans) {
        int n = candidate.size();
        string row(n, '.');
        vector<string> solution(n, row);
        
        for (int i = 0; i < n; ++i) {
            solution[i][candidate[i]] = 'Q';
        }
        
        ans.push_back(solution);
    }
};
```

 入之前所提到的，剪枝处理可以再两个地方进行，在最后添加 ans 的时候再去检查有效性，会重复做很多计算，因为此时的 isValid 是 $$O(n^2)$$ 的复杂度，而如果在试探生成的时候就做检查，很多无效的排列就没有必要生成了，同时此时的 isValid 是 $$O(n)$$ 的复杂度。经测试，经过改动后，下面的代码运行时间为 6 ms

```cpp
class Solution {
public:
    vector<vector<string>> solveNQueens(int n) {
        if (n < 1) return vector<vector<string>>();
        vector<vector<string>> ans;
        
        vector<int> columnIndex(n);
        for (int i = 0; i < n; ++i) columnIndex[i] = i;
        
        vector<int> candidate;
        backtracking(columnIndex, candidate, ans);
        
        return ans;
    }
    
    void backtracking(vector<int>& columnIndex, vector<int>& candidate, vector<vector<string>>& ans) {
        if (candidate.size() > columnIndex.size()) return;
        if (candidate.size() == columnIndex.size()) {
            generateToBoard(candidate, ans);
            return;
        } else {
            for (int i = 0; i < columnIndex.size(); ++i) {
                if (find(candidate.begin(), candidate.end(), columnIndex[i]) != candidate.end()) continue;
                if (isValid(candidate, columnIndex, i)) {
                    candidate.push_back(columnIndex[i]);
                    backtracking(columnIndex, candidate, ans);
                    candidate.pop_back();
                }
            }
        }
    }
    
    bool isValid(vector<int>& candidate, vector<int>& columnIndex, int id) {
        int n = candidate.size();
        for (int i = 0; i < n; ++i) {
            if (n - i == columnIndex[id] - candidate[i] || n - i == candidate[i] - columnIndex[id]) return false;
            
        }
        return true;
    }
    
    void generateToBoard(vector<int>& candidate, vector<vector<string>>& ans) {
        int n = candidate.size();
        string row(n, '.');
        vector<string> solution(n, row);
        
        for (int i = 0; i < n; ++i) {
            solution[i][candidate[i]] = 'Q';
        }
        
        ans.push_back(solution);
    }
};
```

## Solution 2 

https://leetcode.com/problems/n-queens/discuss/19808/Accepted-4ms-c++-solution-use-backtracking-and-bitmask-easy-understand.