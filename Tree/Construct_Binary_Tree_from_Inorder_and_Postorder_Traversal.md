# Construct Binary Tree from Inorder and Postorder Traversal

Tags: Tree, Medium

## Problem

Given inorder and postorder traversal of a tree, construct the binary tree.

Note:
You may assume that duplicates do not exist in the tree.

For example, given

inorder = [9,3,15,20,7]
postorder = [9,15,7,20,3]
Return the following binary tree:

    3
   / \
  9  20
    /  \
   15   7

##　解法

本题与　Construct Binary Tree from Preorder and Inorder Traversal　解题思路完全相同，这里只贴出解答

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
    unordered_map<int, int> map;
    TreeNode* buildTree(vector<int>& inorder, vector<int>& postorder) {
        if (inorder.empty() || postorder.empty()) return NULL;
        
        for (int i = 0; i < inorder.size(); ++i) {
            map[inorder[i]] = i;
        }
        
        int* postorderStart = &postorder[0];
        int* postorderEnd = postorderStart + postorder.size() - 1;
        int* inorderStart =&inorder[0];
        int* inorderEnd =inorderStart + inorder.size() - 1;
        
        return buildTree(postorderStart, postorderEnd, inorderStart, inorderEnd);
    }
    
    TreeNode* buildTree(int* postorderStart, int* postorderEnd, int* inorderStart, int* inorderEnd) {
        TreeNode* root = new TreeNode(*postorderEnd);
        
        if (postorderStart > postorderEnd) return NULL;
        
        int leftLength = map[*postorderEnd] - map[*inorderStart];
        
        root -> left = buildTree(postorderStart, postorderStart + leftLength - 1, inorderStart, inorderStart + leftLength - 1);
        root -> right = buildTree(postorderStart + leftLength, postorderEnd - 1, inorderStart + leftLength + 1, inorderEnd);
        
        return root;
    }
};
```