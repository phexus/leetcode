# Binary Tree Maximum Path Sum

Tags: Tree, Hard

## Problem

Given a binary tree, find the maximum path sum.

For this problem, a path is defined as any sequence of nodes from some starting node to any node in the tree along the parent-child connections. The path must contain at least one node and does not need to go through the root.

For example:
Given the below binary tree,

       1
      / \
     2   3
Return 6.

## 解法

[参考题解](https://algorithm.yuanbin.me/zh-hans/binary_tree/binary_tree_maximum_path_sum.html) 有详细的递归模型的构造过程

我们使用分治的思想来分析路径和/左子树/右子树，设 $$f(root)$$ 为root的子树到root节点(含)路径长度的最大值，那么我们有 $$f(root)=root−>val+max(f(root−>left), f(root−>right))$$
递归模型已初步建立起来，接下来就是分析如何将左右子树的路径长度和最终题目要求的「路径和」挂钩。设 $$g(root)$$ 为当「路径和」中根节点为root时的值，那么我们有 $$g(root)=root−>val+f(root−>left)+f(root−>right)$$

f(root) 就是下面给出解法的 getSingleMaxPathSum() 函数，但是 g(root) 实际上没必要每个结点的值都记录下来，参考解法是做了嵌套类或 pair 结构，如 pair(f(root), g(root))。但本题其实只要求得最大值即可，所以其实可以定义一个全局变量 `maxVal` 不断更新就好，反正树的遍历一定会遍历所有节点的，所以也就不会漏数据。

另外需要注意的一点是，最后返回值就是 int，所以 `maxVal = INT_MIN；`的初始化似乎也没有问题，不会像 Valid_Binary_Tree 一题那么麻烦。
注意 getSingleMaxPathSum 中最后的返回值，如果其路径长度比0还小，那么取这一段路径反而会减少最终的路径和，故不应取这一段，我们使用0表示这一隐含意义。（其实也可以在 left 和 right 时候再和 0 比较）

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
    int maxVal;
    int maxPathSum(TreeNode *root) {
        if (! root) return 0;
        maxVal = INT_MIN;
        getSingleMaxPathSum(root);
        
        return maxVal;
    }
    
    int getSingleMaxPathSum(TreeNode* root) {
        if (! root) return 0;
        
        int leftMax = getSingleMaxPathSum(root -> left);
        int rightMax = getSingleMaxPathSum(root -> right);
        
        maxVal = max(maxVal, root -> val + leftMax + rightMax);
        
        return max(0, root -> val + max(leftMax, rightMax));
    }
};

```