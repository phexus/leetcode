# Find Median from Data Stream

Tags: Heap, Design

## Problem

Median is the middle value in an ordered integer list. If the size of the list is even, there is no middle value. So the median is the mean of the two middle value.

Examples: 

`[2,3,4]` , the median is `3`

`[2,3]`, the median is `(2 + 3) / 2 = 2.5`

Design a data structure that supports the following two operations:

- void addNum(int num) - Add a integer number from the data stream to the data structure.
- double findMedian() - Return the median of all elements so far.

For example:

```
addNum(1)
addNum(2)
findMedian() -> 1.5
addNum(3) 
findMedian() -> 2
```

## Solution - heap

本题与 `剑指offer` 一书的面试题 64 相同，下面的解法也是书上的，使用 STL 的 push_heap, pop_heap, vector 来实现堆。发现 [Solution](https://leetcode.com/problems/find-median-from-data-stream/solution/) 使用优先队列来做更加简洁，之后再补充。

```cpp
class MedianFinder {
public:
    /** initialize your data structure here. */
    MedianFinder() {
        
    }
    
    void addNum(int num) {
        if (((min.size() + max.size()) & 1) == 0) {
            if (!min.empty() && num > min[0]) {
                min.push_back(num);
                push_heap(min.begin(), min.end(), greater<int>());
                num = min[0];
                pop_heap(min.begin(), min.end(), greater<int>());
                min.pop_back();
            }
            max.push_back(num);
            push_heap(max.begin(), max.end(), less<int>());
            
        } else {
            if (!max.empty() && num < max[0]) {
                max.push_back(num);
                push_heap(max.begin(), max.end(), less<int>());
                num = max[0];
                pop_heap(max.begin(), max.end(), less<int>());
                max.pop_back();
            }
            min.push_back(num);
            push_heap(min.begin(), min.end(), greater<int>());
        }
    }
    
    double findMedian() {
        if (min.empty() && max.empty()) return 0.0;
        if ((min.size() + max.size()) & 1 || min.empty()) return max[0];
        else return (min[0] + max[0]) / 2.0;
    }
    
private:
    vector<int> min;
    vector<int> max;
};

/**
 * Your MedianFinder object will be instantiated and called as such:
 * MedianFinder obj = new MedianFinder();
 * obj.addNum(num);
 * double param_2 = obj.findMedian();
 */
```

