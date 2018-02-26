# Median of Two Sorted Arrays

Tags: Array, Binary Search, Divide and  Conquer

## Problem

There are two sorted arrays **nums1** and **nums2** of size m and n respectively.

Find the median of the two sorted arrays. The overall run time complexity should be O(log (m+n)).

**Example 1:**

```
nums1 = [1, 3]
nums2 = [2]

The median is 2.0
```

**Example 2:**

```
nums1 = [1, 2]
nums2 = [3, 4]

The median is (2 + 3)/2 = 2.5
```

## Solution

这道题还是一道挺难的题，要求时间复杂度不得超过 $$O(log (m+n))$$ ，应该就能想到利用二分法了，但是这道题有两个数组，虽然分别是排序的，但是一旦合并重新排序，时间复杂度肯定就超了。

[Solution](https://leetcode.com/problems/median-of-two-sorted-arrays/solution/) 有非常详细的解释，然而我觉得这里面有太多的 trick 了，下次再次遇到肯定还是不会做。。

1. 确保 m <= n
2. j 是依赖 i 变化而变化的，并且 i j 呈负相关
3. 对 i 进行二分迭代，要小心边界问题。这里的边界就采取不比较的策略，所以在 if 条件里要设好，这背后的原因又和 i j 之间的以依赖关系有关了，不太好想明白
4. 元素总数为奇数时，直接返回 maxLeft，这是因为 halfLen 的处理使得中间那个值会分到左半部分来

```cpp
class Solution {
public:
    double findMedianSortedArrays(vector<int>& num1, vector<int>& num2) {
        int m = num1.size(), n = num2.size();
        if (m > n) return findMedianSortedArrays(num2, num1);
        
        int iMin = 0, iMax = m, halfLen = (m + n + 1) / 2;
        while (iMin <= iMax) {
            int i = iMin + (iMax - iMin) / 2;
            int j = halfLen - i;
            
            if (i < iMax && num1[i] < num2[j - 1]) {
                ++iMin;
            } else if (i > iMin && num1[i - 1] > num2[j]) {
                --iMax;
            } else {
                int maxLeft;
                if (i == 0) maxLeft = num2[j - 1];
                else if (j == 0) maxLeft = num1[i - 1];
                else maxLeft = max(num1[i - 1], num2[j - 1]);
                if ((m + n) & 0x1 == 1) return maxLeft;
                
                int minRight;
                if (i == m) minRight = num2[j];
                else if (j == n) minRight = num1[i];
                else minRight = min(num1[i], num2[j]);
                return (maxLeft + minRight) / 2.0;
            }
        }
        return 0.0;
    }
};
```

### 复杂度

- Time complexity: $$O\big(\log\big(\text{min}(m,n)\big)\big)$$
  At first, the searching range is [0, m][0,m]. And the length of this searching range will be reduced by half after each loop. So, we only need $$\log(m)$$ loops. Since we do constant operations in each loop, so the time complexity is $$O\big(\log(m)\big)$$. Since m $$\leq n$$, so the time complexity is $$O\big(\log\big(\text{min}(m,n)\big)\big)$$.
- Space complexity: $$O(1)$$.
  We only need constant memory to store $$9$$ local variables, so the space complexity is $$O(1)$$.