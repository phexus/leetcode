# Binary Tree Level Order Traversal

Tags: Tags: Tree, Medium

## Problem

Given a binary tree, return the level order traversal of its nodes' values. (ie, from left to right, level by level).

For example:
Given binary tree [3,9,20,null,null,15,7],
    3
   / \
  9  20
    /  \
   15   7
return its level order traversal as:
[
  [3],
  [9,20],
  [15,7]
]

## 解法 - 队列

BFS，出队和将子节点入队的实现使用 for 循环，将每一轮的节点输出。
注意：
1. 遍历当前层所有节点，注意需要先保存队列大小，因为在入队出队时队列大小会变化
2. tmpList 保存每层节点的值，每次使用均要初始化，这是当返回结果是二维数组时的通用解法

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
    vector<vector<int>> levelOrder(TreeNode* root) {
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
        
        return result;
    }
};
```

另一种写法，不推荐，不如上面的清晰明了，通过记录 laver 层数来控制添加 二维数组

```cpp
//链接：https://www.nowcoder.com/questionTerminal/04a5560e43e24e9db4595865dc9c63a3
//来源：牛客网

class Solution {
public:
    vector<vector<int> > levelOrder(TreeNode *root) {
        vector<vector<int> > list;
        queue<TreeNode *> q;
        queue<int> level; //记录每一层
        int layer = 0;      //记录当前所在层数
         
        if(root == NULL)
            return list;
         
        TreeNode *cur = root;
        q.push(cur);
        level.push(0);
         
         
        while(!q.empty())
        {
            cur = q.front();            //记录当前根
            layer = level.front();
             
            q.pop();
            level.pop();
             
            if(list.size() < layer+1)
            {
                vector<int> v;
                list.push_back(v);          //list新增一行
            }
             
            list[layer].push_back(cur->val);
             
            if(cur->left != NULL)        //左子树非空
            {
                q.push(cur->left);
                level.push(layer+1);
            }
            if(cur->right != NULL)       //右子树非空
            {
                q.push(cur->right);
                level.push(layer+1);
            }
        }
        return list;
    }
};
```

### 复杂度

使用辅助队列，空间复杂度 $$O(n)$$, 时间复杂度 $$O(n)$$.