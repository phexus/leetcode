# Reverse Pairs

Tags: Divide and Conquer, Binary Indexed Tree, Segment Tree, Binary Search Tree

## Problem

Given an array `nums`, we call `(i, j)` an **important reverse pair** if `i < j` and `nums[i] > 2*nums[j]`.

You need to return the number of important reverse pairs in the given array.

**Example1:**

```
Input: [1,3,2,3,1]
Output: 2

```

**Example2:**

```
Input: [2,4,3,5,1]
Output: 3

```

**Note:**

1. The length of the given array will not exceed `50,000`.
2. All the numbers in the input array are in the range of 32-bit integer.

## Solution 1 - 归并排序

本题与`剑指 offer` 的面试题 36 类似，不过更难，不能像求数组逆序对时一边计数一边排序，会有错误的情况产生，要先计数，再排序。排序是一定要做的，否则后面的计数就会出现错误。

下面的是计数部分的核心代码，j 要定义在循环体外，能减少大量运算，因为随着 i 的增加，j 之前的部分没有必要再记录一遍，当数组中元素数量较多的话会有超时情况。

```cpp
        int j = start2;
        for (int i = start1; i <= end1; ++i) {            
            while (j <= end2 && 2LL * data[j] < data[i]) ++j;
            count += j - start2;
        }
```



```cpp
class Solution {
public:
    int reversePairs(vector<int>& nums) {
        if (nums.empty()) return 0;
        vector<int> tmp(nums.size(), 0);
        return help(nums, tmp, 0, nums.size() - 1);
    }
    
    int help(vector<int>& data, vector<int>& tmp, int start, int end) {
        if (start >= end) return 0;
        
        int mid = start + (end - start) / 2;
        int start1 = start, end1 = mid;
        int start2 = mid + 1, end2 = end;
        
        int left = help(data, tmp, start1, end1);
        int right = help(data, tmp, start2, end2);
        
        int count = 0;
        
        int j = start2;
        for (int i = start1; i <= end1; ++i) {            
            while (j <= end2 && 2LL * data[j] < data[i]) ++j;
            count += j - start2;
        }
        
        int k = end;
        while (end1 >= start1 && end2 >= start2) {
            if (data[end1] > data[end2]) {
                tmp[k--] = data[end1--];
            } else {
                tmp[k--] = data[end2--];
            }
        }
        while (end1 >= start1) tmp[k--] = data[end1--];
        while (end2 >= start2) tmp[k--] = data[end2--];
        
        for (k = start; k <= end; ++k) data[k] = tmp[k];
        return left + right + count;
    }
};
```

### 复杂度

- Time complexity: $$O(nlog(n))$$
  - In each step we divide the array into 2 sub-arrays, and hence, the maximum times we need to divide is equal to $$O(log(n))$$
  - Additional $$O(n)$$ work needs to be done to count the inversions and to merge the 2 sub-arrays after sorting. Hence total time complexity is $$O(n * log(n))$$
- Space complexity: $$O(n)$$. Additional space for storing tmp arrays

## Solution - 其他解法

待补充