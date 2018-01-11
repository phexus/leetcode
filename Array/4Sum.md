# 4 Sum

Tags: Array, Medium

## Question

- leetcode: [4Sum | LeetCode OJ](https://leetcode.com/problems/4sum/)
- nowcoder: [4 Sum](https://www.nowcoder.com/practice/eb632e81417c4d5797cd712b82f7daa1?tpId=46&tqId=29161&tPage=1&rp=1&ru=/ta/leetcode&qru=/ta/leetcode/question-ranking)


### Problem Statement

Given an array S of n integers, are there elements a, b, c, and d in S such that a + b + c + d = target? Find all unique quadruplets in the array which gives the sum of target.

#### Note:

For example, given array S = [1, 0, -1, 0, -2, 2], and target = 0.

A solution set is:
[
  [-1,  0, 0, 1],
  [-2, -1, 1, 2],
  [-2,  0, 0, 2]
]

## 题解 - 排序 + 多重循环 + 头尾指针遍历

解题思路完全类似 3sum，只不过是要求四个数字的和，在时间复杂度上要比3Sum高一个数量级。对于两点要求的处理：

1. 首先要对整个数组进行排序，这样得到的答案自然是排序好的.
2. 对于重复答案的处理和3Sum是一摸一样的。

```cpp
class Solution {
public:
    vector<vector<int>> fourSum(vector<int>& nums, int target) {
        const int length = nums.size();
        vector<vector<int>> result;
        
        if (length < 4) return result;
        
        sort(nums.begin(), nums.end());
        
        for (int i = 0; i < length - 3; ++ i) {
            for (int j = i + 1; j < length - 2; ++ j) {
                int front = j + 1;
                int back = length - 1;
                
                while (front < back) {
                    int sum = nums[i] + nums[j] +nums[front] + nums[back];
                     
                    if (sum < target) ++ front;
                    else if (sum > target) -- back;
                    else {
                        result.push_back(vector<int> {nums[i], nums[j], nums[front], nums[back]});
                        
                        while (front + 1 < back && nums[front + 1] == nums[front]) ++ front;
                        while (back - 1 > front && nums[back - 1] == nums[back]) -- back;
                        
                        ++ front;
                        -- back;
                    }
                    
                }
                
                while (j + 1 < length && nums[j + 1] == nums[j]) ++ j;
            }
            
            while (i + 1 < length && nums[i + 1] == nums[i]) ++ i;
        }
        
        return result;
    }
};


// 另一种写法
class Solution {
public:
    vector<vector<int>> fourSum(vector<int>& nums, int target) {
        const int length = nums.size();
        vector<vector<int>> result;
        
        if (length < 4) return result;
        
        sort(nums.begin(), nums.end());
        
        for (int i = 0; i < length - 3; ++ i) {
            
            if (i > 0 && nums[i - 1] == nums[i]) continue;
            
            for (int j = i + 1; j < length - 2; ++ j) {
                
                if (j > i + 1 && nums[j - 1] == nums[j]) continue;
                
                int front = j + 1;
                int back = length - 1;
                
                while (front < back) {
                    int sum = nums[i] + nums[j] +nums[front] + nums[back];
                     
                    if (sum < target) ++ front;
                    else if (sum > target) -- back;
                    else {
                        result.push_back(vector<int> {nums[i], nums[j], nums[front ++], nums[back --]});
                        
                        while (front < back && nums[front - 1] == nums[front]) ++ front;
                        while (back > front && nums[back + 1] == nums[back]) -- back;
                    }
                }
            }
        }
        
        return result;
    }
};

```

### 源码分析

1. 注意异常处理
2. 注意外层 i,j 去重的两种写法，以及内层 front，end 去重的两种写法
3. 注意 i 的边界问题，因为 j, frontId 和 endId 都在 i 后面，所以减掉3位，i < length - 3，类似的， j < length - 2


### 复杂度分析

时间复杂度是 $$O(n^3)$$, 空间复杂度为 $$O(1)$$.

### 一种优化方案 - 12ms

参考 leetcode 的 [discuss](https://leetcode.com/problems/4sum/discuss/8549)

At loop 1:
// 固定 i 后的最小和 都 大于 target，证明此 i 下不存在解，break 跳出
// 固定 i 后的最大和 都 小于 target，证明 需要增大 i，continue 继续循环
if(nums[i]+nums[i+1]+nums[i+2]+nums[i+3]>target) break;
if(nums[i]+nums[length-3]+nums[length-2]+nums[length-1]<target) continue;

At loop 2:
// 固定 i，j 后的最小和 都 大于 target，证明此 i，j 下不存在解，break 跳出
// 固定 i，j 后的最大和 都 小于 target，证明 需要增大 j，continue 继续循环
if(nums[i]+nums[j]+nums[j+1]+nums[j+2]>target) break;
if(nums[i]+nums[j]+nums[length-2]+nums[length-1]<target) continue;
Then we skip this loop to avoid a waste of time.

```cpp
class Solution {
public:
    vector<vector<int>> fourSum(vector<int>& nums, int target) {
        const int length = nums.size();
        vector<vector<int>> result;
        
        if (length < 4) return result;
        
        sort(nums.begin(), nums.end());
        
        for (int i = 0; i < length - 3; ++ i) {
            
            if (i > 0 && nums[i - 1] == nums[i]) continue;
            if (nums[i] + nums[i + 1] + nums[i + 2] + nums[i + 3] > target) break;
            if (nums[i] + nums[length -3] + nums[length - 2] + nums[length - 1] < target) continue;
            
            for (int j = i + 1; j < length - 2; ++ j) {
                
                if (j > i + 1 && nums[j - 1] == nums[j]) continue;
                if (nums[i] + nums[j] + nums[j + 1] + nums[ j + 2] > target) break;
                if (nums[i] + nums[j] + nums[length -2] + nums[length - 1] < target) continue;
                
                int front = j + 1;
                int back = length - 1;
                
                while (front < back) {
                    int sum = nums[i] + nums[j] +nums[front] + nums[back];
                     
                    if (sum < target) ++ front;
                    else if (sum > target) -- back;
                    else {
                        result.push_back(vector<int> {nums[i], nums[j], nums[front ++], nums[back --]});
                        
                        while (front < back && nums[front - 1] == nums[front]) ++ front;
                        while (back > front && nums[back + 1] == nums[back]) -- back;
                    }
                }
            }
        }
        
        return result;
    }
};

```

## 问题扩展 - KSum

> 根据以上的2Sum, 3Sum, 3Sum Cloest， 还有4Sum，会有一定的规律，扩展到 KSum。

参考[KSum 思路](https://siddontang.gitbooks.io/leetcode-solution/content/array/sum.html)

> 首先，对于2Sum，我们用的解法是以空间复杂度来换取时间复杂度，那么，2Sum我们可不可以in place来解？时间复杂度又是多少？ 答案是当然可以，我们可以先sort一遍，之后再扫一变，sort的时间复杂度是O(nlogn)，扫一遍是O(n),因此，这种解法的时间复杂度是O(nlogn), 当然，如果对于要找index，leetcode上的题不能用这个方法，因为我们sort一遍之后，index会发生一些变化。(实际上2sum我已经用了两种方法，`vector<pair<int, int>> numbersSortedPair(length);`)但是我们可以用以下这个function来作为一个Helper function对于K Sum(考虑到如果K > 2, sort一遍数组的时间开销不算是主要的时间开销了):

```cpp
void twoSum(vector<int> &numbers, int begin, int first, int second, int target, vector<vector<int>>& ret) {
       if(begin >= numbers.size()-1)
            return;
        int b = begin;
        int e = numbers.size()-1;
        while(b < e)
        {
            int rest = numbers[b]+numbers[e];
            if(rest == target)
            {
                vector<int> tmp_ret;
                tmp_ret.push_back(first);
                tmp_ret.push_back(second);
                tmp_ret.push_back(numbers[b]);
                tmp_ret.push_back(numbers[e]);
                ret.push_back(tmp_ret);
                do{b++;}
                while(b<e && numbers[b] == numbers[b-1]);
                do{e--;}
                while(b<e && numbers[e] == numbers[e+1]);
            }
            else if(rest < target)
                ++b;
            else
                --e;
        }
    }
```

以 4Sum 为例，该解法可以延伸到 KSum， 不过是相当于对于 n-2 个数做嵌套循环。这么写出来使得思路清晰，以后遇到了类似问题可以解决。

```cpp
class Solution {
public:

    void twoSum(vector<int> &numbers, int begin, int first, int second, int target, vector<vector<int>>& ret) {
       if(begin >= numbers.size()-1)
            return;
        int b = begin;
        int e = numbers.size()-1;
        while(b < e)
        {
            int rest = numbers[b]+numbers[e];
            if(rest == target)
            {
                vector<int> tmp_ret;
                tmp_ret.push_back(first);
                tmp_ret.push_back(second);
                tmp_ret.push_back(numbers[b]);
                tmp_ret.push_back(numbers[e]);
                ret.push_back(tmp_ret);
                do{b++;}
                while(b<e && numbers[b] == numbers[b-1]);
                do{e--;}
                while(b<e && numbers[e] == numbers[e+1]);
            }
            else if(rest < target)
                ++b;
            else
                --e;
        }
    }
    vector<vector<int> > fourSum(vector<int> &num, int target) {
        vector<vector<int>> ret;
        if(num.size() <= 3) //invalid corner case check
            return ret;
        sort(num.begin(), num.end()); //cause we need the result in quadruplets should be non-descending
        for(int i = 0; i < num.size()-3; ++i)
        {
            if(i > 0 && num[i] == num[i-1]) continue;

            for(int j = i+1; j < num.size()-2; ++j)
            {
                if(j > i+1 && num[j] == num[j-1]) continue;

                twoSum(num, j+1, num[i], num[j], target-(num[i]+num[j]), ret);
            }
        }
        return ret;
    }
};
```