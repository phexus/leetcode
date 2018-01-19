# Merge Intervals

Tags: Array, Medium

## Problem

Given a collection of intervals, merge all overlapping intervals.

For example,
Given [1,3],[2,6],[8,10],[15,18],
return [1,6],[8,10],[15,18].

## 题解 - 排序 - 更新

先排序，注意 comp 要定义在 class 外面，并且在 class 之前定义，否则会报错。

```cpp
/**
 * Definition for an interval.
 * struct Interval {
 *     int start;
 *     int end;
 *     Interval() : start(0), end(0) {}
 *     Interval(int s, int e) : start(s), end(e) {}
 * };
 */
bool comp(const Interval& a, const Interval& b) {
    if (a.start == b.start) {
        return a.end < b.end;
    } else {
        return a.start < b.start;
    }
}

class Solution {
public:
    vector<Interval> merge(vector<Interval>& intervals) {
        const int length = intervals.size();
        
        sort(intervals.begin(), intervals.end(), comp);
        vector<Interval> result;
        
        for (int i = 0; i < length; ++ i) {
            // 注意要判断 result 为空，也就是第一个元素的情况
            if (result.empty() || intervals[i].start > result.back().end) {
                result.push_back(intervals[i]);
            } else {
                result.back().end = max(result.back().end, intervals[i].end);
            }
        }
        
        return result;
    }
};

```

###　源码分析

`if (result.empty() || intervals[i].start > result.back().end)` 的条件设置很巧妙，避免了对只有一个元素的数组进行处理。

### 复杂度分析

时间复杂度是 $$O(nlogn)$$, 由于排序 in-space，空间复杂度为 $$O(1)$$.

### leetcode solution 的超时题解 待补完