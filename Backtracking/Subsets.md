# Subsets

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

## Solution 1 - 分治法

本题和 `剑指 offer` 的面试题 28 字符串的排列的 本题扩展 部分很像。只是把字符串换成了数字数组了，其实字符串也是字符数组。本法必须满足的条件是 数字是不重复的，这样结果就不会有重复的。

解决原版 `剑指 offer` 字符串问题的 [解答1](http://blog.csdn.net/abc7845129630/article/details/52734148) [解答2](http://blog.csdn.net/qianqin_2014/article/details/51598778) 

要求所有的组合，可以把问题转换为 求 长度为 n 的 nums 数组 的长度为 k （ 1<=k<=n） 的组合问题。

解决此问题，可以将 n 个数字拆分成两个部分，第一个数字 和 其余的说有字符。有下面叙述的两张方式：

1. 组合包含这个数字，接下来就是在剩下的 n - 1 个元素的数组中寻找长度为 k - 1 的组合问题了。
2. 组合中不包含这个数字，接下来就是在剩下的 n 个元素的数组中寻找长度为 k 的组合问题了。

以上两个子问题都可递归来解决。

```cpp
class Solution {
public:
    vector<vector<int>> subsets(vector<int>& nums) {
        if (nums.empty()) return vector<vector<int>> ();
        int len = nums.size();
        
        vector<vector<int>> ans;
        vector<int> combine; // 存放每次生成的组合
        for (int i = 0; i <= len; ++i) {
            subsets(nums, 0, i, combine, ans);
        }
        return ans;
        
    }
    
    void subsets(vector<int>& nums, int begin, int subLen, vector<int>& combine, vector<vector<int>>& ans) {
        if (subLen == 0) { // 将生成的组合添加进结果
            ans.push_back(combine);
            return;
        }
        if (begin == nums.size()) return; // 超过范围
        
        combine.push_back(nums[begin]);
        subsets(nums, begin + 1, subLen - 1, combine, ans); // 组合包含这个数字
        combine.pop_back();  // 第二种情况，要把该元素移除
        subsets(nums, begin + 1, subLen, combine, ans); // 组合不包含这个数字
    }
};
```

## Solution 2 - Backtracking

和 combination 系列的做法一致，套用模板。注意 k 要从 0 开始，因为允许为空 []

不用单独处理异常，空的情况都已经包括了。nowcoder 更倾向于这种输出，一种长度的 subset 输出完了之后再输出下一种。

```cpp
class Solution {
public:
    vector<vector<int>> subsets(vector<int>& nums) {
        vector<vector<int>> ans;
        vector<int> subset;
        
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
                subset.push_back(nums[i]);
                backtracking(nums, k - 1, i + 1, subset, ans);
                subset.pop_back();
            }
        }
    }
};
```

[leetcode 的写法 ](https://leetcode.com/problems/subsets/discuss/27278/C++-RecursiveIterativeBit-Manipulation-Solutions-with-Explanations) 没有把 k 提出来，而是每次都 压入结果，达到了一样的效果，输出顺序有点不同，这样的输出是 DFS，即把该元素开头的 subset 走到头再换下一个元素。

```cpp
class Solution {
public:
    vector<vector<int>> subsets(vector<int>& nums) {
        vector<vector<int>> ans;
        vector<int> subset;

        backtracking(nums, 0, subset, ans);
        return ans;
    }
    
    void backtracking(vector<int>& nums, int begin, vector<int>& subset, vector<vector<int>>& ans) {
        ans.push_back(subset);
        for (int i = begin; i < nums.size(); ++i) {
            subset.push_back(nums[i]);
            backtracking(nums, i + 1, subset, ans);
            subset.pop_back();
        }
    }
};
```

### 复杂度

对原有数组排序，时间复杂度近似为 $$O(n \log n)$$. 状态数为所有可能的组合数 $$O(2^n)$$, 生成每个状态所需的时间复杂度近似为 $$O(1)$$, 如`[1] -> [1, 2]`, 故总的时间复杂度近似为 $$O(2^n)$$.

使用了临时空间`list`保存中间结果，`list` 最大长度为数组长度，故空间复杂度近似为 $$O(n)$$.

## Solution 3 - Iterative

[参考](https://algorithm.yuanbin.me/zh-hans/exhaustive_search/subsets.html#) 

[leetcode 参考](https://leetcode.com/problems/subsets/discuss/27278/C++-RecursiveIterativeBit-Manipulation-Solutions-with-Explanations)

1. Initially: `[[]]`
2. Adding the first number to all the existed subsets: `[[], [1]]`;
3. Adding the second number to all the existed subsets: `[[], [1], [2], [1, 2]]`;
4. Adding the third number to all the existed subsets: `[[], [1], [2], [1, 2], [3], [1, 3], [2, 3], [1, 2, 3]]`.

即后一个

每一轮添加的都是 原先的元素 + 新元素 组成的 subset，循环的思路比较清晰

```cpp
class Solution {
public:
    vector<vector<int>> subsets(vector<int>& nums) {
        sort(nums.begin(), nums.end());
        vector<vector<int>> subs(1, vector<int>());
        for (int i = 0; i < nums.size(); i++) {
            int n = subs.size();
            for (int j = 0; j < n; j++) {
                subs.push_back(subs[j]); 
                subs.back().push_back(nums[i]);
            }
        }
        return subs;
    }
}; 

```

### 复杂度

外层 for loop $$O(n)$$, 内层 $$O(2^n)$$, 总时间复杂度 $$O(2^n)$$

## Solution 4 - Bit Manipulation

[leetcode 参考](https://leetcode.com/problems/subsets/discuss/27278/C++-RecursiveIterativeBit-Manipulation-Solutions-with-Explanations)

This is the most clever solution that I have seen. The idea is that to give all the possible subsets, we just need to exhaust all the possible combinations of the numbers. And each number has only two possibilities: either in or not in a subset. And this can be represented using a bit.

There is also another a way to visualize this idea. That is, if we use the above example, `1` appears once in every two consecutive subsets, `2` appears twice in every four consecutive subsets, and `3` appears four times in every eight subsets, shown in the following (initially the `8` subsets are all empty):

`[], [], [], [], [], [], [], []`

`[], [1], [], [1], [], [1], [], [1]`

`[], [1], [2], [1, 2], [], [1], [2], [1, 2]`

`[], [1], [2], [1, 2], [3], [1, 3], [2, 3], [1, 2, 3]`

参考二进制的序号表示就能理解下面的做法了，比较 trick，最后一位每隔 1 个数变换一次，倒数第二位，每 + 2，才变换一次，感觉完全是数学规律。

   0

 10

 11

100

101

110

111

```cpp
class Solution {
public:
    vector<vector<int>> subsets(vector<int>& nums) {
        sort(nums.begin(), nums.end());
        int num_subset = pow(2, nums.size()); 
        vector<vector<int> > res(num_subset, vector<int>());
        for (int i = 0; i < nums.size(); i++)
            for (int j = 0; j < num_subset; j++)
                if ((j >> i) & 1) // 向右移动 i 位，判断是否为 1 
                    res[j].push_back(nums[i]);
        return res;  
    }
};
```

### 复杂度

同上