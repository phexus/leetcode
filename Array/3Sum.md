# 3 Sum

Tags: Array, Medium

## Question

- leetcode: [3Sum | LeetCode OJ](https://leetcode.com/problems/3sum/)
- lintcode: [(57) 3 Sum](http://www.lintcode.com/en/problem/3sum/)
- nowcoder: [3 Sum](https://www.nowcoder.com/practice/345e2ed5f81d4017bbb8cc6055b0b711?tpId=46&tqId=29163&tPage=1&rp=1&ru=/ta/leetcode&qru=/ta/leetcode/question-ranking)

### Problem Statement

Given an array _S_ of _n_ integers, are there elements _a_, _b_, _c_ in _S_
such that _a_ + _b_ + _c_ = 0? Find all unique triplets in the array which
gives the sum of zero.

#### Note:

The solution set must not contain duplicate triplets.
    
    For example, given array S = [-1, 0, 1, 2, -1, -4],
    
    A solution set is:
    [
      [-1, 0, 1],
      [-1, -1, 2]
    ]


## 题解 - 排序 + 头尾指针遍历

参考 Two Sum 的两个指针解法，将问题可以拆解成多个 Two Sum 问题。

因为不能包含重复的答案数组，所以我们要在代码里面做一切去掉重复的操作。最开始我做leetcode的时候是采用的 two sum 的方法 1，用到了 HashMap，把所有满足条件的答案数组存起来，在尝试对二维数组查重，感觉那样太麻烦了，所以先排序再用头尾指针的方法来做。

```cpp
class Solution {
public:
    vector<vector<int>> threeSum(vector<int>& nums) {
        const int length = nums.size();
        vector<vector<int>> result;
        
        if (length < 3) return result;
        
        sort(nums.begin(), nums.end());
        
        for (int i = 0; i < length -2; ++ i) {
            int frontId = i + 1;
            int endId = length - 1;
            
            while (frontId < endId) {
                int sum = nums[i] + nums[frontId] + nums[endId];
                
                if (sum < 0) ++ frontId;
                
                else if (sum > 0) -- endId;
                
                else {
                    result.push_back(vector<int> {nums[i], nums[frontId], nums[endId]});
                    
                    while (frontId + 1 < endId && nums[frontId + 1] == nums[frontId]) ++ frontId;
                    while(endId - 1 > frontId && nums[endId - 1] == nums[endId]) -- endId;
                    
                    ++ frontId;
                    -- endId;
                }
            }
            
            while (i + 1 < length && nums[i + 1] == nums[i]) ++ i;
        }
        
        return result;
    }
};


// 优化部分循环，i > 0 时 break，注意 continue 的判断条件

class Solution {
public:
    vector<vector<int>> threeSum(vector<int>& nums) {
        const int length = nums.size();
        vector<vector<int>> result;
        
        if (length < 3) return result;
        
        sort(nums.begin(), nums.end());
        
        for (int i = 0; i < length -2; ++ i) {
            
            if (nums[i] > 0) break;
            if (i > 0 && nums[i - 1] == nums[i]) continue;
            
            int frontId = i + 1;
            int endId = length - 1;
            
            
            while (frontId < endId) {
                int sum = nums[i] + nums[frontId] + nums[endId];
                
                if (sum < 0) ++ frontId;
                
                else if (sum > 0) -- endId;
                
                else {
                    result.push_back(vector<int> {nums[i], nums[frontId], nums[endId]});
                    
                    while (frontId + 1 < endId && nums[frontId + 1] == nums[frontId]) ++ frontId;
                    while(endId - 1 > frontId && nums[endId - 1] == nums[endId]) -- endId;
                    
                    ++ frontId;
                    -- endId;
                }
            }
        }
        
        return result;
    }
};

```

### 源码分析

参考了[原理分析](https://algorithm.yuanbin.me/zh-hans/integer_array/3_sum.html)
同python解法不同，没有使用hash map
```
S = {-1 0 1 2 -1 -4}
排序后：
S = {-4 -1 -1 0 1 2}
      ↑  ↑        ↑
      i  j        k
         →        ←
i每轮只走一步，j和k根据S[i]+S[j]+S[k]=ans和0的关系进行移动，且j只向后走（即S[j]只增大），k只向前走（即S[k]只减小）
如果ans>0说明S[k]过大，k向前移；如果ans<0说明S[j]过小，j向后移；ans==0即为所求。
至于如何取到所有解，看代码即可理解，不再赘述。
```

1. 注意异常处理
2. 特别注意去重的 3 个 while loop，否则 [0,0,0,0] 测试过不去
3. 注意 i 的边界问题，因为 frontId 和 endId 都在 i 后面，所以减掉两位，i < length -2
4. 最后一个 while loop 可以放在最前面，用 continue 代替，这里的判断条件必须为 i > 0 && nums[i - 1] == nums[i]，不能写成 nums[i + 1] == nums[i]，否则会漏掉结果。

### 复杂度分析

时间复杂度是 $$O(n^2)$$, 空间复杂度为 $$O(1)$$.