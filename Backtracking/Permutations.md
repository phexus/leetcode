# Permutations

Tags: Backtracking

## Problem

Given a collection of **distinct** numbers, return all possible permutations.

For example,
`[1,2,3]` have the following permutations:

```
[
  [1,2,3],
  [1,3,2],
  [2,1,3],
  [2,3,1],
  [3,1,2],
  [3,2,1]
]
```

## Solution 1 - swap 回溯

这道题限制了数字是不重复的，所以和 `剑指 offer` 一书的 面试题28（另外说明下剑指offer 坑的地方，题目没有说明是否要去重，但提供的解答却是不考虑重复问题的，也不说明白）一样了。

通过分治的思想，将 nums 分成两部分，一部分是第一个数字，另一部分是剩下的所有数字。

拿第一个字符和后面所有可能的字符交换（这里由于数字不重复，所以是后面所有的数字），每交换一个，固定好该数字，接着去看后面部分的排列，以此类推，是一个典型的递归问题。

由于辅助函数传递的是引用，所以不要忘了交换完后，再交换回来恢复原先的顺序。

[这幅图挺详细的](https://uploadfiles.nowcoder.net/images/20170705/7578108_1499250116235_8F032F665EBB2978C26C4051D5B89E90)

> 切记！这样即使输入是排序的，输出也不是字典序
>
> ```
> input [1,2,3]
> output [[1,2,3],[1,3,2],[2,1,3],[2,3,1],[3,2,1],[3,1,2]]
> ```

```cpp
class Solution {
public:
    vector<vector<int>> permute(vector<int>& nums) {
        if (nums.empty()) return vector<vector<int>> ();
        
        vector<vector<int>> ans;
        permute(nums, 0, ans);
        //sort(ans.begin(), ans.end()); // 题目没要求字典序，可以不排序
        return ans;
    }
    
    void permute(vector<int>& nums, int id, vector<vector<int>>& ans) {
        if (id == nums.size() - 1) {
            ans.push_back(nums);
        } else {
            for (int i = id; i < nums.size(); ++i) {
                swap(nums[i], nums[id]);
                permute(nums, id + 1, ans);
                swap(nums[i], nums[id]);
            }
        }
    }
};
```

## Solution 2 - Backtracking

上面的做法是剑指 offer 提供的解法，其实还可以用之前 subsets 系列的模板来做。

由于是全排列，所以 k 默认就是 nums.size() ,但是要进行剪枝。题目已经明确了数字不重复，但是选过的元素不能再出现，所以这里用了标准库 find 来去重。其实也可以把存储容器直接换成 hashset，用空间来换时间。

使用回溯法解题的**关键在于如何确定正确解及排除不符条件的解(剪枝)**。

```cpp
class Solution {
public:
    vector<vector<int>> permute(vector<int>& nums) {
        vector<vector<int>> ans;
        vector<int> list;
        
        backtrace(nums, list, ans);
        return ans;
    }
    
    void backtrace(vector<int>& nums, vector<int>& list, vector<vector<int>>& ans) {}
        if (list.size() == nums.size()) {
            ans.push_back(list);
            return;
        } else {
            for (int i = 0; i < nums.size(); ++i) {
                if (find(list.begin(), list.end(), nums[i]) != list.end()) continue;
                list.push_back(nums[i]);
                backtrace(nums, list, ans);
                list.pop_back();
            }
        }
    }
};
```

### 复杂度

引用了[参考资料](https://algorithm.yuanbin.me/zh-hans/exhaustive_search/permutations.html#) 的分析：

以状态数来分析，最终全排列个数应为 $$n!$$, 每个节点被遍历的次数为 $$(n-1)!$$, 故节点共被遍历的状态数为 $$O(n!)$$, 此为时间复杂度的下界，因为这里只算了合法条件下的遍历状态数。若不对 list 中是否包含 nums[i] 进行检查，则总的状态数应为 $$n^n$$ 种。

由于最终的排列结果中每个列表的长度都为 n, 各列表的相同元素并不共享，故时间复杂度的下界为 $$O(n \cdot n!)$$, 上界为 $$n \cdot n^n$$. 实测`helper`中 for 循环的遍历次数在 $$O(2n \cdot n!)$$ 以下，注意这里的时间复杂度并不考虑查找列表里是否包含重复元素。



## Solution 3 - STL next_permutation

还可以用 STL 的方法来做

```cpp
class Solution {
public:
    vector<vector<int>> permute(vector<int>& nums) {
        vector<int> numss(nums);
        sort(numss.begin(), numss.end());
        vector<vector<int>> result;
        do {
            result.push_back(numss);
        } while (next_permutation(numss.begin(), numss.end()));
        return result;
    }
};
```

## Solution 4 - 数学定义 递归

```cpp
class Solution {
public:
    /**
     * @param nums: A list of integers.
     * @return: A list of permutations.
     */
    vector<vector<int> > permute(vector<int>& nums) {
        vector<vector<int> > result;

        if (nums.size() == 1) {
            result.push_back(nums);
            return result;
        }

        for (int i = 0; i < nums.size(); ++i) {
            vector<int> nums_new = nums;
            nums_new.erase(nums_new.begin() + i);

            vector<vector<int> > res_tmp = permute(nums_new);
            for (int j = 0; j < res_tmp.size(); ++j) {
                vector<int> temp = res_tmp[j];
                temp.push_back(nums[i]);
                result.push_back(temp);
            }
        }

        return result;
    }
};
```

## Solution 5 - 迭代

[参考](https://algorithm.yuanbin.me/zh-hans/exhaustive_search/permutations.html#) 总结的非常详细，之后再补充。

## Reference

[这个解答](https://leetcode.com/problems/permutations/discuss/18239/A-general-approach-to-backtracking-questions-in-Java-(Subsets-Permutations-Combination-Sum-Palindrome-Partioning)) 详细总结了Permutations 问题的几种类型