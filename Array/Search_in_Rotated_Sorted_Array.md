# Search in Rotated Sorted Array

Tags: Array, Binary Search, Medium

## Problem

Suppose an array sorted in ascending order is rotated at some pivot unknown to you beforehand.

(i.e., `0 1 2 4 5 6 7` might become `4 5 6 7 0 1 2`).

You are given a target value to search. If found in the array return its index, otherwise return -1.

You may assume no duplicate exists in the array.

## Solution - 二分查找

与 `剑指 offer` 的习题 8 有点类似，这种旋转数组其实在一定程度上也是有序的，也可以用二分查找法的思路来解。[参考解法](https://algorithm.yuanbin.me/zh-hans/binary_search/search_in_rotated_sorted_array.html) 也有比较详细的解释。

**该题较为巧妙的地方在于如何找出旋转数组中的局部有序数组，并使用二分搜索解之** 

当 `nums[mid] != target` 的处理比较关键，不要着急去比较 `nums[mid] < target` 或 `nums[mid] > target` 的情况，而是通过对比 `nums[start]` 与 `nums[mid]` 的关系来寻找局部有序数组，若`num[start] < num[mid]`成立，则start与mid间的元素必有序（要么是前一段有序数组，要么是后一段有序数组，还有可能是未旋转数组）。

接着在有序数组`num[start]~num[mid]`间进行二分搜索，但能在`num[start]~num[mid]`间搜索的前提是`num[start] <= target <= num[mid]`

或者在有序数组`num[mid]~num[end]`间进行二分搜索，同样要注意提前条件。

最后若未找到满足条件的索引，则返回-1.

```cpp
class Solution {
public:
    int search(vector<int>& nums, int target) {
        if (nums.empty()) return -1;
        int ans = 0;
        int start = 0, end = nums.size() - 1;
        
        while (start <= end) { // 注意这里的条件为 <=
            int mid = start + (end - start >> 1);
            
            if (nums[mid] == target) return mid;
            
            if (nums[start] <= nums[mid]) { // 注意这里的 <= 有可能 [3,1]
                if (target >= nums[start] && target < nums[mid]) {
                    end = mid - 1;
                } else {
                    start = mid + 1;
                }
            } else {
                if (target > nums[mid] && target <= nums[end]) {
                    start = mid + 1;
                } else {
                    end = mid - 1;
                }
            }
        }
        return -1;
    }
};
```

### 需要注意的地方

这里涉及到了较多的边界，一定要想清楚` >` 还是` >=` 

另外就是关于二分法的几种写法问题了，九章的推荐解法和上面的解法不同，设置的判断条件是 `start + 1 < end` ，start 和 end 是闭区间。

具体的二分法参考资料 ：

https://algorithm.yuanbin.me/zh-hans/basics_algorithm/binary_search.html

https://www.zhihu.com/question/36132386

http://blog.csdn.net/zsc2014030403015/article/details/51030392

http://blog.csdn.net/lzk413/article/details/35595053

http://zhengboyang.com/2016/03/18/%E4%BA%8C%E5%88%86%E6%90%9C%E7%B4%A2%E6%B3%95%E7%AE%80%E5%8D%95%E5%88%86%E6%9E%90%E4%B8%8E%E6%80%BB%E7%BB%93/

http://duanple.blog.163.com/blog/static/709717672009049528185/

```cpp
public class Solution {
    public int search(int[] A, int target) {
        if (A == null || A.length == 0) {
            return -1;
        }

        int start = 0;
        int end = A.length - 1;
        int mid;
        
        while (start + 1 < end) {
            mid = start + (end - start) / 2;
            if (A[mid] == target) {
                return mid;
            }
            if (A[start] < A[mid]) {
                // situation 1, red line
                if (A[start] <= target && target <= A[mid]) {
                    end = mid;
                } else {
                    start = mid;
                }
            } else {
                // situation 2, green line
                if (A[mid] <= target && target <= A[end]) {
                    start = mid;
                } else {
                    end = mid;
                }
            }
        } // while
        
        if (A[start] == target) {
            return start;
        }
        if (A[end] == target) {
            return end;
        }
        return -1;
    }
}
```

### 复杂度

分两段二分，时间复杂度仍近似为 $$O(\log n)$$.