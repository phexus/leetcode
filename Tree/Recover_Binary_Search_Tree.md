# Recover Binary Search Tree

Tags: Tree, Hard

## Problem

Two elements of a binary search tree (BST) are swapped by mistake.

Recover the tree without changing its structure.

Note:
A solution using O(n) space is pretty straight forward. Could you devise a constant space solution?

## 解法 - Morris Traversal - inorder traversal

[参考解法](https://siddontang.gitbooks.io/leetcode-solution/content/tree/recover_binary_search_tree.html)
[Morris Traversal 图解](http://www.cnblogs.com/AnnieKim/archive/2013/06/15/morristraversal.html)
[leetcode discuss](https://leetcode.com/problems/recover-binary-search-tree/discuss/32559/Detail-Explain-about-How-Morris-Traversal-Finds-two-Incorrect-Pointer)

这题需要修复一颗二叉搜索树的两个交换节点数据，我们知道对于一颗二叉搜索树来说，如果按照中序遍历，那么它输出的值是递增有序的，所以我们只需要按照中序遍历输出，在输出结果里面找到两个异常数据（比它后面输出结果大），交换这两个节点的数据就可以了。
但是这题要求使用O(1)的空间，如果采用通常的中序遍历（递归或者栈）的方式，都需要O(N)的空间，所以这里我们用Morris Traversal的方式来进行树的中序遍历。(注：很多人都忽略了递归也是要消耗栈空间的，递归的空间复杂度永远都不会是 O(1)!!!)

Morris Traversal中序遍历的原理:

- 如果当前节点的左孩子为空，则输出当前节点并将其右孩子作为当前节点。
- 如果当前节点的左孩子不为空，在当前节点的左子树中找到当前节点在中序遍历下的前驱节点，也就是当前节点左子树的最右边的那个节点。
    - 如果前驱节点的右孩子为空，则将它的右孩子设置为当前节点，当前节点更新为其左孩子。
    - 如果前驱节点的右孩子为当前节点，则将前驱节点的右孩子设为空，输出当前节点，当前节点更新为其右孩子

重复上述过程，直到当前节点为空，递归的时候我们同时需要记录错误的节点。假设当前节点为cur，它的前驱节点为pre，如果cur的值小于pre的值，那么cur和pre里面的数据就是交换的了。

另外，节点交换分两种情况，一种是相邻节点，这种交换不破坏两节点与之外的大小性，只破坏两节点之间大小性。另一种是不相邻，这种破坏两节点分别与两边的节点。因此在第一次搜到一个不满足的节点后，要将第二个节点也赋指针，以考虑第一种情况。
举个例子： 163427，第一次找到 6 时，preCurr 指向 6，curr 指向 3，第二次找到 2 时，preCurr 指向 4， curr 指向 2，所以：
```cpp
                if (preCurr && preCurr -> val >= curr -> val) {
                    if (!p1) p1 = preCurr;
                    p2 = curr;
                }
```

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
    void recoverTree(TreeNode* root) {
        TreeNode* curr = root;
        TreeNode* pre = NULL;
        TreeNode* preCurr = NULL;
        TreeNode* p1 = NULL;
        TreeNode* p2 = NULL;
        
        while (curr) {
            if (!curr -> left) {
                if (preCurr && preCurr -> val >= curr -> val) {
                    if (!p1) p1 = preCurr;
                    p2 = curr;
                }
                preCurr = curr;
                curr = curr -> right;
            } else {
                pre = curr -> left;
                
                while (pre -> right && pre -> right != curr) pre = pre -> right;
                
                if (!pre -> right) {
                    pre -> right = curr;
                    curr = curr -> left;
                } else {
                    if (preCurr && preCurr -> val >= curr -> val) {
                        if (!p1) p1 = preCurr;
                        p2 = curr;
                    }
                    preCurr = curr;
                    
                    pre -> right = NULL;
                    curr = curr -> right;
                }
            }
        }
        
        if (p1 && p2) swap(p1 -> val, p2 -> val);
    }
};
```

### 复杂度

时间复杂度 $$O(n)$$
空间复杂度 $O(1)$$$