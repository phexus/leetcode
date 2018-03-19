# Segment Tree Build

Tags: Segment Tree, Binary Tree, Medium, Lintcode

## Problem

The structure of Segment Tree is a binary tree which each node has two attributes `start` and `end` denote an segment / interval.

*start* and *end* are both integers, they should be assigned in following rules:

- The root's *start* and *end* is given by `build` method.
- The left child of node A has `start=A.left, end=(A.left + A.right) / 2`.
- The right child of node A has `start=(A.left + A.right) / 2 + 1, end=A.right`.
- if *start* equals to *end*, there will be no children for this node.

Implement a `build` method with two parameters *start* and *end*, so that we can create a corresponding segment tree with every node has the correct *start* and *end* value, return the root of this segment tree.

**Clarification**

Segment Tree (a.k.a Interval Tree) is an advanced data structure which can support queries like:

- which of these intervals contain a given point
- which of these points are in a given interval

See wiki:
[Segment Tree](https://en.wikipedia.org/wiki/Segment_tree)
[Interval Tree](https://en.wikipedia.org/wiki/Interval_tree)

Example

Given `start=0, end=3`. The segment tree will be:

```
               [0,  3]
             /        \
      [0,  1]           [2, 3]
      /     \           /     \
   [0, 0]  [1, 1]     [2, 2]  [3, 3]

```

Given `start=1, end=6`. The segment tree will be:

```
               [1,  6]
             /        \
      [1,  3]           [4,  6]
      /     \           /     \
   [1, 2]  [3,3]     [4, 5]   [6,6]
   /    \           /     \
[1,1]   [2,2]     [4,4]   [5,5]
```

## Solution

递归着来做，要注意的一点是一定要单独处理 `if (start == end) return root;` 否则会陷入死循环。

```cpp
/**
 * Definition of SegmentTreeNode:
 * class SegmentTreeNode {
 * public:
 *     int start, end;
 *     SegmentTreeNode *left, *right;
 *     SegmentTreeNode(int start, int end) {
 *         this->start = start, this->end = end;
 *         this->left = this->right = NULL;
 *     }
 * }
 */

class Solution {
public:
    /*
     * @param start: start value.
     * @param end: end value.
     * @return: The root of Segment Tree.
     */
    SegmentTreeNode * build(int start, int end) {
        if (start > end) return NULL;
        SegmentTreeNode* root = new SegmentTreeNode(start, end);
        
        if (start == end) return root;
        
        root -> left = build(start, (start + end) / 2);
        root -> right = build((start + end) / 2 + 1, end);
        
        return root;
    }
};
```

