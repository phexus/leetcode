# Binary Tree Level Order Traversal II

Tags: Tags: Tree, Easy

## Problem

Given a binary tree, return the bottom-up level order traversal of its nodes' values. (ie, from left to right, level by level from leaf to root).

For example:
Given binary tree [3,9,20,null,null,15,7],
    3
   / \
  9  20
    /  \
   15   7
return its bottom-up level order traversal as:
[
  [15,7],
  [9,20],
  [3]
]

## 解法1 - BFS + reverse

可以直接调用 std::reverse() 方法，也可以创建一个 stack，先把结果存放在 stack，之后再出栈加到 vector result 里

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
    vector<vector<int> > levelOrderBottom(TreeNode *root) {
        vector<vector<int>> result;
        if (! root) return result;
        
        queue<TreeNode*> q;
        q.push(root);
        
        while (! q.empty()) {
            vector<int> tmpList;
            int size = q.size();
            
            for (int i = 0; i < size; ++ i) {
                TreeNode* node = q.front();
                q.pop();
                tmpList.push_back(node -> val);
                
                if (node -> left) q.push(node -> left);
                if (node -> right) q.push(node -> right);
            }
            result.push_back(tmpList);
        }
        reverse(result.begin(), result.end());
        return result;
    }
};
```

### 复杂度

时间复杂度为 $$O(n)$$, 使用了队列或者辅助栈作为辅助空间，空间复杂度为 $$O(n)$$.

## 解法2 - BFS - 递归

用递归实现层序遍历 与正常遍历不同的是，先进行下一层递归，再把当前层的结果保存到res中 结合代码更好理解



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
    vector<vector<int>> levelOrderBottom(TreeNode* root) {
        vector<vector<int>> result;
        if (! root) return result;
        
        queue<TreeNode*> q;
        q.push(root);
        levelOrderBottom(q, result);
        return result;
    }
    
    void levelOrderBottom(queue<TreeNode*>& q, vector<vector<int>>& result) {
        if (q.empty()) return;
        
        vector<int> tmpList;
        
        int size = q.size();
        for (int i = 0; i< size; ++ i) {
            TreeNode* node = q.front();
            q.pop();
            tmpList.push_back(node -> val);
            
            if (node -> left) q.push(node -> left);
            if (node -> right) q.push(node -> right);
        }
        // 先进行递归调用，再添加到 result
        levelOrderBottom(q, result);
        result.push_back(tmpList);
    }
};
```

##　解法３ - DFS - 递归

[参考解法](https://www.nowcoder.com/questionTerminal/d8566e765c8142b78438c133822b5118)

最后的递归顺序只要保证`dfs(root -> left, height - 1, result);` 在 `dfs(root -> right, height - 1, result);`之前就好，`result[height].push_back(root -> val);` 的顺序无所谓，因为已经精确到 height了，啥时候push都行，但得保证先左后右

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
    vector<vector<int> > levelOrderBottom(TreeNode *root) {
        if (! root) return vector<vector<int>>();
        
        vector<vector<int>> result(getHeight(root), vector<int>());
        dfs(root, result.size() - 1, result);
        
        return result;
    }
    
    int getHeight(TreeNode* root) {
        if (! root) return 0;
        return 1 + max(getHeight(root -> left), getHeight(root -> right));
    }
    
    void dfs(TreeNode* root, int height, vector<vector<int>>& result) {
        if (! root) return;
        
        // 这里的顺序只要确保 left 在 right前就好，因为每一层先左后右，至于push加在哪里都行
        dfs(root -> left, height - 1, result);
        dfs(root -> right, height - 1, result);
        result[height].push_back(root -> val);
    }
};
```


