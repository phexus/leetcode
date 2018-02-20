# Maximum Subarray

Tags: Array, Divide and Conquer, Dynamic Programming

## Problem

Find the contiguous subarray within an array (containing at least one number) which has the largest sum.

For example, given the array `[-2,1,-3,4,-1,2,1,-5,4]`,
the contiguous subarray `[4,-1,2,1]` has the largest sum = `6`.

[click to show more practice.](https://leetcode.com/problems/maximum-subarray/description/#)

More practice:

If you have figured out the O(*n*) solution, try coding another solution using the divide and conquer approach, which is more subtle.

## Solution 1 - greedy

本题与 `剑指 offer` 的面试题 31 一样，最简单的解法就是贪心算法。

用 currSum 来表示当前的累加和，ans 表示最终的结果。依次累加元素，在累加前先判断 currSum 是否为负，如果为 负 的话，就直接丢弃之前的结果，因为负数是对最终结果是负收益的，所以直接用当前元素覆盖 currSum；若不为负，则累加。ans 存储到目前为止的最大累加和记录，所以不会漏掉之前比较大的情况。

> 特别要注意的是，currSum 初始化为 0， 但是 ans 一定要初始化为最小值，否则 都是负数的 case 会出错。

```cpp
class Solution {
public:
    int maxSubArray(vector<int>& nums) {
        if (nums.empty()) return 0; // 这里返回 0 其实并不对，应该做异常处理
        int currSum = 0;
        int ans = 0x80000000;
        
        for (int num : nums) {
            if (currSum < 0) currSum = num;
            else currSum += num;
            
            ans = max(ans, currSum);
        }
        return ans;
    }
};
```

另一种写法

```cpp
class Solution {
public:
    int maxSubArray(vector<int>& nums) {
        if (nums.empty()) return 0;
        int currSum = 0;
        int ans = 0x80000000;
        
        for (int num : nums) {
            currSum = currSum + max(0, num);
            ans = max(ans, currSum);
        }
        return ans;
    }
};
```



类似与这种最大 最小的 问题都可以尝试用动态规划来做，下面的两种解法参考自 [九章](http://www.jiuzhang.com/solutions/maximum-subarray/#tag-highlight-lang-java)

## Solution 2 - 动态规划 - 区间和

sum 来累加到目前为止的累加和，minSum 为 最小的累加和，sum - minSum 就是最大的区间和了。

不过这种做法特别要小心计算顺序，minSum 可以放在开头算，亦可以放在末尾算，但是如果放在中间算的话， -1 这样单个元素的例子就会返回错误结果 0

```cpp
class Solution {
public:
    int maxSubArray(vector<int>& nums) {
        int sum = 0, minSum = 0, ans = 0x80000000;
        
        for (int num : nums) {
            minSum = min(minSum, sum);
            sum += num;
            ans = max(ans, sum - minSum);
        }
        return ans;
    }
};
```

## Solution 3 - 动态规划 - 全局与局部

和 solution 1 的思想是一样的，只不过用局部最大和 与 全局最大和 两个数组来存储中间变量。local 和 global 都可以用 nums[0] 来初始化，由于当前的状态只跟前一个状态有关，所以数组长度定义为 2 ，用 i % 2 来映射，减少了空间消耗。先更新局部，再更新全局。

这里的局部的含义是，以第 i 个数字结尾的子数组的最大和，所以一定包含 nums[i]，和剑指 offer 的解法二的思路一致。

全局其实就是 max（global）

```cpp
class Solution {
public:
    int maxSubArray(vector<int>& nums) {
        int len = nums.size();
        int global[2];
        int local[2];
        global[0] = nums[0];
        local[0] = nums[0];
        
        for (int i = 1; i < len; ++i) {
            local[i % 2] = max(nums[i], nums[i] + local[(i - 1) % 2]);
            global[i % 2] = max(local[i % 2], global[(i - 1) % 2]);
        }
        return global[(len - 1) % 2];
    }
};
```

## 复杂度

以上三种解法时间复杂度均为 $$O(n)$$

空间复杂度 $$O(1)$$

## 其他

题目中提到了 divide and conquer，感觉实现起来比较麻烦，可以参考[1](https://leetcode.com/problems/maximum-subarray/discuss/20360/C++-An-clear-O(n)-divide-and-conquer-solution-with-comments)  [2](https://leetcode.com/problems/maximum-subarray/discuss/20372/How-to-solve-%22Maximum-Subarray%22-by-using-the-divide-and-conquer-approach) 