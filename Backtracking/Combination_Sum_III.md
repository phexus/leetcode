# Combination Sum III

Tags: Array, Backtracking, Medium

## Problem

Find all possible combinations of **k** numbers that add up to a number **n**, given that only numbers from 1 to 9 can be used and each combination should be a unique set of numbers.

**Example 1:**

Input: **k** = 3, **n** = 7

Output:

```
[[1,2,4]]

```

**Example 2:**

Input: **k** = 3, **n** = 9

Output:

```
[[1,2,6], [1,3,5], [2,3,4]]
```
## Solution

一样的套路，理解了 backtracking 的原理后，套用模板即可，不过小心边界条件的处理

```cpp
class Solution {
public:
    vector<vector<int>> combinationSum3(int k, int n) {
        vector<vector<int>> ans;
        vector<int> combList;
        
        backtracking(k, n, 1, combList, ans);
        return ans;
    }
    
    void backtracking(int k, int target, int begin, vector<int>& combList, vector<vector<int>>& ans) {
        if (target < 0) return;
        if (k < 0) return;
        if (k == 0) {
            if (target == 0) ans.push_back(combList);
        } else {
            for (int i = begin; i <= 9; ++i) {
                combList.push_back(i);
                backtracking(k - 1, target - i, i + 1, combList, ans);
                combList.pop_back();
            }
        }
    }
};
```

