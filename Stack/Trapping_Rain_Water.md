# Trapping Rain Water

Tags: Stack, Array，Hard

## Problem

Given n non-negative integers representing an elevation map where the width of each bar is 1, compute how much water it is able to trap after raining.

For example, 
Given [0,1,0,2,1,0,1,3,2,1,2,1], return 6.

本题在 [leetcode solution](https://leetcode.com/problems/trapping-rain-water/solution/) 有详细的题解，多达 4 种解法，之后有空再补充。

## 解法1 - 找最高点 - 左右两边容量相加

参考 [忆水寒的解法](https://www.nowcoder.net/questionTerminal/6a02d61c7e2b49aca2d8e1a5c3208e21)
个人觉得这个解法比较好理解，画画图就明白了。先找到一个最高点，然后分为左半部分与右半部分，因为已经有了最高点在中间立着，所以左右两边遍历时，一旦发下有下降的趋势，就把下降的部分累加到结果中即可。

```cpp
class Solution {
public:
    int trap(vector<int>& height) {
        int result = 0;
        int left = 0, right = 0;
        int maxHighId = 0;
        
        for (int i = 0; i < height.size(); ++i) {
            if (height[i] > height[maxHighId]) maxHighId = i;
        }
        
        for (int i = 0; i < maxHighId; ++i) { // 计算左边容量
            if (height[i] < left) result += left - height[i];
            else left = height[i];
        }
        
        for (int i = height.size() - 1; i > maxHighId; --i) { // 计算右边容量
            if (height[i] < right) result += right - height[i];
            else right = height[i];
        }
        
        return result;
    }
};
```

## 解法2 - 两指针遍历

解法1 需要遍历两遍数组，其实理解了解法1后，是可以把遍历次数优化到 1 次的。形象地讲，上面的解法成立的前提是我们已经找到了最高点，所以不担心水会溢出，那么如果我们用两个指针分别指向数组头部和尾部，动态的找这个指针所代表的相对最大值，再慢慢往中间靠拢也是可以的。

算法思路：
1. 初始化 left 指针为 0，right 指针为 size - 1， leftMax = 0，rightMax = 0
2. 当 left < right 时，循环：
    1) 若 height[left] < height[right]
        a. 若 height[left] >= leftMax, 更新 leftMax；
        b. else result += leftMax - height[left]
        c. ++left;
    2) else 
        a. 若 height[right] >= rightMax, 更新 rightMax；
        b. else result += righttMax - height[right]
        c. --right;

```cpp
class Solution {
public:
    int trap(int A[], int n) {
        int result = 0;
        int left = 0;
        int right = n - 1;
        int leftMax = 0, rightMax = 0;
        
        while (left < right) {
            if (A[left] < A[right]) {
                if (A[left] >= leftMax) leftMax = A[left];
                else result += leftMax - A[left];
                ++left;
            } else {
                if (A[right] >= rightMax) rightMax = A[right];
                else result += rightMax - A[right];
                --right;
            }
        }
        
        return result;
    }
};
```

### 复杂度分析

时间复杂度 一次遍历 $$O(n)$$
空间复杂度 $$O(1)$$