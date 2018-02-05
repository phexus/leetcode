# Populating Next Right Pointers in Each Node

Tags: Tags: Tree, Medium

## Problem

Given a binary tree

    struct TreeLinkNode {
      TreeLinkNode *left;
      TreeLinkNode *right;
      TreeLinkNode *next;
    }
Populate each next pointer to point to its next right node. If there is no next right node, the next pointer should be set to NULL.

Initially, all next pointers are set to NULL.

Note:

You may only use constant extra space.
You may assume that it is a perfect binary tree (ie, all leaves are at the same level, and every parent has two children).
For example,
Given the following perfect binary tree,
         1
       /  \
      2    3
     / \  / \
    4  5  6  7
After calling your function, the tree should look like:
         1 -> NULL
       /  \
      2 -> 3 -> NULL
     / \  / \
    4->5->6->7 -> NULL

## 解法1 - BFS - 不符合题意 - 用了 O(n) 内存

我的解法做完后发现并不符合提议，由于用了辅助队列，实际上空间复杂度为 $$O(n)$$
既然已经做了，权且贴出来呗，这种解法适合任意二叉树，所以并没有用到题目的限定条件 - 满二叉树。所以后续的优化可以从这个条件入手。

```cpp
/**
 * Definition for binary tree with next pointer.
 * struct TreeLinkNode {
 *  int val;
 *  TreeLinkNode *left, *right, *next;
 *  TreeLinkNode(int x) : val(x), left(NULL), right(NULL), next(NULL) {}
 * };
 */
class Solution {
public:
    void connect(TreeLinkNode *root) {
        if (! root) return;
        
        queue<TreeLinkNode*> nodesQueue;
        nodesQueue.push(root);
        
        while (! nodesQueue.empty()) {
            int size = nodesQueue.size();
            
            for (int i = 0; i < size; ++ i) {
                TreeLinkNode* curr = nodesQueue.front();
                nodesQueue.pop();
                
                if (i != size - 1) {
                    TreeLinkNode* next = nodesQueue.front();
                    curr -> next = next;
                }
                
                if (curr -> left) nodesQueue.push(curr -> left);
                if (curr -> right) nodesQueue.push(curr -> right);
            }
        }
    }
};
```

## 解法2 - 非递归

非常巧妙，发现了 next 指针的一些规律：
如果一个子节点是根节点的左子树，那么它的next就是该根节点的右子树，譬如上面例子中的4，它的next就是2的右子树5。
如果一个子节点是根节点的右子树，那么它的next就是该根节点next节点的左子树。譬如上面的5，它的next就是2的next（也就是3）的左子树。

startNode 就是每一行的开始结点，curr 是每行遍历的结点。另外要注意的是，遍历每行结点，生成的是该行的下一行结点的 next 指针。

[另一种写法](https://siddontang.gitbooks.io/leetcode-solution/content/tree/populating_next_right_pointers_in_each_node.html)

```cpp
/**
 * Definition for binary tree with next pointer.
 * struct TreeLinkNode {
 *  int val;
 *  TreeLinkNode *left, *right, *next;
 *  TreeLinkNode(int x) : val(x), left(NULL), right(NULL), next(NULL) {}
 * };
 */
class Solution {
public:
    void connect(TreeLinkNode *root) {
        if (! root) return;
        
        TreeLinkNode* startNode = root;
        
        while (startNode -> left) {
            TreeLinkNode* curr = startNode;
            while (curr) {
                curr -> left -> next = curr -> right;
                if (curr -> next) curr -> right -> next = curr -> next -> left;
                curr = curr -> next;
            }
            startNode = startNode -> left;
        }
        
    }
};
```

## 解法3 -递归 -其实也有隐含栈空间的消耗

把递归的解法也贴出来吧，但是递归的空间复杂度实际上就不是 $$O(n)$$。

```cpp
/**
 * Definition for binary tree with next pointer.
 * struct TreeLinkNode {
 *  int val;
 *  TreeLinkNode *left, *right, *next;
 *  TreeLinkNode(int x) : val(x), left(NULL), right(NULL), next(NULL) {}
 * };
 */
class Solution {
public:
    void connect(TreeLinkNode *root) {
        if (! root) return;
        
        if (root -> left) root -> left -> next = root -> right;
        // 这里必须要对 right 或 left 也判断，否则到叶子节点那一层会有问题
        if (root -> next && root -> right) root -> right -> next = root -> next -> left;
        
        connect(root -> left);
        connect(root -> right);
    }
};
```