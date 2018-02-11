# Single_Number

Tags: Hash Table, Bit Manipulation, Easy

## Problem

Given an array of integers, every element appears *twice* except for one. Find that single one.

**Note:**
Your algorithm should have a linear runtime complexity. Could you implement it without using extra memory?

## Solution 1 - map



```cpp
class Solution {
public:
    int singleNumber(vector<int>& nums) {
        unordered_map<int, int> map;
        
        for (int& i : nums) ++map[i];
        
        for (auto it = map.begin(); it != map.end(); ++it) if (it -> second == 1) return it -> first;
    }
};9
```

还可以用 set 优化下，不过这里用到了 erase，由于是 unordered _set，底层是哈希表，所以删除操作是  $$O(1)$$(最坏是$$O(n)$$，全部hash到一个bin，相当于一个list，不过一般不用考虑这种情况)，若底层为红黑树，则按值删除是 $$Olog(n)$$

```cpp
class Solution {
public:
    int singleNumber(vector<int>& nums) {
        unordered_set<int> set;
        
        for (int& i : nums) {
            if (set.count(i) > 0) set.erase(i);
            else set.insert(i);
        }
        
        return *set.begin();
    }
};
```

### 复杂度

时间复杂度 $$O(n)$$

空间复杂度 $$O(n)$$ , hash table 所占用的空间与 nums 的元素数量相同

## Solution 2 - bit maniputaion

巧妙运用异或运算

始化返回结果`ans`为0，因为`x ^ 0 = x`

```cpp
class Solution {
public:
    int singleNumber(vector<int>& nums) {
        int ans = 0;
        for (int& i : nums) ans ^= i;
        return ans;
    }
};
```

### 复杂度

时间复杂度 $$O(n)$$

空间复杂度 $$O(1)$$

## Solution 3 - math add

这是一个纯数学的方法，可以参考[leetcode solution 3](https://leetcode.com/problems/single-number/solution/) 

因为要用 set 存储去重，还需要求和，C++ 写起来不方便，这里贴了 python 的实例代码

```cpp
class Solution(object):
    def singleNumber(self, nums):
        """
        :type nums: List[int]
        :rtype: int
        """
        return 2 * sum(set(nums)) - sum(nums)
```

### 复杂度

时间复杂度 $$O(n+n)=O(n)$$

空间复杂度 $$O(n)$$ 主要是，set 需要额外的 n 个空间