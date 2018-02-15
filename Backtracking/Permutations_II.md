# Permutations II

Tags: Backtracking

## Problem

Given a collection of numbers that might contain duplicates, return all possible unique permutations.

For example,
`[1,1,2]` have the following unique permutations:

```
[
  [1,1,2],
  [1,2,1],
  [2,1,1]
]
```

## Solution 1 - Backtracking 

在上一道题的 Backtracking 的做法基础上进行剪枝。下面的分析引用[参考资料](https://algorithm.yuanbin.me/zh-hans/exhaustive_search/permutations_ii.html) ，通过举例子的方式挺清楚的：

**剪枝方法为：对于有相同的元素来说，我们只取不重复的一次。**

为了便于分析，我们可以先分析简单的例子，以 [1, 2_1, 2_2][1,21,22] 为例。按照上题 Permutations 的解法，我们可以得到如下全排列。

1. $$[1, 2_1, 2_2]$$
2. $$[1, 2_2, 2_1]$$
3. $$[2_1, 1, 2_2]$$
4. $$[2_1, 2_2, 1]$$
5. $$[2_2, 1, 2_1]$$
6. $$[2_2, 2_1, 1]$$

从以上结果我们注意到`1`和`2`重复，`5`和`3`重复，`6`和`4`重复，从重复的解我们可以发现其共同特征均是第二个 $$2_2$$ 在前，而第一个 $$2_1$$ 在后，因此我们的**剪枝方法为：对于有相同的元素来说，我们只取不重复的一次。**嗯，这样说还是有点模糊，下面以 $$[1, 2_1, 2_2]$$ 和 $$[1, 2_2, 2_1]$$ 进行说明。

首先可以确定 $$[1, 2_1, 2_2]$$ 是我们要的一个解，此时`list`为 $$[1, 2_1, 2_2]$$, 经过两次`list.pop_back()`之后，`list`为 $$[1]$$, 如果不进行剪枝，那么接下来要加入`list`的将为 $$2_2$$, 那么我们剪枝要做的就是避免将 $$2_2$$加入到`list`中，如何才能做到这一点呢？我们仍然从上述例子出发进行分析，在第一次加入 $$2_2$$ 时，相对应的`visited[1]`为`true`(对应 $$2_1$$)，而在第二次加入 $$2_2$$ 时，相对应的`visited[1]`为`false`，因为在`list`为 $$[1, 2_1]$$ 时，执行`list.pop_back()`后即置为`false`。

一句话总结即为：在遇到当前元素和前一个元素相等时，如果前一个元素`visited[i - 1] == false`, 那么我们就跳过当前元素并进入下一次循环，这就是剪枝的关键所在。另一点需要特别注意的是这种剪枝的方法能使用的前提是提供的`nums`是有序数组，否则无效。

注意剪枝的条件 `visited[i] || i > 0 && nums[i] == nums[i - 1] && !visited[i - 1]` 因为要在上一题的基础上做，还是要判断当前元素是否重复的。其实就是说后面的元素不能在前面相同元素还没被访问的情况下被访问。进行排序之后，重复的元素只能从一个方向进行拿，所以可以保证不重复，`只能按一个顺序进行访问连续相同的元素，才能保证唯一`

```cpp
class Solution {
public:
    vector<vector<int>> permuteUnique(vector<int>& nums) {
        vector<vector<int>> ans;
        vector<int> list;
        vector<bool> visited(nums.size(), false);
        
        sort(nums.begin(), nums.end());
        backtracking(nums, list, visited, ans);
        
        return ans;
    }
    
    void backtracking(vector<int>& nums, vector<int>& list, vector<bool>& visited, vector<vector<int>>& ans) {
        if (list.size() == nums.size()) {
            ans.push_back(list);
            return;
        } else {
            for (int i = 0; i < nums.size(); ++i) {
                if (visited[i] || i > 0 && nums[i] == nums[i - 1] && !visited[i - 1]) continue;
                
                visited[i] = true;
                list.push_back(nums[i]);
                backtracking(nums, list, visited, ans);
                visited[i] = false;
                list.pop_back();
            }
        }
    }
};
```



## Solution 2 - swap 

在之前那道题的基础做了修改，注意这里有两次排序，辅助函数传递的 nums 不是引用！！只有这样不复位，并且传递，否则会破坏后面 n - 1 个字符序列的有序性。[具体可以参考这篇解答](http://blog.csdn.net/yangquanhui1991/article/details/52029528)

> 注意，直接加 if (i == id || nums[i] != nums[id])  这个判断，不做其他处理是不行的，例如像 1122 这样的例子就过不去， 画个图就很明显了，nums[i] != nums[id] 只能控制和自己不一样的，但是不能控制连续几个都和自己不一样但是本身却一样的数。下面的做法完全是为了去重，在每一轮交换的时候都不恢复现场，导致 id 一直是动态变化的，而且由于取代了恢复现场的操作，为了下面的不影响上层，就只好传值了，效率比较低。
>
> 我个人是不推荐这种做法的，虽然 leetcode 的排名较高的[解法](https://leetcode.com/problems/permutations-ii/discuss/18596/A-simple-C++-solution-in-only-20-lines)中也是这么做的

```cpp
class Solution {
public:
    vector<vector<int>> permuteUnique(vector<int>& nums) {
        if (nums.empty()) return vector<vector<int>> ();
        
        vector<vector<int>> ans;
        sort(nums.begin(), nums.end());
        permute(nums, 0, ans);
        sort(ans.begin(), ans.end());
        return ans;
    }
    
    void permute(vector<int> nums, int id, vector<vector<int>>& ans) {
        if (id == nums.size() - 1) {
            ans.push_back(nums);
        } else {
            for (int i = id; i < nums.size(); ++i) {
                if (i == id || nums[i] != nums[id]) {
                    swap(nums[i], nums[id]);
                    permute(nums, id + 1, ans);
                    //swap(nums[i], nums[id]);
                }
            }
        }
    }
};
```

## Solution 3 - 字典序

待补充

## reference

[这个博客](http://blog.csdn.net/qianqin_2014/article/details/51598466) 还提供了新的思路

https://www.nowcoder.com/questionTerminal/a43a2b986ef34843ac4fdd9159b69863 也有一些根据 特异全排列，候选为-9~9,19个数字 使用array[19]记录着19个数字在序列中出现的次数

**条件 **： array 中记录 i, array[i]>0 ，就是原始序列中的这个字母还没有被用完