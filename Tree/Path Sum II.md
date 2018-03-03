# Path Sum II

Tags: Tags: Tree, Medium

## Problem

Given a binary tree and a sum, find all root-to-leaf paths where each path's sum equals the given sum.

For example:
Given the below binary tree and sum = 22,
              5
             / \
            4   8
           /   / \
          11  13  4
         /  \    / \
        7    2  5   1
return
[
   [5,4,11,2],
   [5,8,4,5]
]

## 解法1 - 递归

与 path sum 如出一辙，只要处理好传参。
注意： 这里的 curr 不能传引用，因为需要回退，每递归一层都得复制一份，直接在一个变量上改，path 会一直累积下去，有问题。

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
    vector<vector<int> > pathSum(TreeNode *root, int sum) {
        vector<vector<int>> result;
        if (! root) return result;
        
        vector<int> path;
        pathSum(root, 0, sum, path, result);
        return result;
    }
    
    void pathSum(TreeNode* root, int total, int target, vector<int> path, vector<vector<int>>& result) {
        if (! root) return;
        
        total += root -> val;
        path.push_back(root -> val);
        
        if (! root -> left && ! root -> right && total == target) {
            result.push_back(path);
        }
        
        pathSum(root -> left, total, target, path, result);
        pathSum(root -> right, total, target, path, result);
    }
};
```

### 更简洁的写法

把增加累加值改为减少目标值，这样少传一个变量

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
    vector<vector<int>> pathSum(TreeNode* root, int sum) {
        vector<vector<int>> result;
        if (! root) return result;
        
        vector<int> path;
        pathSum(root, sum, path, result);
        
        return result;
    }
    
    void pathSum(TreeNode* root, int sum, vector<int> path, vector<vector<int>>& result) {
        if (! root) return;
        
        path.push_back(root -> val);
        
        if (! root -> left && ! root -> right && sum == root -> val) result.push_back(path);
        
        pathSum(root -> left, sum - root -> val, path, result);
        pathSum(root -> right, sum - root -> val, path, result);
    }
};
```

### 解决 path 不能传引用的问题

看到了[参考解法](https://leetcode.com/problems/path-sum-ii/discuss/36685/12ms-11-lines-C++-Solution)，发现其实 path也是可以传引用的，只要处理好递归出栈后同时也把 path 的值出栈就好， 最后一行添加 `path.pop_back();`，在递归返回前做好回退，能提升整体的效率。

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
    vector<vector<int>> pathSum(TreeNode* root, int sum) {
        vector<vector<int>> result;
        if (! root) return result;
        
        vector<int> path;
        pathSum(root, sum, path, result);
        
        return result;
    }
    
    void pathSum(TreeNode* root, int sum, vector<int>& path, vector<vector<int>>& result) {
        if (! root) return;
        
        path.push_back(root -> val);
        
        if (! root -> left && ! root -> right && sum == root -> val) result.push_back(path);
        
        pathSum(root -> left, sum - root -> val, path, result);
        pathSum(root -> right, sum - root -> val, path, result);
        path.pop_back(); // 很关键！！
    }
};
```

后来发现本题与 `剑指 offer` 面试题 25 一样，这是二刷时的写法：

要小心的就是 `ans.push_back(curr);` 的判定条件是 `sum == root -> val` ，其实这里的 sum 更准确来说是剩余值，另外不要像其他 backtracking 一样 push 之后就 return，这里要继续走到最后，需要 pop_back

```cpp
/*
struct TreeNode {
	int val;
	struct TreeNode *left;
	struct TreeNode *right;
	TreeNode(int x) :
			val(x), left(NULL), right(NULL) {
	}
};*/
class Solution {
public:
    vector<vector<int> > FindPath(TreeNode* root,int expectNumber) {
        if (root == NULL) return vector<vector<int>> ();
        
        vector<vector<int>> ans;
        vector<int> curr;
        help(root, expectNumber, curr, ans);
        return ans;
    }
    
