# Two Sum II - Input array is sorted

Tags: Array, Easy

## Question

- leetcode: [Two Sum II](https://leetcode.com/problems/two-sum-ii-input-array-is-sorted/description/)
- lintcode: [Two Sum II](http://www.lintcode.com/en/problem/two-sum/)
- nowcoder: [Two Sum II](https://www.nowcoder.com/practice/20ef0972485e41019e39543e8e895b7f?tpId=46&tqId=29177&tPage=1&rp=1&ru=/ta/leetcode&qru=/ta/leetcode/question-ranking)

### Problem Statement

Given an array of integers that is already **sorted in ascending order**, find two numbers such that they add up to a specific target number.

The function twoSum should return indices of the two numbers such that they add up to the target, where index1 must be less than index2. Please note that your returned answers (both index1 and index2) are not zero-based.

You may assume that each input would have exactly one solution and you may not use the same element twice.

**Input:** numbers={2, 7, 11, 15}, target=9
**Output:** index1=1, index2=2

## 题解 - 使用头尾指针遍历

参考 [Two Sum](Two_Sum.md) 的第一种解法，已经是排好序的数组，比 two sum 更简单，注意题目要求 **not zero-based**, 返回的 id 要 + 1

```cpp
class Solution {
public:
    vector<int> twoSum(vector<int>& numbers, int target) {
        const int length = numbers.size();
        vector<int> result;
        
        if (length < 2) return result;
        
        int frontId = 0;
        int endId = length -1;
        
        while (frontId < endId) {
            int sum = numbers[frontId] + numbers[endId];
            if (sum < target) ++ frontId;
            else if (sum > target) -- endId;
            else {
                result.push_back(frontId + 1);
                result.push_back(endId + 1);
                break;
            }
        }
        
        return result;
    }
};
```

### 源码分析

1. 异常处理。
2. 注意  **not zero-based**

### 复杂度分析

空间复杂度为 $$O(1)$$, 遍历一次数组，时间复杂度为 $$O(n)$$.