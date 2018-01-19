# https://leetcode.com/problems/convert-sorted-array-to-binary-search-tree/description/

Tags: Array, Easy, Tree

## Question

Given an array where elements are sorted in ascending order, convert it to a height balanced BST.

For this problem, a height-balanced binary tree is defined as a binary tree in which the depth of the two subtrees of every node never differ by more than 1.


Example:

Given the sorted array: [-10,-3,0,5,9],

One possible answer is: [0,-3,9,-10,null,5], which represents the following height balanced BST:

      0
     / \
   -3   9
   /   /
 -10  5

## 题解 - 递归

注意处理 begin > end 以及 begin = end 的递归退出条件。

当数组为偶数位时，根元素应该为第一半 + 1 的那个元素，所以 middle = (begin + end + 1) >> 1;

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
    TreeNode *sortedArrayToBST(vector<int> &num) {
        if (num.size() == 0) return NULL;
        
        return generateBST(num, 0, num.size() - 1);
    }
    
    TreeNode* generateBST(vector<int>& num, int begin, int end) {
        if (begin > end) return NULL;
        if (begin == end) return new TreeNode(num[begin]);
        
        int middle = (begin + end + 1) >> 1;
        TreeNode* root = new TreeNode(num[middle]);
        root -> left = generateBST(num, begin, middle - 1);
        root -> right = generateBST(num, middle + 1, end);
        
        return root;
    }
};
```

### 另一种写法

这里的写法 end 不代表可取到的元素，而代表数组的最后一个元素 + 1，这样当出现 begin = end 的情况就和 begin > end 一样返回 NULL 即可，因为最后的叶子节点已经涵盖到了 root 里了。

```cpp
class Solution {
public:
    TreeNode *sortedArrayToBST(vector<int> &num) {
        return ArrayToBST(num,0,num.size());
    }
    TreeNode *ArrayToBST(vector<int> &num,int begin,int end)
    {
        if(begin >= end)
            return NULL;
        int mid = (begin+end)>>1;
        TreeNode *root = new TreeNode(num[mid]);
        root->left = ArrayToBST(num,begin,mid);
        root->right = ArrayToBST(num,mid+1,end);
        return root;
    }
};
```

### 源码分析

以上两种写法的递归退出条件都要明确。

### 复杂度分析

时间复杂度还是一次树遍历$$O(n)$$，总的空间复杂度是栈空间$$O(logn)$$加上结果的空间$$O(n)$$，额外空间是$$O(logn)$$，总体是$$O(n)$$。
