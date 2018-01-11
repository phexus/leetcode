# 3 Sum Closest

Tags: Array, Medium

## Question

- leetcode: [3Sum Closest | LeetCode OJ](https://leetcode.com/problems/3sum-closest/)
- lintcode: [(59) 3 Sum Closest](http://www.lintcode.com/en/problem/3-sum-closest/)
- nowcoder: [3 Sum Closest](https://www.nowcoder.com/practice/291a866d7c904563876b373b0b157dde?tpId=46&tqId=29162&tPage=1&rp=1&ru=/ta/leetcode&qru=/ta/leetcode/question-ranking)

```
Given an array S of n integers, find three integers in S such that the sum is closest to a given number, target. 
Return the sum of the three integers. You may assume that each input would have exactly one solution.

For example, given array S = {-1 2 1 -4}, and target = 1.
The sum that is closest to the target is 2. (-1 + 2 + 1 = 2).
```

## 题解 - 排序 + 2 Sum + 两根指针 + 优化过滤

和 3 sum 类似，必须在开头要对数组进行排序，要么没法定位指针移动。

```cpp
class Solution {
class Solution {
public:
    int threeSumClosest(vector<int>& nums, int target) {
        const int length = nums.size();
        if (length <= 3) return accumulate(nums.begin(), nums.end(), 0);
        
        int sum = nums[0] + nums[1] + nums[2];
        
        sort(nums.begin(), nums.end());
        
        for (int i = 0; i < length -2; ++ i) {
            int frontId = i + 1;
            int backId = length -1;
            
            while (frontId < backId) {
                int currSum = nums[i] + nums[frontId] + nums[backId];
                    
                if (abs(currSum - target) < abs(sum - target)) sum = currSum;
                
                if (sum == target) return sum;
                (currSum > target) ? -- backId: ++ frontId;
            }
        }
        
        return sum;
    }
};

```

### 源码分析

和前面3Sum解法相似，同理使用三个指针进行循环。
区别在于3sum中的target为0，这里新增一个变量用于比较哪组数据与target更为相近

需要注意的有：

1. 要对数组进行排序，否则没法定位指针的移动
2. 别忘了初始化 sum，否则 sum 初值就为 0，测试无法通过。这里可以选择前三个元素的和，也可以定义一个 `INT_MAX` 常量，这个值 和 `INT_MIN` 是相对应的，在很多比较求最大值最小值的情况，经常用这两个变量。 `int sum = 0； int distance = INT_MAX;`
4. 注意 accumulate 的用法


### 复杂度分析

时间复杂度同理为$$O(n^2)$$，空间复杂度$$O(1)$$