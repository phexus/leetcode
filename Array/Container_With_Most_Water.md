# Container With Most Water

Tags: Array, Medium

## Question

- leetcode: [Container With Most Water](https://leetcode.com/problems/container-with-most-water/description/)
- nowcoder: [Container With Most Water](https://www.nowcoder.com/practice/c97c1400a425438fb130f54fdcef0c57?tpId=46&tqId=29167&tPage=5&rp=5&ru=/ta/leetcode&qru=/ta/leetcode/question-ranking)

### Problem Statement

Given n non-negative integers a1, a2, ..., an, where each represents a point at coordinate (i, ai). n vertical lines are drawn such that the two endpoints of line i is at (i, ai) and (i, 0). Find two lines, which together with x-axis forms a container, such that the container contains the most water.

Note: You may not slant the container and n is at least 2.

## 题解1 - 暴力解法

两个 for 循环嵌套，计算了所有的两两配对方案，（n(n + 1)/2)

```cpp
class Solution {
public:
    int maxArea(vector<int> &height) {
        const int length = height.size();
        if (length < 2) return 0;
        
        int result = 0;
        
        for (int i = 0; i < length; ++ i) {
            for (int j = i + 1; j < length; ++ j ) {
                result = max(result, (j -i) * min(height[i], height[j]));
            }
        }
        
        return result;
    }
};
```

## 题解2 - 头尾两指针同时遍历

从两边往中间找，每次放弃最短的板。
从起点和终点开始找，宽度最大，这时每移动一次其中一个点，必然宽度变小。如此一来，想求最大，只有高度增长才有可能做到，去掉限制----短板，即放弃高度较小的点。

```cpp
class Solution {
public:
    int maxArea(vector<int>& height) {
        const int length = height.size();
        if (length < 2) return 0;
        
        int front = 0;
        int end = length - 1;
        int result = 0;
        
        while (front < end) {
            result = max(result, (end - front) * min(height[front], height[end]));
            if (height[front] < height[end]) ++ front;
            else -- end;
        }
        
        return result;
    }
};


// 更简洁的写法
for(int i = 0,j = height.size()-1;i<j;)
{
     MAXI = max(min(height[i],height[j])*(j-i),MAXI);
     height[i]>height[j]?j--:i++;
}
```

### 复杂度分析

时间复杂度是 $$O(n)$$, 空间复杂度为 $$O(1)$$.