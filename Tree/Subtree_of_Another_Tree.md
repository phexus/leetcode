# Subtree of Another Tree

Tags: Tree, Easy

## Problem

Given two non-empty binary trees **s** and **t**, check whether tree **t** has exactly the same structure and node values with a subtree of **s**. A subtree of **s** is a tree consists of a node in **s** and all of this node's descendants. The tree **s** could also be considered as a subtree of itself.

**Example 1:**
Given tree s:

```
     3
    / \
   4   5
  / \
 1   2
```

Given tree t:

```
   4 
  / \
 1   2
```

Return true, because t has the same structure and node values with a subtree of s.

**Example 2:**
Given tree s:

```
     3
    / \
   4   5
  / \
 1   2
    /
   0
```

Given tree t:

```
   4
  / \
 1   2
```

Return false.

## Solution

本题与 `剑指 offer` 面试题 18 类似。前序遍历，每次判读是否是子树也要递归着判读，这里引入了辅助函数。

不过要特别注意的是，这里的子树指的必须是完全相同的才行，而在书上对于 example2 认为也要返回 true。

所以这里的判断比较精髓 `if (!t && !s) return true;   if (!t || !s) return false;`

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
    bool isSubtree(TreeNode* s, TreeNode* t) {
        if (!s || !t) return false;
        
        return help(s, t) || isSubtree(s -> left, t) || isSubtree(s -> right, t);
    }
    
    bool help(TreeNode* s, TreeNode* t) {
        if (!t && !s) return true;
        if (!t || !s) return false;
        if (s -> val != t -> val) return false;
        
        return help(s -> left, t -> left) && help(s -> right, t -> right);
    }
};
```

