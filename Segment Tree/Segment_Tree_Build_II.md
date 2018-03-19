## Segment Tree Build II

Tags: Segment Tree, Binary Tree, Medium, Lintcode

## Problem

**Clarification**

Segment Tree (a.k.a Interval Tree) is an advanced data structure which can support queries like:

- which of these intervals contain a given point
- which of these points are in a given interval

See wiki:
[Segment Tree](https://en.wikipedia.org/wiki/Segment_tree)
[Interval Tree](https://en.wikipedia.org/wiki/Interval_tree)

Example

Given `[3,2,1,4]`. The segment tree will be:

```
                 [0,  3] (max = 4)
                  /            \
        [0,  1] (max = 3)     [2, 3]  (max = 4)
        /        \               /             \
[0, 0](max = 3)  [1, 1](max = 2)[2, 2](max = 1) [3, 3] (max = 4)
```

## Solution 1 - 即将 TLE

这里偷懒了，[参考](http://blog.csdn.net/yangyangyang20092010/article/details/47863477)用了 stl 的 max_element 来获取最大元素，这样的时间复杂度有点高，感觉更好的方法是提前把最大值，次大值和其对应的 id 建立联系存下来，之后每次判断，id 是否在 start end 的范围内即可。

```cpp
/**
 * Definition of SegmentTreeNode:
 * class SegmentTreeNode {
 * public:
 *     int start, end, max;
 *     SegmentTreeNode *left, *right;
 *     SegmentTreeNode(int start, int end, int max) {
 *         this->start = start;
 *         this->end = end;
 *         this->max = max;
 *         this->left = this->right = NULL;
 *     }
 * }
 */

class Solution {
public:
    /**
     * @param A: a list of integer
     * @return: The root of Segment Tree
     */
    SegmentTreeNode * build(vector<int> &A) {
        return help(A, 0, A.size() - 1);
    }
    
    SegmentTreeNode* help(vector<int>& A, int start, int end) {
        if (start > end) return NULL;
        int max = * std :: max_element(A.begin() + start, A.begin() + end + 1);
        
        SegmentTreeNode* root = new SegmentTreeNode(start, end, max);
        if (start < end) {
            root -> left = help(A, start, (start + end) / 2);
            root -> right = help(A, (start + end) / 2 + 1, end); 
        }
        
        return root;
    }
};
```

## Solution 2

上面的解法来求最大值明显太笨了，[九章算法](http://www.jiuzhang.com/solution/segment-tree-build-ii/#tag-highlight-lang-cpp) 给出了非常机智的解法。

先用 `SegmentTreeNode* root = new SegmentTreeNode(start, end, A[start]);` A[start] 来占位，如果子树的 max 比自己大就更新。

自底向上，不断更新 max

从 1446 ms 缩减到 430 ms

```cpp
/**
 * Definition of SegmentTreeNode:
 * class SegmentTreeNode {
 * public:
 *     int start, end, max;
 *     SegmentTreeNode *left, *right;
 *     SegmentTreeNode(int start, int end, int max) {
 *         this->start = start;
 *         this->end = end;
 *         this->max = max;
 *         this->left = this->right = NULL;
 *     }
 * }
 */

class Solution {
public:
    /**
     * @param A: a list of integer
     * @return: The root of Segment Tree
     */
    SegmentTreeNode * build(vector<int> &A) {
        return help(A, 0, A.size() - 1);
    }
    
    SegmentTreeNode* help(vector<int>& A, int start, int end) {
        if (start > end) return NULL;
        
        SegmentTreeNode* root = new SegmentTreeNode(start, end, A[start]);
        if (start < end) {
            root -> left = help(A, start, (start + end) / 2);
            root -> right = help(A, (start + end) / 2 + 1, end);
            
            if (root -> left -> max > root -> max) root -> max = root -> left -> max;
            if (root -> right -> max > root -> max) root -> max = root -> right -> max;
        }
        
        return root;
    }
};
```

