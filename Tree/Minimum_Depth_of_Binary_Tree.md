# Minimum Depth of Binary Tree

Tags: Tree, Easy

## Problem

Given a binary tree, find its minimum depth.

The minimum depth is the number of nodes along the shortest path from the root node down to the nearest leaf node.

## 解法1 - 递归

本题的递归写法要注意斜树的存在，只有一个分支的时候，只能以该分支的长度作为最小长度。这里参考了[解法](https://www.nowcoder.com/questionTerminal/e08819cfdeb34985a8de9c4e6562e724) 非常巧妙，`对斜树这种特殊情况用return l+r+1来处理，很巧妙。我就只能如果左子树深度为0，则将左子树深度置位整数最大值，如果右子树深度为0，则将右子树深度置为整数最大值。楼主这个更巧妙，赞！`

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
    int minDepth(TreeNode* root) {
        if (! root) return 0;
        
        int leftMinDepth = minDepth(root -> left);
        int rightMinDepth = minDepth(root -> right);
        
        if (! leftMinDepth || ! rightMinDepth) return 1 + leftMinDepth + rightMinDepth;
        
        return 1 + min(leftMinDepth, rightMinDepth);
    }
};

// 另一种写法,也比较简洁

class Solution {
public:
    int minDepth(TreeNode* root) {
        if(! root) return 0;
        if(! root->left) return minDepth(root->right)+1;
        if(! root->right) return minDepth(root->left)+1;
        return min(minDepth(root->left),minDepth(root->right))+1;
    }
};
```

##　解法２ - 非递归 - 广度优先（层次遍历）

仿照了 Maximum Depth of Binary Tree 的解法3，层序遍历，找到第一个左右结点都为null的情况，就返回，这就是第一个叶子节点，也就是最小深度。
特别注意和 Maximum Depth of Binary Tree 的区别，尤其是 `++ minDepth` 的时机


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
    int minDepth(TreeNode* root) {
        if (! root) return 0;
        
        queue<TreeNode*> q;
        q.push(root);
        int minDepth = 0;
        
        while (! q.empty()) {
            int size = q.size();
            ++ minDepth;
            
            for (int i = 0; i < size; ++ i) {
                TreeNode* node = q.front();
                q.pop();
                
                if (! node-> left && ! node -> right) return minDepth;
                if (node -> left) q.push(node -> left);
                if (node -> right) q.push(node -> right);
            }
        }
        
        return minDepth;
    }
};
```

### 复杂度分析

最坏情况下空间复杂度为 $$O(n)$$, 遍历每一个节点，时间复杂度为 $$O(n)$$