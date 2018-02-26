# Largest Rectangle in Histogram

Tags: Stack, Hard

## Problem

Given n non-negative integers representing the histogram's bar height where the width of each bar is 1, find the area of largest rectangle in the histogram.

Above is a histogram where width of each bar is 1, given height = [2,1,5,6,2,3].

The largest rectangle is shown in the shaded area, which has area = 10 unit.

For example,

![img](https://leetcode.com/static/images/problemset/histogram_area.png)

Given heights = [2,1,5,6,2,3],
return 10.

## 解法1 - 双重循环 - 暴力比较 - 超时！！

以下两种解法原理相同，即一个一个比较，时间复杂度为 $$O(n^2)$$ 在 leetcode 上都会超时，不推荐！！！

```cpp
class Solution {
public:
    int largestRectangleArea(vector<int>& heights) {
        int result = 0;
        
        for (int i = 0; i < heights.size(); ++i) {
            int len = 1;
            
            for (int j = i - 1; j >= 0; --j) {
                if (heights[j] >= heights[i]) ++len;
                else break;
            }
            
            for (int j = i + 1; j < heights.size(); ++j) {
                if (heights[j] >= heights[i]) ++len;
                else break;
            }
            
            result = max(result, len * heights[i]);
        }
        
        return result;
    }
};
```

```cpp
class Solution {
public:
    int largestRectangleArea(vector<int>& heights) {
        int result = 0;
        int size = heights.size();
        
        for (int i = 0; i < size; ++i) {
            int len = 0;
            int thisMinHeight = heights[i];
            
            for (int j = i; j < size; ++j) {
                ++len;
                if (heights[j] < thisMinHeight) thisMinHeight = heights[j];
                
                result = max(result, len * thisMinHeight);
            }
        }
        return result;
    }
};
```

##　解法２

用栈来存，也不好理解，需要画图举出几个例子就好理解了。

用堆栈计算每一块板能延伸到的左右边界
对每一块板
 堆栈顶矮，这一块左边界确定，入栈
 堆栈顶高，堆栈顶右边界确定，出栈，计算面积
 入栈时左边界确定
 出栈时右边界确定
 堆栈里元素是严格递增的
本质：中间的短板没有用！
复杂度 O(n)

i - 1 作为右边界，不断向左延伸，计算每个面积，当 st 为空时，取 -1

```cpp
class Solution {
public:
    int largestRectangleArea(vector<int>& heights) {
        int size = heights.size();
        int result = 0;
        
        stack<int> st;
        
        for (int i = 0; i < size; ++i) {
            while (!st.empty() && heights[st.top()] >= heights[i]) {
                int h = heights[st.top()];
                st.pop();
                // 从右往左依次算面积， i - 1 是固定的，st.top() 是变化的
                result = max(result, (i - 1 - (st.empty() ? -1 : st.top())) * h);
            }
            st.push(i);
        }
        
        // 以最后一个结点为右边界，依次往左算面积
        while (!st.empty()) {
            int h = heights[st.top()];
            st.pop();
            result = max(result, (size - 1 - (st.empty() ? -1 : st.top())) * h);
        }
        
        return result;
    }
};
```

### 另一种写法，栈底 -1

提前入栈 -1，用 -1 代表栈空，是减少用栈时代码嵌套复杂程度的通用方法。在更新 result 时就不用单独判断 st.empty() 的情况了。

```cpp
class Solution {
public:
    int largestRectangleArea(vector<int>& heights) {
        int size = heights.size();
        int result = 0;
        
        stack<int> st;
        st.push(-1);
        
        for (int i = 0; i < size; ++i) {
            while (st.top() != -1 && heights[st.top()] >= heights[i]) {
                int h = heights[st.top()];
                st.pop();
                result = max(result, (i - 1 - st.top()) * h);
            }
            st.push(i);
        }
        
        while (st.top() != -1) {
            int h = heights[st.top()];
            st.pop();
            result = max(result, (size - 1 - st.top()) * h);
        }
        
        return result;
    }
};
```

## 总结

感觉这些栈相关的题还是挺难的，似乎都可以用 DP 来做，之后做到 DP 时，再统一看看吧。

[参考](https://leetcode.com/problems/largest-rectangle-in-histogram/discuss/28902/5ms-O(n)-Java-solution-explained-(beats-96))
[参考](https://leetcode.com/problems/largest-rectangle-in-histogram/discuss/28900/O(n)-stack-based-JAVA-solution)
[参考](https://leetcode.com/problems/largest-rectangle-in-histogram/discuss/28905/My-concise-C++-solution-AC-90-ms)
[参考JanePZ](https://www.nowcoder.net/questionTerminal/e3f491c56b7747539b93e5704b6eca40)