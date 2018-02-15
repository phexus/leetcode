# Search Insert Position

Tags: Array, Binary Search, Easy

## Problem

Given a sorted array and a target value, return the index if the target is found. If not, return the index where it would be if it were inserted in order.

You may assume no duplicates in the array.

**Example 1:**

```
Input: [1,3,5,6], 5
Output: 2
```

**Example 2:**

```
Input: [1,3,5,6], 2
Output: 1
```

**Example 3:**

```
Input: [1,3,5,6], 7
Output: 4
```

**Example 1:**

```
Input: [1,3,5,6], 0
Output: 0
```

## Solution 1 - 遍历

最常规的方法，这道题明显不应该这样做，优化空间很大。

```cpp
class Solution {
public:
    int searchInsert(vector<int>& nums, int target) {
        if (nums.empty()) return 0;
        
        for (int i = 0; i < nums.size(); ++i) {
            if (nums[i] >= target) return i;
        }
        return nums.size(); // 遍历完了还没返回得返回 nums.size()
    }
};
```

### 复杂度

时间复杂度 $$O(n)$$

空间复杂度 $$O(1)$$

## Solution 2 - 二分查找

典型的二分查找问题，不过边界问题要考虑好，这里有多种写法，取决于怎么去定义 low 与 high。

下面的解法 low 指的是最小值，high 指的是最大值，是可以被取到的。最终返回的期望值的范围是 [low, high + 1]

例如 1, 3, 5, 6 , target = 2，此时应该 low 与 high 都指向了 1，循环是不能终止的，继续循环才能让 low 指向 3，low > high 才退出，返回 3 所在的位置。

```cpp
class Solution {
public:
    int searchInsert(vector<int>& nums, int target) {
        int low = 0, high = nums.size() - 1;
        //Invariant: the desired index is between [low, high+1]
        while (low <= high) {
            int mid = low + (high - low >> 1); //这样写能够有效防止溢出
            if (nums[mid] == target) return mid;
            if (nums[mid] > target) high = mid - 1;
            else low = mid + 1;
        }
        
        return low;
    }
};
        // (1) At this point, low > high. That is, low >= high+1
        // (2) From the invariant, we know that the index is between [low, high+1], so low <= high+1. Follwing from (1), now we know low == high+1.
        // (3) Following from (2), the index is between [low, high+1] = [low, low], which means that low is the desired index
        //     Therefore, we return low as the answer. You can also return high+1 as the result, since low == high+1
```

另一种写法

```cpp
int searchInsert(vector<int>& nums, int target) {
    int start = 0, end = nums.size(), mid;
    while (start < end) {
        mid = start + (end - start) / 2;
        if (nums[mid] >= target)
            end = mid;
        else
            start = mid + 1;
    }
    return start;
}
```

另一种写法， 见 [参考](https://algorithm.yuanbin.me/zh-hans/binary_search/search_insert_position.html#)

分析三种典型情况：

1. 目标值在数组范围之内，最后返回值一定是`start + 1`
2. 目标值比数组最小值还小，此时`start` 一直为`-1`, 故最后返回`start + 1` 也没错，也可以将`-1` 理解为数组前一个更小的值
3. 目标值大于等于数组最后一个值，由于循环退出条件为`start + 1 == end`, 那么循环退出时一定有`start = A.length - 1`, 应该返回`start + 1`

综上所述，返回`start + 1`是非常优雅的实现。其实以上三种情况都可以统一为一种方式来理解，即索引`-1` 对应于在数组前方插入一个非常小的数，索引`end` 即对应数组后方插入一个非常大的数，那么要插入的数就一定在`start` 和`end` 之间了。

有时复杂的边界条件处理可以通过『补项』这种优雅的方式巧妙处理。

```cpp
class Solution {
public:
    int searchInsert(vector<int> &A, int target) {
        if (A.empty()) return 0;

        int n = A.size();
        int lb = -1, ub = n;
        while (lb + 1 < ub) {
            int mid = lb + (ub - lb) / 2;
            if (A[mid] < target) {
                lb = mid;
            } else {
                ub = mid;
            }
        }
        return ub;
    }
};
```

### 复杂度

时间复杂度 $$O(\log n)$$

空间复杂度 $$O(1)$$.

### Reference

关于二分查找的几种写法对应的终止条件的详细[介绍](https://algorithm.yuanbin.me/zh-hans/basics_algorithm/binary_search.html)