    void help(TreeNode* root, int sum, vector<int>& curr, vector<vector<int>>& ans) {
        if (sum < 0) return;
        
        curr.push_back(root -> val);
        if (!root -> left && !root -> right && sum == root -> val) {
            ans.push_back(curr);
        }
        
        if (root -> left) help(root -> left, sum - (root -> val), curr, ans);
        if (root -> right) help(root -> right, sum - (root -> val), curr, ans);
        curr.pop_back();
    }
};
```



##　解法2 - DFS - 后序遍历

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
    vector<vector<int>> pathSum(TreeNode* root, int sum) {
        vector<vector<int>> result;
        vector<int> path;
        stack<TreeNode*> s;
        TreeNode *pre = NULL, *curr = root;
        int SUM = 0;
        
        while (curr || ! s.empty()) {
            while (curr) {
                s.push(curr);
                SUM += curr -> val;
                path.push_back(curr -> val);
                curr = curr -> left;
            }
            
            curr = s.top();
            if (! curr -> left && ! curr -> right && SUM == sum) result.push_back(path);
            
            if (curr -> right && curr -> right != pre) {
                curr = curr -> right;
            } else {
                pre = curr;
                s.pop();
                SUM -= curr -> val;
                path.pop_back();
                curr = NULL;
            }
        }
        
        return result;
    }
};
```

## 解法3 - BFS -队列

（不知道为啥在 nowcoder 上一直 a 不了，但在 leetcode 上就可以）
本题还用 BFS 去做的话没就显得很麻烦了，而且为了保存路径，不能不构造一个存放 vector<int> 的 queue。然而在更新该路径时又引入了一个大坑。
如果像下面这样写的话，一定会出错，path 在更新完 left 分支后就已经改变了，此时再去 right 分支，就会把原来 left 分支 的脏数据也带进来。
```cpp
            if (curr -> left) {
                q.push(curr -> left);
                total_q.push(total + curr -> left -> val);
                path.push_back(curr -> left -> val);
                path_q.push(path);
            }
            if (curr -> right) {
                q.push(curr -> right);
                total_q.push(total + curr -> right -> val);
                path.push_back(curr -> right -> val);
                path_q.push(path);
            }
```
因此，要在 left 分支完成之后 pop 掉

```cpp
            if (curr -> left) {
                q.push(curr -> left);
                total_q.push(total + curr -> left -> val);
                path.push_back(curr -> left -> val);
                path_q.push(path);
                // 这个pop很关键！！！
                path.pop_back();
            }
            if (curr -> right) {
                q.push(curr -> right);
                total_q.push(total + curr -> right -> val);
                path.push_back(curr -> right -> val);
                path_q.push(path);
            }
```

或者像下面的做法，直接拷贝构造一个新的 tmp_path 上做

```cpp
class Solution {
public:
    vector<vector<int> > pathSum(TreeNode *root, int sum) {
        vector<vector<int>> result;
        if (! root) return result;
        
        queue<TreeNode*> q;
        queue<int> total_q;
        queue<vector<int>> path_q;
        q.push(root);
        total_q.push(root -> val);
        path_q.push({root -> val});
        
        while (! q.empty()) {
            TreeNode* curr = q.front();
            q.pop();
            int total = total_q.front();
            total_q.pop();
            vector<int> path = path_q.front();
            path_q.pop();
            
            if (! curr -> left && ! curr -> right && total == sum) {
                result.push_back(path);
            }
            
            if (curr -> left) {
                q.push(curr -> left);
                total_q.push(total + curr -> left -> val);
                vector<int> tmp_path = path;
                tmp_path.push_back(curr -> left -> val);
                path_q.push(tmp_path);
            }
            if (curr -> right) {
                q.push(curr -> right);
                total_q.push(total + curr -> right -> val);
                vector<int> tmp_path = path;
                tmp_path.push_back(curr -> right -> val);
                path_q.push(tmp_path);
            }
        }
        return result;
    }
};
```