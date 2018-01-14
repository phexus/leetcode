# Array Partition I

Tags: Array, Easy

## Question

- leetcode: [Array Partition I](https://leetcode.com/problems/pascals-triangle-ii/description/)

### Problem Statement

Given an array of 2n integers, your task is to group these integers into n pairs of integer, say (a1, b1), (a2, b2), ..., (an, bn) which makes sum of min(ai, bi) for all i from 1 to n as large as possible.

**Example 1:**
Input: [1,4,3,2]

Output: 4
Explanation: n is 2, and the maximum sum of pairs is 4 = min(1, 2) + min(3, 4).

**Note:**
1. n is a positive integer, which is in the range of [1, 10000].
2. All the integers in the array will be in the range of [-10000, 10000].

## 题解1 - 排序后迭加

满足条件的数字一定是排序后的奇数位数字，即偶数下标的数字。

```cpp
class Solution {
public:
    int arrayPairSum(vector<int>& nums) {
        sort(nums.begin(), nums.end());
        
        const int length = nums.size();
        int sum = 0;
        
        for (int i = 0; i < length; i += 2) {
            sum += nums[i];
        }
        
        return sum;
    }
};
```

### 源码分析

需要先对数组排序。

### 复杂度分析

排序的时间复杂度为 $$O(nlog(n))$$, 空间复杂度为 $$O(1)$$.

## 题解2 - 哈希表存储出现次数

考虑到数组元素的值范围为 -20000 ～ 20000，可以考虑用 vector 构建一个哈希表，存储每个元素出现的次数，这样就不用排序了，时间复杂度也降到了 $$O(n)$$

```cpp
class Solution {
public:
    int arrayPairSum(vector<int>& nums) {
        vector<int> map(20001, 0);
        
        const int length = nums.size();
        
        for (int i = 0; i < length; ++ i) {
            ++ map[nums[i] + 10000];
        }
        
        int sum = 0;
        int count = 0;
        
        for (int i = 0; i < 20001; ) {
            if (map[i] > 0) {
                if ((count & 1) == 0) sum += i - 10000;
                
                -- map[i];
                ++ count;
                
            } else {
                ++ i;
            }
        }
        
        return sum;
    }
};
```

### 源码分析

1. 注意两个 loop 的边界，很容易出错
2. 为了让哈希表的下标表示元素的值，第一个 loop 加了 10000，第二个 loop 要减回来
3. 用了位运算来代替求余判断奇偶

### 复杂度分析

排序的时间复杂度为 $$O(n(n))$$, 空间复杂度为 $$O(1)$$.