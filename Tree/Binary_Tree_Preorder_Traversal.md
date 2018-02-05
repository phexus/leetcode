# Binary Tree Preorder Traversal

Tags: Tags: Tree, Medium

## Problem

Given a binary tree, return the preorder traversal of its nodes' values.

For example:
Given binary tree [1,null,2,3],
   1
    \
     2
    /
   3
return [1,2,3].

## 解法1 - 递归

[参考解法](https://algorithm.yuanbin.me/zh-hans/binary_tree/binary_tree_preorder_traversal.html) 中提到，递归时对返回结果的处理方式不同可进一步细分为遍历和分治两种方法。

### 分治法 - Divide and Conquer

分治法递归构造了 left，right 的 vector，即分开算各自的结果，再按照顺序 merge 到最终结果。
注意 C++ 的 vector 的 push_back 与 insert 的不同

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
    vector<int> preorderTraversal(TreeNode *root) {
        vector<int> result;
        if (root) {
            // Dicvide
            vector<int> left = preorderTraversal(root -> left);
            vector<int> right = preorderTraversal(root -> right);
            
            // Merge
            result.push_back(root -> val);
            result.insert(result.end(), left.begin(), left.end());
            result.insert(result.end(), right.begin(),right.end());
        }
        
        return result;
    }
};
```

### 遍历法 - traversal

需要定义新的函数，参数为 root 和结果向量，将结果作为参数传入递归函数。
注意：传值时使用 vector 的引用
（其实还可以使用类的私有变量保存最终结果，这样就不需要传递结果向量了）
别忘了递归前要检查 root 是否为空

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
    vector<int> preorderTraversal(TreeNode *root) {
        vector<int> result;
        
        preorderTraversal(root, result);
        return result;
    }
    
    void preorderTraversal(TreeNode* root, vector<int>& result) {
        if (root) { // 这里要对对 root 检查是否为空！
            result.push_back(root -> val);
            preorderTraversal(root -> left, result);
            preorderTraversal(root -> right, result);
        }
    }
};
```

### 复杂度分析

两种递归的时间复杂度均为 $$O(n)$$, 空间复杂度最差（斜树）为 $$O(n)$$。不过，遍历的方法未使用额外空间，分治的方法生成了临时变量。

## 解法2 - 迭代

给出了两种迭代的具体方法，注意都要方法 2 要提前判断 root 为空的情况（方法1 其实不用）

### 方法１

[参考解法](https://www.cnblogs.com/SHERO-Vae/p/5800363.html)

对于任一结点P：
1. 访问结点P，并将结点P入栈;
2. 判断结点P的左孩子是否为空，若为空，则取栈顶结点并进行出栈操作，并将栈顶结点的右孩子置为当前的结点P，循环至 1 ;若不为空，则将P的左孩子置为当前的结点P;
3. 直到P为NULL并且栈为空，则遍历结束

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
    vector<int> preorderTraversal(TreeNode* root) {
        vector<int> result;
        if (! root) return result;
        
        TreeNode* p = root;
        stack<TreeNode*> s;
        
        while (p || ! s.empty()) {
            while (p) {
                result.push_back(p -> val);
                s.push(p);
                p = p -> left;
            }
            
            p = s.top();
            s.pop();
            p = p -> right;
        }
        
        return result;
    }
};
```

### 方法２

1. 对root进行异常处理
2. 将root压入栈
3. 访问当前栈顶元素(首先取出栈顶元素，随后pop掉栈顶元素)并存入最终结果
4. 将右、左节点分别压入栈内，以便取元素时为先左后右。
5. 循环至 3 ，终止条件为栈s为空，所有元素均已处理完

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
    vector<int> preorderTraversal(TreeNode* root) {
        vector<int> result;
        if (! root) return result;
        
        stack<TreeNode*> s;
        s.push(root);
        
        while (! s.empty()) {
            TreeNode* p = s.top();
            s.pop();
            
            result.push_back(p -> val);
            
            if (p -> right) s.push(p -> right);
            if (p -> left) s.push(p -> left);
        }
        
        return result;
    }
};
```

###　复杂度分析

使用辅助栈，最坏情况下栈空间与节点数相等，其空间复杂度为 $$O(n)$$, 对每个节点遍历一次，时间复杂度为 $$O(n)$$.
