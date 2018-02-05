# Construct Binary Tree from Preorder and Inorder Traversal

Tags: Tree, Medium

## Problem

Given preorder and inorder traversal of a tree, construct the binary tree.

Note:
You may assume that duplicates do not exist in the tree.

For example, given

preorder = [3,9,20,15,7]
inorder = [9,3,15,20,7]
Return the following binary tree:

    3
   / \
  9  20
    /  \
   15   7

## 解法1 - 递归

本题与 `剑指 offer` 一书的 面试题6 相同。
前序遍历的第一个值就是根节点，那么仍然可以采用以下的方式处理：
通过前序遍历找到根节点
通过根节点将中序遍历数据拆分成两部分（遍历或者哈希，因为限定了树中没有重复元素）
对于各个部分重复上述操作。

下面给出的解法实际上是不全面的，但是在 leetcode 上不太好抛异常，定义全局变量也比较麻烦，就默认输入数据都是合法的了。
例如，按理说，`if (preorderStart == preorderEnd) return root;` 应该还要判断 inorderStart 与 inorderEnd 是否相等，还要判断 * inoederStart 与 * preorderStart 是否相等，若是不相等，不能返回 NULL（因为 NULL 在本题中本身就是有意义的，代表空结点），而应该终止程序。
在中序遍历序列中寻找根结点的位置时，也应该判断是否能找得到，如果找不到也应该终止程序。

边界情况的处理：
下面的解法的递归跳出情况是 `if (preorderStart == preorderEnd) return root;` 所以后面递归生成 root -> left 和 root -> right 时，就必须判断 左，右子树存在的情况，否则会出错。
但是如果递归跳出处理成 `if (preorderStart > preorderEnd) return NULL;` 则后面就不需要判断左右子树是否存在了，因为即时不存在也会递归返回 root。所以前后的处理一定要匹配。


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
    TreeNode* buildTree(vector<int>& preorder, vector<int>& inorder) {
        if (preorder.empty() || inorder.empty()) return NULL;
        
        int* preorderStart = &preorder[0];
        int* preorderEnd = preorderStart + preorder.size() - 1;
        int* inorderStart = &inorder[0];
        int* inorderEnd = inorderStart + inorder.size() - 1;
        
        return buildTree(preorderStart, preorderEnd, inorderStart, inorderEnd);
    }
    
    TreeNode* buildTree(int* preorderStart, int* preorderEnd, int* inorderStart, int* inorderEnd) {
        TreeNode* root = new TreeNode(* preorderStart);
        
        if (preorderStart == preorderEnd) return root;
        
        int* inorderRoot = inorderStart;
        while (inorderRoot <= inorderEnd && *inorderRoot != *preorderStart) ++ inorderRoot;
        
        int leftLength = inorderRoot - inorderStart;
        if (leftLength > 0) root -> left = buildTree(preorderStart + 1,  preorderStart + leftLength, inorderStart, inorderRoot - 1);
        if (inorderEnd - inorderRoot > 0) root -> right = buildTree(preorderStart + leftLength + 1, preorderEnd, inorderRoot + 1, inorderEnd);
        
        return root;
    }
};
```

### 优化

为了保证快速的在中序遍历结果里面找到根节点，引入了 unordered_map,性能提升明显

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
    unordered_map<int, int> map;
    
    TreeNode* buildTree(vector<int>& preorder, vector<int>& inorder) {
        if (preorder.empty() || inorder.empty()) return NULL;
        
        int* preorderStart = &preorder[0];
        int* preorderEnd = preorderStart + preorder.size() - 1;
        int* inorderStart = &inorder[0];
        int* inorderEnd = inorderStart + inorder.size() - 1;
        
        for (int i = 0; i < inorder.size(); ++i) {
            map[inorder[i]] = i;
        }
        
        return buildTree(preorderStart, preorderEnd, inorderStart, inorderEnd);
    }
    
    TreeNode* buildTree(int* preorderStart, int* preorderEnd, int* inorderStart, int* inorderEnd) {
        TreeNode* root = new TreeNode(* preorderStart);
        
        if (preorderStart == preorderEnd) return root;
        
        int leftLength = map[*preorderStart] - map[*inorderStart];
        int* inorderRoot = inorderStart + leftLength;
        
        if (leftLength > 0) root -> left = buildTree(preorderStart + 1,  preorderStart + leftLength, inorderStart, inorderRoot - 1);
        if (inorderEnd - inorderRoot > 0) root -> right = buildTree(preorderStart + leftLength + 1, preorderEnd, inorderRoot + 1, inorderEnd);
        
        return root;
    }
};
```

### 另一种写法

考虑到本题中并没有用到 vector 的特性，只是用来存放固定数组的，所以之前给出的解法都是提前定义了四个指针，传给递归的也是四个指针。不过其实也是可以使用 vector + index 来传参，但是这样就得传 6 个参数。考虑到 vector 类型的 iterator 其实本质上也是指针，也可以把上面的程序改写成 `vector<int>::iterator` 类型。

注意： preorder.end() 返回的并不是最后一个元素，所以要特别小心边界条件的处理
`由end操作返回的迭代器指向vector的“末端元素的下一个”。通常称为超出末端迭代器(off-the-end iterator)，表明它指向了一个不存在的元素。如果vector为空，begin返回的迭代器与end返回的迭代器相同。由end操作返回的迭代器并不指向vector中任何实际的元素，相反，它只是起一个哨兵（sentinel）的作用，表示我们已处理完vector中所有元素。`

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
    unordered_map<int, int> map;
    
    TreeNode* buildTree(vector<int>& preorder, vector<int>& inorder) {
        if (preorder.empty() || inorder.empty()) return NULL;
        
        for (int i = 0; i < inorder.size(); ++i) {
            map[inorder[i]] = i;
        }
        
        return buildTree(preorder.begin(), preorder.end(), inorder.begin(), inorder.end());
    }
    
    TreeNode* buildTree(vector<int>::iterator preorderStart, vector<int>::iterator preorderEnd, vector<int>::iterator inorderStart, vector<int>::iterator inorderEnd) {
        TreeNode* root = new TreeNode(* preorderStart);
        
        if (preorderStart == preorderEnd) return NULL;
        
        int leftLength = map[*preorderStart] - map[*inorderStart];
        
        root -> left = buildTree(preorderStart + 1,  preorderStart + leftLength + 1, inorderStart, inorderStart + leftLength);
        root -> right = buildTree(preorderStart + leftLength + 1, preorderEnd, inorderStart + leftLength + 1, inorderEnd);
        
        return root;
    }
};
```

## 解法2 - 迭代

[参考解法](https://leetcode.com/problems/construct-binary-tree-from-preorder-and-inorder-traversal/discuss/34555/The-iterative-solution-is-easier-than-you-think!)