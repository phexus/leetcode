# Segment Tree Modify

Tags: Segment Tree, Binary Tree, Medium, Lintcode

## Problem

For a `Maximum Segment Tree`, which each node has an extra value `max`to store the maximum value in this node's interval.

Implement a `modify` function with three parameter `root`, `index` and `value` to change the node's value with **\*[start, end] = [index, index]*** to the new given value. Make sure after this change, every node in segment tree still has the **max** attribute with the correct value.

##### ** Notice

We suggest you finish problem [Segment Tree Build](http://www.lintcode.com/problem/segment-tree-build/) and [Segment Tree Query](http://www.lintcode.com/problem/segment-tree-query/)first.

**Example**

For segment tree:

```
                      [1, 4, max=3]
                    /                \
        [1, 2, max=2]                [3, 4, max=3]
       /              \             /             \
[1, 1, max=2], [2, 2, max=1], [3, 3, max=0], [4, 4, max=3]

```

if call `modify(root, 2, 4)`, we can get:

```
                      [1, 4, max=4]
                    /                \
        [1, 2, max=4]                [3, 4, max=3]
       /              \             /             \
[1, 1, max=2], [2, 2, max=4], [3, 3, max=0], [4, 4, max=3]

```

**or** call `modify(root, 4, 0)`, we can get:

```
                      [1, 4, max=2]
                    /                \
        [1, 2, max=2]                [3, 4, max=0]
       /              \             /             \
[1, 1, max=2], [2, 2, max=1], [3, 3, max=0], [4, 4, max=0]

```

**Challenge** ****

Do it in `O(h)` time, h is the height of the segment tree.

## Solution

也可以加一句判断，确保能够精确到只递归一个分支

```cpp
        if (index <= root -> left -> end) modify(root -> left, index, value);
        else modify(root -> right, index, value);
```

最后的更新 max 和 Segment Tree Build II 不同，不能和自己原来的值比较，只需要选出子结点最大的即可。

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
     * @param index: index.
     * @param value: value
     * @return: nothing
     */
    void modify(SegmentTreeNode * root, int index, int value) {
        if (!root || root -> start > index || root -> end < index) return;
        
        if (root -> start == index && root -> end == index) {
            root -> max = value;
            return;
        }
        
        modify(root -> left, index, value);
        modify(root -> right, index, value);
        
        root -> max = root -> left -> max > root -> right -> max ? 
                      root -> left -> max : root -> right -> max;
    }
};
```

