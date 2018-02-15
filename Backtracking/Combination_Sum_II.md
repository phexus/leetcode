# Combination Sum II

Tags: Array, Backtracking, Easy

## Problem

Given a collection of candidate numbers (**C**) and a target number (**T**), find all unique combinations in **C** where the candidate numbers sums to **T**.

Each number in **C** may only be used **once** in the combination.

**Note:**

- All numbers (including target) will be positive integers.
- The solution set must not contain duplicate combinations.

For example, given candidate set `[10, 1, 2, 7, 6, 1, 5]` and target `8`, 
A solution set is: 

```
[
  [1, 7],
  [1, 2, 5],
  [2, 6],
  [1, 1, 6]
]
```

## Solution

下面这种去重的做法要保证传入的初始序列就是有序的

但第一次遍历到某一个元素的时候，就已经能保证把包含这个元素的所有情况考虑到了，因为假如有 3 个元素重复，那么取 1 个， 取 2 个， 取 3 个元素的情况都已经覆盖到了，所以每当一个元素与之前的元素相同时就可以跳过了。

`if (i == begin  || candidates[i] != candidates[i - 1])` i == begin 这种情况是允许的，保证了取多个相同元素的排列不会被错误筛除，同时也保证了 `candidates[i - 1])` 不会越界。

```cpp
class Solution {
public:
    vector<vector<int>> combinationSum2(vector<int>& candidates, int target) {
        vector<vector<int>> ans;
        vector<int> combList;
        
        sort(candidates.begin(), candidates.end()); // 必须要排序，为了后面处理重复结果
        backtracking(candidates, target, 0, combList, ans);
        return ans;
    }
    
    void backtracking(vector<int>& candidates, int target, int begin, vector<int>& combList, vector<vector<int>>& ans) {
        if (target < 0) return;
        if (target == 0) {
            ans.push_back(combList);
            return;
        } else {
            for (int i = begin; i < candidates.size(); ++i) {
                if (i == begin  || candidates[i] != candidates[i - 1]) {
                    combList.push_back(candidates[i]);
                    backtracking(candidates, target - candidates[i], i + 1, combList, ans);
                    combList.pop_back();
                }
            }
        }
    }
};
```

### 复杂度

不能多次取重复的元素，时间复杂度 $$O(n)$$

空间复杂度 $$O(n)$$