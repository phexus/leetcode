# Longest Consecutive Sequence

Tags: Array, Hard

## Problem

Given an unsorted array of integers, find the length of the longest consecutive elements sequence.

For example,
Given [100, 4, 200, 1, 3, 2],
The longest consecutive elements sequence is [1, 2, 3, 4]. Return its length: 4.

Your algorithm should run in O(n) complexity.

## 题解1 - 哈希 set

这里用到了 hashset，value就是它自己的key，而不是像之前的unordered_map那样有键-值对，这里单纯就是为了方便查询这些值，时间复杂度为$$O(1)$$

下面的解法最差时间复杂度是 $$O(n^2)$$

```cpp
class Solution {
public:
    int longestConsecutive(vector<int>& nums) {
        const int length = nums.size();
        unordered_set<int> st(nums.begin(), nums.end());
        
        int result = 0;
        for (int i = 0; i < length; ++ i) {
            if (st.find(nums[i]) != st.end()) {
                int tmp = 1;
                int prev = nums[i] - 1;
                int post = nums[i] + 1;
                st.erase(nums[i]);
                
                while (st.find(prev) != st.end()) {
                    st.erase(prev);
                    -- prev;
                    ++ tmp;
                }
                while (st.find(post) != st.end()) {
                    st.erase(post);
                    ++ post;
                    ++ tmp;
                }
                
                result = max(result, tmp);
            }
        }
        
        return result;
        
    }
};
```

### 优化写法

判断是否为连续对的最小元素，是才进行 while 循环找连续元素。

```cpp
class Solution {
public:
    int longestConsecutive(vector<int>& nums) {
        const int length = nums.size();
        unordered_set<int> st(nums.begin(), nums.end());
        
        int result = 0;
        
        for (int i = 0; i < length; ++ i) {
            if (st.find(nums[i] - 1) == st.end()) {
                int tmp = 1;
                int current = nums[i] + 1;
                while (st.find(current) != st.end()) {
                    ++ current;
                    ++ tmp;
                }
                result = max(result, tmp);
            }
        }
        
        return result;
    }
};
```
### 复杂度分析

虽然时间复杂度似乎是二次的，因为while循环嵌套在for循环中，仔细检查发现它是线性的。因为仅当currentNum标记序列的开始（即currentNum-1不存在于num中）时才到达while循环,while 循环在整个过程中只做 n 次迭代，所以时间复杂度是 $$O(n + n)$$, 即 $$O(n)$$。整个运行时间是线性的。
由于引入了hashset，空间复杂度为 $$O(n)$$.


## 题解2 - 排序 - 遍历

要求连续的序列，首先应该想到排序，从第二个元素开始遍历，若与前一个元素相同则不处理，若与前一个元素相差 1，当前临时变量 tmp + 1，若相差超过 1，更新 result 和 tmp。

```cpp
class Solution {
public:
    int longestConsecutive(vector<int>& nums) {
        const int length = nums.size();
        // 单独处理异常值
        if (length < 1) return 0;
        
        sort(nums.begin(), nums.end());
        
        int result = 1; int tmp = 1;
        for (int i = 1; i < length; ++ i) {
            if (nums[i] != nums[i - 1]) {
                if (nums[i] == nums[i - 1] + 1) {
                    ++ tmp;
                } else {
                    result = max(result, tmp);
                    tmp = 1;
                }
            }
        }
        // 特别注意这里的返回值，最后一个元素若也连续，result 是在循环里没有更新的！！！
        return max(result, tmp);
    }
};
```

### 源码分析

1. 由于后续遍历 result 初值为 1，所以要单独处理空数组的情况，返回 0

2. 若最后一个元素也连续，result 在循环体内并没有更新， 所以要在最后返回时更新。

### 复杂度分析

时间复杂度是 $$O(nlogn)$$, 由于排序 in-space，空间复杂度为 $$O(1)$$.