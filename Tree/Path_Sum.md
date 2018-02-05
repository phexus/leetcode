# Path Sum

Tags: Tags: Tree, Easy

## Problem

Given a binary tree and a sum, determine if the tree has a root-to-leaf path such that adding up all the values along the path equals the given sum.

For example:
Given the below binary tree and sum = 22,

              5
             / \
            4   8
           /   / \
          11  13  4
         /  \      \
        7    2      1
return true, as there exist a root-to-leaf path 5->4->11->2 which sum is 22.

## 解法1 - DFS - 递归

要注意的是， `！ root` 的情况要返回 false，因为若这一分支满足条件之前就会跳出，不会遍历到最后空结点的。

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
    bool hasPathSum(TreeNode *root, int sum) {
        return dfsSum(root, 0, sum);
    }
    
    bool dfsSum(TreeNode* root, int sum , int target) {
        if (! root) return false; // 注意这里要返回 false
        
        sum += root -> val;
        
        if (! root -> left && ! root -> right) {
            if (sum == target) return true;
        }
        
        bool leftSum = dfsSum(root -> left, sum, target);
        bool rightSum = dfsSum(root -> right, sum, target);
        return leftSum || rightSum;
    }
};
```

###　更简洁的写法

在尝试用 DFS 来解决问题时，边界条件的处理可以更加灵活，下面这种做法就是在理解了 DFS 的基础上，巧妙的把 `sum - root -> val` 递归地传给子结点。这样就不需要构造新的辅助函数，没有必要引入 sum 和 target，相比之下上面的做法就显得很啰嗦了。

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
    bool hasPathSum(TreeNode* root, int sum) {
        if (! root) return false;
        
        if (! root -> left && ! root -> right && sum == root -> val) return true;
        
        return hasPathSum(root -> left, sum - root -> val) || hasPathSum(root -> right, sum - root -> val);
    }
};
```

## 解法2 - postorder traversal

看到[leetcode solution](https://discuss.leetcode.com/topic/2455/accepted-by-using-postorder-traversal)提到可以用后序遍历的思路来解，就自己尝试了写，下面是自己的解法，坑实在太多。。

整体框架是套用的后序遍历的模板，用 `tmpSum` 代表遍历过程中的从更结点到当前结点的累加值。
需要在后序遍历中添加跟题目相关的`tmpSum += curr -> val` 和 `tmpSum -= curr -> val`。
`tmpSum += curr -> val` 代表新节点加入后要累加该值，这里要明确何为新节点？我在实现过程中发现这里的限定条件很容易出错。
1. `! pre` 代表此时还未执行过出栈回退分支，所以此时的结点一定是新节点
2. `pre && pre != curr -> left && pre != curr -> right` 注意，这里一定要限定 pre 存在，此时代表已经执行过出栈回退了，`pre != curr -> left && pre != curr -> right` 限定的是当前的结点并不是刚刚回退的，也就意味着此时遍历已经到了新的右边分支，所以也是新节点

`tmpSum -= curr -> val` 代表出栈回退结点时，要把该结点从累加和中剔除。相对来说，这步的执行时机就很好把握了，直接加到出栈的那个逻辑分支里即可。

另外还需要注意的是，其余退出时都应该返回 false，返回 true 的只有在 noChild 时进行判断

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
    bool hasPathSum(TreeNode* root, int sum) {
        if (! root) return false;
        
        stack<TreeNode*> s;
        s.push(root);
        TreeNode* pre = NULL;
        int tmpSum = 0;
        
        while (! s.empty()) {
            TreeNode* curr = s.top();
            
            if (! pre || (pre && pre != curr -> left && pre != curr -> right)) tmpSum += curr -> val;
            
            bool noChild = false;
            if (! curr -> left && ! curr -> right) {
                noChild = true;
                if (tmpSum == sum) return true;
            }
            
            bool visited = false;
            if (pre && (curr -> left == pre || curr -> right == pre)) visited = true;
            
            if (noChild || visited) {
                s.pop();
                pre = curr;
                tmpSum -= curr -> val;
            } else {
                if (curr -> right) s.push(curr -> right);
                if (curr -> left) s.push(curr -> left);
            }
        }
        
        return false;
    }
};
```

### leetcode 给出的后序遍历解法

换用了另一种后序遍历的模板，SUM 增加减少的时机更加明了

```cpp
class Solution {
public:
    bool hasPathSum(TreeNode *root, int sum) {
        stack<TreeNode *> s;
        TreeNode *pre = NULL, *cur = root;
        int SUM = 0;
        while (cur || !s.empty()) {
            while (cur) {
                s.push(cur);
                SUM += cur->val;
                cur = cur->left;
            }
            cur = s.top();
            if (cur->left == NULL && cur->right == NULL && SUM == sum) {
                return true;
            }
            if (cur->right && pre != cur->right) {
                cur = cur->right;
            } else {
                pre = cur;
                s.pop();
                SUM -= cur->val;
                cur = NULL;
            }
        }
        return false;
    }
};
```

## 解法3 - BFS -队列

用 BFS 也可以做，用队列维持从更结点到该层每个结点的和，不漏不重。这背后隐藏的原理是，从根节点到该结点的路径是唯一的，所以和也是唯一的，这样用来做 BFS 的队列和 这个队列就能一一对应起来

```cpp
链接：https://www.nowcoder.com/questionTerminal/508378c0823c423baa723ce448cbfd0c
来源：牛客网

bool hasPathSum3(TreeNode* root, int sum) { //bfs
        if (!root)
        {
            return false;
        }

        queue<TreeNode*> que;
        queue<int> sum_que;
        que.push(root);
        sum_que.push(root->val);

        while (!que.empty())
        {
            TreeNode* cur = que.front();
            que.pop();
            int ret = sum_que.front();
            sum_que.pop();

            if (cur->left==NULL&&cur->right==NULL&&ret==sum)
            {
                return true;
            }
            if (cur->left)
            {
                que.push(cur->left);
                sum_que.push(ret + cur->left->val);
            }
            if (cur->right)
            {
                que.push(cur->right);
                sum_que.push(ret + cur->right->val);
            }
        }
        return false;

    }
```
