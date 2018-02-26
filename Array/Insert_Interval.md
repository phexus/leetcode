# Insert Interval

Tags: Array, Sort, Hard

## Problem

Given a set of non-overlapping intervals, insert a new interval into the intervals (merge if necessary).

You may assume that the intervals were initially sorted according to their start times.

Example 1:
Given intervals [1,3],[6,9], insert and merge [2,5] in as [1,5],[6,9].

Example 2:
Given [1,2],[3,5],[6,7],[8,10],[12,16], insert and merge [4,9] in as [1,2],[3,10],[12,16].

This is because the new interval [4,9] overlaps with [3,5],[6,7],[8,10].

## 解法

感觉这道题就是非常直接的做法，因为题目已经告诉了 intervals 里已经是排好序了的，依次比较 合并 插入就好了。

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
class Solution {
public:
    vector<Interval> insert(vector<Interval>& intervals, Interval newInterval) {
        vector<Interval> ans;
        
        int i = 0;
        while (i < intervals.size() && intervals[i].end < newInterval.start) ans.push_back(intervals[i++]);
        
        while (i < intervals.size() && intervals[i].start <= newInterval.end) {
            newInterval.start = min(newInterval.start, intervals[i].start);
            newInterval.end = max(newInterval.end, intervals[i].end);
            ++i;
        }
        ans.push_back(newInterval);
        
        while (i < intervals.size()) ans.push_back(intervals[i++]);
        
        return ans;
    }
};
```



## Reference

https://leetcode.com/problems/insert-interval/discuss/21602/Short-and-straight-forward-Java-solution

https://leetcode.com/problems/insert-interval/discuss/21632/Very-short-and-easy-to-understand-C++-solution

https://leetcode.com/problems/insert-interval/discuss/21599/Elegant-C++-STL-solution-using-%22equal_range%22-to-find-overlapped-intervals.

https://leetcode.com/problems/insert-interval/discuss/21622/7+-lines-3-easy-solutions

https://www.nowcoder.net/questionTerminal/02418bfb82d64bf39cd76a2902db2190

https://algorithm.yuanbin.me/zh-hans/problem_misc/insert_interval.html