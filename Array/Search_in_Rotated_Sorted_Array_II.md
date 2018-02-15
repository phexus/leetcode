# Search in Rotated Sorted Array II

Tags: Array, Binary Search, Medium

## Problem

> *Follow up* for "Search in Rotated Sorted Array":
> What if *duplicates* are allowed?
>
> Would this affect the run-time complexity? How and why?

Suppose an array sorted in ascending order is rotated at some pivot unknown to you beforehand.

(i.e., `0 1 2 4 5 6 7` might become `4 5 6 7 0 1 2`).

Write a function to determine if a given target is in the array.

The array may contain duplicates.

## Solution

当有重复元素的时候，若 `nums[mid] >= nums[start]` 可以证明 mid 在左边序列里，若 `nums[mid] <= nums[end]` 可以证明 mid 在右半边序列里。从上面的推导也就不难看出，当 `nums[mid] == nums[start] == nums[end]` 的时候就完全分不清到底在哪边的序列了，所以必须对这种情况单独处理。

有两种处理方式，遍历；

start++  end--缩小范围  总会出现A[start]<=A[mid]或者A[mid]<=A[end]

> 另外，这里其实没必要判断 nums[mid] <= nums[end]，因为前面条件排除后这种情况一定会发生，因为 nums[start] >= nuns[end] 一定存在的

## 相同后遍历

```cpp
class Solution {
public:
    bool search(vector<int>& nums, int target) {
        if (nums.empty()) return false;
        int start = 0, end = nums.size() - 1;
        
        while (start <= end) {
            int mid = start + (end - start >> 1);
            if (nums[mid] == target) return true;
            
            if (nums[mid] == nums[start] == nums[end]) {
                for (int& num : nums) if (num == target) return true;
                return false;
            }
            
            if (nums[mid] >= nums[start]) {
                if (target >= nums[start] && target < nums[mid]) end = mid - 1;
                else start = mid + 1;
            } else { // 这里其实没必要判断 nums[mid] <= nums[end]，因为前面条件排除后这种情况一定会发生，因为 nums[start] >= nuns[end] 一定存在
                if (target > nums[mid] && target <= nums[end]) start = mid + 1;
                else end = mid - 1;
            }
        }
        return false;
    }
};
```

### 相同后 缩减指针范围

```cpp
class Solution {
public:
    bool search(int A[], int n, int target) {
        if (n < 1) return false;
        int start = 0, end = n - 1;
        
        while (start <= end) {
            int mid = start + (end - start >> 1);
            if (A[mid] == target) return true;
            
            if (A[mid] == A[start] && A[mid] == A[end]) {
                ++start;
                --end;
                continue;
            }
            
            if (A[mid] >= A[start]) {
                if (target >= A[start] && target < A[mid]) end = mid - 1;
                else start = mid + 1;
            } else {
                if (target > A[mid] && target <= A[end]) start = mid + 1;
                else end = mid - 1;
            }
        }
        return false;
    }
};
```

> 还有写其他的写法如[1](https://leetcode.com/problems/search-in-rotated-sorted-array-ii/discuss/28194/C++-concise-log(n)-solution) [2](https://algorithm.yuanbin.me/zh-hans/binary_search/search_in_rotated_sorted_array_ii.html)，感觉都不如上面的两个解法直观

### 复杂度

时间复杂度最差降到 O(n)