# Remove Duplicates from Sorted Array II

Tags: Array, Medium

## Question

- leetcode: [Remove Duplicates from Sorted Array II | LeetCode OJ](https://leetcode.com/problems/remove-duplicates-from-sorted-array-ii/)
- lintcode: [(101) Remove Duplicates from Sorted Array II](http://www.lintcode.com/en/problem/remove-duplicates-from-sorted-array-ii/)
- nowcoder: [Remove Duplicates from Sorted Array II](https://www.nowcoder.com/practice/567f420f12ed4069b7e1d1520719d409?tpId=46&tqId=29098&tPage=4&rp=1&ru=%2Fta%2Fleetcode&qru=%2Fta%2Fleetcode%2Fquestion-ranking)

```
Follow up for "Remove Duplicates":
What if duplicates are allowed at most twice?

For example,
Given sorted array A = [1,1,1,2,2,3],

Your function should return length = 5, and A is now [1,1,2,2,3].
```

## 题解1 - 用一个计数器记录重复次数

```cpp
class Solution {
public:
    int removeDuplicates(int A[], int n) {
        if (A == NULL || n < 3)
            return 0;
         
        int newId = 0;
        // count 从 1 开始，因为遍历是从第二个元素开始的，默认第一个元素已经是新数组头了
        int count = 1;

        for (int i = 1; i < n; ++i) {
            if (A[i] != A[newId]) {
                A[++ newId] = A[i];
                // 遇到不一样的元素后，复制完成后计数器归 1，代表当前数字只有 1 个
                count = 1;
            } else if (A[i] == A[newId] && count < 2) {
                // 遇到相同元素时，只有当计数器为 1 后，才能完成复制操作，并使计数器 + 1
                A[++ newId] = A[i];
                ++ count;
            }
        }
         
        return newId + 1;
    }
};
```

### 源码分析

这里的 count 的初始值设为 1，注意 reset 和自增的时机。这种思路下有多种写法，不一一列举。特别注意从第二个元素开始遍历，否则会有问题。

### 复杂度分析

遍历一次数组，时间复杂度 $$O(n)$$, 空间复杂度 $$O(1)$$.

## 题解2 - 比较新数组的第前 n 个元素

```cpp
class Solution {
public:
    int removeDuplicates(vector<int>& nums) {
        
        if (nums.size() < 3)
            return nums.size();
        
        int newIndex = 1;
        
        for (int i = 2; i < nums.size(); ++i) {
            if (nums[i] != nums[newIndex] || nums[i] != nums[newIndex - 1])
                nums[++ newIndex] = nums[i];
        }
        
        return newIndex + 1;
    }
};


// 该思路下的另一种写法，判断条件更加简洁，判断 i 即可
class Solution {
public:
    int removeDuplicates(vector<int>& nums) {
        
        if (nums.size() < 3)
            return nums.size();
        
        int newIndex = 1;
        
        for (int i = 2; i < nums.size(); ++i) {
            if (nums[i] != nums[newIndex - 1])
                nums[++ newIndex] = nums[i];
        }
        
        return newIndex + 1;
    }
};

```

### 源码分析

这种思路不需要额外的计数器，逻辑更加简洁，推荐。注意是 nums[i] 和 nums[newIndex - 1] 比较，而不可以是 nums[i] 和 nums[i -1], 因为赋值的原因会改变运来数组的元素，这样会丢元素。

### 复杂度分析

遍历一次数组，时间复杂度 $$O(n)$$, 空间复杂度 $$O(1)$$.
