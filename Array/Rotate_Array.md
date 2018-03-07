# Rotate Array

Tags: Array, Easy

## Problem

Rotate an array of *n* elements to the right by *k* steps.

For example, with *n* = 7 and *k* = 3, the array `[1,2,3,4,5,6,7]` is rotated to `[5,6,7,1,2,3,4]`.

**Note:**
Try to come up as many solutions as you can, there are at least 3 different ways to solve this problem.

[[show hint\]](https://leetcode.com/problems/rotate-array/description/#)

**Hint:**
Could you do it in-place with O(1) extra space?

## Solution 1 - reverse

本题与 `剑指 offer` 面试题 42 左旋转字符串类似，都是调用 3 次 recerse 函数，由于 stl 已经提供了 reverse，就没有自己再造轮子。这种方法也满足了 $$O(1)$$ 的空间复杂度。

```cpp
class Solution {
public:
    void rotate(vector<int>& nums, int k) {
        if (nums.empty()) return;
        
        k %= nums.size();
        
        reverse(nums.begin(), nums.end() - k);
        reverse(nums.end() - k, nums.end());
        reverse(nums.begin(), nums.end());
    }
};
```

### 复杂度

时间复杂度 $$O(n)$$

空间复杂度 $$O(1)$$

## Solution - swap

[solution](https://leetcode.com/problems/rotate-array/solution/) 待补充	