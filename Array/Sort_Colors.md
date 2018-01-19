# Sort Colors

Tags: Array, Medium

## Question

- leetcode: [Sort Colors](https://leetcode.com/problems/sort-colors/description/)
- nowcoder: [Sort Colors](https://www.nowcoder.com/practice/4345e55fdb03498a89a97ec18e62b3ab?tpId=46&tqId=29103&tPage=5&rp=5&ru=/ta/leetcode&qru=/ta/leetcode/question-ranking)

### Problem Statement

Given an array with n objects colored red, white or blue, sort them so that objects of the same color are adjacent, with the colors in the order red, white and blue.

Here, we will use the integers 0, 1, and 2 to represent the color red, white, and blue respectively.

Note:
You are not suppose to use the library's sort function for this problem.

Follow up:
A rather straight forward solution is a two-pass algorithm using counting sort.
First, iterate the array counting number of 0's, 1's, and 2's, then overwrite array with total number of 0's, then 1's and followed by 2's.

Could you come up with an one-pass algorithm using only constant space?

## 题解1 - 解法 (需要特别注意比较顺序)

要求遍历一遍，并且不可用 sort 排序，将 0 都换到 front 的左边，2 都换到 end 的右边。

```cpp
class Solution {
public:
    void sortColors(int A[], int n) {
        int end = n - 1;
        int front = 0;
        int i = 0;
        //注意边界，有 = 
        while (i <= end) {
            // 注意顺序，先判断2，再判断0
            while (A[i] == 2 && i < end) swap(A[i], A[end --]);
            while (A[i] == 0 && i > front) swap(A[i], A[front ++]);
            ++ i;
        }
    }
};
```

### 源码分析

首先要明确的是循环是从数组头部开始遍历的，所以 A[i] 左边的只有可能是 0 或 1，因为 2 都换到了 end 以后了。
每次遍历的顺序有要求，里要先将 2 换到后面，此时当前位置只有可能是 0 或 1，再将 0 换到 front 之前，换回来的只有可能是 1，之后游标 ++，这样已经遍历到的元素一定是0,1,2的顺序。若是先换 0，再换 2，有可能此时换回来的还是0，会有问题！！！

最外层 while 的边界要注意，由于这里定义的 end 代表 end 之后的都是 2，但 end 本身是什么元素并不知道，所以必须遍历到 end。

### 复杂度分析

时间复杂度是 $$O(n)$$, 空间复杂度为 $$O(1)$$.

### 另一种更加清晰的方法

```cpp
class Solution {
public:
    void sortColors(int A[], int n) {
        int end = n - 1;
        int front = 0;
        int i = 0;
        while (i <= end) {
            if (A[i] == 2) swap(A[i], A[end --]);
            else if (A[i] == 0) swap(A[i ++], A[front ++]);
            else ++ i;
        }
    }
};

```

### 源码分析

这里对循环做了更加清晰的划分，没有循环嵌套，要注意 ++ 自增放在了 两个分支 里，顺序就不重要了。

设置3个变量，分别代表数组前部 front，当前遍历的位置 i，数组后部 end
①当A[i] = 0时，必然属于数组前部，则交换A[i] 和 A[front] ,接着i++ , front++
②当A[i] = 1时，只需i++就好，因为只有排好了0和2,1自然就只能在中间了，故不作处理
③当A[i] = 2时，不然属于数组后部，则交换A[i]和A[end]，接着end --，不过此时就不能i++了因为，交换过去的A[i]有可能是0或者2，所以需要在下一个循环里判断，这样它的位置才能够正确