# Search for a Range

Tags: Array, Binary Search, Medium

## Problem

Given an array of integers sorted in ascending order, find the starting and ending position of a given target value.

Your algorithm's runtime complexity must be in the order of *O*(log *n*).

If the target is not found in the array, return `[-1, -1]`.

For example,
Given `[5, 7, 7, 8, 8, 10]` and target value 8,
return `[3, 4]`.

## Solution

典型的 二分搜索问题，很经典，囊括了 lower bound 和 upper bound

先寻找 lower bound，这里有个小细节要处理，由于有重复元素的存在，所以遇到 nums[id] == target 时，代表着 lower bound 应该在该元素左边，所以要缩小右边界 end = mid - 1

另外就是当 lower bound 所指元素不是 target 时就没必要再去找 upper bound 了，因为我用的这种二分模板，有可能会使 start 越界，所以要去索引 start 元素时一定要先检查是否越界。

另外需要注意的是，当 lower bound 存在时， upper bound 一定存在，再不济也是两者重合，所以后面求 upper bound 后，直接返回就好。

还需要处理 nums.empty() 的 corner case，否则后面会报错。

求 lower bound 返回 start，求 upper bound 返回 end

```cpp
class Solution {
public:
    vector<int> searchRange(vector<int>& nums, int target) {
        if (nums.empty()) return vector<int>(2, -1);  // 处理 corner case
        vector<int> ans;
        
        int start = 0, end = nums.size() - 1, mid;
        while (start <= end) {
            mid = start + (end - start) / 2;
            if (nums[mid] >= target) end = mid - 1;  // 缩小右边界，向左夹逼
            if (nums[mid] < target) start = mid + 1;
        }
        if (start >= nums.size() || nums[start] != target) { // 注意检查越界
            return vector<int>(2, -1);
        } else {
            ans.push_back(start);
        }
        
        start = 0, end = nums.size() - 1;
        while (start <= end) {
            mid = start + (end - start) / 2;
            if (nums[mid] <= target) start = mid + 1; // 缩小左边界，向右夹逼
            else end = mid - 1;
        }
        ans.push_back(end);
        
        return ans;
    }
};
```

### 复杂度

两次二分搜索，时间复杂度仍为 $$O(\log n)$$.

空间复杂度 $$O(1)$$

## 补充

[解法](https://www.topcoder.com/community/data-science/data-science-tutorials/binary-search/) 提供了新的思路，其实 找最后一个 k 其实可以转化成找 第一个 k + 1，之后在 - 1，这样就能共用一套函数了，不过我个人觉得上面的解法就已经很清晰了。

[leetcode solution](https://leetcode.com/problems/search-for-a-range/solution/) 没仔细看，也是提炼成了了一个函数