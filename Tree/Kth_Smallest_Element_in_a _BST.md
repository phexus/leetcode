# Kth Smallest Element in a BST

Tags: Tree, Binary Search

## Problem

Given a binary search tree, write a function `kthSmallest` to find the **k**th smallest element in it.

**Note: **
You may assume k is always valid, 1 ≤ k ≤ BST's total elements.

**Follow up:**
What if the BST is modified (insert/delete operations) often and you need to find the kth smallest frequently? How would you optimize the kthSmallest routine?

## Solution 1 - 递归 - 中序 

本题与`剑指 offer` 面试题 63 相同，典型的中序遍历问题，不过递归的写法要注意返回值。

下面的赋值函数定义为 `TreeNode* help(TreeNode* root, int& k)` ，上层调用本层的结果作为前一个元素

`if (k == 0) return ans;` 当 k == 0 时代表已经找到了，所以加速退出递归，直接返回 ans

`if (--k == 0) return root;` 则是代表本轮元素是结果，所以直接返回本次结果 root，上层调用会收到 ans 加速退出递归。

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
    int kthSmallest(TreeNode* root, int k) {
        if (!root || !k) return 0;
        TreeNode* ans = help(root, k);
        return ans ? ans -> val : 0;
    }
    
    TreeNode* help(TreeNode* root, int& k) {
        if (!root) return NULL;
        
        TreeNode* ans = help(root -> left, k);
        if (k == 0) return ans;
        if (--k == 0) return root;
        
        ans = help(root -> right, k);
        return ans;
    }
};
```

