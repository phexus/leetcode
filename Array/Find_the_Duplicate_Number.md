# Find the Duplicate Number

Tags: Array, Two Pointers, Binary Search, Medium

## Problem

Given an array *nums* containing *n* + 1 integers where each integer is between 1 and *n* (inclusive), prove that at least one duplicate number must exist. Assume that there is only one duplicate number, find the duplicate one.

**Note:**

1. You **must not** modify the array (assume the array is read only).
2. You must use only constant, *O*(1) extra space.
3. Your runtime complexity should be less than `O(n2)`.
4. There is only one duplicate number in the array, but it could be repeated more than once.

## Solution 1 - swap

本题与 `剑指 offer` 面试题 51 有点类似，不同之处在于本题限定的范围是 1 - n，所以我们只需要守着 nums[0] 不断交换就好了。

```cpp
class Solution {
public:
    int findDuplicate(vector<int>& nums) {
        if (nums.empty()) return 0;
        
        int len = nums.size();
        while (nums[0] != nums[nums[0]]) swap(nums[0], nums[nums[0]]);
        return nums[0];
    }
};
```

### 复杂度

时间复杂度 $$O(n)$$

空间复杂度 $$O(1)$$

## Solution 2 - Floyd's Tortoise and Hare

[Solution](https://leetcode.com/problems/find-the-duplicate-number/solution/) 提供的一个非常巧妙的做法