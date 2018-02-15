# Subsets II

Tags: Array, Backtracking, Bit Manipulation, Medium

## Problem

Given a set of **distinct** integers, *nums*, return all possible subsets (the power set).

**Note:** The solution set must not contain duplicate subsets.

For example,
If **nums** = `[1,2,3]`, a solution is:

```
[
  [3],
  [1],
  [2],
  [1,2,3],
  [1,3],
  [2,3],
  [1,2],
  []
]
```

## Solution

这道题之于 Subsets 就如同 combination_sum_II 之于 combination——sum。

一样的套路，在 Subsets 的基础上做一样的重复检测就行

这种操作被称为对回溯函数进行一定的剪枝。剪枝通常可以从两个地方出发，一是在返回结果`result.add`之前进行剪枝，另一个则是在`list.add`处剪枝，具体使用哪一种需要视情况而定，哪种简单就选谁。

```cpp
class Solution {
public:
    vector<vector<int>> subsetsWithDup(vector<int>& nums) {
        vector<vector<int>> ans;
        vector<int> subset;
    
        sort(nums.begin(), nums.end()); // 必须排序！！！
        for (int k = 0; k <= nums.size(); ++k) {
            backtracking(nums, k, 0, subset, ans);
        }
        
        return ans;
    }
    
    void backtracking(vector<int>& nums, int k, int begin, vector<int>& subset, vector<vector<int>>& ans) {
        if (k < 0) return;
        if (k == 0) {
            ans.push_back(subset);
            return;
        } else {
            for (int i = begin; i < nums.size(); ++i) {
                if (i == begin || nums[i] != nums[i - 1]) {
                    subset.push_back(nums[i]);
                    backtracking(nums, k - 1, i + 1, subset, ans);
                    subset.pop_back();
                }
            }
        }
    }
};
```

不过和 leetcode 的结果输出不太一致，当然可以再对结果做一次 sort，但是想想上一道题的另一种写法就会发现问题迎刃而解，不要按找 k 来输出，而是这季节添加到结果，这样就是 DFS 的结果，也自然是字典序的。

```cpp
class Solution {
public:
    vector<vector<int>> subsetsWithDup(vector<int>& nums) {
        vector<vector<int>> ans;
        vector<int> subset;
    
        sort(nums.begin(), nums.end());
        backtracking(nums, 0, subset, ans);
        
        return ans;
    }
    
    void backtracking(vector<int>& nums, int begin, vector<int>& subset, vector<vector<int>>& ans) {
        ans.push_back(subset);
        for (int i = begin; i < nums.size(); ++i) {
            if (i == begin || nums[i] != nums[i - 1]) {
                subset.push_back(nums[i]);
                backtracking(nums, i + 1, subset, ans);
                subset.pop_back();
            }
        }
    }
};
```

### 复杂度

和前一道题差不多，最坏情况下时间复杂度为 $$2^n$$. 空间复杂度为 $$O(n)$$.

