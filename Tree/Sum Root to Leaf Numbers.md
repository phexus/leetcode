# Sum Root to Leaf Numbers

Tags: Tags: Tree, Medium

## Problem

Given a binary tree containing digits from 0-9 only, each root-to-leaf path could represent a number.

An example is the root-to-leaf path 1->2->3 which represents the number 123.

Find the total sum of all root-to-leaf numbers.

For example,

    1
   / \
  2   3
The root-to-leaf path 1->2 represents the number 12.
The root-to-leaf path 1->3 represents the number 13.

Return the sum = 12 + 13 = 25.

## 解法1 - DFS + recursively

本题其实和`剑指offer`一书的 面试题 25 有些类似，通过 DFS 先序遍历，储存 从根结点到当前结点的路径，当到达叶子结点时，调用路径求和函数即可。

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
    int sumNumbers(TreeNode *root) {
        vector<int> path;
        int result = 0;
        dfs(root, path, result);
        return result;
    }
    
    void dfs(TreeNode* root, vector<int>& path, int& result) {
        if (!root) return;
        path.push_back(root -> val);
        
        if (!root -> left && !root -> right) {
            result += pathSum(path);
        }
        
        if (root -> left) dfs(root -> left, path, result);
        if (root -> right) dfs(root -> right, path, result);
        path.pop_back();
    }
    
    int pathSum(vector<int>& path) {
        int sum = 0;
        for (int i = 0; i < path.size(); ++i) {
            sum = 10 * sum + path[i];
        }
        return sum;
    }
};
```

### 另一种简洁的递归写法 - 不传引用 + 拷贝 + sum in-place

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
    int sumNumbers(TreeNode* root) {
        return sumDFS(root, 0);
    }
    
    int sumDFS(TreeNode* root, int sum) {
        if (! root) return 0;
        
        sum = sum * 10 + root -> val;
        if (!root -> left && !root -> right) return sum;
        
        return sumDFS(root -> left, sum) + sumDFS(root -> right, sum);
    }
};
```

## 解法2 - DFS - 迭代

下面给出的方法有一个缺陷，就是会改变树中结点的值，所以其实可以把中间结果和结点组成一个 pair 存放在 stack 就行了。

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
    int sumNumbers(TreeNode* root) {
        if (! root) return 0;
        
        int result = 0;
        stack<TreeNode*> s;
        s.push(root);
        
        while (!s.empty()) {
            TreeNode* curr = s.top();
            s.pop();
            
            if (!curr -> left && !curr -> right) result += curr -> val;
            
            if (curr -> right) {
                curr -> right -> val += curr -> val * 10;
                s.push(curr -> right);
            }
            if (curr -> left) {
                curr -> left -> val += curr -> val * 10;
                s.push(curr -> left);
            }
        }
        
        return result;
    }
};
```

## 解法3 - BFS

[leetcode discuss](https://leetcode.com/problems/sum-root-to-leaf-numbers/discuss/41383/Python-solutions-(dfs+stack-bfs+queue-dfs-recursively).) 感觉其实和上面的解法没啥区别，无非就是 栈换成了队列，入队顺序与入栈顺序刚好相反。
倒是 [UESTCODER 的解法](https://www.nowcoder.net/questionTerminal/185a87cd29eb42049132aed873273e83) 用 map 实现了一个类似于链表的结构存储了从叶子结点到根结点的路径，再统一计算求和。不过感觉比较麻烦

```cpp
//链接：https://www.nowcoder.net/questionTerminal/185a87cd29eb42049132aed873273e83
//来源：牛客网

//根据BFS找到所有叶子节点到根节点的路径，然后相加
class Solution {
public:
     int sumNumbers(TreeNode *root) {
        if (root == nullptr)
            return 0;
        int sum = 0;
        //用BFS实现
        unordered_map<TreeNode*,TreeNode*> path;
        path[root] = root;
        //保存叶节点
        vector<TreeNode*> leafNode;
        queue<TreeNode*> q;
        q.push(root);
        while (!q.empty()){
            TreeNode* temp = q.front();
            q.pop();
            if (temp->left != nullptr){
                path[temp->left] = temp;
                q.push(temp->left);
            }
            if (temp->right != nullptr){
                path[temp->right] = temp;
                q.push(temp->right);
            }
            if (temp->left == nullptr && temp->right == nullptr)
                leafNode.push_back(temp);
        }
        //开始找每一个叶子节点到根节点的路径的节点值
        for (auto node : leafNode){
            int sumTemp = 0;
            //代表位数
            int i = 1;
            while (node != root){
                sumTemp += node->val * i;
                node = path[node];
                i *= 10;
            }
            sumTemp += root->val*i;
            sum += sumTemp;
        }
        return sum;
    }
};
```