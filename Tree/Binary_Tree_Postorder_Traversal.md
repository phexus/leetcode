# Binary Tree Postorder Traversal

Tags: Tags: Tree, Hard

## Problem

Given a binary tree, return the postorder traversal of its nodes' values.

For example:
Given binary tree [1,null,2,3],

   1
    \
     2
    /
   3
 

return [3,2,1].

Note: Recursive solution is trivial, could you do it iteratively?

##　解法１ - 递归

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
    vector<int> postorderTraversal(TreeNode *root) {
        vector<int> result;
        
        if (root) {
            vector<int> left = postorderTraversal(root -> left);
            vector<int> right = postorderTraversal(root -> right);
            
            result.insert(result.end(), left.begin(), left.end());
            result.insert(result.end(), right.begin(), right.end());
            result.push_back(root -> val);
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
    vector<int> postorderTraversal(TreeNode *root) {
        vector<int> result;
        postorderTraversal(root, result);
        return result;
    }
    
    void postorderTraversal(TreeNode *root, vector<int>& result) {
        if (root) {
            postorderTraversal(root -> left, result);
            postorderTraversal(root -> right, result);
            result.push_back(root -> val);
        }
    }
};
```

## 解法2 - 迭代

非递归实现，[这里](https://www.cnblogs.com/SHERO-Vae/p/5800363.html) 提供了两种思路，这里选用了第二种，不用构造新的结构

后续遍历的非递归实现还是比较复杂的，`要保证根结点在左孩子和右孩子访问之后才能访问，因此对于任一结点P，先将其入栈。如果P不存在左孩子和右孩子，则可以直接访问它；或者P存 在左孩子或者右孩子，但是其左孩子和右孩子都已被访问过了，则同样可以直接访问该结点。若非上述两种情况，则将P的右孩子和左孩子依次入栈，这样就保证了 每次取栈顶元素的时候，左孩子在右孩子前面被访问，左孩子和右孩子都在根结点前面被访问。`

该实现有很多小细节需要注意，如判断是否访问过 `curr -> left == pre || curr -> right == pre` 这里为或，而且还得确保 pre 不为空。只有 pre 不为空，才能证明了已经进入过出栈的那个分支了，否则会有错。

`将递归写成迭代的难点在于如何在迭代中体现递归本质及边界条件的确立，可使用简单示例和纸上画出栈调用图辅助分析。`

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
    vector<int> postorderTraversal(TreeNode* root) {
        vector<int> result;
        if (! root) return result;
        
        stack<TreeNode*> s;
        s.push(root);
        TreeNode* pre = NULL;
        
        while(! s.empty()) {
            TreeNode* curr = s.top(); // 这里只取栈顶，但不弹出
            
            bool noChild = true;
            if (curr -> left || curr -> right) noChild = false;
            
            bool visited = false;
            // 注意这里的条件是 或 ！！！一定要判断 pre 不为空
            if (pre && (curr -> left == pre || curr -> right == pre)) visited = true;
            
            if (noChild || visited) {
                result.push_back(curr -> val);
                s.pop();
                pre = curr;
            } else {
                if (curr -> right) s.push(curr -> right);
                if (curr -> left) s.push(curr -> left);
            }
        }
        
        return result;
    }
};
```

###　复杂度

最坏情况下栈内存储所有节点，空间复杂度近似为 $$O(n)$$, 每个节点遍历两次或以上，时间复杂度近似为 $$O(n)$$.

## 解法３ - 迭代 - 反转先序遍历

[参考](https://algorithm.yuanbin.me/zh-hans/binary_tree/binary_tree_postorder_traversal.html) 给出了一个非常机智的解法：
`要想得到『左右根』的后序遍历结果，我们发现只需将『根右左』的结果转置即可，而先序遍历通常为『根左右』，故改变『左右』的顺序即可，所以如此一来后序遍历的非递归实现起来就非常简单了。`

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
    vector<int> postorderTraversal(TreeNode *root) {
        vector<int> result;
        if (! root) return result;
        
        stack<TreeNode*> s;
        s.push(root);
        
        while (! s.empty()) {
            TreeNode* p = s.top();
            s.pop();
            
            result.push_back(p -> val);
            // 注意这里入栈顺序
            if (p -> left) s.push(p -> left);
            if (p -> right) s.push(p -> right);
        }
        
        reverse(result.begin(), result.end());
        return result;
    }
};
```

###　复杂度

同先序遍历。
