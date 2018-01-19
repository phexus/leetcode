# Next Permutation

Tags: Array, Medium

## Question

- leetcode: [Next Permutation](https://leetcode.com/problems/next-permutation/description/)
- nowcoder: [Next Permutation](https://www.nowcoder.com/practice/f0069cfcd42649e3b6b0c759fae8cde6?tpId=46&tqId=29148&tPage=1&rp=1&ru=/ta/leetcode&qru=/ta/leetcode/question-ranking)

### Problem Statement


Implement next permutation, which rearranges numbers into the lexicographically next greater permutation of numbers.

If such arrangement is not possible, it must rearrange it as the lowest possible order (ie, sorted in ascending order).

The replacement must be in-place, do not allocate extra memory.

Here are some examples. Inputs are in the left-hand column and its corresponding outputs are in the right-hand column.
1,2,3 → 1,3,2
3,2,1 → 1,2,3
1,1,5 → 1,5,1

## 题解 - 倒序查找 + swap + reverse

通过举例画图，很容易理解，注意当不存在升序对后要整个 reverse

```cpp
class Solution {
public:
    void nextPermutation(vector<int> &num) {
        const int length = num.size();
        if (length < 2) return;
        
        int i = length - 1;
        for ( ; i > 0; -- i) {
            if (num[i] > num[i - 1]) break;
        }
        
        if (i > 0) {
            for (int j = length - 1; ; -- j) {
                if (num[j] > num[i - 1]) {
                    swap(num[i - 1], num[j]);
                    reverse(num.begin() + i, num.end());
                    break;
                }
            }
        }
        
        if (i == 0) reverse(num.begin(), num.end());
    }
};

// 另一种写法 ，用 while 更加简洁
class Solution {
public:
    void nextPermutation(vector<int>& nums) {
        const int length = nums.size();
        if (length < 2) return;
        
        int i = length - 1;
        while (i > 0 && nums[i] <= nums[i - 1]) -- i;
        
        if (i == 0) reverse(nums.begin(), nums.end());
        else {
            int j = length - 1;
            while (nums[j] <= nums[i - 1]) -- j;
            swap(nums[j], nums[i - 1]);
            reverse(nums.begin() + i, nums.end());
        }
        
    }
};
```

### 复杂度分析

时间复杂度是 $$O(n)$$, 空间复杂度为 $$O(1)$$.