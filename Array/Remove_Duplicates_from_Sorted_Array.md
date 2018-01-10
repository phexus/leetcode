# Remove Duplicates from Sorted Array

## Question

- leetcode: [Remove Duplicates from Sorted Array | LeetCode OJ](https://leetcode.com/problems/remove-duplicates-from-sorted-array/)
- lintcode: [(100) Remove Duplicates from Sorted Array](http://www.lintcode.com/en/problem/remove-duplicates-from-sorted-array/)
- nowcoder: [Remove Duplicates from Sorted Array](https://www.nowcoder.com/practice/a519784e89fb40199fbe7993786715b1?tpId=46&tqId=29153&tPage=1&rp=1&ru=/ta/leetcode&qru=/ta/leetcode/question-ranking)

```
Given a sorted array, remove the duplicates in place
such that each element appear only once and return the new length.

Do not allocate extra space for another array,
you must do this in place with constant memory.

For example,
Given input array A = [1,1,2],

Your function should return length = 2, and A is now [1,2].

Example
```

## 题解 - 两根指针从前往后遍历

```cpp
class Solution {
public:
    int removeDuplicates(int A[], int n) {
        if (A == NULL || n <= 0)
            return 0;
        
        int new_id = 0;
        for (int i = 0; i < n; ++ i) {
            if (A[i] != A[new_id]) {
                A[++ new_id] = A[i];
            }
        }
        
        return new_id + 1;
    }
};
```

### 源码分析

与 remove element 类似，由于是已经排好序的，一根指针用来遍历，另一根指针指向新的数组头部，每次比较两根指针所指向的元素，若不同完成覆写。注意 new_id 写入新元素后要自增。最后返回的数组长度也应该　＋１。

### 复杂度分析

遍历一次数组，时间复杂度 $$O(n)$$, 空间复杂度 $$O(1)$$.