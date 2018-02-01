# Maximum Depth of Binary Tree

Tags: Tree, Easy

## Problem

Given a binary tree, find its maximum depth.

The maximum depth is the number of nodes along the longest path from the root node down to the farthest leaf node.

For example:
Given binary tree [3,9,20,null,null,15,7],

    3
   / \
  9  20
    /  \
   15   7
return its depth = 3.

## 解法1 - 递归

本题与 剑指offer 面试题39 相同，用递归来解是最简洁也最好理解的。
`使用深度优先搜索判断左右子树的深度孰大孰小即可，从根节点往下一层树的深度即自增1，遇到NULL时即返回0`。

```cpp
/**
 * Definition for a binary tree node.
 * struct TreeNode {
 *     int val;
 *     struct TreeNode *left;
 *     struct TreeNode *right;
 * };
 */
int maxDepth(struct TreeNode* root) {
    if (! root) return 0;
    
    int leftDepth = maxDepth(root -> left);
    int rightDepth = maxDepth(root -> right);
    
    return leftDepth > rightDepth ? leftDepth + 1 : rightDepth + 1;
    
}
```

### 复杂度分析

由于对每个节点都会使用一次maxDepth，故时间复杂度为 $$O(n)$$, 树的深度最大为 $$n$$, 最小为 $$log_2 n$$, 故空间复杂度介于 $$O(logn)$$ 和 $$O(n)$$ 之间

## 解法2 - 迭代 - 显示栈

使用递归可能会导致栈空间溢出。
[参考解法](https://algorithm.yuanbin.me/zh-hans/binary_tree/maximum_depth_of_binary_tree.html)中给出了两种非递归，迭代的解法。这里先介绍 显示栈 的方法，显式栈空间(使用堆内存)来代替之前的隐式栈空间。

这里用到了 非递归的方式 去实现树的三序遍历，需要对递归与非递归的先、中、后序遍历做到烂熟于胸，[参考资料](https://www.cnblogs.com/SHERO-Vae/p/5800363.html) 其中后序遍历有两种非递归实现，这里推荐第二种，不用构造新的结构体或类

这里的遍历还是有难度的，因为栈里存的必须是完整的一条路径，与之前参考资料给出的非递归实现还不太像

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
    int maxDepth(TreeNode* root) {
        if (! root) return 0;
        
        stack<TreeNode*> s;
        TreeNode *pre = NULL, *curr = NULL;
        s.push(root);
        int maxDepth = 0;
        
        while (! s.empty()) {
            curr = s.top();
            
            if (! pre || pre -> left == curr || pre -> right == curr) { // 继续往下遍历且未到叶子结点
                if (curr -> left) s.push(curr -> left);
                else if (curr -> right) s.push(curr -> right);
                
            } else if (curr -> left == pre) { // 退回了一步的情况，左子树都遍历完了
                if (curr -> right) s.push(curr -> right);
                
            } else { // 到了叶子节点，出栈
                s.pop();
            }
                
            pre = curr;
            if (s.size() > maxDepth) maxDepth = s.size();
            // max(int&, std::stack<TreeNode*>::size_type)
            // 也可用 maxDepth = max(maxDepth, int(s.size())); 需要强制类型转换
            
        }
        
        return maxDepth;
    }
};

```

## 解法3 - 迭代 - 队列 - 广度优先（层序遍历）

本题还可以机智的转化问题，树的最大深度即为广度优先搜索中的层数，故可以直接使用广度优先搜索求出最大深度。
注意：广度优先中队列的使用中，队列的 size 需要在for 循环遍历之前获得，因为它是一个变量。

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
    int maxDepth(TreeNode* root) {
        if (! root) return 0;
        
        queue<TreeNode*> q;
        q.push(root);
        int maxDepth = 0;
        
        while (! q.empty()) {
            int size = q.size();
            
            for (int i = 0; i < size; ++ i) {
                TreeNode* node = q.front();
                q.pop();
                if (node -> left) q.push(node -> left);
                if (node -> right) q.push(node -> right);
            }
            
            ++ maxDepth;
        }
        
        return maxDepth;
    }
};
```

### 复杂度分析

最坏情况下空间复杂度为 $$O(n)$$, 遍历每一个节点，时间复杂度为 $$O(n)$$