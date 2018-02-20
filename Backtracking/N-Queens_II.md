# N-Queens II

Tags: Backtracking, Hard

## Problem

Follow up for N-Queens problem.

Now, instead outputting board configurations, return the total number of distinct solutions.

![img](https://leetcode.com/static/images/problemset/8-queens.png)

## Solution



```cpp
class Solution {
public:
    int totalNQueens(int n) {
        if (n < 1) return 0;
        
        int ans = 0;
        vector<int> columnIndex(n);
        for (int i = 0; i < n; ++i) columnIndex[i] = i;
        
        vector<int> candidate;
        backtracking(columnIndex, candidate, ans);
        return ans;
    }
    
    void backtracking(vector<int>& columnIndex, vector<int>& candidate, int& ans) {
        if (candidate.size() > columnIndex.size()) return;
        if (candidate.size() == columnIndex.size()) {
            ++ans;
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
};
```

