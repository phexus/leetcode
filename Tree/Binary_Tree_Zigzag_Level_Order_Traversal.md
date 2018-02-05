# Binary Tree Zigzag Level Order Traversal

Tags: Tags: Tree, Medium

## Problem

Given a binary tree, return the zigzag level order traversal of its nodes' values. (ie, from left to right, then right to left for the next level and alternate between).

For example:
Given binary tree [3,9,20,null,null,15,7],
    3
   / \
  9  20
    /  \
   15   7
return its zigzag level order traversal as:
[
  [3],
  [20,9],
  [15,7]
]

## 解法1 - BFS - 队列 - reverse

奇数行按 BFS 来做，偶数行把临时数组翻转一下即可，所以定义一个 bool 型变量


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
    vector<vector<int>> zigzagLevelOrder(TreeNode* root) {
        vector<vector<int>> result;
        if (! root) return result;
        
        queue<TreeNode*> q;
        q.push(root);
        bool odd = true;
        
        while (! q.empty()) {
            int size = q.size();
            vector<int> tmpList;
            
            for (int i = 0; i < size; ++ i) {
                TreeNode* curr = q.front();
                q.pop();
                
                tmpList.push_back(curr -> val);
                
                if (curr -> left) q.push(curr -> left);
                if (curr -> right) q.push(curr -> right);
            }
            
            if (! odd) {
                reverse(tmpList.begin(), tmpList.end());
            }
            
            result.push_back(tmpList);
            odd = ! odd;
        }
        
        return result;
    }
};
```

###　复杂度

需要 reverse 的节点数目近似为 n/2, 故时间复杂度 $$O(n)$$. 
最下层节点数目最多 n/2, 故reverse 操作的空间复杂度可近似为 $$O(n/2)$$.
总的时间复杂度为 $$O(n)$$, 空间复杂度也为 $$O(n)$$.

## 解法２ - without reverse operation by using size of each level

[参考解法](https://leetcode.com/problems/binary-tree-zigzag-level-order-traversal/discuss/33825/c++-5ms-version:-one-queue-and-without-reverse-operation-by-using-size-of-each-level)非常巧妙的利用 `int index = leftToRight ? i : size -1 - i;` 使填充 row 时能够逆序填充。

该解法的变量名也比较规范，可读性比较高，学到了～

特别要注意的一点是，`vector<int> row(size);` 必须要用 size 长度初始化，否则后面会有数组越界的问题

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
    vector<vector<int>> zigzagLevelOrder(TreeNode* root) {
        vector<vector<int>> result;
        if (! root) return result;
        
        queue<TreeNode*> nodesQueue;
        nodesQueue.push(root);
        bool leftToRight = true;
        
        while (! nodesQueue.empty()) {
            int size = nodesQueue.size();
            // 这里必须要初始化，否则后边会有数组越界的问题！！！
            vector<int> row(size);
            
            for (int i = 0; i < size; ++ i) {
                TreeNode* curr = nodesQueue.front();
                nodesQueue.pop();
                
                int index = leftToRight ? i : size -1 - i;
                row[index] = curr -> val;
                
                if (curr -> left) nodesQueue.push(curr -> left);
                if (curr -> right) nodesQueue.push(curr -> right);
            }
            
            leftToRight = ! leftToRight;
            result.push_back(row);
        }
        
        return result;
    }
};
```

当然也可以用更高级的双端队列 deque 来做，[参考解法](https://www.nowcoder.net/questionTerminal/47e1687126fa461e8a3aff8632aa5559)
