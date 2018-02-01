# Same Tree

Tags: Tree, Easy

## Problem

Given two binary trees, write a function to check if they are the same or not.

Two binary trees are considered the same if they are structurally identical and the nodes have the same value.

Example 1:

Input:     1         1
          / \       / \
         2   3     2   3

        [1,2,3],   [1,2,3]

Output: true
Example 2:

Input:     1         1
          /           \
         2             2

        [1,2],     [1,null,2]

Output: false
Example 3:

Input:     1         1
          / \       / \
         2   1     1   2

        [1,2,1],   [1,1,2]

Output: false

## 解法

本题比较简单，DFS 即可。需要注意的是一些边界条件的判断，若2个结点均为空，也应该返回 true

```cpp
/**
 * Definition for a binary tree node.
 * struct TreeNode {
 *     int val;
 *     TreeNode *left;
 *     TreeNode *right;
 *     TreeNode(int x) : val(x), left(NULL), right(NULL) {}
 * };
 */
class Solution {
public:
    bool isSameTree(TreeNode* p, TreeNode* q) {
        if (! p && ! q) return true;
        else if (! p || ! q) return false;
        else if (p -> val != q -> val) return false;
        
        return isSameTree(p -> left, q -> left) && isSameTree(p -> right, q -> right);
    }
};
```

###　复杂度分析

DFS， 时间复杂度 $$O(n)$$，空间复杂度取决于递归层数，最差情况下 $$O(n)$$