# Balanced Binary Tree

Tags: Tags: Tree, Easy

## Problem

Given a binary tree, determine if it is height-balanced.

For this problem, a height-balanced binary tree is defined as:

a binary tree in which the depth of the two subtrees of every node never differ by more than 1.

Example 1:

Given the following tree [3,9,20,null,null,15,7]:

    3
   / \
  9  20
    /  \
   15   7
Return true.

Example 2:

Given the following tree [1,2,2,3,3,null,null,4,4]:

       1
      / \
     2   2
    / \
   3   3
  / \
 4   4
Return false.

## 解法1 - 记录 depth + 递归

本题与 `剑指offer` 的面试题 39 相同，是求二叉树的最大深度的衍生题目，详细介绍了两种解法，下面给出的是优化过的只遍历一次的解法
书中 depth 是指针，这里是引用

该解法是DFS，可以认为是后续遍历，先递归再处理 depth 和返回值

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
    bool isBalanced(TreeNode* root) {
        int depth = 0;
        
        return isBalanced(root, depth);
    }
    
    bool isBalanced(TreeNode* root, int& depth) {
        if (! root) {
            depth = 0;
            return true;
        }
        
        int left, right;
        if (isBalanced(root -> left, left) && isBalanced(root -> right, right)) {
            int diff = left - right;
            if (diff <= 1 && diff >= -1) {
                depth = 1 + max(left, right);
                return true;
            }
        }
        return false;
    }
};
```

##　解法２ - bottom up - 自底向上

[leetcode discuss](https://leetcode.com/problems/balanced-binary-tree/discuss/) 有详细的论述第二种方法，基于 DFS，不用记录 depth ，也实现了一次遍历（，其实也记录了depth，只是作为返回值了）

注意，这里把 -1 作为了异常值，代表不平衡，这样非常巧妙的用一个返回值既表示了深度（height），也表示了是否平衡，一旦不平衡，return -1，加速递归收敛

抓住两个核心：子树的高度以及高度之差，返回值应该包含这两种信息。

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
    bool isBalanced(TreeNode* root) {
        return dfsHeight(root) != -1;
    }
    
    int dfsHeight(TreeNode* root) {
        if (! root) return 0;
        
        int left = dfsHeight(root -> left);
        int right = dfsHeight(root -> right);
        
        if (left == -1 || right == -1 || abs(left -right) > 1) return -1;
        
        return 1 + max(left, right);
    }
};
```

###　复杂度分析

因为是 DFS，所以遍历所有节点各一次，时间复杂度为 $$O(n)$$, 使用了部分辅助变量，考虑到递归栈的消耗，空间复杂度为 $$O(n)$$