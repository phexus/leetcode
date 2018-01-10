# Merge Sorted Array

Tags: Array, Easy

## Question

- leetcode: [Merge Sorted Array | LeetCode OJ](https://leetcode.com/problems/merge-sorted-array/)
- lintcode: [(6) Merge Sorted Array](http://www.lintcode.com/en/problem/merge-sorted-array/)
- nowcoder: [Merge Sorted Array](https://www.nowcoder.com/practice/89865d4375634fc484f3a24b7fe65665?tpId=46&tqId=29090&tPage=1&rp=1&ru=/ta/leetcode&qru=/ta/leetcode/question-ranking)

```
Given two sorted integer arrays nums1 and nums2, merge nums2 into nums1 as one sorted array.

Note:
You may assume that nums1 has enough space (size that is greater or equal to m + n) to hold additional elements from nums2. The number of elements initialized in nums1 and nums2 are m and n respectively.
```

## 题解 - 从尾部向首部逐个添加

```cpp
class Solution {
public:
    void merge(vector<int>& nums1, int m, vector<int>& nums2, int n) {
        if (m < 0 || n <= 0)
            return;
        
        int id1 = m - 1;
        int id2 = n - 1;
        
        for (int i = m + n - 1; i >= 0; -- i) {
            if (id1 >= 0 && id2 >= 0) {
                nums1[i] = nums1[id1] > nums2[id2] ? nums1[id1 --] : nums2[id2 --];
            } else if (id1 < 0) {
                nums1[i] = nums2[id2 --];
            }
        }
        
    }
};


// 改用 while 遍历，会更加简明
class Solution {
public:
    void merge(vector<int>& nums1, int m, vector<int>& nums2, int n) {
        if (m < 0 || n <= 0)
            return;
        
        int id1 = m - 1;
        int id2 = n - 1;
        int index = m + n - 1;

        while (id1 >= 0 && id2 >= 0) {
            nums1[index --] = nums1[id1] > nums2[id2] ? nums1[id1 --] : nums2[id2 --];
        }

        while (id2 >= 0) {
            nums1[index --] = nums2[id2 --];
        }
        
    }
};
```

### 源码分析

若从前往后添加的话，为了不覆盖未处理的元素，需要将整个数组往后平移，两个for循环嵌套，时间复杂度为 $$O(n^2)$$。
由于 A 的容量较 B 大，故最后 `id1 < 0` 或者 `id2 < 0` 时仅需处理 B 中的元素，因为 A 中的元素已经在 A 中，无需处理。

### 复杂度分析

最坏情况下需要遍历两个数组中所有元素，时间复杂度是 $$O(n)$$, 空间复杂度为 $$O(1)$$.
