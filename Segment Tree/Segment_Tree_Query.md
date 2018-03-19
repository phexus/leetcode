# Segment Tree Query

Tags: Segment Tree, Binary Tree, Medium, Lintcode

## Problem

For an integer array (index from 0 to n-1, where n is the size of this array), in the corresponding SegmentTree, each node stores an extra attribute `max` to denote the maximum number in the interval of the array (index from start to end).

Design a `query` method with three parameters `root`, `start` and `end`, find the maximum number in the interval [start, end] by the given root of segment tree.

##### ** Notice

It is much easier to understand this problem if you finished [Segment Tree Build](http://www.lintcode.com/problem/segment-tree-build/)first.

**Example**

For array `[1, 4, 2, 3]`, the corresponding Segment Tree is:

```
                  [0, 3, max=4]
                 /             \
          [0,1,max=4]        [2,3,max=3]
          /         \        /         \
   [0,0,max=1] [1,1,max=4] [2,2,max=2], [3,3,max=3]

```

query(root, 1, 1), return `4`

query(root, 1, 2), return `4`

query(root, 2, 3), return `3`

query(root, 0, 2), return `4`

## Solution

线段树的 query 和区间密切相关，当前节点的区间范围与待求范围不交时，则返回最小值作为标志方便后面比较；

当前结点区间范围在待求范围内是直接返回，证明是待求范围的一部分，所以也就没有必要进一步递归来寻找更小的范围了。其余没有囊括的范围肯定会在其他分支。

分别向左向右递归搜寻，返回左右分支最大的，最终每个分支都会递归到成为待求范围的一部分为止

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
     * @param root: The root of segment tree.
     * @param start: start value.
     * @param end: end value.
     * @return: The maximum number in the interval [start, end]
     */
    int query(SegmentTreeNode * root, int start, int end) {
        if (root -> start > end || root -> end < start) return 0x80000000;
        
        if (root -> start >= start && root -> end <= end) return root -> max;
        
        int l = query(root -> left, start, end);
        int r = query(root -> right, start, end);
        return max(l, r);
    }
};
```

