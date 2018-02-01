# Binary Tree Ineorder Traversal

Tags: Tags: Tree, Medium

## Problem

Given a binary tree, return the inorder traversal of its nodes' values.

For example:
Given binary tree [1,null,2,3],
   1
    \
     2
    /
   3
return [1,3,2].

Note: Recursive solution is trivial, could you do it iteratively?

## 解法1 - 递归

### 分治法 - Divide and Conquer

```cpp
/**
 * Definition for binary tree
 * struct TreeNode {
 *     int val;
 *     TreeNode *left;
 *     TreeNode *right;
 *     TreeNode(int x) : val(x), left(NULL), right(NULL) {}
 * };
 */
class Solution {
public:
    vector<int> inorderTraversal(TreeNode *root) {
        vector<int> result;
        
        if (root) {
            vector<int> left = inorderTraversal(root -> left);
            vector<int> right = inorderTraversal(root -> right);
            
            result.insert(result.end(), left.begin(), left.end());
            result.push_back(root -> val);
            result.insert(result.end(), right.begin(), right.end());
        }
        
        return result;
    }
};
```

### 遍历法 - traversal

```cpp
/**
 * Definition for binary tree
 * struct TreeNode {
 *     int val;
 *     TreeNode *left;
 *     TreeNode *right;
 *     TreeNode(int x) : val(x), left(NULL), right(NULL) {}
 * };
 */
class Solution {
public:
    vector<int> inorderTraversal(TreeNode *root) {
        vector<int> result;
        inorderTraversal(root, result);
        return result;
    }
    void inorderTraversal(TreeNode *root, vector<int>& result) {
        if (root) {
            inorderTraversal(root -> left, result);
            result.push_back(root -> val);
            inorderTraversal(root -> right, result);
        }
    }
};
```

## 解法2 - 迭代

###　方法１

```cpp
/**
 * Definition for binary tree
 * struct TreeNode {
 *     int val;
 *     TreeNode *left;
 *     TreeNode *right;
 *     TreeNode(int x) : val(x), left(NULL), right(NULL) {}
 * };
 */
class Solution {
public:
    vector<int> inorderTraversal(TreeNode *root) {
        vector<int> result;
        
        TreeNode* p = root;
        stack<TreeNode*> s;
        
        while (p || ! s.empty()) {
            while (p) {
                s.push(p);
                p = p -> left;
            }
            
            p = s.top();
            s.pop();
            result.push_back(p -> val);
            p = p -> right;
        }
        
        return result;
    }
};
```

### 方法２ - Morris Traversal

中序遍历和前序遍历不同，不能套用前序遍历的解法２的方法２，这里的方法采用了`线索二叉树(Threaded Binary Tree)`的思想

1. 初始化 curr 为 root
2. 当 curr 不为空的情况下：
    若 curr 无左子结点，访问 curr 结点，并将 curr 移至右子结点，curr = curr -> right
    若 curr 有左子结点，将 curr 接到 curr 的左子数的最右子孙结点的右结点处，将 curr 至为 原先 curr 的左子结点，并切断 curr 的左指针(为了避免成环)


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
    vector<int> inorderTraversal(TreeNode* root) {
        vector<int> result;
        
        TreeNode* curr = root;
        TreeNode* pre;
        
        while (curr) {
            if (! curr -> left) {
                result.push_back(curr -> val);
                curr = curr -> right;
            } else {
                pre = curr -> left;
                while (pre -> right) pre = pre -> right;
                
                pre -> right = curr;
                TreeNode* temp = curr;
                curr = curr -> left;
                temp -> left = NULL;
            }
        }
        
        return result;
    }
};
```

### 复杂度

详细的分析见[leetcode解答](https://leetcode.com/problems/binary-tree-inorder-traversal/solution/)
时间复杂度 $$O(n)$$
空间复杂度 $O(n）$$$