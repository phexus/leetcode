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

上面的解法其实改变了树的形状，更优的解法[参考](http://www.cnblogs.com/AnnieKim/archive/2013/06/15/morristraversal.html)

1. 如果当前节点的左孩子为空，则输出当前节点并将其右孩子作为当前节点。

2. 如果当前节点的左孩子不为空，在当前节点的左子树中找到当前节点在中序遍历下的前驱节点。

   a) 如果前驱节点的右孩子为空，将它的右孩子设置为当前节点。当前节点更新为当前节点的左孩子。

   b) 如果前驱节点的右孩子为当前节点，将它的右孩子重新设为空（恢复树的形状）。输出当前节点。当前节点更新为当前节点的右孩子。

3. 重复以上1、2直到当前节点为空。

```cpp
void inorderMorrisTraversal(TreeNode *root) {
    TreeNode *cur = root, *prev = NULL;
    while (cur != NULL)
    {
        if (cur->left == NULL)          // 1.
        {
            printf("%d ", cur->val);
            cur = cur->right;
        }
        else
        {
            // find predecessor
            prev = cur->left;
            while (prev->right != NULL && prev->right != cur)
                prev = prev->right;

            if (prev->right == NULL)   // 2.a)
            {
                prev->right = cur;
                cur = cur->left;
            }
            else                       // 2.b)
            {
                prev->right = NULL;
                printf("%d ", cur->val);
                cur = cur->right;
            }
        }
    }
}
```

### 复杂度

详细的分析见[leetcode解答](https://leetcode.com/problems/binary-tree-inorder-traversal/solution/)
时间复杂度 $$O(n)$$
空间复杂度 $O(1)$$$