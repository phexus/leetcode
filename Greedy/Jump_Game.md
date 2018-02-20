# Jump Game

Tags: Greedy

## Problem

Given an array of non-negative integers, you are initially positioned at the first index of the array.

Each element in the array represents your maximum jump length at that position.

Determine if you are able to reach the last index.

For example:
A = `[2,3,1,1,4]`, return `true`.

A = `[3,2,1,0,4]`, return `false`.

## Solution 1 - 贪心

在 [leetcode solution](https://leetcode.com/problems/jump-game/solution/) 的帮助下，发现此题是一道典型的贪心问题。不用考虑具体的走法，只需要保证每一步能覆盖到即可。每部都追求最大范围反而掉入了此题的陷阱。

一个需要注意的点是，得要倒着往前遍历。如果最后一个点到不了，那看看能否走到 能到最后一个点的点，这样逆着遍历，就把问题拆解成了可以连续跳多次，只要保证每次跳到中转点即可。

```cpp
class Solution {
public:
    bool canJump(vector<int>& nums) {
        int lastPos = nums.size() - 1;
        for (int i = nums.size() - 1; i >= 0; --i) {
            if (nums[i] + i >= lastPos) lastPos = i;
        }
        
        return lastPos == 0;
    }
};
```

### 复杂度

仅仅做了一次遍历，所以时间复杂度 $$O(n)$$

空间复杂度 O(1)

## 其他解法

待补充