# Validate Binary Search Tree

Tags: Tree, Medium

## Problem

Given a binary tree, determine if it is a valid binary search tree (BST).

Assume a BST is defined as follows:

The left subtree of a node contains only nodes with keys less than the node's key.
The right subtree of a node contains only nodes with keys greater than the node's key.
Both the left and right subtrees must also be binary search trees.
Example 1:
    2
   / \
  1   3
Binary tree [2,1,3], return true.
Example 2:
    1
   / \
  2   3
Binary tree [1,2,3], return false.

## 解法1 - 递归

[参考资料](https://algorithm.yuanbin.me/zh-hans/binary_search_tree/validate_binary_search_tree.html#)

本题看似简单，实则坑实在很多，而且以后刷题还是用 leetcode 吧，nowcoder 的 testcase 实在是不全，leetcode 要严苛得多

`递归步的核心步骤为比较当前节点的key和左右子节点的key大小，和定义不符则返回false, 否则递归处理。`然而我一开始就分析错了递归的判断条件，`需要注意的是这里不仅要考虑根节点与当前的左右子节点，还需要考虑左子树中父节点的最小值和右子树中父节点的最大值。否则程序在[10,5,15,#,#,6,20] 这种 case 误判。`

`由于不仅需要考虑当前父节点，还需要考虑父节点的父节点... 故递归时需要引入上界和下界值。画图分析可知对于左子树我们需要比较父节点中最小值，对于右子树则是父节点中的最大值。又由于满足二叉搜索树的定义时，左子结点的值一定小于根节点，右子节点的值一定大于根节点，故无需比较所有父节点的值，使用递推即可得上界与下界，这里的实现非常巧妙。`

错误的解法！！！
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
    bool isValidBST(TreeNode *root) {
        if (! root) return true;
        
        bool left = true;
        if (root -> left && root -> left -> val < root -> val) {
            left = isValidBST(root -> left);
        }
        bool right = true;
        if (root -> right && root -> right -> val > root -> val) {
            right = isValidBST(root -> right);
        }

        return left && right;
    }
};
```

考虑上界、下界情况后，下面的解答在 nowcoder 上可以过，但是在 leetcode 上 `2147483647` 这个 case 过不了。这才发现又是边界问题，当输入就是 INT_MAX 时，应该返回 true 的，但是为了照顾 lower ，upper 已经被覆写后的情况，又不得不返回 false。

```cpp
class Solution {
public:
    bool isValidBST(TreeNode *root) {
        if (! root) return true;
        
        return isValidBST(root, INT_MIN, INT_MAX);
    }
    
    bool isValidBST(TreeNode* root, int lower, int upper) {
        if (! root) return true;
        
        if (root -> val <= lower || root -> val >= upper) return false;
        
        return isValidBST(root -> left, lower, root -> val) && isValidBST(root -> right, root -> val, upper);
    }
};
```

解决该问题有两种思路，一种是 用 long long 来初始化 lower 和 upper，这样测试数据都是 int 型的，所以就不会有错；另一种方法就是单独判断处理这种特殊情况，但是单独处理陷阱很多，网上给出的我解法在 leetcode 上的测试用例也是错的。解法如下：

### long long

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
    bool isValidBST(TreeNode* root) {
        if (! root) return true;
        
        return isValidBST(root, LLONG_MIN, LLONG_MAX);
    }
    
    bool isValidBST(TreeNode* root, long long lower, long long upper) {
        if (!root) return true;
        if (root -> val <= lower || root -> val >= upper) return false;
                
        return isValidBST(root -> left, lower, root -> val) && isValidBST(root -> right, root -> val, upper);
    }
};
```

### without long long

