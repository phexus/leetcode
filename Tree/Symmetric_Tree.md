# Symmetric Tree

Tags: Tags: Tree, Easy

## Problem

Given a binary tree, check whether it is a mirror of itself (ie, symmetric around its center).

For example, this binary tree [1,2,2,3,4,4,3] is symmetric:

    1
   / \
  2   2
 / \ / \
3  4 4  3
But the following [1,2,2,null,3,null,3] is not:
    1
   / \
  2   2
   \   \
   3    3
Note:
Bonus points if you could solve it both recursively and iteratively.

## 解法1 - 递归

[参考资料](https://siddontang.gitbooks.io/leetcode-solution/content/tree/symmetric_tree.html)

本题与 剑指offer 上面试题 59 相同。从根节点往下，需要判断以下 3 个条件：
1. 左右两个结点大小是否相同
2. 左结点的左孩子是否与是否与右结点的右孩子是否相同
3. 左结点的右孩子是否与右结点的左孩子是否相同

递归求解，如果以上三个条件对于每一层都满足，我们就可以认为这棵树是镜像树.

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
    bool isSymmetric(TreeNode* root) {
        if (! root) return true;
        
        return isSymmetric(root -> left, root -> right);
    }
    
    bool isSymmetric(TreeNode* p, TreeNode* q) {
        if (! p && ! q) return true;
        if (! p || ! q) return false;
        if (p -> val != q -> val) return false;
        
        return isSymmetric(p -> left, q -> right) && isSymmetric(p -> right, q -> left);
    }
};

// 另一种写法
class Solution {
public:
    bool isSymmetric(TreeNode* root) { 
        return isSymmetric(root, root);
    }
    
    bool isSymmetric(TreeNode* p, TreeNode* q) {
        if (! p && ! q) return true;
        if (! p || ! q) return false;
        
        return (p -> val == q -> val) && isSymmetric(p -> left, q -> right) && isSymmetric(p -> right, q -> left);
    }
};
```

### 复杂度分析

本质其实就是DFS,时间复杂度为 $$O(n)$$， 空间复杂度取决于树的 height，最差情况下树是斜的，递归消耗的栈空间为 $$O(n)$$

## 解法2 - 迭代

迭代的思路类似于 BFS，对每一层的元素按照一定次序依次入队列，再依次出队列比较，比较的原则与递归的写法一样。这里可以用一个队列，只要做好顺序就行，也可以用两个队列分别来存左右两边的子结点。

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
class Solution {shouye
public:
    bool isSymmetric(TreeNode* root) {
        queue<TreeNode*> q;
        q.push(root);
        q.push(root);
        
        while (! q.empty()) {
            TreeNode* n1 = q.front();
            q.pop();
            TreeNode* n2 = q.front();
            q.pop();
            
            // 这里是 continue，还要继续循环直到队列为空
            if (! n1 && ! n2) continue;
            if (! n1 || ! n2) return false;
            if (n1 -> val != n2 -> val) return false;
            
            q.push(n1 -> left);
            q.push(n2 -> right);
            q.push(n1 -> right);
            q.push(n2 -> left);
        }
        return true;
    }
};
```

###　复杂度分析

时间复杂度同样为为 $$O(n)$$， 空间复杂度取决于队列的长度，最差情况下树是斜的，队列占用额额外空间为 $$O(n)$$