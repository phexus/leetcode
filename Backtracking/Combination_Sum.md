# Combination Sum

Tags: Array, Backtracking, Medium

## Problem

Given a **set** of candidate numbers (**C**) **(without duplicates)** and a target number (**T**), find all unique combinations in **C** where the candidate numbers sums to **T**.

The **same** repeated number may be chosen from **C** unlimited number of times.

**Note:**

- All numbers (including target) will be positive integers.
- The solution set must not contain duplicate combinations.

For example, given candidate set `[2, 3, 6, 7]` and target `7`, 
A solution set is: 

```
[
  [7],
  [2, 2, 3]
]
```

## Solution - 回溯法

[参考](http://blog.csdn.net/versencoder/article/details/52071930) 

和 combination 相比，这里需要凑出 target ，还是一样可以用 回溯法，一样的模板。不过这里要注意的是，每个元素可以重复取，所以在递归调用的时候，begin 应该和 i 保持一致，由上层的 begin 来控制

`backtracking(candidates, target - candidates[i], i, combList, ans);`

另外，nowcoder 上结果要求是有序的，所以在主函数里可以先排序 `sort(candidates.begin(), candidates.end())` leetcode 不用。

```cpp
class Solution {
public:
    vector<vector<int>> combinationSum(vector<int>& candidates, int target) {
        vector<vector<int>> ans;
        vector<int> combList;
        
        backtracking(candidates, target, 0, combList, ans); // 从数组第一个元素开始
        
        return ans;
    }
    
    void backtracking(vector<int>& candidates, int target, int begin, vector<int>& combList, vector<vector<int>>& ans) {
        if (target < 0) return; // 凑过头了
        if (target == 0) {
            ans.push_back(combList);
            return;
        } else {
            for (int i = begin; i < candidates.size(); ++i) { // 循环试探每个数
                combList.push_back(candidates[i]);
                backtracking(candidates, target - candidates[i], i, combList, ans);
                combList.pop_back();
            }
        }
    }
};
```

### 复杂度

按状态数进行分析，时间复杂度 $$O(n!)$$, 使用了list 保存中间结果，空间复杂度 $$O(n)$$.

感觉这种形式来分析时间复杂度都不太好理解。。