[参考资料](https://algorithm.yuanbin.me/zh-hans/binary_search_tree/validate_binary_search_tree.html#) 有错

错误解法，像这样的用例`[3,2,null,1,-2147483648]`会输出错误答案。
```cpp
class Solution {
public:
    bool isValidBST(TreeNode* root) {
        if (! root) return true;
        
        return isValidBST(root, INT_MIN, INT_MAX);
    }
    
    bool isValidBST(TreeNode* root, int lower, int upper) {
        if (!root) return true;
        if (root -> val <= lower || root -> val >= upper) {
            bool rightMax = (root -> val == INT_MAX && !root -> right && (!root -> left || root -> left -> val != INT_MAX));
            bool leftMin = (root -> val == INT_MIN && !root -> left && (!root ->  right || root -> right -> val != INT_MIN));
            if (!(rightMax || leftMin)) return false;
        }
                
        return isValidBST(root -> left, lower, root -> val) && isValidBST(root -> right, root -> val, upper);
    }
};
```

###　总结
看到了这样一篇　[解答](https://leetcode.com/problems/validate-binary-search-tree/discuss/32104/C++-in-order-traversal-and-please-do-not-rely-on-buggy-INT_MAX-INT_MIN-solutions-any-more) 提到了`please do not rely on buggy INT_MAX, INT_MIN solutions any more`。另外，上面的 long long 解法感觉也是相当不靠谱。。`Why rely on LLONG_MIN? Don’t you think if you are in a true interview, the next question the interviewer will ask, “okay I have changed my input type to long” then? I am sorry but using long limit to such question is like taking shortcut to just get your answer accepted and won’t help you in real interview.`

###　正确的递归解法

不存上界下界的值，而存结点

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
    bool isValidBST(TreeNode* root) {
        return isValidBST(root, NULL, NULL);
    }
    
    bool isValidBST(TreeNode* root, TreeNode* minNode, TreeNode* maxNode) {
        if (! root) return true;
        
        if (minNode && root -> val <= minNode -> val || maxNode && root -> val >= maxNode -> val) return false;
        
        return isValidBST(root -> left, minNode, root) && isValidBST(root -> right, root, maxNode);
    }
};
```

### 复杂度

递归遍历所有节点，时间复杂度为 $$O(n)$$, 递归最差情况下，空间复杂度为 $$O(n)$$.

## 解法2 - 中序遍历

二叉搜索树的中序遍历结果应该是一个递增数列

### 递归
[参考](https://leetcode.com/problems/validate-binary-search-tree/discuss/32104/C++-in-order-traversal-and-please-do-not-rely-on-buggy-INT_MAX-INT_MIN-solutions-any-more)

这里要特别注意 pre 在函数调用过程中发生的改变应该被记录下来，所以不能传一个 TreeNode* 型的指针，而应该为 指向指针的指针，另外传参时不能这样写 `TreeNode** pre = NULL; return isValidBST(root, pre);` 会报空指针，因为传给函数的 pre 应该是一个已经初始化好了的指针的地址

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
    bool isValidBST(TreeNode* root) {
        TreeNode* pre = NULL;
        
        return isValidBST(root, &pre);
    }
    
    bool isValidBST(TreeNode* root, TreeNode** ppre) {
        if (!root) return true;
        
        if (!isValidBST(root -> left, ppre)) return false;
        
        if ((*ppre) && root -> val <= (*ppre) -> val) return false;
        
        *ppre = root;
        return isValidBST(root -> right, ppre);
    }
};
```

更清晰的做法是传一个指针的引用

```cpp
class Solution {
public:
    bool isValidBST(TreeNode* root) {
        TreeNode* pre = NULL;
        
        return isValidBST(root, pre);
    }
    
    bool isValidBST(TreeNode* root, TreeNode* &pre) {
        if (!root) return true;
        
        if (!isValidBST(root -> left, pre)) return false;
        
        if (pre && root -> val <= pre -> val) return false;
        
        pre = root;
        return isValidBST(root -> right, pre);
    }
};
```

### 迭代

和递归一样的思路，用一个 pre 指针存上一次的结点

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
    bool isValidBST(TreeNode* root) {
        TreeNode* curr = root;
        TreeNode* pre = NULL;
        
        stack<TreeNode*> s;
        
        while (curr || !s.empty()) {
            while (curr) {
                s.push(curr);
                curr = curr -> left;
            }
            
            curr = s.top();
            
            if (pre && pre -> val >= curr -> val) return false;
            
            pre = curr;
            curr = curr -> right;
            s.pop();
        }
        return true;
    }
};
```

## Reference

[LeetCode: Validate Binary Search Tree 解题报告 - Yu's Garden - 博客园](http://www.cnblogs.com/yuzhangcmu/p/4177047.html) - 提供了4种不同的方法，思路可以参考。